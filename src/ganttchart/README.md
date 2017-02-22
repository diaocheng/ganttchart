## ganttchart插件使用说明
```javascript
    <ganttchart
        :data="data"
        :draggable="draggable"
        :status="status" @sort="sort"
    >
        <!--列标题-->
        <template 
            slot="thead"
            scope="scope"
        ></template>
        <!--图表节点标题-->
        <template 
            slot="title"
            scope="scope"
        ></template>
        <!--列表正文显示区域-->
        <template scope="scope">
            <div class="grid">
                <div>{{ scope.row.name }}</div>
                <div>{{ scope | statusfilter }}</div>
            </div>
        </template>
    </ganttchart>
```

## 参数说明
参数 | 类型 | 说明 | 是否可选 | 默认值 | 备注
--- | --- | --- | --- | --- | ---
data | Array | 甘特图显示主要数据 | 是 | [] | 详情如下所示
status | Object | 任务进行进度样式 | 是 | 如下所示 | 详情如下所示
draggable | Boolean | 是否可拖拽排序 | 是 | false |
```javascript
    const data = [{
        "name": "test01", // 任务名称
        "time": [{
            "status": 1,// 当前任务进行的状态
            // 期望时间
            "expectedtime": {
                "starttime": "2017-01-17 10:38:55",
                "endtime": "2017-01-18 10:38:55"
            },
            // 实际时间
            "actualtime": {
                "starttime": "2017-01-17 10:38:56",
                "endtime": "2017-01-18 10:38:55"
            }
        },{/**重新做本流程*/}]
        // 附加的其他参数(插件不需要，用户可自定义)
    }]
    // 默认值及其基本结构
    const status = {
        "0": "complete",
        "1": "doing",
        "2": "notstart"
    }
```
## 事件
事件名 | 回调参数 | 说明
--- | --- | ---
sort | 排序后的任务列表数据 | 拖拽排序事件回掉