<template>
  <div class="my-todolist">
    <div class="big-head">
			<div id="add-item" class="col-md-6 col-sm-10 col-xs-10">
        <h1 class="text-center">My Todo List</h1>
        <input type="text" class="task-input" placeholder="在这里填写一个任务，回车即可完成添加" v-model="todo" @keyup.enter="addTodo">
			</div>
		</div>

    <div class="main col-md-6 col-sm-10 col-xs-10">
      
      <ul class="task-count">
        <li class="action">
          <button class="btn" v-for="item in hashItems" :class="{active: item.isActive}" @click="handleHash(item)">{{item.title}}</button>
        </li>
      </ul>

      <div class="tasks">
        <span class="no-task-tip" v-if="list.length==0">还没有添加任何任务</span>
        <ul class="todo-list">
          <li class="todo" v-for="(item, index) in filterList"
              :class="{completed: item.isChecked, editing: isEditorItem(item)}">
            <div class="view">
              <input class="toggle" type="checkbox" v-model="item.isChecked">
              <label @dblclick="editTodo(item)">{{item.title}}</label>
              <button @click="deleteTodo(index)" class="destroy"></button>
            </div>
            <input
              v-focus="isEditorItem(item)"
              @blur="editCompleted(item)"
              type="text"
              class="edit"
              v-model="item.title"
              @keyup.enter="editCompleted"
              @keyup.esc="cancelEdit(item)">
          </li>
        </ul>
      </div>
    </div>
    <div class="footer">
      Made with love by yammy
    </div>
  </div>
</template>

<script>
  let store = {
    save (key, value) {
      localStorage.setItem(key, JSON.stringify(value))
    },
    fetch (key) {
      return JSON.parse(localStorage.getItem(key)) || []
    }
  }
  let list = store.fetch('all')
  let filter = {
    all: function (list) {
      return list
    },
    finished: function (list) {
      return list.filter(function (item) {
        return item.isChecked
      })
    },
    unfinished: function () {
      return list.filter(function (item) {
        return !item.isChecked
      })
    }
  }
  export default {
    name: 'todoList',
    data () {
      return {
        todo: '',
        list: list,
        editorTodoItem: null,
        beforeTodoContent: '',
        hash: 'all',
        hashItems: [
          {
            title: '所有任务',
            isActive: true,
            hash: 'all'
          },
          {
            title: '未完成的任务',
            isActive: false,
            hash: 'unfinished'
          },
          {
            title: '完成的任务',
            isActive: false,
            hash: 'finished'
          }
        ]
      }
    },
    methods: {
      addTodo () {
        let item = {title: this.todo, isChecked: false}
        this.list.push(item)
        this.todo = ''
      },
      deleteTodo (index) {
        this.list.splice(index, 1)
      },
      editTodo (item) {
        this.beforeTodoContent = item.title
        this.editorTodoItem = item
      },
      isEditorItem (item) {
        return item === this.editorTodoItem
      },
      editCompleted () {
        this.editorTodoItem = null
      },
      cancelEdit (item) {
        item.title = this.beforeTodoContent
        this.editorTodoItem = null
      },
      handleHash (item) {
        for (let i = 0; i < this.hashItems.length; i++) {
          this.hashItems[i].isActive = false
        }
        item.isActive = true
        this.hash = item.hash
      }
    },
    computed: {
      undoListLength: function () {
        let count = 0
        for (let i = 0; i < this.list.length; i++) {
          if (!this.list[i].isChecked) {
            count++
          }
        }
        return count
      },
      filterList: function () {
        return filter[this.hash] ? filter[this.hash](this.list) : this.list
      }
    },
    directives: {
      'focus': {
        update (el, binding) {
          if (binding.value) {
            el.focus()
          }
        }
      }
    },
    watch: {
      list: {
        handler: function () {
          store.save('all', this.list)
        },
        deep: true
      }
    }
  }
</script>

