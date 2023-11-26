<template>
    <div class="header">
        <input ref="newmessage" v-model="todo" class="input" type="text" @keyup.enter="addnewtask" placeholder="Enter Your Task Here....">&nbsp;
        <v-btn @click="addnewtask" color="black">Add_New</v-btn>
    </div>
    <v-dialog v-model="showAlert" max-width="400">
        <v-card>
          <v-card-title>Todo</v-card-title>
          <v-card-text>
            Task Added Successfully
          </v-card-text>
          <v-card-actions>
            <v-btn @click="showAlert = false">Close</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>

      <v-dialog v-model="showAlertremoved" max-width="400">
        <v-card>
          <v-card-title>Todo</v-card-title>
          <v-card-text>
            Task Removed Successfully
          </v-card-text>
          <v-card-actions>
            <v-btn @click="showAlertremoved = false">Close</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>

      <v-dialog v-model="empty" max-width="400">
        <v-card>
          <v-card-title>Todo</v-card-title>
          <v-card-text>
            you have passed an empty task
          </v-card-text>
          <v-card-actions>
            <v-btn @click="empty = false">Close</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>

      <v-dialog v-model="update" max-width="400">
        <v-card>
          <v-card-title>Todo</v-card-title>
          <v-card-text>
            Updated
          </v-card-text>
          <v-card-actions>
            <v-btn @click="update = false">Close</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>

    <div class="outterbox">
        <div v-if="messages.length === 0 & !loading" class="no-task-message">
            <p>No Task Added</p>
        </div>
        <div v-if="loading" class="loading-spinner">
            <div class="spinner"></div>
            Loading...
        </div>
        <div class="todo-container">
            <div v-for="message in todosFiltered" :key="message.date" class="todo-items">
                
                <div class="text_div">
                    <!-- <h5>{{ message.text }}</h5> -->
                    <div>
                        <input type="checkbox" v-model="message.done" class="checkbox">&nbsp;
                        <input :disabled="message.done" type="text" v-model="message.text" @keyup.enter="updateTask(message)" class="input_2">
                    </div>
                    
                    <div class="remove-item">
                        <v-icon @click="remove(message.id)" color="red">mdi-close</v-icon>
                    </div>
                </div>
        
            </div>
        </div>
    </div>
    <div v-if="messages.length > 0 & !loading" class="extra-container">
        <div><label><input type="checkbox" :checked="!anyremaining" @change="checkAlltodo"> Check All</label></div>
        <div>{{ remaining }} item left</div>
      </div>

      <div v-if="messages.length > 0 & !loading" class="extra-container">
        <div>
          <button :class="{ active: filter == 'all'}" @click="filter = 'all'" small>All</button>&nbsp;
          <button :class="{ active: filter == 'active'}" @click="filter = 'active'" small>Active</button>&nbsp;
          <button :class="{ active: filter == 'done'}" @click="filter = 'done'" small>Done</button>&nbsp;
        </div>
      </div>
</template>

<script>
import { getFirestore, collection, addDoc, orderBy, query, onSnapshot, setDoc, doc, deleteDoc} from 'firebase/firestore';

// Import the functions you need from the SDKs you need
import { initializeApp } from "firebase/app";
import { onUnmounted, ref } from 'vue';
// TODO: Add SDKs for Firebase products that you want to use
// https://firebase.google.com/docs/web/setup#available-libraries

// Your web app's Firebase configuration
// For Firebase JS SDK v7.20.0 and later, measurementId is optional
const firebaseConfig = {
  apiKey: "AIzaSyAFFdlUZo7mqgSJy77Adaux_cRw-4_VqAY",
  authDomain: "todo-ea892.firebaseapp.com",
  projectId: "todo-ea892",
  storageBucket: "todo-ea892.appspot.com",
  messagingSenderId: "522683033919",
  appId: "1:522683033919:web:f17e84fef183168d45cb8a",
  measurementId: "G-CK2DZQ00WS"
};

