<template>
  <div class="container">
    <div class="editors">
      <div class="editor-container">
        <query-editor
          v-if="schema"
          :schema="schema"
          :lite-mode="true"
          :value="queryValue"
          @change="onQueryChange"
          @runQuery="onRunQuery"
        />
      </div>
      <div class="editor-container">
        <result-viewer
          :value="resultValue"
          :lite-mode="true" />
      </div>
    </div>
  </div>
</template>
 
<script>
import { buildClientSchema, introspectionQuery } from 'graphql'
import gqlf from 'graphql-fetch'
import QueryEditor from './QueryEditor'
import ResultViewer from './ResultViewer'

const gqlFetch = gqlf('https://graphql-pokemon.now.sh')

export default {
  components: {
    QueryEditor,
    ResultViewer
  },
  data() {
    return {
      schema: null,
      queryEditorOptions: {},
      queryValue: '',
      resultValue: ``
    }
  },
  async mounted() {
    const introResult = await gqlFetch(introspectionQuery)
    const schema = buildClientSchema(introResult.data)
    this.schema = schema
  },
  methods: {
    onQueryChange(value) {
      this.queryValue = value
    },
    onRunQuery(value) {
      this.executeQuery(value)
        .then(result => (this.resultValue = JSON.stringify(result)))
        .catch(e => console.error(e))
    },
    executeQuery(query) {
      return gqlFetch(query)
    }
  }
}
</script> 

<style lang="stylus">
.CodeMirror
  text-align left !important
  height 100%

.vue-codemirror-wrap
  height 100%

#app
  font-family: 'Avenir', Helvetica, Arial, sans-serif
  -webkit-font-smoothing: antialiased
  -moz-osx-font-smoothing: grayscale
  text-align: center
  color: #2c3e50
  margin-top: 60px

[v-cloak] {
  display: none
}

.container
  margin: 0 auto
  width: 75vw
  height: 50vh

.editors
  border: #ddd solid 1px
  margin-bottom: 10px
  width: 100%
  height: 100%
  display flex
  .editor-container
    flex 1
    border 1px solid rgba(0,0,0,0.2)

.CodeMirror-hints.graphiql
  padding 0px
  .CodeMirror-hint
    border-radius 0px
    padding 5px
    font-size 12px
    &.CodeMirror-hint-active
      background linear-gradient(136.87deg,#6743f3,#4017d6)
      font-weight bold
  .CodeMirror-hint-information
    padding 5px
    background #1be5b2
.CodeMirror-lint-tooltip
  background linear-gradient(136.87deg,#6743f3,#4017d6)
  color white
</style>
