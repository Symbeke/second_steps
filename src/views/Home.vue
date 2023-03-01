<template>
    <div class="wrapper">
        
        <chatList :login="user.login" @choose_chat="choose_chat" @modaleView="modaleView" class="chatList"/>
        <div class="message">
        <chatView :chat="currentChat"
        :login="user.login"/>
        <chatSendMessage
        :chatId="currentChat._id"
        :login="user.login"
        />
        </div>
        <div class="createChatModalWrapper" v-if="createChatIsOpen">
        <chatCreate @modaleHide="modaleHide" class="action"/>
        </div>
         
 
    </div>
</template>

<script>
import axios from 'axios'

import chatList from '@/components/chat/List.vue'
import chatCreate from '@/components/chat/Create.vue'
import chatSendMessage from '@/components/chat/SendMessage.vue'
import chatView from '@/components/chat/View.vue'
import EventBus from '@/EventBus'
export default{
    name:'Home',
    data:()=>({
        serverUrl:'http://195.49.210.34/',
        createChatIsOpen:false,
        user:{},
        intervalId:'',
        currentChat:{
            _id: '',
            history:[
                {
                    text:'hello its me',
                    from:'user1234',
                    timestamp:'2023-02-18T08:14:23.479Z',
                    id:1
                },
                {
                    text:'how are u doin',
                    from:'SymbatLogin',
                    timestamp:'2023-02-18T08:14:23.479Z',
                    id:2
                }
            ]
        }
    }),
    components:{
        chatList,
        chatCreate,
        chatView,
        chatSendMessage,
        
    },
    computed:{
        updateDate:function(){
            return ''
        }
    },
    methods:{
        modaleView:function(){
            this.createChatIsOpen = true
        },
        modaleHide:function(){
            this.createChatIsOpen = false
        },
        sendRequest:async function(path,method,body){
            const url = `${this.serverUrl}${path}`
       if(method === 'GET'){
          return (await axios.get(url)).data
       }else{
           return (await axios.post(url,body)).data
       }
        },
        choose_chat: async function(chat_id){
            // if(this.intervalId){
            //     clearInterval(this.intervalId)
            // }
            this.currentChat=(await this.sendRequest(`chat/${chat_id}`,'GET')).payload[0]
            // this.intervalId = setInterval(()=>this.update_chat_data(chat_id),1000)
        },
        update_chat_data:async function(chat_id){
            const oldChatHistory = JSON.parse(JSON.stringify(this.currentChat))
            this.currentChat=(await this.sendRequest(`chat/${chat_id}`,'GET')).payload[0]
            console.log(this.currentChat,oldChatHistory);
            

            if(oldChatHistory.history?.length < this.currentChat?.history?.length){
                this.notifyMe(this.currentChat.history.at(-1)?.text)
            }
        },
        notifyMe: function (text) {
      if (!("Notification" in window)) {
        alert("This browser does not support desktop notification");
      }
      else if (Notification.permission === "granted") {
        const notification = new Notification(text)
      }
      else if (Notification.permission !== "denied") {
        Notification.requestPermission()
          .then((permission) => {
            if (permission === "granted") {
              const notification = new Notification(text);
            }
          })
    }
  },
  connect:async function(){
    this.webSocket = new WebSocket(`ws://195.49.210.34/ws?login=${this.user.login}`) 
        this.webSocket.onopen = async (event) =>{
            console.log(event);
        }
        this.webSocket.onmessage = async (message) =>{
           const serverData  = JSON.parse(message.data) 
           if(serverData.type === 'newMessage'){
            await this.update_chat_data(serverData.payload.chatId)
           }
           else if(serverData.type === 'newChat'){
            EventBus.$emit('newChat',serverData.payload)
           }
           console.log({serverData});
        }
        this.webSocket.onclose = async() =>{
            console.log('connection close');
        }
        this.webSocket.onerror = async(error)=>{
            console.log({error});
        }
  }
},
    async mounted(){
        const userId = this.$route.params.id
        this.user = (await this.sendRequest(`user/${userId}`,'GET')).payload[0]
        // console.log(this.user);
        EventBus.$emit('userDataReady',this.user)
        EventBus.$on('messageFromMe',(messageData)=>{
            this.currentChat.history.push(messageData)
        })
    }
}
</script>
<style lang="less" scoped>
.createChatModalWrapper{
    width:100vw;
    height:100vh;
    position: fixed;
    display: flex;
    justify-content: center;
    align-items: center;
    top:0;
    left:0;
    background-color: gray;
    opacity: .6;
}
.createChat{
    width: 50vw;
    height: 50vh;
    position: fixed;
    left:calc(50%-25vw);
    top:calc(50%-25vh);
    display: flex;
}
.wrapper {
    // width: 100vw;
    // height: 100vh;
    display: flex;
    // flex-direction: column;
    // justify-content: flex-end;
    // align-items: flex-start;
    // border: 1px solid gray;
    // border-radius: 8px;
    // padding: 15px;
    // input {
    //   width: 80%;
    //   padding: 8px 16px;
    //   outline: none;
    //   border: 1px solid rgba(140, 145, 150, .6);
    //   border-radius: 3px;
    //   &:hover {
    //     border: 1px solid rgba(140, 145, 150, 1);
    //   }
    // }
  }
  .message{
    width: 80%;
    height: 80%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    margin: 0 auto;
  }
  </style>