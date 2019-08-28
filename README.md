# vue-datepicker-infinite
easy datepicker of a vue 2.0 component
<p align="center">


<img src="https://github.com/leepyng/vue-datepicker-infinite/blob/master/git/QQ20180508-153441.gif" alt="Coverage Status">


</p>

# install
	
	npm install vue2-datepicker-infinite --save
	

# how to use
	template:
		<transition  name="slide" >
			<DatePicker @select="calendar.select" :start="calendar.start"  :last="calendar.last" :beginDate="calendar.begin" :endDate="calendar.end" :monthLength="calendar.monthLength"  v-show="calendar.show" ></DatePicker>
		</transition>
	
	script:
		//import
		import DatePicker from './../common/datePicker/datePicker.vue'
		//define
		components:{
			DatePicker
		},
		//set default data
		data(){
			return{
				calendar:{
					monthLength:6,
					show:false,
					start:'2019-08-01',
					last:'2019-08-03',
					begin:'2019-08-01',
					end:'2019-12-01',
					select:(begin,end)=>{
						this.calendar.show=false;
						this.calendar.begin=begin;
						this.calendar.end=end;
					}
				},
			}
		}
		
	
# props description
	monthLength:max month lenght
	beginText:begin text name
	endText:end text name
	isReverseAllow:allow to reverse date when select,
	start:the calendar's begining date
	last:the calendar's last date
	beginDate: begin of the select
	endDate:end of the select
