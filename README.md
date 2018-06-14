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
					start:{year:'2018',month:'07',day:'01'},
					last:{year:'2018',month:'08',day:'01'},
					begin:{year:'2018',month:'07',day:'02'},
					end:{year:'2018',month:'07',day:'03'},
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
	start:the calendar's begining date
	last:the calendar's last date
	beginDate: begin of the select
	endDate:end of the select
