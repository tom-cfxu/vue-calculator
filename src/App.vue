<template>
  <div class="calculator">
    <!-- 显示结果 -->
    <div class="result" style="grid-area:result">
      {{equation}}
    </div>
    <!-- 8个功能按钮 -->
    <button style="grid-area:ac" @click="clear">AC</button>
    <button style="grid-area:plus-minus" @click="backspace">Del</button>
    <button style="grid-area:percent" @click="calculatePercent">%</button>
    <button style="grid-area:add" @click="append('+')">+</button>
    <button style="grid-area:subtract" @click="append('-')">-</button>
    <button style="grid-area:multiply" @click="append('×')">×</button>
    <button style="grid-area:divide" @click="append('÷')">÷</button>
    <button style="grid-area:equal" @click="calculate">=</button>

    <!-- 10个数字按钮 -->
    <button style="grid-area:number-1" @click="append(1)">1</button>
    <button style="grid-area:number-2" @click="append(2)">2</button>
    <button style="grid-area:number-3" @click="append(3)">3</button>
    <button style="grid-area:number-4" @click="append(4)">4</button>
    <button style="grid-area:number-5" @click="append(5)">5</button>
    <button style="grid-area:number-6" @click="append(6)">6</button>
    <button style="grid-area:number-7" @click="append(7)">7</button>
    <button style="grid-area:number-8" @click="append(8)">8</button>
    <button style="grid-area:number-9" @click="append(9)">9</button>
    <button style="grid-area:number-0" @click="append(0)">0</button>

    <button style="grid-area:dot" @click="append('.')">.</button>
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'App',
  components: {
    // HelloWorld
  },
  data(){
    return{
      equation:'0', //计算器结果
      isDecimalAdded:false, //判断是否输入小数点,防止一个数输入超过1个小数点
      isOperatorAdded:false, //判断是否已经输入运算符号×/÷/+/-, 防止一个数连续点击超过一个运算符
      isStarted:false, //判断是否已经开始输入数字,用于正负数,百分比的计算
      isCalculated:false //判断是否已经有结果,防止已经有结果后没有清零
    }
  },
  methods:{
    //检查是否输入运算符号×/÷/+/-
    isOperator(c){ //c:character
      // console.log(c)
      return ['+',"-","×","÷"].indexOf(c) >-1
    },
    //当输入运算符或数字时
    append(c){
      // Start
      if(this.equation==='0'&& !this.isOperator(c)){
        if(c==='.'){
          this.equation+=""+c;
          this.isDecimalAdded=true;
        }else{
          this.equation=""+c;
        }
        this.isStarted=true;
        return
      }
      
      //当this.isOperator(c)=false时,即数字
      if(!this.isOperator(c)){
        if(this.isCalculated){
          this.clear();
          this.equation='';
        }
        if(c==='.' && this.isDecimalAdded){
          return
        }
        if(c==='.'){
          this.isDecimalAdded =true;
          this.isOperatorAdded=true
        }else{
           this.isOperatorAdded=false;
        }
        this.equation+=''+c
      }
      // 当输入运算符时
      if(this.isOperator(c) && !this.isOperatorAdded){
        this.equation+=''+c;
        this.isDecimalAdded=false;
        this.isOperatorAdded=true;
        this.isCalculated=false;
      }
    },
    //当点击等于符号的时候
    calculate(){
      let result =this.equation.replace(new RegExp('×','g'),'*').replace(new RegExp('÷','g'),'/');
      this.equation=parseFloat( eval(result).toFixed(9)).toString();
      this.isDecimalAdded=false;
      this.isOperatorAdded=false;
      this.isCalculated=true;
    },
    //当点击正负号±时
    calculateToggle(){
      if(this.isOperatorAdded ||!this.isStarted){
        return
      }
      this.equation=this.equation+'* -1';
      this.calculate();
    },
    //当点击百分比符号时
    calculatePercent(){
      if(this.isOperatorAdded ||!this.isStarted){
        return
      }
      this.equation=this.equation+'*0.01';
      this.calculate();
    },
    //当点击退格键时
    backspace(){
      //当有结果时,禁用该按钮
      if(this.isCalculated){
        return;
      }
      //删除字符串最后一位,返回新新字符串,
      this.equation = this.equation.substr(0, this.equation.length - 1); 
      //如果只剩一位了,按下则返回0
      if(this.equation.length===0){
        this.equation="0"
      }
    },
    //当点击AC符号时
    clear(){
      this.equation='0';
      this.isDecimalAdded=false;
      this.isOperatorAdded=false;
      this.isStarted=false;
      this.isCalculated=false;
    }
  },
}
</script>

<style lang="less">
::-webkit-scrollbar {
  display: none;
}

::-ms-scrollbar {
  display: none;
}
  body{
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    background-color:#ddd;
  }
  .calculator{
    --button-width:80px;
    --button-height:80px;
    display: grid;
    grid-template-areas: 
    "result result result result"
    "ac plus-minus percent divide"
    "number-7 number-8 number-9 multiply"
    "number-4 number-5 number-6 subtract"
    "number-1 number-2 number-3 add"
    "number-0 number-0 dot equal";
    grid-template-columns: repeat(4,var(--button-width));
    grid-template-rows: repeat(6,var(--button-height));
    box-shadow: -8px -8px 16px -10px rgba(255,255,255,1), 8px 8px 16px -10px rgba(0, 0, 0, 0.15);
    padding:20px;
    border-radius: 20px;
    button{
      margin:8px;
      padding:0;
      border: 0;
      display: block;
      outline: none;
      border-radius: calc(var(--button-height)/2);
      font-size:24px;
      font-family: Helvetica;
      font-weight: normal;
      color: #999;
      background: linear-gradient(135deg,rgb(220, 220, 220) 0%,rgb(246, 246, 246) 100% );
      box-shadow: -4px -4px 10px -8px rgba(255,255,255,1), 4px 4px 10px -8px rgba(0, 0, 0, 0.3);
    }
    button:active{
      box-shadow: -4px -4px 10px -8px rgba(255,255,255,1) inset, 4px 4px 10px -8px rgba(0, 0, 0, 0.3) inset !important;
    }
    .result{
      text-align: right;
      line-height:var(--button-height);
      font-size:40px;
      font-family:Helvetica;
      padding: 0 20px;
      color:#666
    }
  }
</style>
