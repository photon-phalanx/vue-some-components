<template>
    <div class="quarter-selector" ref="quarterSelector">
      <input type="text" class="quarter-input" @focus="focusHandle" v-model="rangeStr"/>
      <transition name="drop">
        <div class="dropdown" v-show="focus">
          <quarter-block class="quarter-block" :min-year="leftMinYear" :max-year="rightCurrentYear" :max-quarter="leftMaxQuarter" :reset="-Infinity" :currentYear="leftCurrentYear"
                         @yearChange="yearChangeHandler(1, $event)" @quarterChange="quarterChangeHandler(1, $event)"></quarter-block>
          <quarter-block class="quarter-block" :min-year="leftCurrentYear" :max-year="rightMaxYear" :min-quarter="rightMinQuarter" :max-quarter="rightMaxQuarter" :reset="Infinity" :currentYear="rightCurrentYear"
                         @yearChange="yearChangeHandler(2, $event)" @quarterChange="quarterChangeHandler(2, $event)"></quarter-block>
        </div>
      </transition>
    </div>
</template>

<script>
  import QuarterBlock from './quarterBlock'

  export default {
    name: 'quarter-selector',
    data () {
      return {
        rangeStr: '',
        focus: false,
        leftCurrentYear: this.rightMaxYear,
        rightCurrentYear: this.rightMaxYear,
        leftCurrentQuarter: -Infinity,
        rightCurrentQuarter: Infinity
      }
    },
    components: {
      QuarterBlock
    },
    props: {
      leftMinYear: {
        type: Number,
        default: 1900
      },
      rightMaxYear: {
        type: Number,
        default: new Date().getFullYear()
      }
    },
    methods: {
      focusHandle () {
        this.focus = true
      },
      yearChangeHandler (pos, payload) {
        if (pos === 1) this.leftCurrentYear = payload
        else this.rightCurrentYear = payload
      },
      quarterChangeHandler (pos, payload) {
        if (pos === 1) this.leftCurrentQuarter = payload
        else this.rightCurrentQuarter = payload
      },
      closeHandler () {
        this.focus = false
        // 正常数值就输出
        if (Math.abs(this.leftCurrentQuarter) + Math.abs(this.rightCurrentQuarter) !== Infinity) {
          let emitObj = {
            startDate: `${this.leftCurrentYear}-Q${this.leftCurrentQuarter}`,
            endDate: `${this.rightCurrentYear}-Q${this.rightCurrentQuarter}`
          }
          this.$emit('submit', emitObj)
          this.rangeStr = emitObj.startDate + '--' + emitObj.endDate
        } else {
          this.rangeStr = ''
        }
      }
    },
    computed: {
      leftMaxQuarter () {
        if (this.leftCurrentYear < this.rightCurrentYear) return Infinity
        else return Math.min(this.rightCurrentQuarter, this.rightMaxQuarter)
      },
      rightMinQuarter () {
        if (this.leftCurrentYear < this.rightCurrentYear) return -Infinity
        else return this.leftCurrentQuarter
      },
      rightMaxQuarter () {
        let date = new Date()
        let year = date.getFullYear()
        let month = date.getMonth()
        let quarter = month / 3 + 1
        if (this.rightCurrentYear < year) return Infinity
        else return quarter
      }
    },
    created () {
      if (this.rightMaxYear < this.leftMinYear) {
        throw new Error('设置的年份上限大于下限了?检查一下？')
      }
    },
    mounted () {
      let el = this.$refs.quarterSelector
      this.clickHandler = (e) => {
        if (!e.path.includes(el)) {
          this.closeHandler()
        }
      }
      window.addEventListener('click', this.clickHandler)
    },
    beforeDestroy () {
      window.removeEventListener('click', this.clickHandler)
    }
  }
</script>

<style scoped lang="less">
  .drop-enter, .drop-leave-to {
    opacity: 0;
    transform: scale(0.2);
  }
  .drop-enter-active, .drop-leave-active {
    transition: all 0.3s;
  }
  .quarter-selector {
    position: relative;
    display: inline-block;
    .quarter-input {
      display: inline-block;
      width: 100%;
      height: 100%;
      line-height: 1.5;
      padding: 4px 7px;
      font-size: 12px;
      border: 1px solid #dddee1;
      border-radius: 4px;
      color: #495060;
      background-color: #fff;
      position: relative;
      cursor: text;
      transition: border .2s ease-in-out,background .2s ease-in-out,box-shadow .2s ease-in-out;
      &.focus {
        border-color: #57a3f3;
        outline: 0;
        box-shadow: 0 0 0 2px rgba(45,140,240,.2);
      }
    }
    .dropdown {
      position: absolute;
      right: 0;
      top: 120%;
      height: 170px;
      width: 285px;
      transform-origin: center top 0;
      background-color: #fff;
      box-sizing: border-box;
      border-radius: 4px;
      box-shadow: 0 1px 6px rgba(0,0,0,.2);
      z-index: 900;
      .quarter-block {
        margin-right: 10px;
        margin-left: 10px;
      }
    }
  }
</style>
