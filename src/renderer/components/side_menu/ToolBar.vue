<template>
  <div>
    <v-toolbar
      v-if="!inSearch"
      id="ToolBar"
      height="40px"
    >
      <v-btn
        id="menu"
        :class="{ menu_activated:isMenuOn, menu_normal:!isMenuOn}"
        :ripple="false"
        @click="$emit('turnMenuOn');isMenuOn=!isMenuOn"
      >
        menu
      </v-btn>
      <v-btn
        v-if="!isSideMenuFolded&&!isGroupOn"
        id="groupOff"
        :ripple="false"
        @click="$emit('turnGroupOn'); isGroupOn=true; $emit('ensureSelectOff')"
      >
        group
      </v-btn>
      <v-btn
        v-if="!isSideMenuFolded&&isGroupOn"
        id="groupOn"
        :ripple="false"
        @click="$emit('turnGroupOff'); isGroupOn=false"
      >
        group
      </v-btn>
      <v-spacer />
      <!-- search button -->
      <v-btn
        v-show="!isSideMenuFolded"
        class="no-background-hover"
        style="right:20px"
        icon
        large
        @click="toggleSearch()"
      >
        <v-icon>mdi-magnify</v-icon>
      </v-btn>
      <v-btn
        v-if="isSideMenuFolded"
        icon
        class="no-background-hover"
        large
        @click="$emit('turnOffSideMenu')"
      >
        <img
          svg-inline
          src="../../assets/icons/FullScreen.svg"
          alt="FullScreen"
        >
      </v-btn>
      <!-- fold sideMenu button -->
      <v-btn
        v-if="!isSideMenuFolded"
        icon
        class="no-background-hover"
        large
        @click="$emit('foldSideMenu'); $emit('unlockFold');isGroupOn=false; $emit('turnGroupOff');$emit('ensureSelectOff')"
      >
        <img
          svg-inline
          src="../../assets/icons/crossButton.svg"
          alt="crossButton"
        >
      </v-btn>
    </v-toolbar>

    <!-- search clicked: show search bar -->
    <v-text-field
      v-if="inSearch"
      autofocus
      hide-details
      filled
      rounded
      dense
      placeholder="Search Edits"
      prepend-inner-icon="mdi-magnify"
      class="text-field"
      @blur="toggleSearch()"
    />
  </div>
</template>

<script>
export default {
  name: 'ToolBar',
  props: {
    isSideMenuFolded: Boolean,
  },
  data() {
    return {
      inSearch: false,
      isGroupOn: false,
      isMenuOn: false
    }
  },
  watch: {
    isThatHovered: function (val) {
      console.log(val)
    },
    isSideMenuFolded: function (val) {
      if (val) {
        this.isGroupOn = false
      }
    }
  },
  methods: {
    toggleSearch() {
      this.inSearch = !this.inSearch
    },
  }

}
</script>

<style scoped>
.menu_activated {
  background: #FFA24D !important;
}

.menu_normal {
  background: #858585 !important;
}

#groupOff {
  width: 65px;
  height: 26px;
  background: #858585;
  border-radius: 36px;
  font-family: Roboto, sans-serif;
  font-style: normal;
  font-weight: normal;
  font-size: 12px;
  letter-spacing: -0.333333px;
  color: #F4F4F4;
  box-shadow: 0 0 0 #c6c6c6;
  margin-left: 6px
}

#groupOn {
  width: 65px;
  height: 26px;
  background: #FFA24D;
  border-radius: 36px;

  font-family: Roboto, sans-serif;
  font-style: normal;
  font-weight: normal;
  font-size: 12px;
  letter-spacing: -0.333333px;
  color: #F4F4F4;
  box-shadow: 0 0 0 #c6c6c6;
  margin-left: 6px
}

#menu {
  width: 65px;
  height: 26px;
  border-radius: 36px;
  font-family: Roboto, sans-serif;
  font-style: normal;
  font-weight: normal;
  font-size: 12px;
  letter-spacing: -0.333333px;
  color: #F4F4F4;
  box-shadow: 0 0 0 #c6c6c6;
}

#ToolBar {
  background: #E9E9E9;
  border-radius: 36px;
  box-shadow: 0 0 0 #c6c6c6;
}

.text-field {
  border-radius: 36px;
}

.no-background-hover {
  width: 26px;
  height: 26px;
  right: 10px;
}

.no-background-hover::before {
  background-color: transparent !important;
}
</style>
