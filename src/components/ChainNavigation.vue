<template>
    <section>
        <section class="contain chain-nav">
            <router-link exact-active-class="active" tag="button" :to="{ name: 'power' }">{{ $t('lang.votingPower') }}</router-link>
            <router-link exact-active-class="active" tag="button" :to="{ name: 'producers' }">{{ $t('lang.producers') }}</router-link>
            <router-link exact-active-class="active" tag="button" :to="{ name: 'info' }" exact>{{ $t('lang.chainInfo') }}</router-link>
            <button v-if="canShowScatterButton()" @click="loginWithScatter">{{ $t('lang.scatter') }}</button>
            <button v-if="!canShowScatterButton()" @click="logoutScatter">Logout</button>
        </section>
        <hr/>
    </section>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import { mapActions, mapGetters, mapState } from "vuex";
import { getAccount } from "@/utils/eos.util";
/**
 * Bootstrap-vue components
 */

@Component({
  components: {},
  computed: {
    ...mapState(["scatter", "chainState", "network"]),
    ...mapGetters(["identity", "account"])
  },
  methods: {
    ...mapActions(["login", "logout"])
  }
})
export default class ChainNavigation extends Vue {
  scatter!: any;
  identity!: any;
  network!: any;
  account!: any;
  login!: () => void;
  logout!: () => void;

  canShowScatterButton() {
    if (!this.scatter) return true;
    if (!this.identity) return true;
    if (!this.account) return true;
    //        	if(this.account) return true;
    return false;
  }

  async loginWithScatter() {
    // User does not have Scatter.
    if (!this.scatter) return this.$router.push("/help#setting-up-scatter");

    await this.login();
    if (!this.account || !await getAccount(this.account.name)) {
      (this as any).$toasted.error(
        "There was an issue finding this account on the network. Perhaps it doesn't exist on this chain.",
        {
          theme: "primary",
          position: "top-center",
          duration: 5000
        }
      );
      this.$router.push("/");
    }
  }

  async logoutScatter(){
      this.logout();
  }
}
</script>

<style lang="scss">
</style>