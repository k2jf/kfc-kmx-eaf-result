<template>
  <i-content>
    <i-table
      :columns="getSourceTableColunms"
      :data="currentTable.data"
      :loading="currentTable.loading"
      size="small"
      v-if="isShowTable" />
  </i-content>
</template>

<script>
import { Content, Avatar, Table } from 'iview'
import SourceCellRender from './components/SourceCellRender.vue'

import { api } from './api'

import FOLDER from './static/FOLDER.png'
import CSV from './static/CSV.png'
import ECEL from './static/ECEL.png'
import TET from './static/TET.png'
import WORD from './static/WORD.png'
import ZIP from './static/ZIP.png'

export default {
  name: 'PasResultsList',
  components: {
    'i-table': Table,
    'i-content': Content
  },
  props: {
    pasQueryParams: {
      type: Object,
      required: false,
      default () {
        return {
          filePath: '/test-lxt/102282/31284/20190226140308149/EXTRACTION',
          expand: false
        }
      }
    }
  },
  data () {
    return {
      currentTable: {
        data: null,
        loading: true
      }, // 当前表格数据
      isShowTable: false,
      fileTypes: ['zip', 'pdf', 'xls', 'txt', 'doc', 'csv'],
      token: 'eyJjdHkiOiJKV1QiLCJlbmMiOiJBMTkyQ0JDLUhTMzg0IiwiYWxnIjoiZGlyIn0..K09zHAVbgBDJLugW2TsKhg.ImY4y0pxJw1buidfWO6W7p7xwf7TxdOhBfndlPWhoCfcK7ggiqAj5qyWiMXCHbTr4scEGmzv1kROmGKJaNvX-aVFnEsnXSdjCjtfHT_GX-e0MSBWKfsfOgCtuLznXk5wcVK0BFf1mQXOQUS74JWmTNK9OGfRqyKwAm_iwI3CBz46OFgZ3H53VhXZZhLM1N-Uz0FRtgZ8JtIAL_CIP5ZcMotSH7OgCRWNanIT6s5b8JXBaHOcjM1qkzPlY0kSuNlm.ZlizGrSVV40yJEvnTMdFQsc_lyxPW7v0'
    }
  },
  computed: {
    getSourceTableColunms () {
      return [
        { title: '#', type: 'index', width: 60, ellipsis: true },
        {
          title: '名称',
          key: 'name',
          render: (h, params) => {
            return h(
              'div',
              {
                style: {
                  display: 'flex'
                }
              },
              [
                h(Avatar, {
                  props: {
                    src: this.getIcon(params.row.name),
                    shape: 'square',
                    size: 'small'
                  },
                  style: {
                    marginRight: '10px',
                    cursor: this.isFolder(params.row.name) ? 'pointer' : 'default'
                  },
                  on: {
                    click: () => {
                      if (!this.isFolder(params.row.name)) return
                      this.getPasResultsData(params.row.path)
                    }
                  }
                }),
                h(
                  this.isFolder(params.row.name) ? 'a' : 'span',
                  {
                    style: {
                      alignSelf: 'center',
                      cursor: this.isFolder(params.row.name) ? 'pointer' : 'default'
                    },
                    on: {
                      click: () => {
                        if (!this.isFolder(params.row.name)) return
                        this.getPasResultsData(params.row.path)
                      }
                    }
                  },
                  params.row.name
                )
              ]
            )
          }
        },
        { title: '大小(B)', key: 'size', minWidth: 100, maxWidth: 160 },
        {
          title: '操作',
          minWidth: 100,
          maxWidth: 160,
          render: (h, params) => {
            if (!this.isFolder(params.row.name)) {
              return h(SourceCellRender, {
                props: {
                  index: params.index,
                  exportFile: this.exportFile
                }
              })
            }
            return h('span')
          }
        }
      ]
    }
  },
  watch: {
    'currentTable.data': {
      handler (curVal, oldVal) {
        if (!curVal) return
        this.isShowTable = true
      }
    }
  },
  created () {
    this.getPasResultsData()
  },
  methods: {
    // 调用接口获取PAS结果文件列表数据
    getPasResultsData (filePath) {
      this.currentTable.loading = true
      filePath = filePath || this.pasQueryParams.filePath

      this.$axios.get(`${api.pasService}?filePath=${filePath}&expand=${this.pasQueryParams.expand}`).then(res => {
        this.currentTable.loading = false
        this.currentTable.data = res.data.result
      })
    },
    // 文件下载
    exportFile (index) {
      let filePath = this.currentTable.data[index].path
      let fileName = this.currentTable.data[index].name
      let url = `${api.pasDownload}?filePath=${filePath}&fileName=${fileName}`
      const link = document.createElement('a')
      link.href = url
      link.setAttribute('download', fileName)
      document.body.appendChild(link)
      link.click()
    },
    // 根据后缀名获取文件图标
    getIcon (fileName) {
      let icon = FOLDER
      if (!fileName) return icon
      let index = fileName.lastIndexOf('.')
      let ext = fileName.substr(index + 1)

      switch (ext) {
      case 'zip':
        icon = ZIP
        break
      case 'doc':
        icon = WORD
        break
      case 'csv':
        icon = CSV
        break
      case 'excel':
        icon = ECEL
        break
      case 'txt':
        icon = TET
        break
      default:
        icon = FOLDER
      }
      return icon
    },
    // 判断是否是文件夹
    isFolder (fileName) {
      let index = fileName.lastIndexOf('.')
      let ext = fileName.substr(index + 1)
      return !this.fileTypes.includes(ext)
    }
  }
}
</script>
