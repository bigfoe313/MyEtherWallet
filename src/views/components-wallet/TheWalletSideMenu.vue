<template>
  <div class="mew--wallet-side-menu">
    <v-navigation-drawer
      app
      class="wallet-sidemenu"
      :src="background"
      width="300"
      :dark="$vuetify.theme.dark"
    >
      <template #prepend>
        <div class="pa-5 pb-3">
          <div class="mt-2 mb-4">
            <router-link :to="{ name: 'Dashboard' }">
              <img width="120" src="@/assets/images/icons/logo-mew.png" />
            </router-link>
          </div>
          <balance-card />
          <mew-super-button
            font-class="mew-body"
            class="mt-3 px-3"
            title="Buy ETH here"
            color-theme="outline"
            style="height: 46px; border-radius: 5px"
            @click.native="openSimplex"
          >
            <!-- going to change slot name -->
            <template #contentSlot>
              <img
                src="@/assets/images/icons/icon-visa-white.png"
                alt="Master card"
                height="11"
              />
              <img
                src="@/assets/images/icons/icon-mastercard-mew.png"
                alt="Master card"
                height="16"
                class="ml-2 mr-8"
              />
            </template>
          </mew-super-button>
        </div>
      </template>

      <v-list>
        <v-list-item-group model="menuSelected">
          <template v-for="(item, idx) in sectionOne">
            <v-list-item
              v-if="!item.children"
              :key="item + idx + 1"
              :to="item.route"
            >
              <v-list-item-icon class="mx-3">
                <img
                  width="26"
                  height="26"
                  :src="item.icon"
                  :alt="item.title"
                />
              </v-list-item-icon>

              <v-list-item-content>
                <v-list-item-title
                  class="white--text font-weight-regular mew-body"
                  v-text="item.title"
                />
              </v-list-item-content>
            </v-list-item>

            <v-list-group
              v-if="item.children"
              :key="item + idx + 2"
              prepend-icon=""
            >
              <template #activator>
                <v-list-item-icon class="mx-3">
                  <img
                    width="26"
                    height="26"
                    :src="item.icon"
                    :alt="item.title"
                  />
                </v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title
                    class="white--text font-weight-regular mew-body"
                    v-text="item.title"
                  ></v-list-item-title>
                </v-list-item-content>
              </template>
              <v-list-item
                v-for="child in item.children"
                :key="child.title"
                dense
                class="pl-4"
                :to="child.route"
              >
                <v-list-item-content>
                  <v-list-item-title
                    class="pl-13 white--text font-weight-regular mew-body"
                    v-text="child.title"
                  ></v-list-item-title>
                </v-list-item-content>
              </v-list-item>
            </v-list-group>
          </template>
        </v-list-item-group>
      </v-list>

      <v-divider class="my-4 mx-6" />

      <v-list>
        <v-list-item
          v-for="(item, idx) in sectionTwo"
          :key="item + idx"
          dense
          @click="item.fn()"
        >
          <v-list-item-icon class="mx-3">
            <img width="26" height="26" :src="item.icon" :alt="item.title" />
          </v-list-item-icon>

          <v-list-item-content>
            <v-list-item-title
              class="white--text mew-body font-weight-regular"
              v-text="item.title"
            />
          </v-list-item-content>
        </v-list-item>

        <div class="mt-3 px-8 d-flex align-center justify-space-between">
          <theme-switch />
          <div class="searchText--text">v{{ version }}</div>
        </div>
      </v-list>
    </v-navigation-drawer>
    <mew-popup
      :is-open="showLogoutPopup"
      :title="$t('interface.menu.logout')"
      :button-left="logout.btnLeft"
      :button-right="logout.btnRight"
      popup-type="confirm"
      @onClick="onLogout"
    ></mew-popup>
    <module-settings
      :on-settings="onSettings"
      @closeSettings="toggleSettings"
    />
  </div>
</template>

<script>
import background from '@/assets/images/backgrounds/bg-light.jpg';
import dashboard from '@/assets/images/icons/icon-dashboard-enable.png';
import send from '@/assets/images/icons/icon-send-enable.png';
import nft from '@/assets/images/icons/icon-nft.png';
import swap from '@/assets/images/icons/icon-swap-enable.png';
import dapp from '@/assets/images/icons/icon-dapp-center-enable.png';
import tools from '@/assets/images/icons/icon-contract-enable.png';
import settings from '@/assets/images/icons/icon-setting-enable.png';
import logout from '@/assets/images/icons/icon-logout-enable.png';
import BalanceCard from '@/modules/balance/ModuleBalanceCard';
import ModuleSettings from '@/modules/settings/ModuleSettings';
import ThemeSwitch from '@/components/theme-switch/ThemeSwitch';

