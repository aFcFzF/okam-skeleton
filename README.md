# Skeleton 组件

## 概述
小程序骨架屏

## 使用指南
* 引入：在 `Okam` 文件中引入 声明组件

``` js
import Skeleton from 'okam-skeleton/Skeleton';
import Placeholder from 'okam-skeleton/Placeholder';
// 若使用zw-lab
// import Skeleton from '@baidu/zw-lib/src/components/Skeleton/Skeleton';
// import Placeholder from '@baidu/zw-lib/src/components/Skeleton/Placeholder';
export default {
    components: {
        Skeleton,
        Placeholder
    }
};
```
* 使用：在 `Okam` 文件中使用组件

```
<skeleton :loading="listLoading" fade :active="showActive">
    <div slot="placeholder" >
        <placeholder
            title
            title-width="30%"
            avatar
            size="small"
            shape="circle"
            align="center"
            :rows="1"
            rows-width="100%"
            split
        ></placeholder>
    </div>
</skeleton>
```

## API

### 参数
| 参数 | 说明 | 类型 | 可选值 | 默认值 |
|---|---|---|---|---|
| theme | bar的主题颜色 | string  | `light`, `dark` | `light` |




### 插槽
| 名称 | 说明 |
|-|-|
| body | 页面的主体内容 |


### 事件

| 事件名 | 说明 | 参数 |
|---|---|---|
| @back | 点击自定义顶bar左上角的返回 icon |  |




## 示例
<!-- 这里只能写相对路径... -->
[CustomBarPage](../../example/src/pages/CustomBarPage/index.vue ':include :type=code')