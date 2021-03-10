<!--
 * @Author: your name
 * @Date: 2021-03-09 03:13:45
 * @LastEditTime: 2021-03-11 01:18:36
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \thousandFormatDirective\src\App.vue
-->
<template>
  <div id="app">
    <el-button @click="edit=!edit">点击</el-button>
    <template>
      <el-table
         :data="tableData"
         style="width: 100%"
         height="1050">
        <el-table-column
         prop="zip"
         label="输入时显示千分位，但v-model绑定的值是没有千分位的"
         width="400">
          <template slot-scope="scope">
            <div v-if="!edit">{{scope.row.thousandShow}}</div>
            <el-input v-else v-thousandFormat.thousandShow="scope.row"  v-model="scope.row.thousandShow"></el-input>
            <div>实际发送绑定thousandShow的值一直是<span style="color: #ff0000;">{{scope.row.thousandShow}}</span></div>
          </template>
        </el-table-column>
      </el-table>
    </template>
  </div>
</template>

<script>
/* eslint-disable */
export default {
  mounted(){
    setTimeout(() => {
        this.tableData = [{
            thousandShow:"2222222",
        },{
            thousandShow:"8888888",
        },{
            thousandShow:"33333333",
        }]
    }, 1000);
  },
  directives:{
     thousandFormat:{
       bind(el, binding, vnode){
         let event = new Event('input');
         function numFormat(num){
             let num1 = num.split(".")[0]
             let num2 = num.split(".")[1]
             let c = num1.toString().replace(/(\d)(?=(?:\d{3})+$)/g, '$1,');
             if(num.toString().indexOf(".")!==-1){
                 return c+"."+num2
             }
             else {
                 return c
             }
         }
         let key = Object.keys(binding.modifiers)[0]
         el.addEventListener("input",function (event) {
            let inputValue = binding.value[key]
            let cursorPos = event.target.selectionStart||0;
            // 以从后数起来说，光标位置是不变的
            let curPosFromBack = inputValue.length - cursorPos
            // debugger
            let inpVal = inputValue =  inputValue.replace(/,/g,"")
            binding.value[key] = inpVal //这里变化会导致视图变化
            let t =  numFormat(inpVal) //转换为千分位格式
            vnode.context.$nextTick(function () {
               el.querySelector('input').value = t //在上面由于value变化而更新视图后，再赋值给input.value,
                                      //这样可以防止这次赋值先于上面的更新视图执行
                //这里重新将光标的位置重置到输入数字的位置，否则每次正则变换后光标总是跳到最后
               el.querySelector('input').setSelectionRange(t.length-curPosFromBack, t.length-curPosFromBack);
                                  
            })
         })
         el.dispatchEvent(event);
       }
     }
  },
  data() {
    return {
      tableData: [{
          thousandShow:"",
      },{
          thousandShow:"",
      }],
      edit:false
    }
  }
}
</script>