<style>
  .my-todolist {
    display: flex;
    min-height: 100vh;
    flex-direction: column;
  }
  .big-head {
    display: flex;
    justify-content: center;
    padding: 5em 0;
    /* background-color: #f5f2f0; */
  }
  input {
    outline: 0;
  }
  ul {
    padding: 0;
    margin: 0;
    list-style: none;
  }
  .main {
    margin: 0px auto;
    flex: 1;
  }
  .task-input {
    width: 99%;
    height: 30px;
    outline: 0;
    border: 1px solid #ccc;
  }
  .task-count {
    display: flex;
    margin: 10px 0;
  }
  .task-count li {
    padding-left: 10px;
    flex: 1;
    color: #dd4b39;
  }
  .task-count li:nth-child(1) {
    padding: 5px 0 0 10px;
  }
  .action {
    text-align: center;
    display: flex;
  }
  .action button {
    margin: 0px 10px;
    flex: 1;
    padding: 5px 0;
  }
  .action a:nth-child(3) {
    margin-right: 0;
  }
  .active {
    border: 1px solid rgba(175, 47, 47, 0.2);
  }
  .no-task-tip {
    padding: 10px 0 10px 10px;
    display: block;
    border-bottom: 1px solid #ededed;
    color: #777;
  }
  .big-title {
    color: #222;
  }
  .todo-list {
    margin: 0;
    padding: 0;
    list-style: none;
  }
  .todo-list li {
    position: relative;
    font-size: 16px;
    border-bottom: 1px solid #ededed;
  }
  .todo-list li:hover {
    background-color: #fafafa;
  }
  .todo-list li.editing {
    border-bottom: none;
    padding: 0;
  }
  .todo-list li.editing .edit {
    display: block;
    width: 506px;
    padding: 13px 17px 12px 17px;
    margin: 0 0 0 43px;
  }
  .todo-list li.editing .view {
    display: none;
  }
  .todo-list li .toggle {
    text-align: center;
    width: 40px;
    /* auto, since non-WebKit browsers doesn't support input styling */
    height: auto;
    position: absolute;
    top: 5px;
    bottom: 0;
    margin: auto 0;
    border: none; /* Mobile Safari */
    -webkit-appearance: none;
    appearance: none;
  }
  .toggle {
    text-align: center;
    width: 40px;
    /* auto, since non-WebKit browsers doesn't support input styling */
    height: auto;
    position: absolute;
    top: 5px;
    bottom: 0;
    margin: auto 0;
    border: none; /* Mobile Safari */
    -webkit-appearance: none;
    appearance: none;
  }
  .toggle:after {
    content: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" viewBox="-10 -18 100 135"><circle cx="50" cy="50" r="40" fill="none" stroke="#ededed" stroke-width="3"/></svg>');
  }
  .toggle:checked:after {
    content: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" viewBox="-10 -18 100 135"><circle cx="50" cy="50" r="40" fill="none" stroke="#bddad5" stroke-width="3"/><path fill="#5dc2af" d="M72 25L42 71 27 56l-4 4 20 20 34-52z"/></svg>');
  }
  .todo-list li label {
    white-space: pre-line;
    word-break: break-all;
    padding: 15px 60px 15px 15px;
    margin-left: 45px;
    display: block;
    line-height: 1.2;
    transition: color 0.4s;
  }
  .todo-list li.completed label {
    color: #d9d9d9;
    text-decoration: line-through;
  }
  .todo-list li .destroy {
    display: none;
    position: absolute;
    top: 0;
    right: 10px;
    bottom: 0;
    width: 40px;
    height: 40px;
    font-size: 30px;
    color: #cc9a9a;
    margin: auto 0 11px;
    transition: color 0.2s ease-out;
  }
  .todo-list li .destroy:hover {
    color: #af5b5e;
  }
  .todo-list li .destroy:after {
    content: '×';
  }
  .todo-list li:hover .destroy {
    display: block;
  }
  .todo-list li .edit {
    display: none;
  }
  .todo-list li.editing:last-child {
    margin-bottom: -1px;
  }
  .footer {
    display: flex;
    justify-content: center;
    padding: 2em 0;
    background-color: #f5f2f0;
  }
</style>