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
}
const addDebt = ()=>{
  debt.value += inputdebt
  localStorage.debt = debt.value
}
const depositDebt = ()=>{
  debt.value -= inputdeposit
  localStorage.debt = debt.value
}

</script>
