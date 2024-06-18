<script>
  // @ts-nocheck
  import { onMount } from "svelte";
  import * as echarts from "echarts";
  import Layout from "./+layout.svelte";
  import { text } from "@sveltejs/kit";

  const paths = [
    "/data/data_index.csv",
    "/data/data_maotai.csv",
    "/data/data_mengjie.csv",
  ];
  let plots = [null, null, null];

  let stock = 0;
  function plotChart(data, dom) {
    // 基于准备好的dom，初始化echarts实例
    var myChart = echarts.init(dom, null, {
      height: 400,
    });
    window.addEventListener("resize", function () {
      myChart.resize();
    });
    // 指定图表的配置项和数据
    var option = {
      title: {
        text: "ECharts 入门示例",
      },
      tooltip: {},
      legend: {
        data: ["销量"],
      },
      xAxis: {
        data: ["衬衫", "羊毛衫", "雪纺衫", "裤子", "高跟鞋", "袜子"],
      },
      yAxis: {},
      series: [
        {
          name: "销量",
          type: "bar",
          data: [5, 20, 36, 10, 10, 20],
        },
      ],
    };
    // 使用刚指定的配置项和数据显示图表。
    myChart.setOption(option);
  }
  let getDataReplot = async (no) => {
    let response = await fetch(paths[no]);
    let text = await response.text();
    console.log(text);
    let data = text
      .split("\n")
      .map((line) => Number(line.split(",")[2]))
      .slice(1);
    console.log(data);
    plots.forEach((plot) => plotChart(data, plot));
  };

  $: button_style = [0, 1, 2].map((index) =>
    stock === index ? "tab tab-active" : "tab",
  );

  onMount(() => plots.forEach((plot) => plotChart(null, plot)));
</script>

<svelte:head>
  <title>Investment Simulation</title>
</svelte:head>

<header class="navbar bg-base-300 justify-center">
  <h1 class="btn btn-ghost text-xl place-self-center">
    Investment Simulation Plots
  </h1>
</header>
<header class="navbar bg-base-300 justify-center">
  <p>CSI300 Index, Maotai and Mengjie</p>
</header>

<div role="tablist" class="tabs tabs-boxed">
  <a
    role="tab"
    class="tab {button_style[0]}"
    on:click={async () => {
      stock = 0;
      await getDataReplot(stock);
    }}>Index</a
  >
  <a
    role="tab"
    class="tab {button_style[1]}"
    on:click={async () => {
      stock = 1;
      await getDataReplot(stock);
    }}>Maotai</a
  >
  <a
    role="tab"
    class="tab {button_style[2]}"
    on:click={async () => {
      stock = 2;
      await getDataReplot(stock);
    }}>Mengjie</a
  >
</div>

<main>
  <figure>
    <div bind:this={plots[0]} class="">收益对持有期曲线图</div>
    <figcaption class="text-center">低水平组🙁（正确率0.45）</figcaption>
  </figure>
  <figure>
    <div bind:this={plots[1]} class="">收益对持有期曲线图</div>
    <figcaption class="text-center">中水平组😐（正确率0.5）</figcaption>
  </figure>
  <figure>
    <div bind:this={plots[2]} class="">收益对持有期曲线图</div>
    <figcaption class="text-center">高水平组😄（正确率0.55）</figcaption>
  </figure>
</main>

<footer class="footer footer-center p-4 bg-base-300 text-base-content">
  <aside>
    <p>
      Made by <strong>Cavendish</strong>. The source code is on
      <a
        class="link link-primary"
        href="https://github.com/Pelapis/invest-simulation">GitHub</a
      >.
    </p>
  </aside>
</footer>
