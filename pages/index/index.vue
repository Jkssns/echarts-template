<template>
	<view class="content">
		<echarts-template
			id="echartsId"
			v-if="option"
			:option="option"
		></echarts-template>
	</view>
</template>

<script>
	import EchartsTemplate from '@/components/echarts-template.vue'
	export default {
		components: {
			EchartsTemplate
		},
		data() {
			return {
				option: null,
				// 后台返回的数据, 通过是否通过判断颜色 isPass true: 通过|红色 false: 不通过|蓝色
				originData: { isPass: false },
			}
		},
		mounted() {
			const originData = JSON.parse(JSON.stringify(this.originData))
			this.option = {
				// 加入绑定的dom Id
				id: 'echartsId', 
				// echarts要执行的事件
				// handleAction: function(currentChart) {
				// 	console.log(currentChart) // 当前echarts实例
				// 	currentChart.on('click', (params) => {
				// 		console.log(params) // { ...params }
				// 	})
				// },
				xAxis: {
					type: 'category',
					data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']
				},
				yAxis: {
					type: 'value'
				},
				series: [{
					data: [120, 200, 150, 80, 70, 110, 130],
					type: 'bar',
					itemStyle: {
						normal: {
							// 不能使用箭头函数
							color: function(params) {
								console.log(this) // { isPass: false }
								// 通过this获取后台返回的数据
								if (this.isPass) {
									return 'red'
								} else {
									return 'blue'
								}
							},
							// 自定义datas放在同一级里面
							datas: originData,
						}
					},
				}]
			}
		}
	}
</script>

<style>
	.content {
		width: 500px;
		height: 300px;
	}
</style>
