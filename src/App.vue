<template>
	<div>
		<ganttchart
			:data="data"
			:draggable="draggable"
			:status="status" @sort="sort"
		>
			<template scope="scope">
				<div class="grid">
					<div>{{ scope.row.name }}</div>
					<div>{{ scope | statusfilter }}</div>
				</div>
			</template>
		</ganttchart>
	</div>
</template>
<script>
	import Ganttchart from './ganttchart';
	import data from 'data';
	import status from 'status';
	export default {
		name: 'statisticsgantt',
		data() {
			return {
				data: data,
				status: status,
				draggable: true
			}
		},
		components: {
			Ganttchart
		},
		filters: {
			statusfilter(value) {
				const row = value.row;
				const col = value.col;
				const tmp = row.cols[col.key];
				if (!tmp) {
					return;
				}
				// 完成状态文字
				let text = '';
				switch (tmp.status) {
					case 0:
						text = '已完成';
						break;
					case 1:
						text = '正在进行';
						break;
					case 2:
						text = '还未开始';
						break;
					default:
						text = '还未开始';
						break;
				}
				return text;
			}
		},
		methods: {
			sort(data) {
				console.log(data)
			}
		}
	}
</script>
<!-- 只把样式限定在本模块 -->
<style>
body {
	padding: 0;
	margin: 0;
	line-height: 1.4;
}
</style>