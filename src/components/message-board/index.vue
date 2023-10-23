<template>
    <div class="page-container">
        <div class="page-header">
            <span>Message Board</span>
        </div>
        <div class="main-container">
            <div class="post-section">
                <span>Type something in the box and then hit "Post":</span>
                <textarea v-model="newMessage" placeholder="Enter your message"></textarea>
                <button class="post-it" @click="postMessage($event)">Post</button>
                <button v-if="messages.length > 0" class="delete-all" @click="showConfirmationDialog">Delete All</button>
            </div>
            <div class="list-section" v-if="messages.length > 0">
                <div class="list-header">
                    <span>Messages</span>
                    <div class="right-side">
                        <span class="sort-caption" v-if="checked">Sort by oldest first</span>
                        <span v-else>Sort by newest first</span>
                        <label class="switch">
                            <input v-model="checked" type="checkbox" @click="toggleCheckbox">
                            <div class="slider round"></div>
                        </label>
                    </div>
                </div>
                <div class="cards">
                    <div class="card" v-for="message in messages" :key="message.id">
                        <div class="upper-box">
                            <span class="user-name">~{{ message.source }}</span>
                            <span>{{ message.timestamp }}</span>
                            <button class="delete-all" @click="deleteThis(message.id)">Delete</button>
                        </div>
                        <div class="lower-box">
                            <span class="thread">{{ message.text }}</span>
                        </div>
                    </div>
                </div>
            </div>
            <div class="empty-section" v-else>
                <span>No Messages, please write something...</span>
            </div>

        </div>
        <confirmation-dialog
            v-if="showDialog"
            :show="showDialog"
            :message="`Are you sure you want to delete all messages?`"
            @confirm="handleConfirm"
            @cancel="handleCancel"
        ></confirmation-dialog>
    </div>

</template>
<script>
//import moment from 'moment';
import axios from "axios";
import ConfirmationDialog from './comfirmation-dialog.vue';
export default {
    name: 'message-board',
    data(){
        return {
            newMessage: '',
            messages: [],
            checked: true,
            showDialog: false
        }
    },
    components: {
        ConfirmationDialog
    },
    mounted(){
        this.getMessages();
    },
    methods: {
        toggleCheckbox(){
            this.checked = !this.checked;
            this.checked ? this.sortArrayAsend() : this.sortArrayDsec();
            console.log(this.messages);
        },
        showConfirmationDialog() {
            this.showDialog = true;
        },
        //TO DO make single function
        handleConfirm() {
            this.showDialog = false;
            this.messages.forEach(item => {
                this.deleteThis(item.id);
            });
            this.getMessages();
            //this.messages = [];
        },
        handleCancel() {
            this.showDialog = false;
        },
        getMessages(){
            const messages =  axios.get('https://mapi.harmoney.dev/api/v1/messages/', {
                headers:{
                    "Authorization": "JGkKkhdnzDwR8nb7",
                }
            });
            messages
            .then((res) => {
                if(res?.data){
                    this.messages = res.data;
                }
            })
            .catch((err) => {
                console.error(err);
            })
        },
        postMessage() {
            const postIt = axios.post(
                "https://mapi.harmoney.dev/api/v1/messages/",
                {
                    text: this.newMessage
                },
                {
                    headers: {
                        "Authorization": "JGkKkhdnzDwR8nb7",
                    },
                }
            );
            postIt
            .then(() => {
                window.alert('Message has been uploaded successfully');
                this.getMessages();
            })
            .catch((err)=>{
                console.log(err);
            })
            /* .finally(()=>{
                console.log(Date.now());

                let dateTime = moment(Date.now()).format('LLL'); //moment.unix(Date.now())
                if (this.newMessage.trim() !== '') {
                    this.messages.push({
                        id: this.messages.length + 1,
                        text: this.newMessage.trim(),
                        source: 'Me',
                        timestamp: dateTime,
                        unixStamp: Date.now()
                    });
                    this.newMessage = '';
                }
                console.log(this.messages);
                //this.sortArrayAsend();
            }) */
        },
        sortArrayAsend(){
            this.messages.sort((a, b) => new Date(a.timestamp) - new Date(b.timestamp));
        },
        sortArrayDsec(){
            this.messages.sort((a, b) => new Date(b.timestamp) - new Date(a.timestamp));
        },
        deleteThis(id){
            const deleteIt = axios.delete(`https://mapi.harmoney.dev/api/v1/messages/${id}/`,
            {
                headers: {
                    "Authorization": "JGkKkhdnzDwR8nb7",
                },
            });
            deleteIt
            .then(() => {
                window.alert('Deleted Successfully');
                this.getMessages();
            })
            .catch((err)=>{
                console.error('Failed to Delete the message', err);
            })
            /* .finally(()=>{
                const indexToRemove = this.messages.findIndex(obj => obj.id === id);
                if (indexToRemove !== -1) {
                    this.messages.splice(indexToRemove, 1);
                }
            }); */
        }
    }
}
</script>

