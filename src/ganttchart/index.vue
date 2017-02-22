<template>
    <div class="ganttchart">
        <table>
            <thead>
                <tr>
                    <th>节点名称</th>
                    <th v-for="col in cols">
                        <slot
                            name="thead"
                            :col="col"
                        >
                            <div>{{ col.starttime }}</div>
                            <div>{{ col.endtime }}</div>
                        </slot>
                    </th>
                </tr>
            </thead>
            <tbody ref="chart">
                <tr v-for="row in rows">
                    <th>
                        <slot
                            name="title"
                            :row="row"
                        >
                            <div>{{ row.name }}</div>
                            <div>{{ row.time[0].expectedtime.starttime }}</div>
                            <div>{{ row.time[0].expectedtime.endtime }}</div>
                        </slot>
                    </th>
                    <td
                        v-for="(col, index) in cols"
                        :class="getClass(row, col)"
                    >
                        <slot
                            :row="row"
                            :col="col"
                            v-if="row.cols[col.key]"
                        >
                            <div class="grid">
                                <div>{{ row.name }}</div>
                                <div>{{ this.status[row.cols[col.key].status] }}</div>
                            </div>
                        </slot>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>
<script>
    import _ from 'lodash';
    import sortable from 'sortablejs';
    export default {
        name: 'ganttchart',
        props: {
            data: {
                type: Array,
                default() {
                    return [];
                }
            },
            draggable: {
                type: Boolean,
                default() {
                    return false;
                }
            },
            status: {
                type: Object,
                default() {
                    return {}
                }
            }
        },
        data() {
            return {
                sort: []
            }
        },
        computed: {
            chart() {
                const rows = _.cloneDeep(this.data);
                const cols = [];
                const temp = {};
                // 获取行数信息
                for (let i = 0, length = rows.length; i < length; i++) {
                    const time = rows[i].time;
                    // 把时间转换为列下标
                    rows[i].cols = {};
                    for (let j = 0, len = time.length; j < len; j++) {
                        const expectedtime = time[j].expectedtime;
                        const actualtime = time[j].actualtime;
                        const starttime = actualtime.starttime || expectedtime.starttime;
                        const endtime = actualtime.endtime || expectedtime.endtime;
                        let key = `${starttime}-${endtime}`;
                        // 判断是否重复
                        if (!temp[key]) {
                            temp[key] = true;
                            cols.push({
                                starttime: starttime,
                                endtime: endtime,
                                key: key
                            });
                        }
                        rows[i].cols[key] = rows[i].time[j];
                    }
                }
                // 行从上到下时间依次增大
                rows.sort(function (first, next) {
                    let time1 = first.time[0];
                    let time2 = next.time[0];
                    if (time1 && time2) {
                        time1 = new Date(time1.expectedtime.starttime).getTime();
                        time2 = new Date(time2.expectedtime.starttime).getTime();
                    }
                    return time1 > time2;
                });
                // 列从左到右时间依次增大
                cols.sort(function (first, next) {
                    let time1 = new Date(first.starttime).getTime();
                    let time2 = new Date(next.starttime).getTime();
                    return time1 > time2;
                });
                return {
                    rows: rows,
                    cols: cols
                }
            },
            rows() {
                return this.chart.rows
            },
            cols() {
                return this.chart.cols
            }
        },
        mounted() {
            // 存储排序后的任务数据
            this.sort = _.cloneDeep(this.rows);
            // 拖拽插件
            const sort = new sortable(this.$refs.chart, {
                group: 'chart',
                sort: true,
                animation: 150,
                handle: '.drag-handle',
                draggable: 'tr',
                ghostClass: "chart-drag-ghost",
                chosenClass: "chart-drag-chosen",
                dragClass: "chart-drag",
                scroll: false,
                // 拖拽排序回调函数
                onSort: (e) => {
                    const data = _.cloneDeep(this.sort);
                    // 改变data中的数据顺序
                    const temp = data[e.oldIndex];
                    // 把当前值从data中去掉
                    // 从而让后面的元素下标减小一
                    data.splice(e.oldIndex, 1);
                    // 把当前值添加到新的位置
                    // 并让后面元素下标加一
                    data.splice(e.newIndex, 0, temp);
                    this.sort = data;
                    this.$emit('sort', _.cloneDeep(this.sort));
                }
            });
        },
        methods: {
            getClass(row, col) {
                const tmp = row.cols[col.key];
                if (!tmp) {
                    return;
                }
                const style = [];
                if (this.draggable) {
                    style.push('drag-handle');
                }
                // 样式
                style.push(this.status[tmp.status]);

                return style;
            }
        }
    }
</script>
<style
    src="./ganttchart.less"
    lang="less"
></style>