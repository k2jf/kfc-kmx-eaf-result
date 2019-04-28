# kfc-kmx-eaf-result

## 功能
用于展示eaf结果文件列表

## 维护者
youli

| 属性        | 说明     | 类型   | 默认值 |
| ----------- | -------- | ------ | ------ |
| filePath | 请求参数 | String | -      |
| expand | 请求参数 | Boolean | -      |

## 示例
```
<template>
    <EafResultList :filePath="filePath" :expand="expand"></EafResultList>
</template>

<script>
    import EafResultList from '@/components/kfc-kmx-eaf-result'

    export default {
    components: {
        EafResultList
    },
    data () {
        return {
          filePath: '/test-lxt/102282/31284/20190226140308149/EXTRACTION',
          expand: false
        }
    },
    }
</script>
