<template>
    <div>
        <div ref="scroll" flex class="scroll-wrapper" style="height: calc(100%-1.1rem);">
            <div flex-box="1" flex="dir:top cross: center">
              <van-field v-model="contractAddress" center type="text" :placeholder="$t('input_contract_addr')" style="margin-top: 0.3rem;"/>
               <button :disabled="!registerEnable" class="jcc-moac-alarm-button jcc-moac-alarm-register-button" style="width:100%;margin-top: 0.4rem;" @click="goRegister">
              {{ $t("confirm_register") }}
                </button>
            </div>
        </div>
   </div>
</template>
<script>
import { moacWallet } from "jcc_wallet";
import tpInfo from "@/js/tp";
import AlarmContractInstance from "@/js/contract";
import { Toast } from "vant";
import * as transaction from "@/js/transaction";
import scrollIntoView from "@/mixins/scrollIntoView";
import { browser } from "@/js/util";
export default {
  data() {
    return {
      contractAddress: ""
    }
  },
  mixins: [scrollIntoView],
  computed: {
    registerEnable() {
      return moacWallet.isValidAddress(this.contractAddress.trim());
    }
  },
  beforeDestroy() {
    AlarmContractInstance.destroy();
  },
  methods: {
    async goRegister() {
      try {
        const node = await tpInfo.getNode();
        const instance = AlarmContractInstance.init(node);
        Toast.loading({
          duration: 0,
          forbidClick: true,
          loadingType: "spinner",
          message: this.$t("message.register_loading")
        });
        setTimeout(async () => {
          const state = await instance.contractRegister(this.contractAddress);
          console.log("contract register state: ", state);
          if (state) {
            Toast.success(this.$t("message.register_succeed"));
          } else {
            Toast.fail(this.$t("message.register_failed"));
          }
        }, 2000)
      } catch (error) {
        console.log("deposit error: ", error);
        Toast.fail(error.message);
      }
    }
  }
}
</script>
<style lang="scss" scoped>
</style>