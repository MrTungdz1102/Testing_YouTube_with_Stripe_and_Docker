<script setup>
import Navbar from './src/components/Navbar.vue';
import {
  NConfigProvider,
  darkTheme,
  lightTheme,
  NBackTop,
  NMessageProvider,
  NIcon,
  NLoadingBarProvider,
  NH1,
} from 'naive-ui';
import { ref, onMounted } from 'vue';
import { BackToTop } from '@vicons/carbon';
import { localDB } from './src/utils/localDB';

const theme = ref(localStorage.theme === 'dark' ? darkTheme : lightTheme);

const handleToggleTheme = () => {
  const currentTheme = theme.value.name;
  theme.value = currentTheme === 'light' ? darkTheme : lightTheme;
  localStorage.theme = theme.value.name;
};

onMounted(() => {
  const { init } = localDB();
  init();
});
</script>

<template>
  <div id="no-support-mobile-devices">
    <n-h1 :style="{ fontSize: '8vw' }">
      This page is currently not supported on mobile devices
    </n-h1>
  </div>
  <div id="main-body">
    <n-config-provider :theme="theme">
      <n-message-provider>
        <n-loading-bar-provider>
          <Navbar @toggle-theme="handleToggleTheme" />
          <div :style="{ paddingTop: '54px' }" />
          <router-view />
          <n-back-top
            :visibility-height="500"
            :style="{ filter: 'invert(100%)', boxShadow: 'none' }"
          >
            <n-icon :component="BackToTop" :size="24" />
          </n-back-top>
        </n-loading-bar-provider>
      </n-message-provider>
    </n-config-provider>
  </div>
</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Roboto:ital,wght@0,400;0,500;0,700;1,400;1,500&display=swap');
body {
  font-family: 'Roboto', sans-serif;
}

#no-support-mobile-devices {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100vh;
  padding: '50px';
  text-align: center;
}

#main-body {
  display: none;
}

@media only screen and (min-width: 1024px) {
  #main-body {
    display: initial;
  }
  #no-support-mobile-devices {
    display: none;
  }
}
</style>
