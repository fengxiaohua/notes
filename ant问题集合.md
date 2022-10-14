# ant-design问题

## ant-select allowClear失效

https://blog.csdn.net/agua001/article/details/115631745

## ant-select 多选情况下下拉样式问题

```
几个重点的属性
:max-tag-count="1"				// 最多显示几个tag
:max-tag-text-length="3"	// 每个tag最长的长度，两个字符一个长度
你得算算不能超过最长的宽度，或者你设置为min-width来设置最小宽度实现动态横向扩展

/deep/ .ant-select-selection {
  padding: 4px 0;
  min-width: 200px;
  min-height: 40px;
  width: max-content;
  border: 1px solid #EEEEEE;
  border-radius: 6px;
}

// 多选情况下下拉小箭头可居中的设置（单选也没问题）
/deep/ .ant-select-selection--multiple .ant-select-arrow{
  top: 50%;
}
```

## ant-button loading 样式问题

```
.ant-btn.ant-btn-loading:not(.ant-btn-circle):not(.ant-btn-circle-outline):not(.ant-btn-icon-only) {
    width: 100px;
    padding-left: 20px;
 }
```

## table中template渲染ant-select,ant-input等组件涉及事件回调更新表格问题

```
1. 每个组件绑定与表格相同的key，表格渲染更新，组件同时更新；
2. 每次事件会自动更新表格数据，如果表格不更新，自己手动更新key值（但是只能更新一次，多次更新会导致页面闪烁问题）
```

## ant-select-tree value重复导致控制台警告从而导致页面卡顿问题

```
value设置为不同即可
```

