<template>
<div class="wrapper">
    <img v-on:click="close_button_handler" src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAB4AAAAeCAYAAAA7MK6iAAAACXBIWXMAAAsTAAALEwEAmpwYAAABSklEQVR4nO2WO04DMRCGv0ukIA9ESRGq0BEOEAkizhHyOAsdKIiCkj6PmxAuAAodWbokSmRpVlqtYnvWu4EU+aWRVrZmP894PDYcdYA6BXrAFJgBv2IzGesCtSKBFeAJWAEbj62BN+AsL/QOiBTAtC2Adih0IBFkhSaj74dEus4BTcLVkVcD0+tKe1kDfikQGttQc2Rs1XsFXALzHXNmrAE0Lb4ryaRVPceqzY+N6in4XMaQhdn8713gicMxCYjhu8Zs/iMX+MOzV2mQFrqRDmeVppq/gYuEzznwpfCL9gH+VPj9HGSqJ/9VXN09HqeOC1xzNJCmp4GYuWuL79LXQIyeFYWS1R5RXvyLAqGmmk9QqqV8cWiuxVstNFa/gIeA6f1Bagem3aT3hpwqAQ9SmZooX7PsqUZVudrGwLu010i+R3JOvUfmKP5aW9phkM5mm0cTAAAAAElFTkSuQmCC" class="close">
    <input v-model="label" type="text" placeholder="Title..." class="title">
    <input v-model="description" type="text" placeholder="Description..." class="description">
        <hr>     
        
        <h3>Choose members</h3>
        <div class="addMemberWrapper">
            <input @keypress.enter="add_member_button_handler" list="usersList" v-model="selectMember" type="text" placeholder="Choose a member..." class="addMember">
            <div v-on:click="add_member_button_handler" class="addMemberButton">Add</div>
            </div>
            <div class="addMembersList">
                Added members:{{selectedMembers}}
            </div>
            <div v-on:click="create_chat_button_handler" class="submitButton">Create chat</div>
            <datalist id="usersList">
                <option v-for="user in usersList"
                :key="user._id"
                :value="user.login"></option>
            </datalist>
</div>
    </template>

<script>
import axios from 'axios'
export default{
    name:'chatCreate',
    data:()=>({
        serverUrl:'http://195.49.210.34/',
        label:'',
        description:'',
        selectMember:'',
        selectedMembers:[],
        usersList:[]
    }),
    async mounted(){
        this.usersList = (await this.sendRequest('user','GET')).payload
    },
    methods:{
        create_chat_button_handler:async function(){
            if(!this.label) return alert('no title')
            if(!this.description) return alert('no description')
            if(this.selectedMembers.length <= 0) return alert('choose al least 1 member')
            
                const data_for_create_chat = {userId:this.$route.params.id,
                                            label : this.label,
                                            description:this.description,
                                             members:this.selectedMembers
                                            }
            const response = await this.sendRequest('chat/create','POST',data_for_create_chat)
            if(response.info.status === 'Error') return alert(response.payload)
            return alert(`chat ${this.label} created successfully`)
        },
        add_member_button_handler:async function(){
            if(!this.selectMember) return alert ('Please choose members')
            if(this.selectedMembers.includes(this.selectMember)) return alert('You have already chosen this member' )
            
            // let selectedMemberIsExist =false;
            // for(const user of this.usersList){
            //     if(user.login === this.selectMember){
            //         selectedMemberIsExist = true;
            //     }
            // }
            // if(!selectedMemberIsExist) return alert(`User with login ${user.login} already exist`)

            let selectedMemberIsExist =false;
            let counter = 0;
             for(const user of this.usersList){
                 if(user.login === this.selectMember){
                    selectedMemberIsExist = true;
                }
                if(this.selectedMembers.includes(user.login))
                counter++
             }
            if(!selectedMemberIsExist) return alert(`User with login ${this.selectMember}  doesn't exist`)
            if(counter !== this.selectedMembers.length) return alert("User with login  doesn't exist")
            
            this.selectedMembers.push(this.selectMember)
            this.selectMember = ''
        },
        close_button_handler:function(){
            this.$emit('modaleHide')
        },
        sendRequest:async function(path,method,body){

       const url = `${this.serverUrl}${path}`
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
.wrapper{
    position: absolute;
    display:flex;
    flex-direction:column;
    justify-content:space-evenly;
    align-items:center;
    width:50vw;
    height:auto;
    position: relative;
    padding: 20px;
    border:1px solid rgb(26, 22, 22);
    border-radius: 10px;
    
    .addMemberWrapper{
        border-radius: 25px;
        width: 50px;
        display: flex;
        justify-content: space-evenly;
        align-items: center;
        input{
            width: 60vh;
        }
    }
    .close{
        width: 2.5vh;
      position: absolute;
      right: 5px;
      top: 5px;
      cursor: pointer;
      margin: 0;
    }
    .button {
      padding: 4px 16px;
      border: 1px solid rgba(37, 47, 37, 1);
      border-radius: 10px;
      cursor: pointer;
    }
    input {
      width: 40%;
      padding: 8px 16px;
      outline: none;
      border: 1px solid rgba(37, 47, 37, 1);
      border-radius: 10px;
    }
    * {
      margin-top: 20px;
    }
    hr {
      width: 100%;
    }
    h3 {
      padding: 0;
      margin: 0;
    }
} 
</style>