<template>
  <div>
    <div class="header">
      <nav class="tabs">
        <router-link to="/dashboard" :class="['link', path === 'dashboard' && 'active-link']">
          <div class="column--item column--item--dashboard">
            <span class="text--link">
              {{ $t('dashboard.dashboard') }}
            </span>
          </div>
        </router-link>
        <router-link to="/assets" :class="['link', path === 'assets' && 'active-link']">
          <div class="column--item">
            <span>
              {{ $t('assets.assets') }}
            </span>
          </div>
        </router-link>
        <router-link
          v-if="network.isStoreEnabled"
          to="/dapp-staking"
          :class="['link', path === 'dapp-staking' && 'active-link']"
        >
          <div class="column--item">
            <span class="text--link">
              {{ $t('common.staking') }}
            </span>
          </div>
        </router-link>
        <router-link
          v-if="enableBridge"
          to="/bridge"
          :class="['link', path === 'bridge' && 'active-link']"
        >
          <div class="column--item">
            <span class="text--link">
              {{ $t('bridge.bridge') }}
            </span>
          </div>
        </router-link>
        <div class="tabs__indicator" :class="getIndicatorClass(path)" />
      </nav>

      <button type="button" class="button--option" @click="showOption = !showOption">
        <icon-base class="icon--dot" stroke="currentColor" icon-name="option">
          <icon-3dots />
        </icon-base>
      </button>
    </div>

    <div v-if="showOption" class="wrapper--bottom">
      <SocialMediaLinks />
      <div class="wrapper--option">
        <LightDarkMode />
        <LocaleChanger />
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import Icon3dots from 'components/icons/Icon3dots.vue';
import { endpointKey, providerEndpoints } from 'src/config/chainEndpoints';
import { useStore } from 'src/store';
import { computed, defineComponent, ref } from 'vue';
import { useRouter } from 'vue-router';
import LightDarkMode from '../common/LightDarkMode.vue';
import LocaleChanger from '../common/LocaleChanger.vue';
import SocialMediaLinks from '../common/SocialMediaLinks.vue';

export default defineComponent({
  components: {
    Icon3dots,
    LocaleChanger,
    SocialMediaLinks,
    LightDarkMode,
  },
  setup() {
    const store = useStore();
    const currentNetworkIdx = computed(() => store.getters['general/networkIdx']);
    const network = ref(providerEndpoints[currentNetworkIdx.value]);
    const showOption = ref(false);
    const isH160 = computed(() => store.getters['general/isH160Formatted']);
    const enableBridge = computed(
      () => isH160.value && currentNetworkIdx.value !== endpointKey.SHIBUYA
    );
    const router = useRouter();
    const path = computed(() => router.currentRoute.value.path.split('/')[1]);

    const getIndicatorClass = (path: string): string => {
      switch (path) {
        case 'dashboard':
          return 'tabs__dashboard';
        case 'assets':
          return 'tabs__assets';
        case 'dapp-staking':
          return 'tabs__staking';
        case 'bridge':
          return 'tabs__bridge';
        default:
          return 'tabs__staking';
      }
    };

    return {
      showOption,
      network,
      getIndicatorClass,
      path,
      enableBridge,
    };
  },
});
</script>

<style lang="scss" scoped>
@import './styles/sidebar-mobile.scss';
</style>
