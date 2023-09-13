<script>
  import Diamond from "./components/Diamond.svelte";

  import { test_elem_1, test_elem_2 } from "./data/data.js"
  import { combElems, RTD, myDiamond } from "./components/combine_distributions.js"
  
  import Scrolly from "./helpers/Scrolly.svelte"

  let alpha = 0.92;

  function wrangle_data() {
    const me = combElems(test_elem_1, test_elem_2)
    const rtd = RTD(me, alpha)
    const dat = myDiamond(me, rtd)
    const diamond_dat_raw = dat.counts
    return diamond_dat_raw.filter(d => d.types !== "")
  }
  
  const diamond_dat = wrangle_data()

  let currentStep = 0;

  let initialData = diamond_dat;
  let renderedData = initialData;
  // Series of if-else to play with data.
  $: {
    if (currentStep === 0) {
      renderedData = initialData.map((d => 
        ({...d, 
            x1: Math.ceil(d.coord_on_diag),
            y1: Math.ceil(d.coord_on_diag),
            value: null,
            types: ""
          })));
    } else if (currentStep === 1) {
      renderedData = initialData.map((d => 
        ({...d, 
            x1: Math.ceil(d.coord_on_diag),
            y1: Math.ceil(d.coord_on_diag),
            types: ""
          })));
    } else if (currentStep === 2) {
      renderedData = initialData.map((d => 
          ({...d, 
            x1: d.which_sys == "left" ? Math.ceil(d.coord_on_diag) : d.x1, 
            y1: d.which_sys == "left" ? Math.ceil(d.coord_on_diag) : d.y1,
            types: d.which_sys == "left" ? "" : d.types,
          })));
    } else if (currentStep === 3) {
      renderedData = initialData;
    }
  }

</script>

<main>
  <h1>Allotaxonometry for all</h1>
  <h2>Boys 1895 vs 1930</h2>
  
  <section>
      <div class='sticky'>
        <Diamond diamond_dat={renderedData}/>
      </div>

      <div class="steps">
        <Scrolly bind:value={currentStep}>
          {#each ["d3", "and", "svelte", "are awesome"] as text, i}
            <div class='step' class:active={currentStep === i}>
              <div class="step-content">
                <p>{text}</p>
              </div>
            </div>
          {/each}
        </Scrolly>
      </div>
      <div class='reference-step'>
        {currentStep}
      </div>  
  </section>
  
  <h2>What are divergences?</h2>
  <p>Divergence metrics are what keep everything together blah blah </p>
</main>


<style>
  main {
    text-align: center;
    margin: 0 auto;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    min-height: 120vh;
    max-width: 768px;
    padding: 20px;
    background: white;
  }
  
  h1 {
    font-size: 50px;
    margin-bottom: 1rem;
  }

  h2 {
    font-size: 20px;
    margin-bottom: 3rem;
    color: hsla(212, 10%, 53%, 1);
  }


  /* Scrollytelling hidden magic */

  .step {
    height: 90vh;
    opacity: 0.3;
    transition: opacity 300ms ease;

    display: flex;
    justify-content: center;
    place-items: center;
  }

  .step.active {
    opacity: 1;
  }

  .step-content {
    background-color: white;
    border: 1px solid black;
    padding: 0.75rem 1rem;
    border-radius: 3px; 
  }

  section {
    position: relative;
  }

  .steps {
    position: relative;
    z-index: 2;
  }

  .sticky {
    position: sticky;
    margin-top: 30px;
    height: 90vh;
    top: 5vh; /* (100vh - 90vh) / 2 */
    width: 100%; /* Full width */
    z-index: 1;
    margin-bottom: 1rem;
  }

  .reference-step {
    position: fixed;
    bottom: 0;
    right: 0;
    padding: 1rem;
  }

  .initial-chart {
    margin-bottom: 1rem;
  }

  /* Transition section */

  :global(rect) {
    transition: 
    x 700ms cubic-bezier(0.76, 0, 0.24, 1), 
    y 700ms cubic-bezier(0.76, 0, 0.24, 1),
    fill 700ms cubic-bezier(0.76, 0, 0.24, 1),
    width 700ms cubic-bezier(0.76, 0, 0.24, 1),
    height 700ms cubic-bezier(0.76, 0, 0.24, 1),
    opacity 700ms cubic-bezier(0.76, 0, 0.24, 1);
  }


</style>