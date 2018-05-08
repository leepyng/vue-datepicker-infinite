# vue-datepicker-infinite
easy datepicker of a vue 2.0 component

# install
	download file into the project

# how to use
	template:
		<transition  name="slide" >
			<DatePicker @select="calendar.select" :start="calendar.start" :beginDate="calendar.begin" :endDate="calendar.end" :monthLength="calendar.monthLength"  v-show="calendar.show" ></DatePicker>
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
	beginDate: begin of the select
	endDate:end of the select
