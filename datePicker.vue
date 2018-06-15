<template>

<div class="date-picker-box">
  <div class="dpb-title">
    <span @click="cancel">取消</span>
    选择日期
  </div>
  <div class="dpb-week">
    <table>
      <thead>
        <tr>
          <th>日</th>
          <th>一</th>
          <th>二</th>
          <th>三</th>
          <th>四</th>
          <th>五</th>
          <th>六</th>
        </tr>
      </thead>
    </table>
  </div>
  <div class="dpb-week-days">
    <div class="dpbwd-table" v-for="(months, i) in data" :key="i">
      <div class="dpbwd-table-month">
        {{ month + i > 12 ? `${year + 1}年${month + i - 12}月` : `${year}年${month + i}月` }}
      </div>
      <div class="dpbwd-table-items">
        <table class="dpbwd-table-items-table">
          <tbody >
            <tr v-for="(weeks, i) in months" :key="i">
              <td :class="isActive(date) ? 'active' : (isInRange(date) ? 'range' : isDisable(date) ? 'disable' : '')"
									v-for="(date, i) in weeks" @click="select(date)"
									:key="i">
                <div class="page-table-item">
                  <em class="page-table-note">
                    {{isBegin(date)?beginText:(isEnd(date)?endText:'')}}
                  </em>
                  <strong class="page-table-text">
										{{date.year === new Date().getFullYear() && date.month === new Date().getMonth() + 1 && date.day === new Date().getDate() ? '今天' : date.day}}
									</strong>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</div>
	
</template>
<script>
	class myDate {
		constructor({year, month, day} = {}) {
			this.year = year
			this.month = month
			this.day = day
		}
		toTimestamp() {
			return new Date(this.year, this.month, this.day).getTime()
		}
	}
	export default {
		name: 'datePicker',
		data() {
			return {
				data: [],
				year: new Date().getFullYear(),
				month: new Date().getMonth() + 1,
				begin: '',
				end: '',
				lastDate: '',
			}
		},
		props: {	
			monthLength: {
				type:Number,
				default:6
			},
			beginText: {
				type:String,
				default:'入住'
			},
			endText: {
				type:String,
				default:'离店'
			},
			start: {
				type:Object,
				default() {
					return new myDate({year: new Date().getFullYear(), month: new Date().getMonth() + 1, day: new Date().getDate()})
				}
			},
			last: {
				type:Object,
				default() {
					return {}
				}
			},
			beginDate: {
				type:Object,
				default() {
					return {}
				}
			},
			endDate: {
				type:Object,
				default() {
					return {}
				}
			},
		},
		watch: {
			beginDate(val) {
				this.begin=val
			},
			endDate(val) {
				this.end=val
			},
			last(val) {
				this.lastDate=val
			}
		},
		methods: {
			// 是否为不可选择日期
			isDisable(date) {
				const { start: begin = {}, lastDate: end} = this
				const s1 = new myDate(begin).toTimestamp()
				const s2 = new myDate(end).toTimestamp()
				const s = new myDate(date).toTimestamp()
        return s1 > s || s2 < s
			},
			// 是否选中
			isActive(date) {
				const { begin: start, end } = this
				const s1 = new myDate(start).toTimestamp()
				const s2 = new myDate(end).toTimestamp()
				const s = new myDate(date).toTimestamp()
				return s === s1 || s === s2
			},
			//是否为开始日期
			isBegin(date) {
				const { begin: start } = this
				const s1 = new myDate(start).toTimestamp()
				const s = new myDate(date).toTimestamp()
				return s === s1
			},
			//是否为结束日期
			isEnd(date) {
				const { end } = this
				const s2 = new myDate(end).toTimestamp()
				const s = new myDate(date).toTimestamp()
				return s === s2
			},
			//选中日期
			select(date) {
				if(this.isDisable(date)) return
				if(!this.begin || (this.begin && this.end)) {
					this.begin = date
					this.end = ''
				}else if(this.begin && !this.end) {
					if (this.$el.hasAttribute('reverse')) {
						//反选日期
						if(this.isOpposite(date, this.begin)) {
							this.end = this.begin
							this.begin = date
						}else{
							this.end = date
						}
					} else {
						const { year, month, day } = date
						const first = new Date(year, month, day)
						const { year: year2, month: month2, day: day2 } = this.begin
						const secend = new Date(year2, month2, day2)
						if (first < secend) {
							this.begin = date
							this.end = ''
						}
						else this.end = date
					}
					//选中日期	
					this.$emit('select', this.begin, this.end)
				}
			},
			cancel() {
				this.begin=this.beginDate
				this.end=this.endDate
				
				this.$emit('select')
			},
			//判断是否反转日期
			isOpposite(end,start) {
				const s1 = new myDate(start).toTimestamp()
				const s2 = new myDate(end).toTimestamp()
        return s2 < s1
			},
			//查看是否是已经选中的日期区间中
			isInRange(date) {
				if(!date) return
				const { begin: start, end } = this
				const s1 = new myDate(start).toTimestamp()
				const s2 = new myDate(end).toTimestamp()
				const s = new myDate(date).toTimestamp()
				if((s<s2)&&(s>s1))return true
			},
			//获取天数差
			getDaysSize(s1, s2) {
				var s1 = new Date(s1)
				var s2 = new Date(s2)
				var days = s2.getTime() - s1.getTime()
				var time = parseInt(days / (1000 * 60 * 60 * 24), 10)
				return time
			},
			//将数据格式化表格日期格式
			monthDate(year, month) {
				//获取当前月份最后一天的日期
				var lastDay = new Date(year, month, 0).getDate()
				//计算当前月份第一天是星期几
				// var weekday=this.getWeekday(year+'-'+month+'-01')
				const weekday = new Date(year, month - 1, 1).getDay()
				//定义存放当前月份的数组
				var data = []
				//定义日期表格数组
				var result = []
				//计算出当前月份每一天到数组中
				for(let day = 1; day <= lastDay; day++) {
					data.push(new myDate({ day, month, year }))
				}
				//补全日期前几天
				for(var i=0;i<weekday;i++) {
					data.unshift('')
				}
				//切成6行
				for(var i=0,len=data.length;i<len;i+=7) {
						result.push(data.slice(i,i+7))
				}
				//补全日期后几天
				var length=result[(result.length-1)].length
				if(length<7) {
						for(var i=0;i<(7-length);i++) {
								result[(result.length-1)].push('')
						}
				}
				return result
			},
			//初始化表格数据
			tableDate() {
				const monthLength = this.monthLength
				const data = []
				const year = this.year //获取完整的年份(4位)
				const month = this.month //获取当前月份
				for(let i = 0; i < monthLength; i++) {
					const y = month + i > 12 ? year + 1 : year
					const m = month + i > 12 ? month + i - 12 : month + i
					const re = this.monthDate(y, m)
					data.push(re)
				}
				this.data = data
			}
		},
		mounted() {
			this.end=this.endDate
			this.begin=this.beginDate
			this.lastDate=this.last
			this.tableDate()
		}
	}
