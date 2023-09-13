<script>
    import * as d3 from "d3";
  
    export let diamond_dat;

    let height = 600;
    let width = 600;
  
    const margin = { innerTop: 70, innerRight: 70, diamondTop: 20, diamondRight: 20 };
  
    const innerHeight = height - margin.innerTop - margin.innerRight;
    const diamondHeight = innerHeight - margin.diamondTop - margin.diamondRight;
     
    
    const max_rank = d3.max(diamond_dat, (d) => d.rank_L[1]);
    const xyDomain = [1, 10**Math.ceil(Math.max(Math.log10(max_rank))-1)];
    const max_xy = d3.max(diamond_dat, d => d.x1)
  
    let xy = d3.scaleBand()
               .domain(d3.range(60))
               .range([0, diamondHeight])
    
  
    function get_coords_polygons(is_blue) {
      let wxy  = d3.scaleBand().domain(d3.range(60)).range([0, innerHeight+margin.diamondTop])
      return is_blue ?
          [ 
            {"x":max_xy, "y":max_xy}, {"x":0, "y":0}, {"x":0, "y":max_xy}
          ].map(d => [wxy(d.x), wxy(d.y)].join(',')).join(" ") :
          [
            {"x":max_xy, "y":max_xy}, {"x":0, "y":0}, {"x":max_xy, "y":0}
          ].map(d => [wxy(d.x), wxy(d.y)].join(',')).join(" ")
    }
  
    const blue_triangle = get_coords_polygons(true)
    const grey_triangle = get_coords_polygons(false)
  
    let color_scale = d3.scaleSequentialLog()
                        .domain([max_xy, 1])
                        .interpolator(d3.interpolateInferno)
  
    let logScale = d3.scaleLog()
                     .domain(xyDomain)
                     .range([0, innerHeight-margin.diamondTop])
    
    const logFormat10 = logScale.tickFormat()
    const xyTicks = logScale.ticks()
  
  
  </script>

<div class="chart-container">
  <svg {width} {height} transform="translate(60 130) scale (-1,1) rotate(45)">
    <g class='inner-chart' transform="translate({margin.innerRight}, {margin.innerTop})">
      <polygon points={blue_triangle} fill="#89CFF0" fill-opacity=0.2 stroke="black" stroke-width=0.5/>
      <polygon points={grey_triangle} fill="grey" fill-opacity=0.2 stroke="black" stroke-width=0.5/>
  
      {#each diamond_dat as d}
        <rect
          x={xy(d.x1)}
          y={xy(d.y1)}
          width={xy.bandwidth()}
          height={xy.bandwidth()}
          fill={color_scale(d.value)}
          opacity = {d.value === null ? 0 : 1}
          stroke="black"
          stroke-width=0.2
        ></rect>  
      {/each}
      <g class='axis x' transform="translate(0, {innerHeight-margin.diamondTop})">
        {#each xyTicks as tick, index}
          <g class="xtick" transform="translate({logScale(tick)}, 0)">
            <line x1="0" x2="0" y1="0" y2="6" stroke="black"></line>
          </g>
          <g class="tick-text" transform="translate({logScale(tick)}, 0) scale(-1,1) rotate(45)">
            <text font-size="10" dx="13" dy="13">{logFormat10(tick)}</text>
          </g>
        {/each}
      </g>
      <g class='axis y' transform="translate({innerHeight-margin.diamondTop}, 0)">
        {#each xyTicks as tick, index}
          <g class="ytick" transform="translate(0, {logScale(tick)})">
            <line x1="0" x2="-6" y1="0" y2="0" stroke="black"></line>
          </g>
          <g class="tick-text" transform="translate(0, {logScale(tick)}) scale(-1,1) rotate(45)">
            <text font-size="10" dx="-23" dy="10">{logFormat10(tick)}</text>
          </g>
        {/each}
      </g>
    </g>
  </svg>
</div>

<style>
  .chart-container {
      position: relative;
  }
</style>
