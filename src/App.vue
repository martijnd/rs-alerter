<script setup lang="ts">
import { onMounted, ref } from 'vue';

function checkNotifyPermission() {
  if (!("Notification" in window)) {
    alert("This browser does not support desktop notification");
  }

  else if (Notification.permission === "granted") {
    return true;
  }

  else if (Notification.permission !== "denied") {
    Notification.requestPermission().then(function (permission) {
      return permission === "granted";
    });
  }
}

function notify(message: string) {
  console.log('notify', message);
  if (checkNotifyPermission()) {
    new Notification(message);
  }
}

const offset = ref(2);
type DataType = Record<'clawdia' | 'caches', {
  time: number;
  value: boolean;
  message: (offset: number) => string;
}>;

const data = ref<DataType>({
  clawdia: {
    time: 45,
    value: true,
    message: (offset: number) => `Clawdia is spawning in ${offset} minutes.`
  },
  caches: {
    time: 0,
    value: true,
    message: (offset: number) => `Caches are starting in ${offset} minutes.`
  }
});

function checkTimes() {
  const currentTime = new Date().getMinutes();

  Object.entries(data.value).forEach(([, { value, message, time }]) => {
    if (value && currentTime === (time - offset.value + 60) % 60) {
      notify(message(offset.value));
    }
  });
}


onMounted(() => {
  setInterval(checkTimes, 60 * 1000);
  checkNotifyPermission();
  checkTimes();
});
</script>

<template>
  <div>
    <h1>RS alerter</h1>

    <label for="offset">
      <h2>Offset</h2><input id="offset" type="number" v-model="offset" />
    </label>


    <label for="clawdia">
      <h2>Clawdia</h2>
      <input id="clawdia" type="checkbox" v-model="data.clawdia.value" />
    </label>


    <label for="caches">
      <h2>Guthixian caches</h2>
    </label><input id="caches" type="checkbox" v-model="data.caches.value" />

  </div>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
}

.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}

.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
