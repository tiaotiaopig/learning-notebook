# ECharts 快速上手

## 获取 ECharts

你可以通过以下几种方式获取 Apache EChartsTM。

- 从 [Apache ECharts 官网下载界面](https://echarts.apache.org/zh/download.html) 获取官方源码包后构建。
- 在 ECharts 的 [GitHub](https://github.com/apache/echarts/releases) 获取。
- 通过 npm 获取 echarts，`npm install echarts --save`，详见“[在 webpack 中使用 echarts](https://echarts.apache.org/zh/tutorial.html#在 webpack 中使用 ECharts)”
- 通过 [jsDelivr](https://www.jsdelivr.com/package/npm/echarts) 等 CDN 引入

## 引入 ECharts

通过标签方式直接引入构建好的 echarts 文件

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <!-- 引入 ECharts 文件 -->
    <script src="https://cdn.jsdelivr.net/npm/echarts@5.1.2/dist/echarts.min.js"></script>
</head>
</html>
```

## 绘制一个简单的图表

在绘图前我们需要为 ECharts 准备一个具备高宽的 DOM 容器。

```
<body>
    <!-- 为 ECharts 准备一个具备大小（宽高）的 DOM -->
    <div id="main" style="width: 600px;height:400px;"></div>
</body>
```

然后就可以通过 [echarts.init](https://echarts.apache.org/zh/api.html#echarts.init) 方法初始化一个 echarts 实例并通过 [setOption](https://echarts.apache.org/zh/api.html#echartsInstance.setOption) 方法生成一个简单的柱状图，下面是完整代码。

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>ECharts</title>
    <!-- 引入 echarts.js -->
    <script src="echarts.min.js"></script>
</head>
<body>
    <!-- 为ECharts准备一个具备大小（宽高）的Dom -->
    <div id="main" style="width: 600px;height:400px;"></div>
    <script type="text/javascript">
        // 基于准备好的dom，初始化echarts实例
        var myChart = echarts.init(document.getElementById('main'));

        // 指定图表的配置项和数据
        var option = {
            title: {
                text: 'ECharts 入门示例'
            },
            tooltip: {},
            legend: {
                data:['销量']
            },
            xAxis: {
                data: ["衬衫","羊毛衫","雪纺衫","裤子","高跟鞋","袜子"]
            },
            yAxis: {},
            series: [{
                name: '销量',
                type: 'bar',
                data: [5, 20, 36, 10, 10, 20]
            }]
        };

        // 使用刚指定的配置项和数据显示图表。
        myChart.setOption(option);
    </script>
</body>
</html>
```

这样你的第一个图表就诞生了！

## 小结

1. 引入**ECharts**,有各种方式
2. 为**echarts**准备一个dom节点（作为echarts的渲染容器），每一个echarts实例独占一个dom节点
3. 准备好**option**（就是一个j比较大的avascript对象，每一个成员都是一个组件**component**）,option我们可以理解为一个配置项
4. 使用 `option` 来描述其对图表的各种需求，包括：有什么数据、要画什么图表、图表长什么样子、含有什么组件、组件能操作什么事情等等。简而言之，`option` 表述了：`数据`、`数据如何映射成图形`、`交互行为`。

