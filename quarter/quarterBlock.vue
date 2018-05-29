<template>
    <div class="block">
      <div class="title-wrapper">
        <i class="ivu-icon ivu-icon-ios-arrow-left" v-show="currentYear > minYear" @click="yearChange(-1)"></i>
        <span class="title">{{currentYear}}</span>
        <i class="ivu-icon ivu-icon-ios-arrow-right" v-show="currentYear < maxYear" @click="yearChange(1)"></i>
      </div>
      <div class="content-wrapper">
        <div v-for="index in 4" :class="{selected: currentQuarter === index, frozen: index < minQuarter || index > maxQuarter}"
             class="quarter" @click="quarterChange(index)">Q{{index}}</div>
      </div>
    </div>
</template>

<script>
  export default {
    name: 'quarter-block',
    data () {
      return {
        currentQuarter: 0
      }
    },
    created () {
      this.currentYear = new Date().getFullYear()
    },
    props: {
      minYear: {
        type: Number,
        required: true
      },
      maxYear: {
        type: Number,
        required: true
      },
      minQuarter: {
        type: Number,
        default: -Infinity
      },
      maxQuarter: {
        type: Number,
        default: Infinity
      },
      // 当取消季度选择时，左边应该归为负无穷，以让右边能够选择全部，右边相反同理
      reset: {
        type: Number,
        required: true
      },
      currentYear: {
        required: true,
        type: Number
      }
    },
    watch: {
      minQuarter (val) {
        if (this.currentQuarter < this.minQuarter || this.currentQuarter > this.maxQuarter) {
          this.currentQuarter = this.reset
          this.$emit('quarterChange', this.currentQuarter)
        }
      },
      maxQuarter (val) {
        if (this.currentQuarter < this.minQuarter || this.currentQuarter > this.maxQuarter) {
          this.currentQuarter = this.reset
          this.$emit('quarterChange', this.currentQuarter)
        }
      }
    },
    methods: {
      yearChange (payload) {
        this.$emit('yearChange', this.currentYear + payload)
      },
      quarterChange (index) {
        if (index < this.minQuarter || index > this.maxQuarter) return
        this.currentQuarter = (this.currentQuarter === index) ? this.reset : index
        this.$emit('quarterChange', this.currentQuarter)
      }
    }
  }
</script>

<style scoped lang="less">
  .block {
    display: inline-block;
    width: 120px;
    height: 160px;
    .title-wrapper {
      display: flex;
      justify-content: center;
      align-items: center;
      height: 40px;
      font-size: 14px;
      text-align: center;
      .title {
        flex: 1;
      }
      .ivu-icon {
        flex: 0 0 14px;
      }
    }
    .content-wrapper {
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
      align-items: center;
      .quarter {
        line-height: 50px;
        text-align: center;
        font-size: 20px;
        flex: 0 0 50%;
        height: 60px;
        background-origin: content-box;
        padding: 5px;
        border: 1px solid #ddd;
        box-sizing: border-box;
        cursor: pointer;
        &.selected {
          background-color: #66ccff;
        }
        &.frozen {
          background-color: #fff;
          color: #ddd;
          cursor: not-allowed;
        }
      }
    }
  }
</style>
