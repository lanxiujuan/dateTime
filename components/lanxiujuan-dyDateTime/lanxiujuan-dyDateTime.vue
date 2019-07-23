<template>
  <picker @change="bindTimeChange"
    @columnchange="columnchange"
    mode="multiSelector"
    :value="index"
    :disabled="disabled"
    :range="array">
    <view class="uni-input"
      v-if="timeValue">{{timeValue}} 
    </view>
    <view v-else
      class="placeholder">{{placeholder}} </view>
  </picker>
</template>
<script>
export default {
  name: 'dy-Datetime-Picker',
  props: {
    disabled: {
      type: Boolean,
      default: () => false
    },
    placeholder: {
      type: String,
      default() {
        return '请选择'
      }
    },
 
    timeType: {
      type: String,
      default() {
        return 'anyTime'
      }
    },
    timeOptions: {
      type: Array,
      default() {
        return []
      }
    },


    disabled: {
      type: Boolean,
      default() {
        return false
      }
    },
    minDate: {
      type: Number,
      default() {
        return new Date('2001/01/05').getTime()
      }
    },
    timeIntervalMsec: {
      type: Number,
      default() {
        return 0
      }
    },
    maxDate: {
      type: Number,
      default() {
        return new Date('2050/12/31').getTime()
      }
    },
    minTime: {
      type: String,
      default() {
        return '00:00'
      }
    },
    maxTime: {
      type: String,
      default() {
        return '23:59'
      }
    },
    startColumn: {
      type: String,
      default() {
        return 'year'
      }
    },
    columnCount: {
      type: Number,
      default: 3
    },
    format: {
      type: Object,
      default() {
        return {}
      }
    },
    value: {
      default() {
        return ''
      }
    }
  },
  data() {
    return {
      index: [0,0],
      array: [],
      hourArr: [],
      minsArr: [],
      secondArr: [],
      yearArr: [],
      monthArr: [],
      dateArr: [],
      yearIndex: 0, // 年份选中下标
      timeValue: this.value&&this.moment(this.value)
    }
  },
  mounted() {
    this.init()
  },
  methods: {
    moment(strTime, type) {
      if (!strTime) {
        return
      }
      let time = new Date(strTime)
      let y = time.getFullYear()
      let m = time.getMonth() + 1
      m = m < 10 ? `0${m}` : m
      let d = time.getDate()
      d = d < 10 ? `0${d}` : d
      let hh = time.getHours()
      hh = hh < 10 ? `0${hh}` : hh
      let mm = time.getMinutes()
      mm = mm < 10 ? `0${mm}` : mm
      let ss = time.getSeconds()
      ss = ss < 10 ? `0${ss}` : ss
      let value = `${y}-${m}-${d} ${hh}:${mm}:${ss}`
      if (type === 'date') {
        value = `${y}-${m}-${d}`
      }
      if (type === 'time') {
        value = `${hh}:${mm}:${ss}`
      }
      if (type === 'datetime') {
        value = `${y}/${m}/${d} ${hh}:${mm}:${ss}`
      }
      return value
    },
    bindTimeChange(e) {
      let timeValue = ''
      let indexArr = e.detail.value
      let year = parseInt(this.array[0][indexArr[0]])
      let month = parseInt(this.array[1][indexArr[1]])
      month = month < 10 ? `0${month}` : month
      let dateindex = indexArr[2] ? indexArr[2] : 0
      let date = parseInt(this.array[2][dateindex])
      date = date < 10 ? `0${date}` : date
      let time = ''
      
      if (this.timeOptions.length) {
        let timindex = indexArr[3] ? indexArr[3] : 0
        time = this.array[3][timindex]
      } else {
        let hourindex = indexArr[3] ? indexArr[3] : 0
        let hour = parseInt(this.array[3][hourindex])
        hour = hour < 10 ? `0${hour}` : hour
        let minsindex = indexArr[4] ? indexArr[4] : 0
        let minuts = parseInt(this.array[4][minsindex])
        minuts = minuts < 10 ? `0${minuts}` : minuts
        time = `${hour}:${minuts}`
      }
      this.timeValue = `${year}-${month}-${date} ${time}`
      this.$emit('getData', this.timeValue)
    },
    indexInit() {},

    init() {
      this.dateInit() // 初始化年月日
	  console.log(this.timeOptions,'this.timeOptions')
      if (this.timeType === 'anyTime' && !this.timeOptions.length) {
        this.timeInit()
      } else {
      this.array[3] =  this.timeOptions
      }

      let arr = []
      this.valueEchoed()
    },
    valueEchoed() {
		console.log("value" +":"+value);
      let initTime = this.value || new Date().getTime()
      let value = this.moment(initTime)
      if (value) {
        value = value.split(' ')
        let dayArr = value[0].split('-')
        dayArr &&
          dayArr.forEach((item, index) => {
            let ids = 0

            ids = this.array[index].findIndex(
              items => parseInt(items) === parseInt(dayArr[index])
            )
            console.log(ids, 'index', index)
            if (index === 0) {
              this.yearIndex = ids
              this.array[1] = this.monthArr[ids]
              this.array[2] = this.getDateArr(
                parseInt(this.array[0][ids]),
                parseInt(dayArr[1])
              )
            }
            let indexArr = this.array[index]
            ids =
              indexArr &&
              indexArr.findIndex(items => parseInt(items) === parseInt(item))
            if (ids < 0) {
              ids = 0
            }
            this.index[index] = ids
          })
      }

      if (value && !this.timeOptions.length) {
		
        let timeArr = value[1].split(':')
		 
        timeArr.forEach((item, i) => {
          let index = i + 3
		  // 不匹配秒数
		  if(i>1){
			  return false
		  }
          let ids =  this.array[index].findIndex(
            items => parseInt(items) === parseInt(item)
          )
          if (ids < 0) {
            ids = 0
          }
          if (i === 0) {
            this.array[4] = this.minsArr[ids]
          }
          // this.array[4] = this.minsArr[minIndex]
          this.index[index] = ids
        })
      }

      if (this.timevalue && this.timeOptions.length) {
        let index =
          this.array[3].findIndex(item => item === this.timevalue) || 0
        this.index[3] = index
      }
    },

    getIndexArr(arr) {
      // 根据数组格式时间获取到对应当前可选时间对应的index数组
      let index = []
      let minArr = []
      let hourindex = 0
      let MMindex = 0

      for (let i = 0; i < this.array[0].length; i++) {
        let item = this.array[0][i]
        if (parseInt(item) === parseInt(arr[0])) {
          minArr = this.minsArr[i]
          hourindex = i
          break
        }
      }
      index.push(hourindex)

      for (let i = 0; i < minArr.length; i++) {
        let item = minArr[i]
        if (parseInt(item) === parseInt(arr[0])) {
          minArr = this.minsArr[i]
          MMindex = i
          break
        }
      }
      index.push(MMindex)
      return index
    },
    dateInit() {
      let minDate = this.moment(this.minDate, 'date')

      if (!minDate) {
        return false
      }
      minDate = minDate && minDate.split('-')
      let maxDate = this.moment(this.maxDate, 'date')
      maxDate = maxDate && maxDate.split('-')
      let yearStar = minDate[0] && minDate[0] ? parseInt(minDate[0]) : 1900
      let yearEnd = maxDate[0] ? parseInt(maxDate[0]) : 2050
      let monthStar = minDate[1] ? parseInt(minDate[1]) : 1
      let monthEnd = maxDate[1] ? parseInt(maxDate[1]) : 12
      let dateStar = minDate[2] ? parseInt(minDate[2]) : 0
      let dateEnd = maxDate[2] ? parseInt(maxDate[2]) : 31
      for (let y = yearStar; y <= yearEnd; y++) {
        let ytext = y < 10 ? `0${y}` : y
        this.yearArr.push(`${ytext}年`)
        let mGroup = []
        let dateGroup = []
        if (y === yearStar && y !== yearEnd) {
          for (let m = monthStar; m <= 12; m++) {
            let mtext = m < 10 ? `0${m}` : m
            mGroup.push(`${mtext}月`)
          }
        }
        if (y === yearEnd && y !== yearStar) {
          for (let m = 1; m <= monthEnd; m++) {
            let mtext = m < 10 ? `0${m}` : m
            mGroup.push(`${mtext}月`)
          }
        }
        if (y === yearStar && y === yearEnd) {
          for (let m = monthStar; m <= monthEnd; m++) {
            let mtext = m < 10 ? `0${m}` : m
            mGroup.push(`${mtext}月`)
          }
        }

        if (y !== yearStar && y !== yearEnd) {
          for (let m = 1; m <= 12; m++) {
            let mtext = m < 10 ? `0${m}` : m
            mGroup.push(`${mtext}月`)
          }
        }
        this.monthArr.push(mGroup)
      }

      this.array[0] = this.yearArr
      this.array[1] = this.monthArr[0]
      this.array[2] = this.getDateArr(
        parseInt(this.yearArr[0]),
        parseInt(this.monthArr[0])
      )
      // this.index
    },

    getDateArr(y, m) {
      let minDate = this.moment(this.minDate, 'date').split('-')
      let maxDate = this.moment(this.maxDate, 'date').split('-')
      let yearStar = minDate && minDate[0] ? parseInt(minDate[0]) : 1900
      let yearEnd = maxDate && maxDate[0] ? parseInt(maxDate[0]) : 2050
      let monthStar = minDate[1] ? parseInt(minDate[1]) : 1
      let monthEnd = maxDate[1] ? parseInt(maxDate[1]) : 12
      let datearr = []
      let maxnum = 30
      let date31 = [1, 3, 5, 7, 8, 10, 12]
      if (date31.includes(m)) {
        maxnum = 31
      }
      // 判断是平年还是闰年的2月份
      if (m === 2) {
        if (y % 400 == 0 || (y % 4 == 0 && y % 100 != 0)) {
          maxnum = 29
        } else {
          maxnum = 28
        }
      }
      let dateStar = minDate[2] ? parseInt(minDate[2]) : 1
      let dateEnd = maxDate[2] ? parseInt(maxDate[2]) : maxnum
      if (
        (y !== yearEnd && y !== yearStar) ||
        (y === yearStar && m !== monthStar) ||
        (y === yearEnd && m !== monthEnd) ||
        (yearStar === yearEnd && m !== monthStar && m !== monthEnd)
      ) {
        for (let d = 1; d <= maxnum; d++) {
          let dtext = d < 10 ? `0${d}` : d
          datearr.push(`${dtext}日`)
        }
      }
      if (y === yearStar && m === monthStar && m !== monthEnd) {
        for (let d = dateStar; d <= maxnum; d++) {
          let dtext = d < 10 ? `0${d}` : d
          datearr.push(`${dtext}日`)
        }
      }
      if (y === yearEnd && m === monthEnd && m !== monthStar) {
        for (let d = 1; d <= dateEnd; d++) {
          let dtext = d < 10 ? `0${d}` : d
          datearr.push(`${dtext}日`)
        }
      }
      // 当开始时间和结束时间的年月都相同时 判断最小日期和最大日期
      if (
        y === yearEnd &&
        m === monthEnd &&
        y === yearStar &&
        m === monthStar
      ) {
        for (let d = dateStar; d <= dateEnd; d++) {
          let dtext = d < 10 ? `0${d}` : d
          datearr.push(`${dtext}日`)
        }
      }
      return datearr
    },
 

    timeInit() {
      let start = this.minTime.split(':')
      let end = this.maxTime.split(':')
      let hourStar = start[0] ? parseInt(start[0]) : 0
      let minsStar = start[1] ? parseInt(start[1]) : 0
      let hourEnd = end[0] ? parseInt(end[0]) : 23
      let minsEnd = end[1] ? parseInt(end[1]) : 59
      let hourArr = []
      let minsArr = []
      let mintobj = {}
      for (let i = hourStar; i <= hourEnd; i++) {
        let itex = i < 10 ? `0${i}` : i
        hourArr.push(`${itex}时`)
        let minsStarNum = i === hourStar ? minsStar : 0
        let minsEndNum = i === hourEnd ? minsEnd : 59
        let hoursminsArr = []
        for (let j = minsStarNum; j <= minsEndNum; j++) {
          let mintex = j < 10 ? `0${j}` : j
          hoursminsArr.push(`${mintex}分`)
        }
        mintobj[itex] = hoursminsArr
        minsArr.push(hoursminsArr)
        this.hourArr = hourArr
        this.minsArr = minsArr
        this.array[3] = hourArr
        this.array[4] = minsArr[0]
      }
    },
    // 当时发生改变时 加载对应的分钟数组
    columnchange(e) {
      const minIndex = e.detail.value
      const column = e.detail.column
      if (column === 0) {
        this.yearIndex = e.detail.value
        let monthArr = this.monthArr[this.yearIndex]
        this.$set(this.array, 1, monthArr)
        let dateArr = this.getDateArr(
          parseInt(this.yearArr[this.yearIndex]),
          parseInt(this.monthArr[this.yearIndex][0])
        )
        this.$set(this.array, 2, dateArr)
      }
      if (column === 1) {
        let dateArr = this.getDateArr(
          parseInt(this.yearArr[this.yearIndex]),
          parseInt(this.monthArr[this.yearIndex][minIndex])
        )
        this.$set(this.array, 2, dateArr)
      }
      if (column === 3 && !this.timeOptions.length) {
        this.$set(this.array, 4, this.minsArr[minIndex])
      }
    }
  }
}
</script>
<style>
.placeholder {
  color: #b5b8c2;
  font-size: 30rpx;
}

</style>
