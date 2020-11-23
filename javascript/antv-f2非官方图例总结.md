![](https://imgkr2.cn-bj.ufileos.com/a1283032-d398-4bb8-accb-44ffd431c52b.png?UCloudPublicKey=TOKEN_8d8b72be-579a-4e83-bfd0-5f6ce1546f13&Signature=KHZD6u5QbDU9RwD0WkD3VFb3bfc%253D&Expires=1606048638)

# 一、前言

最近在开发钉钉小程序，然后有很多图表需求，研究了 echarts，发现现有的小程序 echarts 库钉钉不支持，ucharts 使用简单，但是自定义功能少，满足不了需求，最后把注意力放到了 antv-f2。

antv-f2 的优势是自定义功能多，基本上和 antv-g2 没啥区别，强大的 api 基本可以满足你的需求。缺点就是官方的图例实在太少，相比于 echarts 和 ucharts 本身已经集成好多种类型，antv-f2 似乎是只提供了我们作画的工具，想画什么图还要自己去设计，这点对于新手来说的话不是那么的友好。当然写这篇文章的我也只是个新手，记录一下自己的研究，也希望可以让别人更容易上手这个工具。

# 二、个人环境

开发平台：uniapp+antv 小程序插件(基于 f2: 3.6.3 f2-context: 0.0.2)
<br>
测试环境：钉钉小程序

由于是使用 uniapp，且插件已经将 antv-f2 封装了起来，因此以下所有示例的模板都如下：

```
<template>
  <f2 :init="initChart" />
</template>

<script>
import f2 from '@/components/i-uni-f2';
export default {
  methods: {
    initChart(F2, config) {
      // 在这里配置参数，下面的图例皆是这个函数
    }
  }
};
</script>
```

# 三、图例

## 1.仪表盘

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4e8d1859870e43ffad69ee2cdc13c51a~tplv-k3u1fbpfcp-watermark.image)

### 代码
```
// 仪表盘配置
const initChart = (F2, config) => {
			const Shape = F2.Shape
			Shape.registerShape('point', 'pointer', {
				draw(cfg, container) {
					const group = container.addGroup({});
					// 获取极坐标系下画布中心点
					const center = this.parsePoint({
						x: 0,
						y: 0
					});
          // 指针的结束位置
					const a = this.parsePoint({
						x: cfg.origin._origin.value/9,
						y: 2
					})
					// 绘制指针
					group.addShape('line', {
						attrs: {
							x1: center.x,
							y1: center.y,
							x2: a.x,
							y2: a.y,
							strokeStyle: cfg.color,
							lineWidth: 3,
							lineCap: 'round',
						},
					});
					group.addShape('circle', {
						attrs: {
							x: center.x,
							y: center.y,
							r: 5,
							stroke: cfg.color,
							lineWidth: 4.5,
							fill: '#fff',
						},
					});

					return group;
				},
			})
			const data = [{
				value: 6
			}]
      // 初始化实例
			const chart = new F2.Chart(config)
      // 读取数据
			chart.source(data)
      // 设置极坐标
			chart.coord('polar', {
				startAngle: (-10 / 8) * Math.PI,
				endAngle: (2 / 8) * Math.PI,
				radius: 1
			});
			chart.scale('value', {
				min: 0,
				max: 9,
				ticks: [2.25, 3.75, 5.25, 6.75],
			});
      // 设置坐标轴
			chart.axis('1', false);
			chart.axis('value', {
				line: null,
				tickLine: null,
				grid: null,
				label: (text, index, total) => {
					const cfg = {
						fontSize: 10,
						textAlign: 'center',
					}
					if(text==='2.25'){
						cfg.text = '差'
					}else if(text==='3.75'){
						cfg.text = '中'
					}else if(text==='5.25'){
						cfg.text = '良'
					}else{
						cfg.text = '优'
					}
					return cfg
				},
			});
			chart.legend(false);
			chart
				.point()
				.position('value*1')
				.shape('pointer')
				.color('#1890FF');

			// 绘制仪表盘刻度线
			chart.guide().line({
				start: [3, 2.7],
				end: [3.0035, 2.5],
				style: {
					stroke: '#19AFFA', // 线的颜色
					lineDash: null, // 虚线的设置
					lineWidth: 3,
				},
			});
			chart.guide().line({
				start: [4.5, 2.7],
				end: [4.5, 2.5],
				style: {
					stroke: '#19AFFA', // 线的颜色
					lineDash: null, // 虚线的设置
					lineWidth: 3,
				},
			});

			chart.guide().line({
				start: [6, 2.7],
				end: [6.0035, 2.5],
				style: {
					stroke: '#19AFFA', // 线的颜色
					lineDash: null, // 虚线的设置
					lineWidth: 3,
				},
			});

			// 绘制仪表盘背景
			chart.guide().arc({
				top: false,
				start: [0, 3],
				end: [9, 3],
				style: {
					stroke: '#CBCBCB',
					lineWidth: 8,
					lineDash: null,
				},
			});

			// 绘制指标
			chart.guide().arc({
				start: [0, 3],
				end: [data[0].value, 3],
				style: {
					stroke: '#1890FF',
					lineWidth: 8,
					lineDash: null,
				},
			});
			// 绘制指标数字
			chart.guide().text({
				position: ['50%', '85%'],
				content: '合格率',
				style: {
					fontSize: 11,
					fill: '#545454',
					textAlign: 'center',
				},
				offsetY: 10
			});
			chart.guide().text({
				position: ['50%', '90%'],
				content: `${data[0].value * 10} %`,
				style: {
					fontSize: 11,
					fill: '#545454',
					textAlign: 'center',
				},
				offsetY: 20,
			});
			// 渲染，然后返回chart
			chart.render()
			return chart
		}
```

