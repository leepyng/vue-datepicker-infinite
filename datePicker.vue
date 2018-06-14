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
				<div class="dpbwd-table" v-for="(item,index) in data">
					<div class="dpbwd-table-month">
						{{(month+index)>12?((year+1)+'-0'+(month+index-12)):(year+'-'+(month+index))}}
					</div>
					<div class="dpbwd-table-items">
						<table class="dpbwd-table-items-table">
							<tbody >
								<tr v-for="week in item">
									<td :class="
												isActive(day)
												?'active':
												(isInRange(day)
													?'range':
													isDisable(day)?'disable':'')
												" v-for="day in week" @click="select(day)">
										<div class="page-table-item">
											<em class="page-table-note">
												{{
													isBegin(day)?beginText:(isEnd(day)?endText:'')
												}}
											</em>
											<strong class="page-table-text">
												<!-- {{day}} -->
											{{day.day}}</strong>
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
	export default{
		name:'datePicker',
		data(){
			return{
				data:[],
				year:(new Date()).getFullYear(),
				month:(new Date()).getMonth()+1,
				begin:'',
				end:'',
				lastDate:'',

			}	
		},
		props:{	
			monthLength:{
				type:Number,
				default:6
			},
			beginText:{
				type:String,
				default:'入住'
			},
			endText:{
				type:String,
				default:'离店'
			},

			start:{
				type:Object,
				default:function(){
					return {}
				}
			},
			last:{
				type:Object,
				default:function(){
					return {}
				}
			},
			beginDate:{
				type:Object,
				default:function(){
					return {}
				}
			},
			endDate:{
				type:Object,
				default:function(){
					return {}
				}
			},
			

		},
		watch:{
			beginDate(val){
				this.begin=val

			},
			endDate(val){
				this.end=val

			},
			last(val){
				this.lastDate=val;

			}
		},
		methods:{
			//是否为不可选择日期
			isDisable(day){
				var start=this.start;
				var lastDate=this.lastDate;
				
				
				var s1 = (new Date(start.year+'-'+start.month+'-'+start.day)).getTime();
				var s2 = (new Date(lastDate.year+'-'+lastDate.month+'-'+lastDate.day)).getTime();
            	var s = (new Date(day.year+'-'+day.month+'-'+day.day)).getTime();
            
            	return (s1>s||s2<s)?true:false;

			},
			//是否选中
			isActive(day){
				var begin=this.begin;
				var end=this.end;
				var s1 = (new Date(begin.year+'-'+begin.month+'-'+begin.day)).getTime();
				var s2 = (new Date(end.year+'-'+end.month+'-'+end.day)).getTime();
            	var s = (new Date(day.year+'-'+day.month+'-'+day.day)).getTime();
            	return (s==s1||s==s2)?true:false
				
			},
			//是否为开始日期
			isBegin(day){
				var begin=this.begin;
				var end=this.end;
				var s1 = (new Date(begin.year+'-'+begin.month+'-'+begin.day)).getTime();
            	var s = (new Date(day.year+'-'+day.month+'-'+day.day)).getTime();
            	return s==s1?true:false

			},
			//是否为结束日期
			isEnd(day){
				var begin=this.begin;
				var end=this.end;
				var s1 = (new Date(end.year+'-'+end.month+'-'+end.day)).getTime();
            	var s = (new Date(day.year+'-'+day.month+'-'+day.day)).getTime();
            	return s==s1?true:false

			},
			//选中日期
			select(day){
				
				if(this.isDisable(day))return;

				if(!this.begin||(this.begin&&this.end)){
					this.begin=day;
					this.end='';
				}else if(this.begin&&!this.end){
					//反选日期
					if(this.isOpposite(day,this.begin)){
						this.end=this.begin;
						this.begin=day;
						
					}else{
						this.end=day;
					}
					//选中日期	
					this.$emit('select',this.begin,this.end)
				}

			},
			cancel(){
				this.begin=this.beginDate;
				this.end=this.endDate;
				
				this.$emit('select')
			},
			//判断是否反转日期
			isOpposite(end,begin){
				var s1 = (new Date(begin.year+'-'+begin.month+'-'+begin.day)).getTime();
            	var s2 = (new Date(end.year+'-'+end.month+'-'+end.day)).getTime();
            	
            	return s2<s1?true:false;
			},
			//查看是否是已经选中的日期区间中
			isInRange(day){
				if(!day)return;
				var begin=this.begin;
				var end=this.end;
				var s1 = (new Date(begin.year+'-'+begin.month+'-'+begin.day)).getTime();
            	var s2 = (new Date(end.year+'-'+end.month+'-'+end.day)).getTime();
            	var s = (new Date(day.year+'-'+day.month+'-'+day.day)).getTime();
            	if((s<s2)&&(s>s1))return true;

			},
			//获取天数差
			getDaysSize(s1, s2){
				var s1 = new Date(s1);
	            var s2 = new Date(s2);

	            var days = s2.getTime() - s1.getTime();
	            var time = parseInt(days / (1000 * 60 * 60 * 24));
	            return time;
			},
			//获取周几
			getWeekday(date){
	            var nowDate=new Date();
	            var days=this.getDaysSize(nowDate,date);
	            var mydate=new Date(date); 
	            var myday=mydate.getDay()//注:0-6对应为星期日到星期六 
	            return myday;
			},

			//将数据格式化表格日期格式
	        monthDate(year,month){
	            
	            //或取当前月份最后一天的日期
	            var day = new Date(year,month,0); 
	            var lastDay=day.getDate();
	            //计算当前月份第一天是星期几
	            var weekday=this.getWeekday(year+'-'+month+'-01');
	            //定义存放当前月份的数组
	            var data=[];
	            //定义日期表格数组
	            var result = [];
	            //计算出当前月份每一天到数组中
	            for(var day=1;day<=lastDay;day++){
	            	var day=day<10?'0'+day:day;
	            
					data.push({day,month,year });
	            }

	            //补全日期前几天
	            for(var i=0;i<weekday;i++){
	                 data.unshift('');
	            }
	            //切成6行
	            for(var i=0,len=data.length;i<len;i+=7){
	               result.push(data.slice(i,i+7));
	            }
	            //补全日期后几天
	            var length=result[(result.length-1)].length;
	            if(length<7){
	                for(var i=0;i<(7-length);i++){
	                    result[(result.length-1)].push('');
	                }
	            }
	            
	            return result;
	        },
	        //初始化表格数据
	        tableDate(){
	        	var monthLength=this.monthLength;
	        	var data=[];
				// var date = new Date();
				var year=this.year; //获取完整的年份(4位)
				var month=this.month; //获取当前月份
				// console.log(year,month,monthLength)

	        	for(var i=0;i<monthLength;i++){
	        		var y=(month+i)>12?year+1:year;
	        		var m=(month+i)>12?(month+i-12):(month+i);
	        		// console.log(y,m)
	        		var re=this.monthDate(y,m<10?'0'+m:m);
	        		data.push(re);
	        	}
	       		this.data=data;

	        }


		},
		mounted(){
		
			this.end=this.endDate;
			this.begin=this.beginDate;
			this.lastDate=this.last;
			this.tableDate();
			
		}

	}
