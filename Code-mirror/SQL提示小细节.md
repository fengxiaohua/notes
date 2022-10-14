https://juejin.cn/post/6844904165173362696

```
<template>
  <div class="code-mirror-clss relative !h-full w-full overflow-hidden" ref="el"></div>
</template>

<script lang="ts" setup>
  import { ref, onMounted, onUnmounted, watchEffect, watch, unref, nextTick } from 'vue'
  import { useDebounceFn } from '@vueuse/core'
  import { useAppStore } from '/@/store/modules/app'
  import { useWindowSizeFn } from '/@/hooks/event/useWindowSizeFn'
  import CodeMirror from 'codemirror'
  import { MODE } from './../typing'
  // css
  import './codemirror.css'
  import 'codemirror/theme/idea.css'
  import 'codemirror/theme/material-palenight.css'
  // modes
  import 'codemirror/mode/javascript/javascript'
  import 'codemirror/mode/css/css'
  import 'codemirror/mode/htmlmixed/htmlmixed'

  import 'codemirror/addon/hint/show-hint.css'
  import 'codemirror/mode/sql/sql'

  import 'codemirror/addon/hint/show-hint'
  import 'codemirror/addon/hint/sql-hint'

  const props = defineProps({
    mode: {
      type: String as PropType<MODE>,
      default: MODE.JSON,
      validator(value: any) {
        // 这个值必须匹配下列字符串中的一个
        return Object.values(MODE).includes(value)
      },
    },
    value: { type: String, default: '' },
    readonly: { type: Boolean, default: false },
  })

  const emit = defineEmits(['change'])

  const el = ref()
  let editor: Nullable<CodeMirror.Editor>

  const debounceRefresh = useDebounceFn(refresh, 100)
  const appStore = useAppStore()

  watch(
    () => props.value,
    async (value) => {
      await nextTick()
      const oldValue = editor?.getValue()
      if (value !== oldValue) {
        editor?.setValue(value ? value : '')
      }
    },
    { flush: 'post' },
  )

  watchEffect(() => {
    editor?.setOption('mode', props.mode)
  })

  watch(
    () => appStore.getDarkMode,
    async () => {
      setTheme()
    },
    {
      immediate: true,
    },
  )

  function setTheme() {
    unref(editor)?.setOption(
      'theme',
      appStore.getDarkMode === 'light' ? 'idea' : 'material-palenight',
    )
  }

  function refresh() {
    editor?.refresh()
  }

  async function init() {
    const addonOptions = {
      autoCloseBrackets: true,
      autoCloseTags: true,
      foldGutter: true,
      smartIndent: true,
      matchBrackets: true, // 括号匹配
      extraKeys: { Ctrl: 'autocomplete' }, //自定义快捷键
      hintOptions: {
        completeSingle: false, // 当只敲击了一个单个的字母时，在不打开提示框的情况下，是否认为这个字母是提示语的一部分(解决无法删除提示问题)
        //自定义提示选项
        tables: {
          users: ['name', 'score', 'birthDate'],
          countries: ['name', 'population', 'size'],
        },
      },
      gutters: ['CodeMirror-linenumbers'],
    }

    editor = CodeMirror(el.value!, {
      value: '',
      mode: props.mode,
      readOnly: props.readonly,
      tabSize: 2,
      theme: 'material-palenight',
      lineWrapping: true,
      lineNumbers: true,
      ...addonOptions,
    })
    editor?.setValue(props.value)
    setTheme()
    editor?.on('change', () => {
      emit('change', editor?.getValue())
    })
    // 代码自动提示功能，记住使用cursorActivity事件不要使用change事件，这是一个坑，那样页面直接会卡死
    // 如果使用 cursorActivity 当光标或选区移动或对编辑器内容进行任何更改时，将触发该事件 所以Enter换行也会，避免这种情况
    // 使用 inputRead 代替 cursorActivity 每当从隐藏的文本区域中读取新输入（由用户键入或粘贴）时，就会触发（可以提示体验）
    editor?.on('inputRead', function () {
      editor?.showHint()
    })
  }

  onMounted(async () => {
    await nextTick()
    init()
    useWindowSizeFn(debounceRefresh)
  })

  onUnmounted(() => {
    editor = null
  })
</script>
<style lang="less" scoped>
  .code-mirror-clss {
    border: 1px solid #ffe4dd;
    border-radius: 12px;
    background: #fffaf9;
  }

  :deep(.CodeMirror-line) {
    background: #fffaf9;
  }
</style>

```

