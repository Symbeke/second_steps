<template>
    <div class="footer">
            <textarea @keypress.enter="send_message_button_handler" v-model="textMessage" type="text" placeholder="Type a message..." class="sendMessage"></textarea>
            <div :disabled="!chatId" v-on:click="send_message_button_handler" class="sendButton">Send Message</div>
    </div>
        </template>

    <script>
    import axios from 'axios'
import EventBus from '@/EventBus'
    export default{
        name:'chatSendMessage',
        data:()=>({
        serverUrl:'http://195.49.210.34/',
        textMessage:''
        }),
        props:{
        chatId:{
            type:String,
            default:{
            }
          },
        login:{
                type:String,
                default:''
            }
        },
        methods:{
        send_message_button_handler:async function(){    
            const data_for_send_message = {
                chatId:this.chatId,
                login : this.login,
                text:this.textMessage
            }
            const response = await this.sendRequest('chat/send/text','POST',data_for_send_message)
            const messageData = response.payload

            EventBus.$emit('messageFromMe',messageData)
            EventBus.$emit('updateScroll')

            if(response.info.status === 'Error') return alert(response.payload)
            // return alert(`message ${this.textMessage} created successfully`)
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
       }
    }
    }
    </script>
    <style lang="less" scoped>
    footer{
    height:155px;
    padding:20px 30px 10px 20px;
  }
   footer textarea{
    resize:none;
    border:none;
    display:flex;
    flex-direction: column;
    width:100%;
    height:80px;
    border-radius:3px;
    padding:20px;
    font-size:13px;
    margin-bottom:13px;
  }
   footer textarea::placeholder{
    color:#ddd;
  }
   footer img{
    height:30px;
    cursor:pointer;
  }
  .sendButton{
    text-decoration:none;
    text-transform:uppercase;
    font-weight:bold;
    color:#6fbced;
    vertical-align:top;
    margin-left:333px;
    margin-top:5px;
    display:inline-block;
  }
    </style>