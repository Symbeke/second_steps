<template>
    <div class="wrapper">
      <div class="innerWrapper">
      <div class="messageList"
      v-chat-scroll="{always: false, smooth: true, scrollonremoved:true, smoothonremoved: false}">
      
        <div v-for="message in chat.history" 
        :class="{messageWrapper:true, fromMe:login === message.from, notFromMe: login !== message.from }"
        :key="message.id">
          <div class="from">
            {{ message.from }}
          </div>
          <div class="text">
            {{message.text}}
          </div>
          <div class="timestamp">
            {{ formatDate(message.timestamp) }}
          </div>
        </div>
      </div>
      </div>
    </div>
  </template>
  
  <script>
import EventBus from '@/EventBus';
  export default {
    name: 'chatview',
    props:{
        chat:{
            type:Object,
            default:{

            },
            history:[]
          },
        login:{
                type:String,
                default:''
            }
        },
        methods:{
          formatDate:function(timestamp){
            const date = new Date(timestamp)
            const Hours = String(date.getHours())
            const Minutes = String(date.getMinutes())
            const handled_Hours = Hours.length < 2 ? '0'+Hours:Hours
            const handled_Minutes = Minutes.length < 2 ? '0'+Minutes:Minutes
            return `${handled_Hours} : ${handled_Minutes}`
          }
        },
        async mounted(){

        }
    }
  
  </script>
  
  <style lang="less" scoped>
    .wrapper {
      // width: 80%;
      height: 90vh;
      display: flex;
      flex-direction: column;
      
      .innerWrapper{
        height: 90vh;
        overflow-y: scroll;
        width: inherit;
      }
      .messageList {
        width: 100%;
        height: 100%;
        display: flex;
        flex-direction: column;
        // justify-content: flex-end;
        // align-items: center;
        // overflow-y: auto;
      
        .messageWrapper {
          
          max-width: 60%;
          height: auto;
          min-height: 77px;
          border-radius: 15px;
          display: flex;
          flex-direction: column;
          justify-content: center;
          align-items: center;
          font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
          padding: 20px;
          margin-bottom: 20px;
          * {
            width: 100%;
          }
          .text {
            font-size: 1em;
          }
          .from {
            font-size: 1.1em;
            text-align: left;
          }
          .timestamp {
            font-size: .9em;
            color: rgba(120, 113, 113, 0.9);
            text-align: right;
          }
        }
        .fromMe {
          background-color: rgba(50, 192, 50, .8);
          text-align: right;
          align-self: flex-end;
        }
        .notFromMe {
          background-color: #c9c9c9;
          text-align: left;
          align-self: flex-start;
        }
      }
    }
  </style>