</script>
<style scoped>
	.date-picker-box{
		width: 100%;
		height: 120%;
		position: fixed;
		top: 0;
		left: 0;
		background-color: #fff;
		z-index:24;
	}
	.dpb-title{
	    height: 50px;
	    text-align: center;
	    line-height: 50px;
	    background: #fff;
	    font-size: 15px;
		position: relative;

	}
	.dpb-title>span{
		position: absolute;
		right: 12px;
		line-height: 50px;
		font-size: 13px;
	}
	.dpb-week>table{
		background: #fff;
	    font-size: 13px;
	    width: 100%;
	    border-collapse: separate;
	    table-layout: fixed;
	}
	.dpb-week>table>thead>tr>th{
		height: 30px;
	    text-align: center;
	    font-weight: 400;
	    border-bottom: 1px solid #e0e0e0;
	    width: 14.28571%;
	    vertical-align: middle;
	}
	.dpb-week-days{
		position: relative;
	    width: 100%;
	    height: 75%;
	    overflow: auto;
	    padding-bottom: 120px;

	}
	.dpbwd-table-month{
		height: 30px;
	    display: -webkit-box;
	    -webkit-box-align: center;
	    -webkit-box-pack: center;
	    font-size: 15px;
	    font-weight: 700;
	    background: hsla(210,8%,95%,.9);
	    position: -webkit-sticky;
	    position: sticky;
	    top: 0;
	    z-index: 1;
	    border-bottom: 1px solid #e0e0e0;
	}
	.page-table-item{


		box-sizing: border-box;
		position: relative;
		/*display: -webkit-box;*/
		-webkit-box-orient: vertical;
		-webkit-box-align: center;
		-webkit-box-pack: end;

	}
	.page-table-text{
	    font-size: 13px;
	    height: 15px;
	    line-height: 15px;
	    overflow: hidden;
	    text-overflow: ellipsis;
	    white-space: nowrap;
	
	}
	.page-table-note{
		color: #ff5000;
	    font-size: 12px;
	    height: 15px;
	    line-height: 15px;
	    overflow: hidden;
	    text-overflow: ellipsis;
	    white-space: nowrap;
	    font-style: inherit;
	}
	.page-table-note,.page-table-text{
		display: block;
	    width: 100%;
	    text-align: center;
	    font-weight: 400;
	}
	.dpbwd-table-items-table>tbody>tr>td.disable .page-table-text, .dpbwd-table-items-table>tbody>tr>td.disable .page-table-note{
   		color: #ccc!important;
    	background: none!important;
	}
	.dpbwd-table-items-table>tbody>tr>td.range{
		background: #fff6d6;
    	border-bottom-color: #fff6d6!important;
	}
	.dpbwd-table-items-table>tbody>tr>td.active{
	    background: #ffc900;
    	border-bottom-color: #ffc900!important;
	}
	.dpbwd-table-items-table>tbody>tr>td.disable{
		background: #fafafa!important;
    	border-bottom-color: #e0e0e0!important;
	}
	.dpbwd-table-items-table>tbody>tr>td{
	    height: 60px;;
		text-align: center;
		width: 14.28571%;
		position: relative;
		border-bottom: 1px solid #e0e0e0;
	}
	.dpbwd-table-items-table{
		width: 100%;
	    border-collapse: separate;
	    table-layout: fixed;
	}
</style>