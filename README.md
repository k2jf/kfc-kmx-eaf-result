# kfc-pas-results-list

## 功能
用于展示PAS结果文件列表

## 维护者
youli

| 属性        | 说明     | 类型   | 默认值 |
| ----------- | -------- | ------ | ------ |
| queryParams | 请求参数 | Object | -      |

## 示例
```
<template>
    <PasResultsList :pasQueryParams="pasQueryParams"></PasResultsList>
</template>

<script>
    import PasResultsList from '@/components/kfc-pas-results-list'
    components: {
        PasResultsList
    },
    export default {
    data () {
        return {
        pasQueryParams: {
          filePath: '/test-lxt/102282/31284/20190226140308149/EXTRACTION',
          expand: false
        }
        }
    },
    }
</script>