export default {
  components: {
    BalanceCard,
    ModuleSettings,
    ThemeSwitch
  },
  data() {
    return {
      menuSelected: 0,
      version: process.env.VERSION,
      background: background,
      onSettings: false,
      showLogoutPopup: false,
      logout: {
        btnLeft: {
          title: 'Cancel',
          colorTheme: 'basic'
        },
        btnRight: {
          title: 'Log out',
          colorTheme: 'error'
        }
      },
      sectionOne: [
        {
          title: this.$t('interface.menu.dashboard'),
          route: { name: 'Dashboard' },
          icon: dashboard
        },
        {
          title: this.$t('sendTx.send-tx'),
          route: { name: 'SendTX' },
          icon: send
        },
        {
          title: this.$t('interface.menu.nft'),
          route: { name: 'NFTManager' },
          icon: nft
        },
        {
          title: this.$t('common.swap'),
          route: { name: 'Swap' },
          icon: swap
        },
        {
          title: this.$t('interface.menu.dapps-center'),
          route: { name: 'Dapps' },
          icon: dapp
        },
        {
          title: this.$t('interface.menu.tools'),
          icon: tools,
          children: [
            {
              title: this.$t('interface.menu.interact-contract'),
              route: { name: 'InteractWithContract' }
            },
            {
              title: this.$t('interface.menu.deploy'),
              route: { name: 'DeployContract' }
            },
            {
              title: this.$t('interface.menu.sign-message'),
              route: { name: 'SignMessage' }
            }
          ]
        }
      ],
      sectionTwo: [
        {
          title: this.$t('common.settings'),
          icon: settings,
          fn: this.toggleSettings
        },
        {
          title: this.$t('common.logout'),
          icon: logout,
          fn: this.toggleLogout
        }
      ]
    };
  },
  methods: {
    toggleSettings() {
      this.onSettings = !this.onSettings;
    },
    onLogout(res) {
      this.showLogoutPopup = false;
      if (res.title === this.logout.btnRight.title) {
        this.$router.push({ name: 'Home' });
      }
    },
    toggleLogout() {
      this.showLogoutPopup = !this.showLogoutPopup;
    },
    openSimplex() {
      window.open('https://ccswap.myetherwallet.com', '_blank');
    }
  }
};
</script>

<style lang="scss">
.wallet-sidemenu {
  .v-list-item--link {
    border-top: none;
  }
  .v-list-item--active {
    .v-list-item__content {
      .v-list-item__title {
        font-weight: 500 !important;
      }
    }
  }
  .v-list-group__header__append-icon {
    .v-icon {
      color: var(--v-white-base) !important;
    }
  }
  .v-divider {
    border-color: rgba(255, 255, 255, 0.22) !important;
  }

  .v-list-item--link:hover {
    background-color: rgba(255, 255, 255, 0.2) !important;
  }

  .v-list-item:after {
    min-height: 40px !important;
  }
  .mew-body.font-weight-bold {
    font-weight: 400 !important;
  }
  .v-list-item--active.v-list-item:not(.v-list-group__header) {
    background-color: rgba(255, 255, 255, 0.1) !important;
  }
  .v-list-item--active::before {
    opacity: 0 !important;
  }

  .v-navigation-drawer__content {
    margin-right: 5px;
    margin-bottom: 10px;
    &::-webkit-scrollbar {
      width: 4px;
      height: 4px;
    }
    &::-webkit-scrollbar-button {
      width: 0;
      height: 0;
    }
    &::-webkit-scrollbar-thumb {
      background: #7b91ac;
      border: 0 none #fff;
      border-radius: 50px;
    }
    &::-webkit-scrollbar-thumb:hover {
      background: #7b91ac;
    }
    &::-webkit-scrollbar-thumb:active {
      background: #4b4949;
    }
    &::-webkit-scrollbar-track {
      background: #e1dfdf;
      border: 0 none #fff;
      border-radius: 39px;
    }
    &::-webkit-scrollbar-track:hover {
      background: #ddd5d5;
    }
    &::-webkit-scrollbar-track:active {
      background: #dedede;
    }
    &::-webkit-scrollbar-corner {
      background: transparent;
    }
  }
}
</style>