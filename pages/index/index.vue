<template>
	<view class="content">
			<view class="item">
			<div class="lable">限定最小选择日期：</div>
			<view class="time">
				<dy-Datetime-Picker  :placeholder="placeholder" :maxDate="maxDate"
				 :minDate="minDate" @getData="getData" :timeIntervalMsec="timeIntervalMsec"
				 :type="timeType"></dy-Datetime-Picker>
			</view>
		</view>
		<!--  -->
		<view class="item">
			<div class="lable">限定最大选择日期：</div>
			<view class="time">
				<dy-Datetime-Picker :placeholder="placeholder" :maxDate="maxDate" @getData="getData"></dy-Datetime-Picker>
			</view>
		</view>

		<!--  -->
	<view class="item">
			<div class="lable">指定开始结束时间：</div>
			<view class="time">
				<dy-Datetime-Picker :placeholder="placeholder" :maxDate="maxDate"
				 :minDate="minDate" :minTime="minTime" :maxTime="maxTime" @getData="getData" 
				 :type="timeType"></dy-Datetime-Picker>
			</view>
		</view>
		
		<!--  -->
		<!-- 时间为固定选项时  传入固定选项数组 timeOptions -->
		<view class="item">
			<div class="lable">指定时间选项：</div>
			<view class="time">
				<dy-Datetime-Picker :value="childValue" :placeholder="placeholder" :timeOptions="timeOptions" :maxDate="maxDate"
				 :minDate="minDate" :minTime="minTime" :maxTime="maxTime" @getData="getData" :timeIntervalMsec="timeIntervalMsec"
				 :type="timeType"></dy-Datetime-Picker>
			</view>
		</view>
		<view>

		</view>
	</view>
</template>

<script>
	import dyDatetimePicker from '../../components/lanxiujuan-dyDateTime/lanxiujuan-dyDateTime.vue'
	export default {
		components: {
			dyDatetimePicker
		},
		data() {
			return {

				childValue: '2013-10-12 03:30',
				placeholder: '请选择开始时间',
				// 最小日期
				minDate: '2011-10-12',
				// 最大日期
				maxDate: '2019-08-12',
				defaultMinDate: new Date('2001-1-3').getTime(),
				defaultMaxDate: new Date('2050-12-31').getTime(),
				// 最小时间
				minTime: '10:00',
				// 最大时间
				maxTime: '23:59',
				// 默认时间
				defaultValue: 0,
				// 用于判断是否显示默认的时间， 只有当日期时间都有固定值才为true
				isShowDefaultValue: false,
				// 时间固定选项
				timeOptions: [],
				// 时间类型
				timeType: 'anyTime',
				// 间隔的毫秒
				timeIntervalMsec: 20,
				fixedTimeAarr: [],
				datetimeKey: new Date().getTime()
			}
		},
		onLoad() {

			this.timeOptions = [
				'9:00-12:00',
				'13:00-18:00',
				'18:00-23:00',
			]
		},
		methods: {
			getData(time) {
				console.log(time, '回调时间')
			}
		}
	}
</script>

<style>
	* {
		padding: 0px;
		margin: 0 auto;
		box-sizing: border-box;
	}


	.content {
		text-align: center;

		margin-top: 20px;

	}

	.item {
		box-sizing: border-box;
		width: 100%;
		height: 40px;
		line-height: 40px;
		background-color: #ffffff;
		position: relative;
		padding: 0px 10px 0px 100px;
		margin-bottom: 10px;
		text-align: right;
	}

	.item .lable {
		position: absolute;
		left: 10px;
		top: 0px;
		color: #333333;
	}
</style>
