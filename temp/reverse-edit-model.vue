<!--
 * @Author: your name
 * @Date: 2021-08-19 11:15:00
 * @LastEditTime: 2021-08-19 15:12:24
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /vulcan/frontend/vulcan-assets-development /src/components/import/components/reverse-edit-model.vue
-->
<template>
  <a-form-model ref="reverseEditForm" class="reverse-content" label-align="left" :model="reverseEditForm" :rules="rules" :label-col="labelCol" :wrapper-col="wrapperCol">
    <a-form-model-item label="所属主题" prop="formTheme">
      <a-tree-select
        v-model="reverseEditForm.category"
        :replace-fields="{children: 'subThemeList', title: 'themeName', key: 'themeCode', value: 'themeCode' }"
        show-search
        tree-node-filter-prop="title"
        :dropdown-style="{ maxHeight: '400px', overflow: 'auto' }"
        placeholder="请选择所属主题"
        allow-clear
        tree-default-expand-all
        :tree-data="themeTreeList"
      />
    </a-form-model-item>
    <a-form-model-item label="数据连接类型" prop="dataConnectType">
      <a-select
        v-model="reverseEditForm.dataConnectType"
        show-search
        allow-clear
        placeholder="请选择数据连接类型"
        option-filter-prop="children"
        :filter-option="filterOption"
        @change="handleDataConnectTypeChange"
      >
        <a-select-option v-for="(item, index) in dataConnectTypes" :key="index" :value="item">
          {{ item }}
        </a-select-option>
      </a-select>
    </a-form-model-item>
    <a-form-model-item label="数据库" prop="dataBase">
      <a-select
        v-model="reverseEditForm.dataBase"
        show-search
        allow-clear
        placeholder="请选择数据库"
        option-filter-prop="children"
        :filter-option="filterOption"
        @change="handleDataBaseChange"
      >
        <a-select-option v-for="(item, index) in dataConnectTypes" :key="index" :value="item">
          {{ item }}
        </a-select-option>
      </a-select>
    </a-form-model-item>
    <a-form-model-item label="更新已有表">
      <a-radio-group v-model="reverseEditForm.isUpdate" name="upDateGroup">
        <a-radio :value="0">
          不更新
        </a-radio>
        <a-radio :value="1">
          更新
        </a-radio>
      </a-radio-group>
    </a-form-model-item>
    <a-form-model-item class="reverse-content-item" label="数据表" prop="dataTable">
      <div class="reverse-content-item-header">
        <a-radio-group v-model="reverseEditForm.dataTable" class="reverse-content-item-header__group" name="dataTableGroup">
          <a-radio :value="0">
            全部
          </a-radio>
          <a-radio :value="1">
            部分
          </a-radio>
        </a-radio-group>
        <div class="reverse-content-item-header__search">
          <a-space>
            <a-input v-model="reverseEditForm.searchValue" />
            <a-button
              class="search-btn"
              type="primary"
            >
              查询
            </a-button>
          </a-space>
        </div>
      </div>
      <!-- 表格 -->
      <div class="table-container">
        <vxe-grid
          ref="senseTable"
          v-bind="gridOptions"
          :columns="columns"
          :data="table.list"
          :loading="table.isTableLoading"
          height="auto"
        />
      </div>
      <!-- 分页 -->
      <div class="table-pager">
        <vxe-pager
          :loading="table.isTableLoading"
          :current-page="table.currentPage"
          :page-size="table.pageSize"
          :page-sizes="[10, 20, 50, 100]"
          :total="table.total"
          :layouts="['Total','PrevPage', 'JumpNumber', 'NextPage', 'Sizes','FullJump']"
          @page-change="handlePageChange"
        />
      </div>
    </a-form-model-item>
  </a-form-model>
</template>

