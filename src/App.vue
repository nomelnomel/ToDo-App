<template>
  <v-app id="app">
    <v-main>
      <v-container>
        <v-row justify="center">
          <v-col xs="12" sm="8">
            <v-card>
              <v-toolbar color="primary" dark flat>
                <v-toolbar-title>ToDo App</v-toolbar-title>
                <v-spacer></v-spacer>
                <v-btn icon
                       @click="openSearch()"
                       v-if="!searchOpen">
                  <v-icon>
                    mdi-magnify
                  </v-icon>
                </v-btn>
                <v-text-field
                    label="Search task"
                    v-if="searchOpen"
                    class="mt-6 animate__animated"
                    :class="searchAnime"
                    v-model="search"
                    autofocus
                    @blur="closeSearch()"
                    @keyup.esc="closeSearch()"
                />
              </v-toolbar>

              <v-list two-line subheader>
                <v-subheader class="headline">
                  {{ counter }} task{{ counter === 1 ? '' : 's' }}
                </v-subheader>

                <v-list-item>
                  <v-list-item-content>
                    <v-list-item-title>
                      <v-text-field
                          label="Write new task"
                          v-model="newTodo"
                          @keyup.enter="addTodo"/>
                    </v-list-item-title>
                  </v-list-item-content>
                </v-list-item>
              </v-list>

              <v-list v-if="!isEmpty">
                <v-list-item
                    v-for="(todo, i) in filteredTasks"
                    :key="i"
                >
                  <template v-if="!todo.edit">
                    <v-list-item-action>
                      <v-checkbox v-model="todo.checked"></v-checkbox>
                    </v-list-item-action>
                    <v-list-item-title
                        :class="{idsone : todo.checked}"
                        @dblclick="editTodo(i)"
                    >
                      {{ todo.text }}
                    </v-list-item-title>
                    <v-btn
                        fab
                        small
                        color="error"
                        v-if="todo.checked"
                        @click="removeTodo(i)"
                    >
                      <v-icon class="white--text">
                        mdi-close
                      </v-icon>
                    </v-btn>
                  </template>
                  <template v-else>
                    <v-text-field
                        :value="todoBeforeEdit"
                        v-model="editingText"
                        @keyup.enter="doneEdit"
                        @blur="doneEdit"
                        @keyup.esc="cancelEdit"
                        label="Press Enter or click outside to approve edit / press Esc to cancel"
                        autofocus
                    ></v-text-field>
                  </template>

                </v-list-item>
              </v-list>
              <v-list v-else>
                <v-list-item>
                  <v-list-item-title class="title" >
                    There is no tasks
                  </v-list-item-title>
                </v-list-item>
              </v-list>
              <v-layout>
                <v-btn
                    class="ml-5 mb-5"
                    @click="checkAll"
                    :disabled="counter === 0">
                  Check all
                </v-btn>
                <v-spacer></v-spacer>
                <v-btn
                    class="mr-5 mb-5"
                    :disabled="!checked"
                    @click="clearChecked">
                  Clear checked
                </v-btn>
              </v-layout>
            </v-card>
          </v-col>
        </v-row>

      </v-container>
    </v-main>
  </v-app>
</template>

<script>


export default {
  name: 'ToDoApp',
  data() {
    return {
      newTodo: '',
      todoBeforeEdit: '',
      editingText:'',
      todoIndex: null,
      searchOpen: false,
      searchClose: false,
      search: '',
      todos: [
        {
          text: 'zxczxc',
          checked: false,
          edit: false
        },
        {
          text: 'qweqwe',
          checked: false,
          edit: false
        }
      ],
      active: false,
    }
  },
  methods: {
    addTodo() {
      if (this.newTodo && this.newTodo.trim()){
        let todo = {
          text: this.newTodo,
          checked: false,
          edit: false
        }
        this.todos.push(todo)
        this.newTodo = ''
      }
    },
    removeTodo(i) {
      this.todos.splice(i, 1)
    },
    clearChecked() {
      this.todos = this.todos.filter(todo => todo.checked === false
      )
    },
    checkAll() {
      this.todos = this.todos.map(todo => {
        todo.checked = true
        return todo
      })
    },
    editTodo(i){
      this.todoBeforeEdit = this.todos[i].text
      this.todoIndex = i
      this.todos[i].edit = true
      this.editingText = this.todoBeforeEdit
    },
    doneEdit(){
      this.todos[this.todoIndex].text = this.editingText
      this.todos[this.todoIndex].edit = false
    },
    cancelEdit(){
      this.todos[this.todoIndex].text = this.todoBeforeEdit
      this.todos[this.todoIndex].edit = false
    },
    openSearch(){
      this.searchClose = false
      this.searchOpen = true
    },
    closeSearch(){
      this.searchClose = true
      setTimeout(()=>{
        this.searchOpen = false
        this.search = ''
      }, 750)
    }
  },
  computed: {
    counter() {
      return this.todos.length
    },
    checked() {
      return this.todos.filter(todo => todo.checked).length > 0
    },
    filteredTasks(){
      let newList = {}
      if(this.search !== ''){
        newList = this.todos.filter(todo => todo.text.toLowerCase().indexOf(this.search.toLowerCase()) > -1)
      }
      else newList = this.todos
      return newList
    },
    isEmpty(){
      return this.filteredTasks.length === 0
    },
    searchAnime(){
      return{
        'animate__backInRight' : this.searchOpen,
        'animate__backOutRight': this.searchClose
      }
    }
  },
}
</script>

<style>

.idsone {
  text-decoration: line-through;
}

.v-list-item__title {
  white-space: normal !important;
}

</style>
