<template>
    <view :prop="option" class="echarts__item" ></view>
</template>

<script>
	/* 如果有函数则转字符串，并加标识符 */
	const json = (obj) =>  {
		for(let i in obj) {
			if (typeof obj[i] === 'object') {
				json(obj[i])
			} else if(typeof obj[i] === 'function' && i !== 'handleAction'){
				obj[i] = obj[i].toString() + '$func$tion$'
			}
		}
		return obj
	}
	export default {
		name: 'AEcharts',
		props: {
			option: Object,
        },
		data() {
			return {
                description: 'echarts组件',
			}
		},
		created() {
			this.option = json(this.option)
		},
	}
</script>

<script module="echarts" lang="renderjs" >
	import * as echarts from 'echarts';
	/* 解析字符串函数，创建动态作用域并改变this指向 */
	const parse = (obj) => {
        for(let i in obj) {
            if (typeof obj[i] === 'object') {
                parse(obj[i])
            } else {
                if (typeof obj[i] === 'string' && obj[i].includes('$func$tion$')) {
					var a 
					obj[i] = obj[i].replace(/\$func\$tion\$/g, '')
					obj[i] = eval('a =' + obj[i])
					if (obj['datas']) {
						obj[i] = obj[i].bind(obj['datas'])
					} 
                }
            }
        }
        return obj
	}
	let myChart

	export default {
		mounted() {
			if (window.echarts && typeof window.echarts === 'object') {
				this.init()
			} else {
				// 动态引入类库
				const script = document.createElement('script')
				script.src = 'static/echarts.min.js'
				script.onload = this.init.bind(this)
				document.head.appendChild(script)
			}
		},
	     watch: {
            option: {
                deep: true,
                handler() {
                    this.init()
                }
            }
        },
		methods: {
			init() {
				this.option = parse(this.option)
				let dom = document.getElementById(this.option.id);
				if (dom) {
					myChart = echarts.init(dom)
					if (this.option && typeof this.option === "object") {
						myChart.clear();
						myChart.setOption(this.option, true);
						myChart.resize();
						/* 把当前实例传入父组件，父组件自定义执行方法 */
						if (this.option.handleAction) {
							this.option.handleAction(myChart)
						}
					}
				}
			},
		}
	}
</script>


<style scoped lang="scss">
	.echarts__item {
		display: inline-block;
		width: 100%;
		height: 100%;
	}
</style>
