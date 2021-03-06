<template>
  <!-- modifiers with override and prevent -->
  <div tabindex="0" class="todos" @keydown.meta.s.prevent="save">
    <!-- v-if -->
    <h2 v-if="!todos.length">Hello, what would you like to do today?</h2>
    <!-- v-else-if -->
    <h2 v-else-if="todos.length > 5">Hold up, you look a little packed!</h2>
    <!-- v-else -->
    <h2 v-else>Add another item!</h2>
    <!-- v-model -->
    <input type="text" v-model="newTodo"  @keydown.enter="addTodo"/>
    <!-- v-on -->
    <button @click="addTodo">Add</button>
    <!-- v-show -->
    <div class="notes" :class="{'danger': todos.length > 5}">
      <h2 v-show="!todos.length">
        Add a todo!
      </h2>
      <!-- v-for -->
      <div :class="['todo', {'done': todo.done}]" v-for="(todo, index) in todos" :key="todo.id" @click="todo.done = !todo.done">
        <input type="checkbox" v-model="todo.done">
        <span class="todo__message">{{todo.message}}</span>
        <button class="delete" @click.stop="remove(index)">X</button>
      </div>
    </div>
    <transition name="slide-fade">
      <div class="toast" v-show="showToast">
        {{toastMessage}}
      </div>
    </transition>
  </div>
</template>

<script>
export default {
  data () {
    return {
      todos: JSON.parse(window.localStorage.getItem('todos')) || [],
      newTodo: '',
      toastMessage: '',
      showToast: false
    };
  },
  methods: {
    addTodo () {
      this.todos.push({
        message: this.newTodo,
        done: false,
        id: `todo-${this.todos.length}`
      });
      this.newTodo = '';
    },
    save () {
      window.localStorage.setItem('todos', JSON.stringify(this.todos));
      this.toast('Saved notes');
    },
    remove (index) {
      this.todos.splice(index, 1);
    },
    toast (message) {
      this.toastMessage = message;
      this.showToast = true;
      let self = this;
      setTimeout(() => { self.showToast = false; }, 2000);
    }
  }
};
</script>

<style scoped>
.todos {
  outline: none;
}

h2 {
  margin-top: 1em;
}

input[type="text"] {
  border: 0;
  border-bottom: 2px solid lightslategray;
  width: 300px;
  outline: none;
  padding: .6em;
}

input:focus {
  border-color: slateblue;
}

button {
  background: slateblue;
  color: white;
  border-radius: .2em;
  padding: .7em;
  border: 0;
  cursor: pointer;
}

.notes {
  margin: 2em 1em;
  padding: 3em;
  background: whitesmoke;
  border-radius: 1em;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.missing > h2 {
  color: grey;
}

.danger {
  border: 1px solid orange;
}

.todo {
  width: 400px;
  display: flex;
  align-items: center;
  font-size: 1.2em;
  margin: .5em 0;
  cursor: pointer;
}

.todo__message {
  flex-grow: 1;
  display: flex;
}

.delete {
  color: #2c3e50;
  align-self: right;
  background: none;
}

.done {
  text-decoration: line-through;
}

input[type="checkbox"] {
  margin-right: .5em;
}

.toast {
  position: absolute;
  background-color: rgba(106, 90, 205, .9);
  border: 1px solid slateblue;
  border-radius: .2em;
  top: 2em;
  right: 2em;
  padding: 1em;
  color: white;
  font-weight: bold;
}

.slide-fade-enter-active {
  transition: all .3s ease;
}
.slide-fade-leave-active {
  transition: all .8s cubic-bezier(1.0, 0.5, 0.8, 1.0);
}
.slide-fade-enter, .slide-fade-leave-to
/* .slide-fade-leave-active below version 2.1.8 */ {
  transform: translateX(10px);
  opacity: 0;
}
</style>
