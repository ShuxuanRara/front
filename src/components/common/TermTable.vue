<template>
<div style="width:1101px">
  <div style="margin-bottom: 10px">
  <label>学期：</label>
  <el-select v-model="termSelected" placeholder="请选择学期" @change="getTermSelected">
    <el-option
      v-for="item in termOptions"
      :key="item.term"
      :label="item.label"
      :value="item.label">
    </el-option>
  </el-select>
  </div>
  <el-table
    :data="termTableData"
    border
    style="width: 100%"
    :row-class-name="tableRowClassName">
    <el-table-column label="你的学期课表">
    <el-table-column
      prop="time"
      label=""
      width="100">
    </el-table-column>
    <el-table-column
      prop="Mon"
      label="星期一"
      width="200">
    </el-table-column>
    <el-table-column
      prop="Tues"
      label="星期二"
      width="200">
    </el-table-column>
    <el-table-column
      prop="Wed"
      label="星期三"
      width="200">
    </el-table-column>
    <el-table-column
      prop="Thur"
      label="星期四"
      width="200">
    </el-table-column>
    <el-table-column
      prop="Fri"
      label="星期五"
      width="200">
    </el-table-column>
    </el-table-column>
  </el-table>
  </div>
</template>

<script>
import {getCookie} from '../global/cookie'
// import termOptions from '../global/termOptions.js'
import termTable from '../global/termTable.js'
  export default {
    data() {
      return {
        termTableData:termTable,
        // options: termOptions,
        termSelected: ''
      }
    },
    computed:{
      termOptions(){
        let myData = new Date()
        var year1 = myData.getFullYear()
        let month1 = myData.getMonth()
        var options = []
        if(month1==0|| month1==1 ){
          options[0] = {
            term:(year1-1)+"2",
            label:(year1-1)+'-'+year1+'第一学期',
            value:(year1-1)+'-'+year1+'第一学期',
          },
          options[1]={
            term:(year1-1)+"1",
            label:(year1-1)+'-'+year1+'第二学期',
            value:(year1-1)+'-'+year1+'第二学期',
          }
        }else if(month1>=2&&month1<=7){
          options[0] = {
            term:(year1+1)+"1",
            label:(year1-1)+'-'+year1+'第二学期',
            value:(year1-1)+'-'+year1+'第二学期'
          },
          options[1]={
            term:(year1+1)+"2",
            label:year1+'-'+(year1+1)+'第一学期',
            value:year1+'-'+(year1+1)+'第一学期',
          }
        }else{
          options[0] = {
            term:(year1)+"1",
            label:year1+'-'+(year1+1)+'第一学期',
            value:year1+'-'+(year1+1)+'第一学期',
          },
          options[1]={
            term:(year1)+"2",
            label:year1+'-'+(year1+1)+'第二学期',
            value:year1+'-'+(year1+1)+'第二学期',
          }
        }
        
        return options
      }
    },
    methods:{
      tableRowClassName({row, rowIndex}) {
        if (rowIndex === 2||rowIndex === 5) {
          return 'warning-row';
        }
        return '';
      },
      getTermSelected(){
        this.$axios
        .get('/stuAndTea/getTermTable', { //获取查询学期课表接口
            term: this.termSelected
        })
        .then((result)=> {
            if (result.data.code === 1) {
              for(var i =0; i < 7; i++){
                if(i==2||i==5) continue;
                this.termTableData[i].Mon = ""
                this.termTableData[i].Tues = ""
                this.termTableData[i].Wed = ""
                this.termTableData[i].Thur = ""
                this.termTableData[i].Fri = ""
              }
              var time,week,info
              for(var i = 0; i < result.data.datas.length; i++){
                time = result.data.datas[i].time
                week = result.data.datas[i].week
                info = result.data.datas[i].info
                switch (week) {
                  case "Mon":
                    this.termTableData[time].Mon = info
                    break
                  case "Tues":
                    this.termTableData[time].Tues = info
                    break
                  case "Wed":
                    this.termTableData[time].Wed = info
                    break
                  case "Thur":
                    this.termTableData[time].Thur = info
                    break
                  case "Fri":
                    this.termTableData[time].Fri = info
                    break  
                }
              }
            }else if(result.data.code === 0){
              this.$router.replace({ path: '/' });
            }
            else{
                console.log("查询失败");
                return false;
            }
        })
        .catch((error)=> {
            alert(error)
        })
      }
    }
  }
</script>
<style>
.el-table .warning-row {
    background: #f5f7fa;
  }
</style>