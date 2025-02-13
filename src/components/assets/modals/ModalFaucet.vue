<template>
  <astar-simple-modal
    v-if="!isLoading"
    :show="isModalFaucet"
    title="Faucet"
    :is-closing="isClosingModal"
    @close="closeModal"
  >
    <div class="wrapper--modal wrapper--faucet">
      <div class="wrapper__row--title">
        <span class="text--accent">{{ $t('assets.modals.whatIsFaucet') }}</span>
      </div>
      <div class="wrapper__row--information">
        <span class="text--md">{{ $t('assets.modals.faucetIntro') }}</span>
      </div>
      <div class="box--faucet-amount">
        <div class="box__column-amount">
          <span class="text--accent">{{ $t('assets.modals.youWillReceive') }}</span>
          <span class="text--xl">{{ $n(faucetAmount) }} {{ unit }}</span>
        </div>
      </div>
      <div v-if="!isAbleToFaucet" class="box--faucet--next-available">
        <span class="text--accent color--warning">
          {{ $t('assets.modals.faucetNextRequest') }}</span
        >
        <span class="text--xl">
          {{
            $t('assets.modals.countDown', {
              hrs: countDown.hours,
              mins: countDown.minutes,
              secs: countDown.seconds,
            })
          }}</span
        >
      </div>
      <div v-if="isAbleToFaucet" class="wrapper__row--button">
        <button class="btn btn--confirm" @click="handleRequest">{{ $t('confirm') }}</button>
      </div>
    </div>
  </astar-simple-modal>
</template>
<script lang="ts">
import { useFaucet } from 'src/hooks';
import { defineComponent, computed, ref } from 'vue';
import { fadeDuration } from '@astar-network/astar-ui';

export default defineComponent({
  props: {
    isModalFaucet: {
      type: Boolean,
      required: true,
    },
    handleModalFaucet: {
      type: Function,
      required: true,
    },
  },
  setup(props) {
    const isClosingModal = ref<boolean>(false);
    const isModalFaucet = computed(() => props.isModalFaucet);
    const { requestFaucet, isLoading, unit, isAbleToFaucet, countDown, faucetAmount } =
      useFaucet(isModalFaucet);

    const closeModal = (): void => {
      isClosingModal.value = true;

      setTimeout(() => {
        props.handleModalFaucet({ isOpen: false });
        isClosingModal.value = false;
      }, fadeDuration);
    };

    const handleRequest = async () => {
      try {
        await requestFaucet();
      } catch (error) {
        console.error(error);
      } finally {
        closeModal();
      }
    };
    return {
      faucetAmount,
      isLoading,
      unit,
      isAbleToFaucet,
      countDown,
      isClosingModal,
      closeModal,
      handleRequest,
    };
  },
});
</script>

<style lang="scss" scoped>
@use 'src/components/assets/styles/modal-faucet.scss';
</style>
