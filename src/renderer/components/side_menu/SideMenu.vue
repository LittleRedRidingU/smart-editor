<template>
  <div
    id="side-menu"
  >
    <ToolBar
      :is-side-menu-folded="isSideMenuFolded"
      @turnOffSideMenu="$emit('maximizeEditor')"
      @foldSideMenu="$emit('foldSideMenu')"
      @turnMenuOn="turnTheMenuOn()"
      @turnGroupOn="isGroupOn=true; $emit('turnGroupOn');$emit('lockFold')"
      @turnGroupOff="isGroupOn=false; $emit('turnGroupOff');$emit('unlockFold')"
      @unlockFold="$emit('unlockFold')"
      @lockFold="$emit('lockFold')"
      @ensureSelectOff="ensureSelectOff=!ensureSelectOff;"
    />
    <Menu
      v-if="isMenuOn"
      id="menu"
    />
    <div id="ContentFamily">
      <GroupPanel
        v-if="isGroupOn"
        :edits="allEdits"
        @selected-edits-updated="selectedEditsUpdated"
      />
      <EditPanel
        :is-group-on="isGroupOn"
        :is-side-menu-folded="isSideMenuFolded"
        :ensure-select-off="ensureSelectOff"
        :edits="allEdits"
        @unlockFold="$emit('unlockFold')"
        @lockFold="$emit('lockFold')"
        @selected-edits-updated="selectedEditsUpdated"
        @delete-selected-edits="deleteSelectedEdits"
        @ensure-select-off="ensureSelectOff=!ensureSelectOff;"
        @apply-selected-edits="applySelectedEdits"
      />
    </div>
  </div>
</template>

<script>
import ToolBar from './ToolBar'
import EditPanel from './edit_panel/EditPanel'
import Menu from './menu/Menu'
import GroupPanel from './group_panel/GroupPanel'
import editManager from "../../model/EditManager";
import { bus } from "../../main";

export default {
  name: 'SideMenu',
  components: {EditPanel, ToolBar, Menu, GroupPanel},
  props: {
    isSideMenuFolded: Boolean,
  },
  data() {
    return {
      isMenuOn: false,
      isGroupOn: false,
      ensureSelectOff: false,
      selectedEdits: [],
      allEdits: editManager.edits
    }
  },
  computed: {},
  watch: {
    isSideMenuFolded: function (val) {
      if (val) {
        this.isGroupOn = false
      }
    }
  },
  methods: {
    turnTheMenuOn() {
      if (this.isMenuOn) {
        this.isMenuOn = false
        this.$emit('unlockFold')
      } else {
        this.isMenuOn = true
        this.$emit('lockFold')
      }
    },
    selectedEditsUpdated(selectedEdit) {
      console.log("IN sidemenu: " + selectedEdit)
      let index = this.selectedEdits.indexOf(selectedEdit)
      if (index === -1) {
        // if the edit is not in list, add it
        this.selectedEdits.push(selectedEdit)
      } else {
        // if the edit is in the list, it is unselect operation; delete it
        this.selectedEdits.splice(index, 1);
      }
      // let textarea update highlight range
      bus.$emit("selected-edits-updated", this.selectedEdits)
    },
    deleteSelectedEdits() {
      for (let i = 0; i < this.selectedEdits.length; i++) {
        editManager.deleteEdit(this.selectedEdits[i].id)
        this.refreshEdits()
      }
    },
    refreshEdits() {
      this.allEdits = editManager.edits
    },
    applySelectedEdits() {
      const ids = this.selectedEdits.map(edit => edit.id)
      console.log(ids)
      ids.forEach(id => editManager.getEditById(id).undoRedo())

      this.refreshEdits()
      bus.$emit('update-text-value')
    }

  }
}

</script>

<style scoped>
#ContentFamily {
  display: flex;
  flex-direction: row;
  overflow: auto;
  flex: 2;
}

#menu {
  flex: 0 0 auto;
}

#side-menu {
  display: flex;
  flex-direction: column;
}
</style>
