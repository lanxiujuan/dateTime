
# dateTime 日期时间选择组件  基于uniapp 
# 详细demo请到GitHub下载 bug补丁有更新
![image](https://github.com/lanxiujuan/dateTime/blob/master/images/img1.jpg)
### 1 :value="childValue" 传入时间  回显

### 2 :placeholder="placeholder"指定提示语
### 3 :timeOptions="timeOptions"  时间选项为指定选项 类型为数组 例如固定为上午9:00 晚上22:00
### 4 :minDate="minDate" 最小可选择年月日 如1900-01-01
![image](https://github.com/lanxiujuan/dateTime/blob/master/images/img2.jpg)	
### 5 :maxDate="maxDate" 最大可选择年月日 如 2050-12-31
![image](https://github.com/lanxiujuan/dateTime/blob/master/images/img3.jpg)	
### 6 :minTime="minTime" 传入最小可选择时间 如9:00 默认为00:00
![image](https://github.com/lanxiujuan/dateTime/blob/master/images/img4.jpg)	
### 7 :maxTime="maxTime"  传入最大可选择时间 如20:00 默认为：23:59
![image](https://github.com/lanxiujuan/dateTime/blob/master/images/img5.jpg)			
				
- 2019-07-23 更新缺陷修复 
1、修复小程序端最大最小时间报错问题 传入value值若不为时间戳时 必须为年/月/日 时：分：秒

