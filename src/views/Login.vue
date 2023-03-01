<template>
    <div class="home">
      <div class="Switcher">
        <button class="auth" v-on:click="switch_handler(true)">Authorization</button>
        <button class="reg" v-on:click="switch_handler(false)">Registration</button>
      </div>
      <div class="forms">
      <Auth v-if="switcher"/>
      <Reg v-if="!switcher"/>
      </div>
    </div>
  </template>
  
  <script>
  // @ is an alias to /src
  import Auth from '@/components/Auth.vue'
  import Reg from '@/components/Reg.vue'
  
  export default {
    name: 'Login',
    components: {
      Auth,
      Reg
    },
    data:() =>({
      switcher:false
    }),
    methods:{
      switch_handler:function(isSwitch){
        this.switcher = isSwitch
      }
    }
  }
  </script>
  
  <style lang="less" scoped>
  .home {
    width: 100vw;
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    
    .switcher{
      width:50%;
      height:45px;
      padding: 8px 16px 8px 20px;
      & * {
        width:25%;
        cursor: pointer;
        font-size: 1.25em;
        font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        letter-spacing: 2em;
      }
    }
    .auth {
      margin: 10px;
    }
    .reg{
      margin: 10px;
    }
  }
    
  </style>
  