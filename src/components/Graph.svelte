<script>
  import { onMount, afterUpdate } from 'svelte';
  import * as d3 from 'd3';

  export let playerMoneyHistory;
  export let svgId = 'mySvg2';

  let svg;

  onMount(() => {
    svg = d3.select("#" + svgId).attr("width", "550").attr("height", "200");
    drawChart(); // Initial draw
  });

  afterUpdate(() => {
    drawChart(); // Redraw chart to reflect updated playerMoneyHistory
  });

  function drawChart() {
    // Clear the SVG
    svg.selectAll("*").remove();

    // Set the dimensions and margins of the graph
    let margin = {top: 20, right: 20, bottom: 40, left: 60},
        width = 550 - margin.left - margin.right,
        height = 200 - margin.top - margin.bottom;

    // Append a 'group' element to 'svg'
    // Moves the 'group' element to the top left margin
    let g = svg.append("g").attr("transform", "translate(" + margin.left + "," + margin.top + ")");

    // Add X axis --> it is a date format
    let x = d3.scaleLinear()
              .domain([0, playerMoneyHistory.length - 1])
              .range([0, width]);
  
    // Add Y axis
    let y = d3.scaleLinear()
              .domain([-4000, 2000])
              .range([ height, 0 ]);
    g.append("g")
     .call(d3.axisLeft(y).ticks(5));

     g.append("g")
     .attr("transform", "translate(0," + y(0) + ")")
     .call(d3.axisBottom(x).ticks(Math.min(playerMoneyHistory.length, 10)).tickFormat(i => `${i + 1}`))
     .selectAll("text")  
        .style("text-anchor", "end")
        .attr("dx", "-.8em")
        .attr("dy", ".15em")
        .attr("transform", "rotate(-65)");

    // Add the line
    g.append("path")
     .datum(playerMoneyHistory)
     .attr("fill", "none")
     .attr("stroke", "steelblue")
     .attr("stroke-width", 2)
     .attr("d", d3.line()
       .x((_, i) => x(i))
       .y((d) => y(d))
     );

    // Text label for the x axis
    svg.append("text")             
       .attr("transform",
             "translate(" + (width/2 + margin.left) + " ," + 
                            (height + margin.top + 40) + ")")
       .style("text-anchor", "middle")
       .text("Bets");

    // Text label for the y axis
    svg.append("text")
       .attr("transform", "rotate(-90)")
       .attr("y", 0 + margin.left / 4)
       .attr("x",0 - (height / 2) - margin.top)
       .attr("dy", "0em")
       .style("text-anchor", "middle")
       .text("Money");
  }
</script>

<svg id={svgId}>></svg>

<style>
  svg {
    margin-top: 10px;
    padding-top: 10px;
  }
</style>
