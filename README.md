# kfc-pas-results-list

## 功能
用于展示PAS结果文件列表

## 维护者
youli

| 属性        | 说明     | 类型   | 默认值 |
| ----------- | -------- | ------ | ------ |
| filePath | 请求参数 | String | -      |
| expand | 请求参数 | Boolean | -      |

## 示例
```
<template>
    <PasResultsList :filePath="filePath" :expand="expand"></PasResultsList>
</template>

<script>
    import PasResultsList from '@/components/kfc-pas-results-list'

    export default {
    components: {
        PasResultsList
    },
    data () {
        return {
          filePath: '/test-lxt/102282/31284/20190226140308149/EXTRACTION',
          expand: false
        }
    },
    }
</script>
