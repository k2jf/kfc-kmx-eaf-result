<template>
  <i-content>
    <i-table v-if="isShowTable" :columns="getSourceTableColunms" :data="currentTable.data" :loading="currentTable.loading"/>
  </i-content>
</template>

<script>
import { Content, Avatar, Table } from 'iview'
import SourceCellRender from './components/SourceCellRender.vue'

import FOLDER from './static/FOLDER.png'
import CSV from './static/CSV.png'
import ECEL from './static/ECEL.png'
import TET from './static/TET.png'
import WORD from './static/WORD.png'
import ZIP from './static/ZIP.png'

export default {
  name: 'PasResultsList',
  props: {
    pasQueryParams: {
      type: Object,
      required: true,
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
      pasResultsData: null,
      currentTable: {
        name: 'source',
        data: null,
        loading: true
      }, // 当前表格数据
      isShowTable: false,
      fileTypes: ['zip', 'pdf', 'xls', 'txt', 'doc', 'csv'],
      token: 'eyJjdHkiOiJKV1QiLCJlbmMiOiJBMTkyQ0JDLUhTMzg0IiwiYWxnIjoiZGlyIn0..K09zHAVbgBDJLugW2TsKhg.ImY4y0pxJw1buidfWO6W7p7xwf7TxdOhBfndlPWhoCfcK7ggiqAj5qyWiMXCHbTr4scEGmzv1kROmGKJaNvX-aVFnEsnXSdjCjtfHT_GX-e0MSBWKfsfOgCtuLznXk5wcVK0BFf1mQXOQUS74JWmTNK9OGfRqyKwAm_iwI3CBz46OFgZ3H53VhXZZhLM1N-Uz0FRtgZ8JtIAL_CIP5ZcMotSH7OgCRWNanIT6s5b8JXBaHOcjM1qkzPlY0kSuNlm.ZlizGrSVV40yJEvnTMdFQsc_lyxPW7v0'
    }
  },
  created () {
    this.getPasResultsData()
  },
  methods: {
    // 调用接口获取PAS结果文件列表数据
    getPasResultsData (filePath, currentTableName) {
      this.currentTable.loading = true
      filePath = filePath || this.pasQueryParams.filePath
      currentTableName = currentTableName || 'source'

      let xhr = new XMLHttpRequest()
      xhr.timeout = 6000
      xhr.ontimeout = function (event) {
        alert('请求超时！')
      }
      let url = `http://10.12.20.36:28085/pas/services/hdfs/browse?filePath=${filePath}&expand=${this.pasQueryParams.expand}`

      xhr.open('GET', url)
      xhr.setRequestHeader('K2_KEY', this.token)
      xhr.setRequestHeader('Content-Type', 'application/json;charset=UTF-8')
      xhr.onreadystatechange = () => {
        if (xhr.readyState === 4) {
          this.currentTable.loading = false
          this.currentTable.name = currentTableName
          this.currentTable.data = JSON.parse(xhr.responseText).result
        }
      }
      xhr.send(null)
    },
    showDetailInfo (index) {
      const currentTableName = this.currentTable.name
      let filePath = this.pasQueryParams.filePath
      switch (currentTableName) {
        case 'source':
          filePath = this.currentTable.data[index].path
          this.getPasResultsData(filePath, 'sourceDetail')
          break
        case 'sourceDetail':
          filePath = this.currentTable.data[index].path
          this.getPasResultsData(filePath, 'sourceDetailItems')
          break
        default:
          break
      }
    },
    exportFile (index) {
      let filePath = this.currentTable.data[index].path
      let fileName = this.currentTable.data[index].name
      let url = `http://10.12.20.36:28085/pas/services/hdfs/download?filePath=${filePath}&fileName=${fileName}`
      window.open(url)
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
  },
  watch: {
    'currentTable.data': {
      handler (curVal, oldVal) {
        if (!curVal) return
        this.isShowTable = true
      }
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
                    shape: 'square'
                  },
                  style: {
                    marginRight: '10px',
                    cursor: this.currentTable.name !== 'sourceDetailItems' ? 'pointer' : 'default'
                  },
                  on: {
                    click: () => {
                      this.showDetailInfo(params.index)
                    }
                  }
                }),
                h(
                  this.currentTable.name !== 'sourceDetailItems' ? 'a' : 'span',
                  {
                    style: {
                      alignSelf: 'center',
                      cursor: this.currentTable.name !== 'sourceDetailItems' ? 'pointer' : 'default'
                    },
                    on: {
                      click: () => {
                        this.showDetailInfo(params.index)
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
  components: {
    'i-table': Table,
    'i-content': Content
  }
}
</script>

<style lang="css" scoped>
.ivu-table-row .ivu-avatar {
  width: 16px !important;
  height: 16px !important;
}
</style>