</script>
<style scoped>
.date-picker-box {
  width: 100%;
  height: 120%;
  position: fixed;
  top: 0;
  left: 0;
  background-color: #fff;
  z-index: 24;
}
.dpb-title {
  height: 50px;
  text-align: center;
  line-height: 50px;
  background: #fff;
  font-size: 15px;
  position: relative;
}
.dpb-title > span {
  position: absolute;
  right: 12px;
  line-height: 50px;
  font-size: 13px;
}
.dpb-week > table {
  background: #fff;
  font-size: 13px;
  width: 100vw;
  border-collapse: collapse;
  table-layout: fixed;
}
.dpb-week > table > thead > tr > th {
  height: 30px;
  text-align: center;
  font-weight: 400;
  width: 14.28571%;
  vertical-align: middle;
}
.dpb-week-days {
  position: relative;
  width: 100%;
  height: 75%;
  overflow: auto;
  padding-bottom: 120px;
}
.dpbwd-table-month {
  height: 30px;
  line-height: 30px;
  text-align: center;
  font-size: 15px;
  font-weight: 700;
  background: hsla(210, 8%, 95%, 0.9);
  top: 0;
  z-index: 1;
  position: sticky;
}
.page-table-item {
  box-sizing: border-box;
  position: relative;
  /*display: -webkit-box;*/
  -webkit-box-orient: vertical;
  -webkit-box-align: center;
  -webkit-box-pack: end;
}
.page-table-text {
  font-size: 16px;
  height: 15px;
  line-height: 15px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}
.page-table-note {
  color: #ff5000;
  font-size: 12px;
  height: 15px;
  line-height: 15px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  font-style: inherit;
}
.page-table-note,
.page-table-text {
  display: block;
  width: 100%;
  text-align: center;
  font-weight: 400;
}
.dpbwd-table-items-table > tbody > tr > td.disable .page-table-text,
.dpbwd-table-items-table > tbody > tr > td.disable .page-table-note {
  color: #ccc !important;
}
.dpbwd-table-items-table > tbody > tr > td.range {
  background: #fff6d6;
}
.dpbwd-table-items-table > tbody > tr > td.active {
  color: white;
  background: #ffc900;
}
/* .dpbwd-table-items-table > tbody > tr > td.disable {
  background: #fafafa !important;
} */
.dpbwd-table-items-table > tbody > tr > td {
  height: 60px;
  text-align: center;
  width: 14.28571%;
  position: relative;
}
.dpbwd-table-items-table {
  width: 100%;
  border-collapse: collapse;
  table-layout: fixed;
}
</style>