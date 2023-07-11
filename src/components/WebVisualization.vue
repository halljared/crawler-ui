<script setup lang="ts">
  import type { WebNode } from '@/types/WebNode';
  import * as d3 from 'd3';
  import { ref, onMounted } from 'vue';
  const visualization = ref(null);
  const props = defineProps<{ webNode: WebNode }>();

  onMounted(() => {
    const $ele = visualization.value as unknown as HTMLElement;
    const height = $ele.offsetHeight - 10;
    const width = height;
    const cx = width * 0.5; // adjust as needed to fit
    const cy = height * 0.5; // adjust as needed to fit
    const radius = Math.min(width, height) / 2 - 30;

    // Create a radial tree layout. The layout’s first dimension (x)
    // is the angle, while the second (y) is the radius.
    const tree = (d3.tree()
        .size([2 * Math.PI, radius])
        .separation((a, b) => (a.parent == b.parent ? 1 : 2) / a.depth)) as d3.TreeLayout<WebNode>;

    // Sort the tree and apply the layout.
    const root = tree(d3.hierarchy(props.webNode))
        .sort((a, b) => d3.ascending(a.data.url, b.data.url));

    // Creates the SVG container.
    const svg = d3.create("svg")
        .attr("width", width)
        .attr("height", height)
        .attr("viewBox", [-cx, -cy, width, height])
        .attr("style", "width: 100%; font: 10px sans-serif;");

    // Append links.
    svg.append("g")
        .attr("fill", "none")
        .attr("stroke", "#555")
        .attr("stroke-opacity", 0.6)
        .attr("stroke-width", 1.5)
      .selectAll()
      .data(root.links())
      .join("path")
        // @ts-ignore
        .attr("d", d3.linkRadial()
        // @ts-ignore
        .angle(d => d.x)
        // @ts-ignore
        .radius(d => d.y));

    // Append nodes.
    svg.append("g")
      .selectAll()
      .data(root.descendants())
      .join("circle")
        .attr("transform", d => `rotate(${d.x * 180 / Math.PI - 90}) translate(${d.y},0)`)
        .attr("r", 5.5);

    $ele.append(svg.node() as Node);
  })

</script>
<template>
  <div class="visualization" ref="visualization"></div>
</template>
<style>
  .visualization {
    height:100%;
  }
  .visualization circle {
    fill: #999
  }
  .visualization circle:hover {
    fill: rgb(65, 65, 117);
  }
</style>