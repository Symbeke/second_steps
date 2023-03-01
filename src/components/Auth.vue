<template>
    <div class="wrapper">
      <input v-model="authData.login" type="text" placeholder="Login" class="login">
      <input v-model="authData.password" type="text" placeholder="Password" class="password">
      {{ authData.login  }}
      {{ authData.password }}
      <button class="approve" v-on:click="buttonClickHandler">Authorization</button>
    </div>
  </template>
  
  <script>
  export default {
    name: 'Auth',
  data:() => ({
    serverUrl:'http://195.49.210.34/',
    authData :{
      login:'',
      password:''
    },
    response:{}
  }),
  methods:{
    buttonClickHandler:async function(){
      if(! this.authData.login  ||  !this.authData.password) return alert("please fill in the fields")
      const request_body = {
      userData:{
            login:this.authData.login,
            password:this.authData.password
          }
        }
        const response = await this.sendRequest('user/authorization','POST',request_body)
        console.log({response});
        if(response.info.status === "OK" && response.payload.isAuth){
          
          {this.$router.push({ name: 'Home',params:{ id:response.payload.userData[0]._id}})}
        }
        else{alert('login or password is incorrect')}

        if(response.info.status === "Error") alert(response.payload)
        // if(response.info.status === "Ok")  this.$router.push({ name: 'home' })
        
        },

    sendRequest:async function(path,method,body){
      const request_config = {
        method,
        headers:{
          'Content-Type':'application/json'
        },
        body:null
      }
      if(method != 'GET') request_config.body = JSON.stringify(body)
    // fetch(`${this.serverUrl}${path}`,request_config)
    // .then(response =>{
    //     response.json()
    //     .then(
    //         jsonRes =>{
    //             this.response = jsonRes
    //         })
    //     .catch(error=>
    //     {
    //     console.log({error});
    //     })
    //     }
    // )
    // .catch(error => {
    //     console.log({error});
    //     })
    let response = await fetch(`${this.serverUrl}${path}`,request_config);
    return await response.json();
    //  console.log({response});
    
        }
        }
        
    }
  </script>
  
  <style lang="less" scoped>
    .wrapper {
      display: flex;
      flex-direction: column;
      justify-content: space-evenly;
      align-items: center;
      border: 1px solid gray;
      border-radius: 8px;
      padding: 25px;
      input {
        width: 80%;
        padding: 8px 16px;
        outline: none;
        border: 1px solid rgba(140, 145, 150, .6);
        border-radius: 3px;
        &:hover {
          border: 1px solid rgba(140, 145, 150, 1);
        }
      }
    }
  </style>