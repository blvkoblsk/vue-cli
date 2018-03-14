<template>
  <div class="project-select page">
    <StepWizard
      :tab-id.sync="tab"
      :title="$route.query.title || 'Vue Project Manager'"
      :hide-tabs="!!hideTabs"
      class="frame"
    >
      <VueTab
        id="existing"
        label="Projects"
        icon="storage"
      >
        <ProjectSelectList/>
      </VueTab>

      <VueTab
        id="create"
        label="Create"
        icon="add_box"
      >
        <div class="content">
          <FolderExplorer/>
        </div>

        <div class="actions-bar center">
          <VueButton
            icon-left="add"
            :label="$route.query.action || 'Create a new project here'"
            class="big primary"
            :to="{ name: 'project-create' }"
          />
        </div>
      </VueTab>

      <VueTab
        id="import"
        label="Import"
        icon="unarchive"
      >
        <div class="content">
          <FolderExplorer/>
        </div>

        <div class="actions-bar center">
          <VueButton
            icon-left="unarchive"
            :label="$route.query.action || 'Import this folder'"
            class="big primary"
            :disabled="!folderCurrent.isVueProject"
            @click="importProject()"
          />
        </div>
      </VueTab>
    </StepWizard>
  </div>
</template>

<script>
import FOLDER_CURRENT from '../graphql/folderCurrent.gql'
import PROJECT_IMPORT from '../graphql/projectImport.gql'

export default {
  name: 'ProjectSelect',

  data () {
    return {
      folderCurrent: {},
      tab: undefined,
      hideTabs: this.$route.query.hideTabs
    }
  },

  apollo: {
    folderCurrent: FOLDER_CURRENT
  },

  async mounted () {
    await this.$nextTick()
    this.tab = this.$route.query.tab || 'existing'
  },

  methods: {
    async importProject () {
      await this.$apollo.mutate({
        mutation: PROJECT_IMPORT,
        variables: {
          input: {
            path: this.folderCurrent.path
          }
        }
      })

      this.$router.push({ name: 'project-home' })
    }
  }
}
</script>

<style lang="stylus" scoped>
@import "~@/style/imports"

.folder-explorer
  height 100%

.folder-explorer
  flex 100% 1 1

.project-select
  height 100%
</style>