// Initialize Firebase
const app = initializeApp(firebaseConfig);
const db = getFirestore(app);

    export default {
        name: 'RealTodo',
        components: {},
        methods: {
            addnewtask: function () {
                if (this.todo.trim() !== '') {
                addDoc(collection(db,'messages'),{
                    text: this.$refs.newmessage.value,
                    date: Date.now(),
                    done: false
                });

                this.showAlert = true;

                this.todo = '';

                setTimeout(() => {
                    this.showAlert = false;
                }, 3000);
            }else{
                this.empty = true;
                setTimeout(() => {
                    this.empty = false;
                }, 3000);
            }
            },
            updateTask:function (message) {
                setDoc(doc(db, 'messages', message.id), {
                    text:message.text,
                    date:message.date,
                    done: message.done
                })
                this.update = true;
                setTimeout(() => {
                    this.update = false;
                }, 3000);
            },
            remove: function (id) {
                deleteDoc(doc(db,'messages', id))
                this.showAlertremoved = true
                setTimeout(() => {
                this.showAlertremoved = false;
                }, 3000);
            },
            checkAlltodo(){
                this.messages.forEach((todo) => todo.done = event.target.checked)
            },
        },
        computed: {
            remaining() {
                return this.messages.filter(todo => !todo.done).length
            },
            anyremaining(){
                return this.remaining != 0
            },
            todosFiltered(){
                if(this.filter == 'all'){
                return this.messages
                }else if(this.filter == 'active'){
                return this.messages.filter(todo => !todo.done)
                }else if(this.filter == 'done'){
                return this.messages.filter(todo => todo.done)
                }

                return this.todos
            },
        },
        data: () => {
            return {
                messages:ref([]),
                todo:'',
                loading: true,
                filter:'all',
                showAlert: false,
                showAlertremoved: false,
                empty:false,
                update:false
            }
        },
        mounted(){
            const latestQuery = query(collection(db,"messages"),orderBy('date'));
            const livemessages = onSnapshot(latestQuery,(snapshot)=>{
                this.messages = snapshot.docs.map((doc) => {
                    return {
                        id:doc.id,
                        text:doc.data().text,
                        date:doc.data().date,
                        done: doc.data().done || false
                    }
                });
                this.loading = false;
            });
            onUnmounted(livemessages)
        },
    }
</script>

<style>
    .input{
        width: 100%;
        padding: 5px;
        border: 1px dotted grey;
        border-radius: 5px;
    }
    .header{
        margin-top: 5%;
        display: flex;
        margin-bottom: 5px;
    }
    .todo-items{
        padding: 5px;
    }
    .todo-container{
        height: 200px;
        overflow-y: auto;
        scrollbar-width: thin;
        scrollbar-color: #888888 #f0f0f0;
    }
    .todo-container::-webkit-scrollbar {
        width: 5px;
    }

    .todo-container::-webkit-scrollbar-thumb {
        background-color: #888888;
        border-radius: 5px;
    }

    .todo-container::-webkit-scrollbar-track {
        background-color: #f0f0f0;
        border-radius: 5px;
    }
    .text_div{
        padding: 10px;
        margin-top: 10px;
        display: flex;
        justify-content: space-between;
        border-radius: 5px;
        background: rgba(204, 204, 204, 0.5);
        transition: 0.4s;
    }
    .dark .text_div {
        background: rgba(88, 88, 88, 0.5);
    }
    .input_2{
        border: 1px dotted grey;
        padding: 5px 5px;
        border-radius: 5px;
    }
    .checkbox:checked + .input_2 {
        border: none;
        text-decoration: line-through;
    }
    .outterbox{
        margin-top: 20px;
    }
    .no-task-message {
        padding: 10px;
        background-color: #f8d7da;
        border: 1px solid #f5c6cb;
        color: #721c24;
        border-radius: 5px;
        margin-top: 10px;
    }
    .loading-spinner {
        margin-top: 20px;
    }

    .spinner {
        border: 8px solid rgba(0, 0, 0, 0.1);
        border-top: 8px solid #3498db;
        border-radius: 50%;
        width: 50px;
        height: 50px;
        animation: spin 1s linear infinite;
        margin-bottom: 10px;
    }

    @keyframes spin {
        0% { transform: rotate(0deg); }
        100% { transform: rotate(360deg); }
    }
    .extra-container{
        margin-top: 10px;
        display: flex;
        align-items: center;
        justify-content: space-between;
        font-size: 13px;
        border-top: 1px solid lightgrey;
        padding-top: 14px;
        margin-bottom: 14px;
    }
    button{
        padding: 0 5px; border: 1px solid grey; border-radius: 5px;
    }
    button:hover{
        background: lightgreen;
    }
    button:focus{
        outline: none;
    }
    .active {
        background: lightgreen;
        color: black;
    }
</style>