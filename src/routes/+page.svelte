<script>
  import * as tf from '@tensorflow/tfjs'
  import * as toxicity from "@tensorflow-models/toxicity";
    
  let loading = false
  const threshold = 0.9;

  let pred = null;
  let sentence =""

  async function classifyToxicity(threshold, sentence) {
    const model = await toxicity.load(threshold);
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

