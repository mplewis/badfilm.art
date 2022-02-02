<script setup lang="ts">

const oneDaySecs = 60 * 60 * 24;
const options = 8;
const breakAtChars = 20;
const songLength = 4500;

type ImdbID = string;

type Category = {
  name: string;
  desc: string;
  examples: ImdbID[];
};

const categories: Category[] = [

]

// async function main() {
//   const pretoggled = {};
//   Object.keys(filters).forEach((k) => (pretoggled[k] = false));

//   const app = Vue.createApp({
//     data: () => ({
//       message: "hello!",
//       items: null,
//       filters: pretoggled,
//       spun: false,
//       winner: null,
//     }),

//     computed: {
//       mode() {
//         if (!this.items) return "loading";
//         if (!this.spun) return "ready";
//         if (!this.winner) return "spinning";
//         return "done";
//       },

//       filtered() {
//         if (!this.items) return [];
//         remaining = this.items.slice();
//         Object.entries(this.filters).forEach(([name, enabled]) => {
//           if (!enabled) return;
//           remaining = remaining.filter((item) => filters[name](item));
//         });
//         return remaining;
//       },
//     },

//     methods: {
//       async spin() {
//         this.spun = true;
//         const start = buildWheel(this.filtered);
//         result = await start();
//         this.winner = result;
//       },

//       spinAgain() {
//         this.winner = null;
//         this.spin();
//       },

//       reset() {
//         this.spun = false;
//         this.winner = null;
//       },
//     },

//     async mounted() {
//       await validateSession();
//       this.items = await library();
//     },
//   });

//   app.mount("#app");
// }

// function buildWheel(library) {
//   const items = library.slice();
//   shuffle(items);
//   shuffle(colors);
//   const segments = items.slice(0, options).map((item, idx) => ({
//     original: item,
//     text: multiline(item.title),
//     fillStyle: colors[idx],
//   }));

//   const wheel = new Winwheel({
//     textAlignment: "outer",
//     pointerAngle: 90,
//     pointerGuide: {
//       display: true,
//       strokeStyle: "black",
//       lineWidth: 3,
//     },
//     numSegments: segments.length,
//     segments,
//     animation: {
//       type: "spinToStop",
//       duration: songLength / 1000,
//       spins: 6,
//     },
//   });

//   const music = new Audio("fanfare.mp3");

//   return function () {
//     return new Promise((resolve) => {
//       wheel.startAnimation();
//       music.play();

//       setTimeout(() => {
//         return resolve(wheel.getIndicatedSegment().original);
//       }, songLength);
//     });
//   };
// }

// function shuffle<T>(arr: T[]): void {
//   arr.sort(() => Math.random() - 0.5);
// }

// function multiline(s: string): string {
//   if (s.length <= breakAtChars) return s;
//   for (let i = breakAtChars; i > 0; i--) {
//     char = s[i];
//     breakpoint = !char.match(/[A-Za-z0-9]/);
//     if (breakpoint) {
//       const first = s.slice(0, i + 1).trim();
//       const rest = s.slice(i + 1, s.length);
//       return first + "\n" + multiline(rest);
//     }
//   }
//   return s;
// }

// function epoch() {
//   return Number(new Date()) / 1000;
// }
</script>

<template>
  <div>
    <h1 class="is-size-1 has-text-weight-bold has-text-centered mb-4">W H E E L P L E X</h1>

    <p v-if="mode === 'loading'">Loading data from your Plex server. Please wait...</p>

    <div v-if="mode === 'ready'">
      <p class="has-text-weight-bold">Filter your collection:</p>
      <ul class="my-2">
        <li>
          <label class="checkbox">
            <input type="checkbox" v-model="filters.unwatched" />
            Unwatched
          </label>
        </li>
        <li>
          <label class="checkbox">
            <input type="checkbox" v-model="filters.bad_critic" />
            Bad critic reviews
          </label>
        </li>
        <li>
          <label class="checkbox">
            <input type="checkbox" v-model="filters.bad_audience" />
            Bad audience reviews
          </label>
        </li>
        <li>
          <label class="checkbox">
            <input type="checkbox" v-model="filters.disparity" />
            Audiences liked, but critics hated
          </label>
        </li>
      </ul>

      <p class="has-text-weight-bold">{{ filtered.length }} movies selected</p>
      <button class="button has-text-weight-bold is-size-3 my-2 is-success" v-on:click="spin">SPIN</button>
    </div>
  </div>

  <div v-show="mode === 'spinning' || mode === 'done'">
    <div class="columns">
      <div class="column">
        <canvas id="canvas" width="660" height="660"></canvas>
      </div>

      <div class="column">
        <div v-if="mode === 'done'">
          <h1 class="is-size-3 has-text-weight-bold">{{ winner.title }}</h1>
          <p>{{ winner.genres.join(' | ') }}</p>
          <p>
            Rotten Tomatoes {{ winner.critic_rating }}/10 | Audience
            {{ winner.audience_rating }}/10
          </p>
          <img
            :src="winner.thumbnail_url"
            alt="Movie poster"
            width="300"
            style="min-height: 450px"
            class="my-3"
          />
          <p>{{ winner.summary }}</p>
        </div>
      </div>
    </div>
  </div>

  <div v-if="mode === 'done'" class="mt-4 has-text-centered">
    <button class="button has-text-weight-bold is-size-5 mx-2 is-danger" v-on:click="reset">Reset</button>
    <button
      class="button has-text-weight-bold is-size-5 mx-2 is-success"
      v-on:click="spinAgain"
    >Spin Again</button>
  </div>
</template>
