<template>
  <v-app :class="mode">
    <HeaderSection :mode="mode" @toggle="toggle"/>
    
    
    <v-container>
      
      <v-Row :align="center" class="justify-content-center">
        <v-col class="col-md" md="6">
          
        <img src="https://upload.wikimedia.org/wikipedia/commons/6/67/Microsoft_To-Do_icon.png" class="image" alt="">
          <div class="todo_section">
            <RealTodo/>
            <!-- <TodoSection :mode="mode"/> -->
          </div>
        </v-col>
      </v-Row>
    </v-container>
  </v-app>
</template>

<script>
import HeaderSection from './components/HeaderSection';
import RealTodo from './components/RealTodo';
// import TodoSection from './components/TodoSection';

export default {
  name: 'App',
  data() {
    return {
      mode: 'light',
    }
  },
  components: {
    HeaderSection,
    RealTodo
},
created () {
  window.addEventListener('keyup', this.keyPress)
},

  methods: {
    keyPress (e) {
      if (e.key === 'up'){
        this.toggle()
      }
    },
    toggle (){
      if (this.mode === "dark"){
        this.mode = "light"
      }else{
        this.mode = "dark"
      }
    }
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Roboto', sans-serif;
}
v-app{
  width: 100vw;
  height: 100vh;
  background-color: #f3f3f3;
  color: #152028;
  transition: background 0.5s ease-in-out;
}
.image{
  width: 100px;
}
.dark{
  background-color: #102734;
  color: #f3f3f3;
}
.justify-content-center{
  justify-content: center;
}
</style>
