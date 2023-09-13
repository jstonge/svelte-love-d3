<script>
  import Diamond from "./components/Diamond.svelte";

  import { test_elem_1, test_elem_2 } from "./data/data.js"
  import { combElems, RTD, myDiamond } from "./components/combine_distributions.js"
  
  let alpha = 0.92;

  function wrangle_data() {
    const me = combElems(test_elem_1, test_elem_2)
    const rtd = RTD(me, alpha)
    const dat = myDiamond(me, rtd)
    const diamond_dat_raw = dat.counts
    return diamond_dat_raw.filter(d => d.types !== "")
  }
  
  const diamond_dat = wrangle_data()

</script>

<Diamond {diamond_dat}/>

