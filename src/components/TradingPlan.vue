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
    "Never, ever argue with your trading system - Michael Covel",
    "Do more of what works and less of what doesn't. - Steve Clark",
    "Do not anticipate and move without market confirmation - being a little late in your trade is your insurance that you are right or wrong. - Jesse Livermore",
    "Most traders take a good system and destroy it by trying to make it into a perfect system. - Robert Prechter",
    "It was really later on when I decided that the reason there are so many technical indicators out there is because none of them work very well. - Chuck Hayes",
    "Sheer will and determination is no substitute for something that actually works. - Jason Klatt",
    "I get real, real concerned when I see trading strategies with too many rules (you should too). - Larry Connors",
    "There is a time to go long, a time to go short and a time to go fishing. - Jesse Livermore",
    "I just wait until there is money lying in the corner, and all I have to do is go over there and pick it up. I do nothing in the meantime. - Jim Rogers",
    "I believe in analysis and not forecasting. - Nicolas Darvas",
    "Short-term volatility is greatest at turning points and diminishes as a trend becomes established. - George Soros",
    "Novice Traders trade 5 to 10 times too big. They are taking 5 to 10 percent risk, on a trade they should be taking 1 to 2 percent risk on. - Bruce Kovner",
    "Are you willing to lose money on a trade? If not, then don't take it. You can only win if you're not afraid to lose. And you can only do that if you truly accept the risks in front of you. - Sami Abusaad",
    "You never know what kind of setup [the] market will present to you, your objective should be to find [an] opportunity where risk-reward ratio is best. -Jaymin Shah",
    "I have two basic rules about winning in trading as well as in life: 1. If you don’t bet, you can’t win. 2. If you lose all your chips, you can’t bet. - Larry Hite",
    "Letting losses run is the most serious mistake made by most investors. - William O'Neil",
    "Accepting losses is the most important single investment device to ensure the safety of capital. - Gerald M. Loeb",
    "The key to trading success is emotional discipline. If intelligence were the key, there would be a lot more people making money trading… I know this will sound like a cliche, but the single most important reason that people lose money in the financial markets is that they don’t cut their losses short. -Victor Sperandeo",
    "It’s ok to be wrong; it’s unforgivable to stay wrong. - Martin Zweig",
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
const m15SwingTrend = ref('uptrend')
const m15InternalTrend = ref('uptrend')

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
  if (executionTf.value === 'm15') {
    return phases[m15SwingTrend.value][m15InternalTrend.value][position.value];
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
            <th colspan="2" class="border">M15</th>
          </tr>
          <tr>
            <th class="border">Swing</th>
            <th class="border">Internal</th>
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
            <td class="border text-center"><button @click="() => m15SwingTrend = trendReverser[m15SwingTrend]">{{trends[m15SwingTrend]}}</button></td>
            <td class="border text-center"><button @click="() => m15InternalTrend = trendReverser[m15InternalTrend]">{{trends[m15InternalTrend]}}</button></td>
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
          <label class="text-xl flex-row align-middle" for="executionTf">
            <input type="radio" name="executionTf" value="m15" v-model="executionTf">
            M15
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

      <div class="col-span-2 flex flex-col gap-y-2">
        <h1 class="text-3xl font-bold mb-2">Declaration</h1>
        <label class="text-lg">
          <input type="checkbox">
          I have a clear trade plan with defined entry, exit points, and a rationale based on my strategy.
        </label>
        <label class="text-lg">
          <input type="checkbox">
          I am risking 0.5% of my capital on this trade and have set a stop-loss order accordingly.
        </label>
        <label class="text-lg">
          <input type="checkbox">
          I have set a realistic profit target and calculated the appropriate position size.
        </label>
        <label class="text-lg">
          <input type="checkbox">
          I have performed thorough technical analysis to support this trade setup.
        </label>
        <label class="text-lg">
          <input type="checkbox">
          I am in a clear and rational state of mind, free from emotional biases, and will follow my plan without deviation.
        </label>
        <label class="text-lg">
          <input type="checkbox">
          I accept the outcome of this trade, whether it results in a profit or a loss, and will use it as a learning opportunity.
        </label>
      </div>
    </div>
  </div>

</template>
