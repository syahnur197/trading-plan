<script setup>
import { ref, computed } from 'vue'
import { saveAs } from 'file-saver';
import html2canvas from 'html2canvas';

// constants
const tradingQuotes = [
    "The goal of a successful trader is to make the best trades. Money is secondary. - Alexander Elder",
    "Every trader has strengths and weaknesses. Some are good at following trends, others are good at playing the ranges. Recognize what you are good at and leverage that. - Jack Schwager",
    "In trading, it's not about how much you make but rather how much you don’t lose. - Bernard Baruch",
    "The stock market is filled with individuals who know the price of everything, but the value of nothing. - Philip Fisher",
    "It’s not whether you’re right or wrong that’s important, but how much money you make when you’re right and how much you lose when you’re wrong. - George Soros",
    "Opportunities come infrequently. When it rains gold, put out the bucket, not the thimble. - Warren Buffett",
    "The biggest risk is not taking any risk. In a world that is changing really quickly, the only strategy that is guaranteed to fail is not taking risks. - Mark Zuckerberg",
    "Do more of what works and less of what doesn’t. - Steve Clark",
    "Amateurs think about how much money they can make. Professionals think about how much money they could lose. - Jack Schwager",
    "The four most dangerous words in investing are: 'This time it’s different.' - Sir John Templeton",
    "Risk comes from not knowing what you’re doing. - Warren Buffett",
    "The market is a device for transferring money from the impatient to the patient. - Warren Buffett",
    "The trend is your friend until the end when it bends. - Ed Seykota",
    "Plan your trade and trade your plan. - Linda Raschke",
    "It takes 20 years to build a reputation and five minutes to ruin it. If you think about that, you’ll do things differently. - Warren Buffett",
    "In investing, what is comfortable is rarely profitable. - Robert Arnott",
    "Trading doesn’t just reveal your character, it also builds it if you stay in the game long enough. - Yvan Byeajee",
    "Good traders manage the downside; they don’t worry about the upside. - Mark Minervini",
    "Letting losses run is the most serious mistake made by most investors. - William O’Neil",
    "Successful trading is about making small profits consistently, managing risk, and controlling your emotions. - Unknown",
    "Markets can remain irrational longer than you can remain solvent. - John Maynard Keynes",
    "Don't focus on making money; focus on protecting what you have. - Paul Tudor Jones",
    "The key to trading success is emotional discipline. If intelligence were the key, there would be a lot more people making money trading. - Victor Sperandeo",
    "The elements of good trading are: (1) Cutting losses, (2) Cutting losses, and (3) Cutting losses. If you can follow these three rules, you may have a chance. - Ed Seykota",
    "You learn more from your losses than your wins. - Unknown",
    "The trick is to stop thinking of it as 'your' money. - Unknown",
    "The goal of a successful trader is to make the best trades. Money is secondary. - Alexander Elder",
    "The game taught me the game. And it didn’t spare me the rod while teaching. - Jesse Livermore",
    "An investor without investment objectives is like a traveler without a destination. - Ralph Seger",
    "The most important quality for an investor is temperament, not intellect. - Warren Buffett",
    "Every once in a while, the market does something so stupid it takes your breath away. - Jim Cramer",
    "I’m only rich because I know when I’m wrong. I basically have survived by recognizing my mistakes. - George Soros",
    "Trade with an edge, manage risk, be consistent, and keep it simple. - Unknown",
    "Confidence is not 'I will profit on this trade.' Confidence is 'I will be fine if I don't profit from this trade.' - Yvan Byeajee",
    "Do not be embarrassed by your failures, learn from them and start again. - Richard Branson",
    "If most traders would learn to sit on their hands 50 percent of the time, they would make a lot more money. - Bill Lipschutz",
    "The goal of a successful trader is to make the best trades. Money is secondary. - Alexander Elder",
    "Losing a position is aggravating, whereas losing your nerve is devastating. - Ed Seykota",
    "The goal of a successful trader is to make the best trades. Money is secondary. - Alexander Elder",
    "It is not the strongest of the species that survive, nor the most intelligent, but the one most responsive to change. - Charles Darwin",
];

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
  <div class="md:w-2/3 mx-auto">
    <div class="flex flex-row justify-end my-4 px-4">
      <button class="bg-teal-400 py-2 px-4 hover:bg-teal-300 text-teal-800" @click="download">Download</button>
    </div>
    <div id="capture" class="grid grid-cols-2 gap-x-4 gap-y-4 my-12 px-4 py-4">
      <div class="col-span-2">
        <h1 class="text-4xl font-bold">Trading Plan</h1>
        <h3 class="italic text-gray-600">
          {{tradingQuotes[Math.floor(Math.random() * tradingQuotes.length)]}}
        </h3>
      </div>

      <div class="col-span-2 flex flex-col gap-y-2 mb-2">
        <h1 class="text-3xl font-bold mb-2">Trend</h1>

        <table>
          <tr>
            <th colspan="2" class="border">D1</th>
            <th colspan="2" class="border">H4</th>
          </tr>
          <tr>
            <th class="border">Swing</th>
            <th class="border">Internal</th>
            <th class="border">Swing</th>
            <th class="border">Internal</th>
          </tr>
          <tr>
            <td class="border text-center"><button @click="() => d1SwingTrend = trendReverser[d1SwingTrend]">{{trends[d1SwingTrend]}}</button></td>
            <td class="border text-center"><button @click="() => d1InternalTrend = trendReverser[d1InternalTrend]">{{trends[d1InternalTrend]}}</button></td>
            <td class="border text-center"><button @click="() => h4SwingTrend = trendReverser[h4SwingTrend]">{{trends[h4SwingTrend]}}</button></td>
            <td class="border text-center"><button @click="() => h4InternalTrend = trendReverser[h4InternalTrend]">{{trends[h4InternalTrend]}}</button></td>
          </tr>
        </table>
      </div>

      <div class="col-span-2 md:col-span-1 flex flex-col gap-y-2">
        <h1 class="text-3xl font-bold my-2">Position</h1>
        <div class="flex flex-row md:flex-col gap-2">
          <label class="text-xl flex-row align-middle" for="position">
            <input type="radio" name="position" value="buy" v-model="position">
            Buy
          </label>
          <label class="text-xl flex-row align-middle" for="position">
            <input type="radio" name="position" value="sell" v-model="position">
            Sell
          </label>
        </div>

        <h1 class="text-3xl font-bold my-2">Execution Timeframe</h1>
        <div class="flex flex-row md:flex-col gap-2">
          <label class="text-xl flex-row align-middle" for="executionTf">
            <input type="radio" name="executionTf" value="d1" v-model="executionTf">
            D1
          </label>
          <label class="text-xl flex-row align-middle" for="executionTf">
            <input type="radio" name="executionTf" value="h4" v-model="executionTf">
            H4
          </label>
        </div>

        <h1 class="text-3xl font-bold my-2">Phase</h1>
        <label class="text-xl font-bold" v-html="getPhase">
        </label>
      </div>

      <div class="col-span-2 md:col-span-1 flex flex-col gap-y-2">
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
