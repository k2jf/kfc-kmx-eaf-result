<template>
  <a @click="onDownloadClick">下载</a>
</template>

<script>
export default {
  name: 'SourceCellRender',
  props: {
    index: {
      type: Number,
      required: true,
      default () {
        return 0
      }
    },
    exportFile: {
      type: Function,
      required: true
    }
  },
  methods: {
    onDownloadClick () {
      this.exportFile(this.index)
    }
  }
}
</script>