### 踩坑点
> 1.由于最开始是直接从antv-g2的图例直接复制代码过来的，g2和f2的用法其实差不多，但是有部分api名字不一样，如g2的chart.data和f2的chart.source，这里踩坑了就这能根据错误信息去对文档了。<br>
>2.g2中的draw方法的cfg参数是会自动处理data的数值映射的，而f2中的不会，所以在draw方法中手动处理了指针的结束位置（x代表弧度，y代表半径，极坐标中都是这样表示的）

## 2.子弹图
![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e8e45c377d6e4f63a19f910c8ad59ab8~tplv-k3u1fbpfcp-watermark.image)

### 代码
```
initChart(F2, config) {
	const data = [{
			月份: '1月',
			height: 5,
			width: 1
		},
		{
			月份: '2月',
			height: 5,
			width: 1
		},
		{
			月份: '3月',
			height: 5,
			width: 1
		},
		{
			月份: '4月',
			height: 5,
			width: 1
		},
		{
			月份: '5月',
			height: 5,
			width: 1
		},
	];
	const chart = new F2.Chart(config)
	chart.legend(false) // 不显示图例
  // 坐标反转
	chart.coord({
		transposed: true
	});
	chart.axis(false)
	chart.source(data, {
    // 制定纵坐标范围
		width: {
			min: 0,
			max: 2
		}
	});
	data.forEach((item, index) => {
    // 生成注释
		chart.guide().text({
			content: index * '2',
			position: [(index + 1) * 20 - 10 + '%', '30%']
		})
		chart.guide().text({
			content: index,
			position: [(index + 1) * 20 - 10 + '%', '50%'],
			offsetY: 10
		})
		chart.guide().text({
			content: index + 1 + '月',
			position: [(index + 1) * 20 - 10 + '%', '80%'],
			offsetY: 5
		})
	})
	chart.interval().position('width*height').color('月份', '#D9F2FD-#385074').size(10).adjust('stack')
	chart.render()
	return chart
}
```

### 踩坑点
>1.这里需要制定纵坐标的范围，原因是如果不这样做图的位置永远会在最上方，把纵坐标的位置限制在坐标轴的中间就能够使图纵向居中，方便进行上下的文字布局。<br>
>2.最好不要用默认的坐标轴，通过guide方法来配置文字注释会更加灵活。

# 四、结语
研究这个antv-f2可谓是费心费力了，如果觉得对你有帮助的话就点个赞吧。
