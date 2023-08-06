<template>
  <v-app>
  <Theheader />
  <main>
  <v-container>
    <p>返済額 {{ debt.toLocaleString() }} <span v-show="debt">利息{{ratio}}%</span></p>
      
    <v-text-field label="融資額" type="number" v-model="inputdebt" />
    
    <v-text-field
      v-show="!debt"
      label="利息(%)/日" 
      type="number" 
      v-model="inputratio" />
    
    <v-btn 
      color="purple-lighten-3" 
      v-show="!debt" 
      @click="firstAddDebt()">融資<v-icon icon="mdi-bank"></v-icon></v-btn>
    
    <v-btn 
      color="purple-lighten-3" 
      v-show="debt" 
      @click="addDebt()">追加融資<v-icon icon="mdi-bank"></v-icon></v-btn>
    
   
    <v-text-field 
      v-show="debt"
      label="返済額" 
      type="number" 
      v-model="inputdeposit" />
    
    
    <v-btn 
      color="lime-lighten-3" 
      v-show="debt" 
      @click="depositDebt()">返済<v-icon icon="mdi-pig-variant-outline"></v-icon></v-btn>
  </v-container>
    <v-card
    class="mx-auto"
    max-width="500"
  >
    <v-card-title>
      T<span class="text-caption
">supa</span><v-icon icon="mdi-bank-circle-outline" />S<span class="text-caption">una</span>　ローン履歴
    </v-card-title>

    <v-divider></v-divider>

    <v-virtual-scroll
      :items="logs"
      height="320"
      item-height="48"
    >
      <template v-slot:default="{ item }">
        <v-list-item 
          :class="[ item.value < 0? 'log_minus':'log_plus']"
          :title="`￥${item.value}`"
          :subtitle="`${item.time}`"
        >
          <template v-slot:prepend>
            <v-icon v-if="item.value < 0">mdi-pig-variant-outline</v-icon>
            <v-icon v-if="item.value > 0">mdi-bank</v-icon>
          </template>
        </v-list-item>
      </template>
    </v-virtual-scroll>
  </v-card>
  </main>
  
  <Thefooter />
  </v-app>
</template>

<script setup>
//===========
//Initialize
//===========

const days = parseInt((new Date() - new Date(localStorage.date)) / 1000 / 60 / 60 / 24);
let debt = ref(Number(localStorage.debt) || 0);
let ratio = Number(localStorage.ratio) || 1;
if(days > 0){
  debt.value *= (1+ratio/100)**(days||0);
  localStorage.debt = debt.value;
  localStorage.date = new Date();
}

let logs =reactive([])
try{
  logs = JSON.parse(localStorage.logs)
}catch{
  logs = reactive([])
}finally{
  console.log(logs)
}
//=====
//input
//=====
let inputdebt = 1000000
let inputratio = 1
let inputdeposit = 1000000

const firstAddDebt = ()=>{
  debt.value = inputdebt;
  localStorage.debt = debt.value
  localStorage.ratio = inputratio
  ratio = inputratio
  addLog(inputdebt)
}
const addDebt = ()=>{
  debt.value += inputdebt
  localStorage.debt = debt.value
  addLog(inputdebt)
}
const depositDebt = ()=>{
  debt.value -= inputdeposit
  localStorage.debt = debt.value
  addLog(-inputdeposit)
}
//=====
//Log
//=====
const clearLog = ()=> logs = []
const timestamp = ()=>{
  let time = new Date()
  return time.getMonth()+"月"+time.getDate()+"日 "+time.getHours()+":"+time.getMinutes()
}
const addLog = value => {
  if(logs.length > 200) logs.pop()
    
  logs.unshift({value,time:timestamp()})
  localStorage.logs = JSON.stringify(logs)
  

}

</script>

<style>
.log_plus{
  background-color:#CE93D8;
  color:white
}
.log_minus{
  background-color:#E6EE9C;
}
</style>