<style lang="less" scoped>
    .page-container{
        display: flex;
        flex-direction: column;
        gap: 15px;
        width: 80%;
        margin: auto;
        .page-header{
            display: flex;
            padding: 10px;
            span{
                font-size: 20px;
                font-weight: 600;
            }
        }
        .main-container{
            display: flex;
            flex-direction: column;
            gap: 10px;
            .post-section{
                display: flex;
                gap: 18px;
                background: #E0E0E0;
                padding: 10px;
                textarea{
                    border: none;
                    border-radius: 5px;
                }
                .post-it{
                    border: none;
                    border-radius: 5px;
                    font-weight: 600;
                    padding: 0 15px;
                    cursor: pointer;
                }
                .delete-all{
                    border: none;
                    border-radius: 5px;
                    color: white;
                    font-weight: 600;
                    background: #e91a0e;
                    padding: 0 15px;
                    cursor: pointer;
                }
                span{
                    padding-top: 6px;
                    font-weight: 500;
                }
            }
            .list-header{
                display: flex;
                justify-content: space-between;
                padding: 20px 0;
                align-items: center;
                span{
                    font-size: 16px;
                    font-weight: 600;
                }
            }
            .list-section{
                border-top: 1px solid #E0E0E0;
                .cards{
                    display: flex;
                    flex-direction: column;
                    gap: 10px;
                }
            }
            .empty-section{
                display: flex;
                padding-top: 15px;
            }
        }
    }
    .card{
        border-top: 1px solid #E0E0E0;
        .upper-box{
            display: flex;
            gap: 10px;
            justify-content: flex-start;
            padding: 15px 0;
            .user-name{
                font-weight: 600;
                font-size: 14px;
            }
        }
        .lower-box{
            display: flex;
        }
    }
    .switch {
        position: relative;
        display: inline-block;
        width: 60px;
        height: 34px;
    }
    .switch input {
        display: none;
    }

    .slider {
        position: absolute;
        cursor: pointer;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        background-color: #ccc;
        -webkit-transition: 0.4s;
        transition: 0.4s;
    }

    .slider:before {
        position: absolute;
        content: "";
        height: 26px;
        width: 26px;
        left: 4px;
        bottom: 4px;
        background-color: white;
        -webkit-transition: 0.4s;
        transition: 0.4s;
    }

    input:checked + .slider {
        background-color: #101010;
    }

    input:focus + .slider {
        box-shadow: 0 0 1px #101010;
    }

    input:checked + .slider:before {
        -webkit-transform: translateX(26px);
        -ms-transform: translateX(26px);
        transform: translateX(26px);
    }

    .slider.round {
        border-radius: 34px;
    }

    .slider.round:before {
        border-radius: 50%;
    }
    .sort-caption{
        font-size: 14px;
    }
    .right-side{
        display: flex;
        gap: 10px;
        align-items: center;
    }
    
    
</style>