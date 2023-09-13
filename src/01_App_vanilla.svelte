<script>
  import * as d3 from "d3";

  import { test_elem_1, test_elem_2 } from "./data/data.js"
  import { combElems, RTD, myDiamond } from "./components/combine_distributions.js"

  let width = 600;
  let height = 600;

  let alpha = 0.92;

  function wrangle_data() {
    const me = combElems(test_elem_1, test_elem_2)
    const rtd = RTD(me, alpha)
    const dat = myDiamond(me, rtd)
    const diamond_dat_raw = dat.counts
    return diamond_dat_raw.filter(d => d.types !== "")
  }
  
  const diamond_dat = wrangle_data()

  let xy = d3.scaleBand()
             .domain(d3.range(60))
             .range([0, diamondHeight])

  let color_scale = d3.scaleSequentialLog()
           .domain([d3.max(diamond_dat, d => d.x1), 1])
           .interpolator(d3.interpolateInferno)

</script>

<svg {width} {height}>
  <g class='inner-chart'>
    {#each diamond_dat as d}
      <rect
        x={xy(d.x1)}
        y={xy(d.y1)}
        width={xy.bandwidth()}
        height={xy.bandwidth()}
        fill={color_scale(d.value)}
        stroke="black"
        stroke-width=0.2
      ></rect>  
    {/each}
  </g>
</svg>

