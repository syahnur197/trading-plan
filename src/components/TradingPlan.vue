<script setup>
import { ref, computed } from 'vue'
import { saveAs } from 'file-saver';
import html2canvas from 'html2canvas';

// constants
const trends = {
  uptrend: '🟢',
  downtrend: '🔴',
};

const trendReverser = {
  uptrend: 'downtrend',
  downtrend: 'uptrend',
}

const phases = {
  uptrend: {
    uptrend: {
      buy: '<span class="text-green-700">Pro Swing</span>, <span class="text-green-700">Pro Internal</span>',
      sell: '<span class="text-red-700">Counter Swing</span>, <span class="text-red-700">Counter Internal</span>',
    },
    downtrend: {
      buy: '<span class="text-green-700">Pro Swing</span>, <span class="text-red-700">Counter Internal</span>',
      sell: '<span class="text-red-700">Counter Swing</span>, <span class="text-green-700">Pro Internal</span>',
    },
  },
  downtrend: {
    uptrend: {
      buy: '<span class="text-red-700">Counter Swing</span>, <span class="text-green-700">Pro Internal</span>',
      sell: '<span class="text-green-700">Pro Swing</span>, <span class="text-red-700">Counter Internal</span>',
    },
    downtrend: {
      buy: '<span class="text-red-700">Counter Swing</span>, <span class="text-red-700">Counter Internal</span>',
      sell: '<span class="text-green-700">Pro Swing</span>, <span class="text-green-700">Pro Internal</span>',
    },
  }
}

// data

const d1SwingTrend = ref('uptrend')
const d1InternalTrend = ref('uptrend')
const h4SwingTrend = ref('uptrend')
const h4InternalTrend = ref('uptrend')

const position = ref('buy');
const executionTf = ref('h4');

// methods

const getPhase = computed(() => {
  if (executionTf.value === 'd1') {
    return phases[d1SwingTrend.value][d1InternalTrend.value][position.value];
  }
  if (executionTf.value === 'h4') {
    return phases[h4SwingTrend.value][h4InternalTrend.value][position.value];
  }
})

const download = () => {
  const style = document.createElement('style');
  document.head.appendChild(style);
  style.sheet?.insertRule('body > div:last-child img { display: inline-block; }');

  html2canvas(document.querySelector("#capture")).then(canvas => {
    style.remove();
    canvas.toBlob(function(blob) {
        saveAs(blob, "trade-plan.png");
    });
  });
}


</script>

<template>
  <div class="w-2/3 mx-auto">
    <div class="flex flex-row justify-end my-4">
      <button class="bg-teal-400 py-2 px-4 hover:bg-teal-300 text-teal-800" @click="download">Download</button>
    </div>
    <div id="capture" class="grid grid-cols-2 gap-x-4 gap-y-4 my-12 px-4 py-4">
      <div class="col-span-2">
        <h1 class="text-4xl font-bold">Trading Plan</h1>
      </div>

      <div class="col-span-1 flex flex-col gap-y-2">
        <h1 class="text-3xl font-bold mb-2">Trend</h1>

        <label class="text-xl">D1 Swing: <button @click="() => d1SwingTrend = trendReverser[d1SwingTrend]">{{trends[d1SwingTrend]}}</button></label>
        <label class="text-xl">D1 Internal: <button @click="() => d1InternalTrend = trendReverser[d1InternalTrend]">{{trends[d1InternalTrend]}}</button></label>
        <label class="text-xl">H4 Swing: <button @click="() => h4SwingTrend = trendReverser[h4SwingTrend]">{{trends[h4SwingTrend]}}</button></label>
        <label class="text-xl">H4 Internal: <button @click="() => h4InternalTrend = trendReverser[h4InternalTrend]">{{trends[h4InternalTrend]}}</button></label>

        <h1 class="text-3xl font-bold my-2">Position</h1>
        <label class="text-xl flex-row align-middle" for="position">
          <input type="radio" name="position" value="buy" v-model="position">
          Buy
        </label>
        <label class="text-xl flex-row align-middle" for="position">
          <input type="radio" name="position" value="sell" v-model="position">
          Sell
        </label>

        <h1 class="text-3xl font-bold my-2">Execution Timeframe</h1>
        <label class="text-xl flex-row align-middle" for="executionTf">
          <input type="radio" name="executionTf" value="d1" v-model="executionTf">
          D1
        </label>
        <label class="text-xl flex-row align-middle" for="executionTf">
          <input type="radio" name="executionTf" value="h4" v-model="executionTf">
          H4
        </label>

        <h1 class="text-3xl font-bold my-2">Phase</h1>
        <label class="text-xl font-bold" v-html="getPhase">
        </label>
      </div>

      <div class="col-span-1 flex flex-col gap-y-2">
        <h1 class="text-3xl font-bold mb-2">Setup</h1>
        <label class="text-xl">
          <input type="checkbox">
          Break of Structure
        </label>
        <label class="text-xl">
          <input type="checkbox">
          Swept Liquidity
        </label>
        <label class="text-xl">
          <input type="checkbox">
          Imbalance
        </label>
        <label class="text-xl">
          <input type="checkbox">
          Order Block that caused break of structure
        </label>
        <label class="text-xl">
          <input type="checkbox">
          Unmitigated Order Block at 75% retracement
        </label>
        <label class="text-xl">
          <input type="checkbox">
          Imbalance at 75% retracement
        </label>
        <label class="text-xl">
          <input type="checkbox">
          Plenty of room next to supply/demand
        </label>
      </div>
    </div>
  </div>

</template>
