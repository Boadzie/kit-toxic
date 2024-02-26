<script>
  import * as tf from '@tensorflow/tfjs'
  import * as toxicity from "@tensorflow-models/toxicity";
  import { fly } from 'svelte/transition';
 
    
  let loading = false
  const threshold = 0.05;

  let pred = null;
  let sentence =""
  let model = null;

  async function classifyToxicity(threshold, sentence) {
    if(!model) {
      model = await toxicity.load(threshold);
    }
    pred = await model.classify(sentence);
  }

  async function handleClick() {
    loading = true
    await classifyToxicity(threshold, sentence);
    loading = false
  }

  const handleInput = (event) => {
    sentence = event.target.value;
  }

</script>

<section class="m-auto w-1/2 ">
  <div class="flex flex-col min-h-screen justify-center items-center">
  <h1 class="text-5xl my-6 font-bold text-slate-500">Toxicity Predictor - Tensorflow.js</h1>
  <form class="flex flex-col gap-4 w-[35rem]" on:submit|preventDefault={handleClick}>
    <textarea class="focus:outline-none text-slate-500 ring-2 px-4 py-2 rounded-sm" type="text" on:input={handleInput} value={sentence} required></textarea>
    <button class="rounded-sm bg-slate-700 w-24 px-4 py-2 text-white ">Check</button>
  </form>
  {#if loading == true}
  <div class="flex justify-center items-center mt-4">
    <div class="rounded-full border-4 border-t-4 border-green-200 h-24 w-24 animate-spin "></div>
  </div>
  {:else if pred}
  <div class="toxicity-predictor  lg:w-3/4 my-4 flex flex-wrap flex-row items-center">
    {#each pred as { label, results }}
    <div class="lg:w-1/2 bg-[#023047] p-2 rounded-sm">
    <p before="👎 " class="inline-flex items-center gap-x-4 before:content-[attr(before)] before:font-black text-red-400 p-2 "><span class="text-lg font-bold text-white">Label:</span> {JSON.stringify(label)}</p>
    </div>
    <div class="lg:w-1/2 flex gap-4 rounded-sm bg-slate-100">
    {#each results as {match, probabilities }}
      <p class=" p-2 font-bold text-slate-500 inline-flex items-center gap-x-4">Match: <span class="text-lg font-bold text-green-700">{JSON.stringify(match > 0 ? "Toxic" : "No match")} </span>  </p>
    {/each}
    </div>
    {/each}

  </div>
  {/if}
  </div>
  
</section>

<style>
.toxicity-predictor {
  animation: fade-in 1.5s ease-in-out;
}

@keyframes fade-in {
  from {
    opacity: 0;
    transform: translate3d(0, -10%, 0);
  }
  to {
    opacity: 1;
    transform: none;
  }
}
</style>