<template>

	<picker @change="bindTimeChange" @columnchange="columnchange" mode="multiSelector" :value="index" :range="array">
		<view class="uni-input" v-if="timeValue">{{timeValue}}
		</view>
		<view v-else>{{placeholder}}</view>
	</picker>
</template>
<script>
	export default {
		name: 'dy-Datetime-Picker',
		props: {
			placeholder: {
				type: String,
				default () {
					return '请选择'
				}
			},
			timeType: {
				type: String,
				default () {
					return 'anyTime'
				}
			},
			timeOptions: {
				type: [Array],
				default () {
					return []
				}
			},
			readonly: {
				type: Boolean,
				default () {
					return false
				}
			},

			// 弹出框默认选中的值
			defaultValue: {
				type: Number,
				default () {
					return 0
				}
			},

			disabled: {
				type: Boolean,
				default () {
					return false
				}
			},
			minDate: {
				type: String,
				default () {
					return '1900-01-01'
				}
			},
			timeIntervalMsec: {
				type: Number,
				default () {
					return 0
				}
			},
			maxDate: {
				type: String,
				default () {
					return '2050-12-31'
				}
			},
			minTime: {
				type: String,
				default () {
					return '00:00'
				}
			},
			maxTime: {
				type: String,
				default () {
					return '23:59'
				}
			},
			startColumn: {
				type: String,
				default () {
					return 'year'
				}
			},

			format: {
				type: Object,
				default () {
					return {}
				}
			},
			value: {
				default () {
					return ''
				}
			}
		},
		data() {
			return {
				index: [],
				array: [],
				hourArr: [],
				minsArr: [],
				secondArr: [],
				yearArr: [],
				monthArr: [],
				dateArr: [],
				yearIndex: 0, // 年份选中下标
				timeValue: this.moment(this.value)
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
				let value = `${y}-${m}-${d} ${hh}:${mm}`
				if (type === 'date') {
					value = `${y}-${m}-${d}`
				}
				if (type === 'time') {
					value = `${hh}:${mm}`
				}
				return value
			},
			bindTimeChange(e) {
				let timeValue = ''
				let indexArr = e.detail.value
				let year = parseInt(this.array[0][indexArr[0] || 0])
				let monthIndex = indexArr[1] || 0
				let month = parseInt(this.array[1][monthIndex])
				month = month < 10 ? `0${month}` : month
				let dateindex = indexArr[2] ? indexArr[2] : 0
				let date = parseInt(this.array[2][dateindex])
				date = date < 10 ? `0${date}` : date
				let time = ''
				if (this.timeOptions.length) {
					let timindex = indexArr[3]>0 ? indexArr[3] : 0
					console.log(timindex,'timindex',indexArr)
					time = this.array[3][timindex]
					console.log(this.array[3],this.array[3][timindex],'this.array[3][timindex]')
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
				if (this.timeType === 'anyTime' && !this.timeOptions.length) {
					// 初始化时间
					this.timeInit()
				} else {
					// 初始化时分为指定选项
					this.intervalTimeInit()
				}

				let arr = []
				this.valueEchoed()
			},
			valueEchoed() {
				let value = this.moment(this.value||new Date().getTime())
				if (value) {
					value = value.split(' ')
					let dayArr = value[0].split('-')
					dayArr.forEach((item, index) => {
						let ids = this.array[index].findIndex(
							items => parseInt(items) === parseInt(item)
						)
						if (ids < 0) {
							ids = 0
						}
						if (index === 0) {
							this.array[1] = this.monthArr[ids]
							this.array[2] = this.getDateArr(
								parseInt(this.array[0][0]),
								parseInt(dayArr[1])
							)
						}
						this.index[index] = ids
					})
				}
				// 时分回选
				if (value && !this.timeOptions.length) {
					let timeArr = value[1].split(':')
					timeArr.forEach((item, i) => {
						let index = i + 3
						let ids = this.array[index].findIndex(
							items => parseInt(items) === parseInt(item)
						)
						if (ids < 0) {
							ids = 0
						}
						if (index === 0) {
							this.array[4] = this.minsArr[ids]
						}
						this.index[index] = ids
					})
				}
				// 回选时间选项为指定选项
				if (this.timeOptions.length) {
					let index = 0
					index = this.array[3].findIndex(
						items => items === value[1]
					)
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
				// console.log(y, m, yearEnd, yearStar, monthStar, monthEnd)
				// 2020 1 2050 2001 1 12
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
			intervalTimeInit() {

				this.array[3] = this.timeOptions
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
					this.array[1] = this.monthArr[this.yearIndex]
					this.array[2] = this.getDateArr(
						parseInt(this.yearArr[this.yearIndex]),
						parseInt(this.monthArr[this.yearIndex][0])
					)

				}

				if (column === 1) {
					this.array[2] = this.getDateArr(
						parseInt(this.yearArr[this.yearIndex]),
						parseInt(this.monthArr[this.yearIndex][minIndex])
					)
				}

				if (column === 3 && !this.timeOptions.length) {
					this.array[4] = this.minsArr[minIndex]
				}
			}
		}
	}
</script>