<script>
export default {
  data() {
    return {
      // 树数据
      themeTreeList: [],
      // 表单布局
      labelCol: { span: 4 },
      wrapperCol: { span: 20 },
      // 表单
      reverseEditForm: {
        isUpdate: true,
        isNotUpdate: false
      },
      // 校验规则
      rules: {
        formTheme: [
          { required: true, message: '请选择所属主题', trigger: 'blur' }
        ],
        dataConnectType: [
          { required: true, message: '请选择数据连接类型', trigger: 'blur' }
        ],
        dataBase: [
          { required: true, message: '请选择数据库', trigger: 'blur' }
        ],
        dataTable: [
          { required: true, message: '请选择是否更新数据表', trigger: 'blur' }
        ]
      },
      // 数据连接类型
      dataConnectTypes: ['a', 'b', 'c'],
      // 表格配置项
      gridOptions: {
        resizable: true,
        autoResize: true,
        stripe: true,
        class: 'sense-table',
        headerCellClassName: 'sense-table-header-cell',
        headerRowClassName: 'sense-table-header-row',
        cellClassName: 'sense-table-cell',
        rowClassName: 'sense-table-row',
        // border: 'none',
        sortConfig: {
          trigger: 'cell',
          remote: true
        }
      },
      // 表格列
      columns: [
        {
          field: 'id',
          title: '行号',
          titleHelp: { message: '数据在Excel表格中的行数' }
        },
        {
          field: 'baseInfo',
          title: '基本信息'
        },
        {
          field: 'result',
          title: '结果'
        },
        {
          field: 'desc',
          title: '备注'
        }
      ],
      // 表格相关数据
      table: {
        list: [
          { id: 2, baseInfo: 'xxx(主题)', result: '成功', desc: '备注' },
          { id: 3, baseInfo: 'xxx(主题)', result: '失败', desc: '备注' }
        ],
        isTableLoading: false, // 表格loading
        property: 'id', // 表格排序字段名
        order: 'desc', // 表格排序方式 desc | asc
        currentPage: 1, // 当前页
        pageSize: 10, // 当前页码
        total: 0// 列表总条数
      }
    }
  },
  methods: {
    /**
    * @description 过滤
    */
    filterOption(input, option) {
      return (
        option.componentOptions.children[0].text.toLowerCase().indexOf(input.toLowerCase()) >= 0
      )
    },
    /**
     * @description 数据连接类型选择
     */
    handleDataConnectTypeChange(val) {
      this.reverseEditForm.dataConnectType = val
      console.log(val)
    },
    /**
     * @description 数据库选择
     */
    handleDataBaseChange(val) {
      this.reverseEditForm.dataBase = val
      console.log(val)
    },
    /**
     * @description: 更改分页
     * @param {number} currentPage 当前页
     * @param {number} pageSize 每页条数
     */
    handlePageChange({ currentPage, pageSize }) {
      this.table.currentPage = currentPage
      this.table.pageSize = pageSize
      this.getList()
    },
    /**
     * @description: 获取列表数据
     */
    getList() {
      this.table.isTableLoading = true
      const { currentPage, pageSize, property, order } = this.table
      const parmas = {
        page: currentPage,
        pageSize
        // 其他参数
      }
      // 排序字段
      if (property && order) {
        parmas.sorts = `${property}:${order}`
      }

      // getTableListTest(parmas).then(res => {
      //   this.table.list = res.data?.content?.list
      //   // 为了掩饰‘无效’的数据状态, 无意义
      //   this.table.list[0].drStatus = '0'
      //   this.table.isTableLoading = false
      //   this.table.total = res.data?.content?.total
      // })
    }
  }
}
</script>

<style lang="less" scoped>
form .ant-select, form .ant-cascader-picker {
    /* width: 100%; */
    width: 180px;
}

.reverse-content {
    &-item {
        display: flex;
        &-header {
            display: flex;
            justify-content: space-between;
            &__group {
        display: flex;
        justify-content: flex-start;
    }
    &__search {
        display: flex;
        justify-content: flex-end;
    }
        }
    }
}
</style>
