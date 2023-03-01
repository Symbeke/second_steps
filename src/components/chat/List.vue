<template>
    
<div class="wrapper">
  <div class="inputField">
    <img   v-on:click="create_button_handler" src="../../assets/plus.png" class="createBtn">
  <input type="text" placeholder="search" class="search">
  
  </div>
    <div class="list">
    <div  class="list_element"
        v-for="chat in availableChats"
        v-bind:key="chat._id"
        v-on:click="choose_button_handler(chat._id)">
      <div class="photo">
        <img src="../../assets/download (1).png" height="50px" width="50px">
        </div>
        <div class="chat_content">
    <div class="title">
      </div>
        {{chat.label}}
      <div class="lastMessage">
        <div class="from">
            {{ chat.history.at(-1).from }}:&nbsp;
            </div>
            <div class="text">
                {{ chat.history.at(-1).text .slice(0,20)}}
                </div>
      </div>
     </div>
      </div>
      </div>
</div>
  


        </template>

    <script>
    import axios from 'axios'
    import EventBus from '@/EventBus'

    export default{
        name:'chatList',
        components:{
            
        },
        props:{
          login:{
            type:String,
            default:''
          }
        },
    data:() => ({
      serverUrl:'http://195.49.210.34/',
      availableChats:[]
    }),
    async mounted(){
      EventBus.$on('userDataReady', async(user)=> {
        await this.update_chat_list(user.login)
      // setInterval(async ()=>await this.update_chat_list(user.login),1000)
      EventBus.$on('newChat', async(chat_data)=> {
      await this.update_chat_list(user.login)
      })
    }) 
    },
  methods:{
    update_chat_list:async function(login){
      const  response = await this.sendRequest(`chat/user/${login}`, "GET")
        this.availableChats = response.payload
    },
    create_button_handler:function(){
      this.$emit('modaleView')
    },
    sendRequest:async function(path,method,body){
        const url = `${this.serverUrl}${path}`
        // method = method.toLowerCase()
        // return (await axios[method](url,body).data)
        if(method === 'GET'){
           return (await axios.get(url)).data
        }else{
            return (await axios.post(url,body)).data
        }
    //     const request_config = {
    //         method,
    //         headers}
    // axios.post()
        },
        choose_button_handler:function(chat_id){
          this.$emit('choose_chat',chat_id)
          EventBus.$emit('updateScroll')
        }
    }
}
</script>
        <style lang="less" scoped>
        .wrapper {
        width: 260px;
        height:100hv;
        font-size: 15px;
        background-color: rgb(87, 172, 200);
        vertical-align: top;
        display: flex;
        flex-direction: column;
        }
    .list_element{
      box-shadow: 0 0 3px;
      display: flex;
      gap: 25px;
      border-radius: 12px;
      margin-top: 2px;
      &:hover {
        background-color: rgba(163, 161, 161, 0.1);
      }  
      .photo {
        // label {
        width: 50px;
        border-radius: 100%;
        display: grid;
        justify-content: center;
        align-content: center;
        }
      .chat_content {
          display: flex;
          flex-direction: column;
          margin-top: 10px;
          .title {
              font-size: 1.5em;
          }
          .last_message {
              display: flex;
                        .text {
                            display: flex;
                        }
                    }
                }   
            }  
    .createBtn{
      width: 30px;
      height: 30px;
      margin: auto;
      margin-left: 5px;
    }
.from{

  padding-left:0;
  margin:0;
  list-style-type:none;
  /*overflow-y:scroll;*/
  color: white;
  /*padding:10px 0;*/

}
.inputField{
  display: flex;
}

.lastMessage{
  /*display:inline-block;*/
  vertical-align:top;
  /*margin-top:12px;*/
  color: white;
  display: flex;
  flex-direction: column;
}
 .search{
    width: 100%;
    height: 34px;
    line-height: 50px;
    padding: 0px 60px 0 16px;
    background-color: #c6d4d5;
    border: none;
    border-radius: 10px;
    color: #f1f6f7;
    background-image: url("../../assets/icons8-search-120.png");
    background-repeat: no-repeat;
    background-position: 170px;
    background-size: 25px;
    margin-top: 4px;
    margin-left: 5px;
    margin-right: 4px;
 }
 
.photo{
  display: flex;
}
        
      </style>