<script setup lang="ts">
type LeaderBoardPlayer = {
  username: string;
  items: number;
};

type SpawnedItem = {
  name: string;
  number?: number;
  zone?: string;
  createdAt?: Date;
  rarity: number;
};

type Population = {
  [key: string]: number;
};

// TODO — type data response from useFetch as this
type MetaResponse = {
  leaderboard: LeaderBoardPlayer[];
  latestSpawned: SpawnedItem;
  mostSpawned: SpawnedItem;
  leastSpawned: SpawnedItem;
  population: Population;
};

const config = useRuntimeConfig();

useSeoMeta({
  title: "Welcome to p4nth3rworld",
  description: "The game doesn't stop when the stream ends.",
});

const { status, data, error, refresh } = await useFetch(`meta/`, {
  baseURL: config.public.world_api_url,
});

onMounted(() => {
  document.addEventListener("visibilitychange", () => {
    if (!document.hidden) {
      refresh();
    }
  });
});

// const latestSpawnedDate = computed(() => {
//   const createdAt = new Date(data.value?.meta.latestSpawned.createdAt);
//   return `${createdAt.getDate()} ${DateUtils.getMonthStringFromInt(createdAt.getMonth())} ${createdAt.getFullYear()} ${createdAt.getHours()}:${DateUtils.addLeadingZero(createdAt.getMinutes())}:${createdAt.getSeconds()}`;
// });

const locationBgClass = {
  mountain: "from-indigo-400",
  forest: "from-emerald-400",
  swamp: "from-lime-400",
  city: "from-cyan-400",
  desert: "from-yellow-400",
};

const emojis = ["🥇", "🥈", "🥉"];
</script>

<template>
  <section class="flex flex-col gap-3 justify-center items-center m-8 mb-16">
    <div class="emojis">
      <span>🎒</span>
      <span>📦</span>
      <span>🌍</span>
      <span>📍</span>
      <span>🌳</span>
      <span>🗺️</span>
    </div>
    <h1 class="text-4xl font-bold">This is p4nth3rworld</h1>
    <h2 class="text-2xl mb-6">The game doesn't stop when the stream ends.</h2>
    <a
      href="https://twitch.tv/whitep4nth3r/chat"
      class="bg-violet-700 hover:bg-violet-500 focus:bg-emerald-700 active:bg-emerald-700 focus:outline-none focus:ring focus:ring-emerald-300 text-white font-bold uppercase py-2 px-4 rounded-lg text-xl transition"
      >Play now</a
    >
  </section>
  <section class="mb-16" v-auto-animate>
    <h2 class="font-bold uppercase text-xl">Leaderboard</h2>
    <div class="grid grid-cols-1 sm:grid-cols-3 gap-3 my-4">
      <div v-for="(player, key) in data.meta.leaderboard" :key="player.username">
        <p class="flex gap-1 text-xl">
          <span>{{ emojis[key] }}</span
          >{{ player.items }} @{{ player.username }}
        </p>
      </div>
    </div>
  </section>
  <section class="mb-16 grid grid-cols-1 sm:grid-cols-3 gap-3 my-4" v-auto-animate>
    <div>
      <p class="font-bold uppercase text-lg mb-4">Latest Spawn ({{ data.meta.latestSpawned.zone }})</p>
      <!-- <p>@ {{ latestSpawnedDate }}</p> -->
      <InventoryItem :name="data.meta.latestSpawned.name" :rarity="data.meta.latestSpawned.rarity" />
    </div>
    <div>
      <p class="font-bold uppercase text-lg mb-4">Most Spawned</p>
      <InventoryItem
        :name="data.meta.mostSpawned.name"
        :count="data.meta.mostSpawned.number"
        :rarity="data.meta.mostSpawned.rarity" />
    </div>
    <div>
      <p class="font-bold uppercase text-lg mb-4">Least Spawned</p>
      <InventoryItem
        :name="data.meta.leastSpawned.name"
        :count="data.meta.leastSpawned.number"
        :rarity="data.meta.leastSpawned.rarity" />
    </div>
  </section>

  <section class="mb-16" v-auto-animate>
    <h2 class="font-bold uppercase text-xl">Current Population</h2>
    <div class="grid grid-cols-1 sm:grid-cols-2 gap-3 my-4" v-auto-animate>
      <InventoryInfoItem
        v-for="(count, zone) in data.meta.population"
        class="bg-gradient-to-bl via-zinc-950 via-50% to-zinc-950 to-100%"
        :class="locationBgClass[zone]"
        :key="zone"
        :icon="`/icons/zones/${zone}.svg`"
        iconSecondary="/icons/utils/population.svg"
        iconSecondaryAlt="people representing population"
        :iconAlt="zone.toString()"
        :topText="zone"
        :bottomText="`Zone ${count}`" />
    </div>
  </section>
</template>

<style>
.emojis {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr 1fr 1fr;
  gap: 1rem;
  width: max-content;
  margin: auto;
  font-size: clamp(2rem, 4vw, 4rem);
}
</style>
