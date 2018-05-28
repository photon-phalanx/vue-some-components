# 季度选择器

> QuarterSelecter

## Usage
```vue
<quarter-selector :leftMinYear="1900" :rightMaxYear="2010" @submit="handleSubmit"></quarter-selector>
```
props
```
      leftMinYear: {
        type: Number,
        default: 1900
      },
      rightMaxYear: {
        type: Number,
        default: new Date().getFullYear()
      }
```

submit事件
```
  {
    startDate: yyyy-Qx,
    endDate: yyyy-Qx
  }
```
## bugs
现在还需要依赖iview的icon，有时间自己搞icon，先下班吧
