<script setup>
const props = defineProps({
  filterList: {
    type: Array,
    require: true,
  },
  token: {
    type: String,
    require: true,
  },
})
const emit = defineEmits(['change-todo-status', 'edit-todo', 'save-todo', 'delete-todo'])
//更新清單狀態
const changeTodoStatus = (id) => {
  emit('change-todo-status', id)
}
const saveTodo = (id) => {
  emit('save-todo', id)
}
//刪除清單
const deleteTodo = (id) => {
  emit('delete-todo', id)
}
//開啟編輯
const editTodo = (id) => {
  const exitTodo = props.filterList.find((todo) => todo.id === id)
  exitTodo.isEditng = true
}
//取消編輯
const cancelEdit = (id) => {
  const exitTodo = props.filterList.find((todo) => todo.id === id)
  exitTodo.tempContent = exitTodo.content
  exitTodo.isEditng = false
}
</script>
<template>
  <ul class="todoList_item">
    <li v-for="item in props.filterList" :key="item.id">
      <label class="todoList_label">
        <!-- 清單狀態 -->
        <div v-if="item.isEditng">
          <input
            class="todoList_input"
            type="checkbox"
            @click="changeTodoStatus(item.id)"
            disabled
          />
        </div>
        <div v-else-if="item.status">
          <input
            class="todoList_input"
            type="checkbox"
            checked
            @click="changeTodoStatus(item.id)"
          />
        </div>
        <div v-else>
          <input class="todoList_input" type="checkbox" @click="changeTodoStatus(item.id)" />
        </div>
        <!-- 清單內容 -->
        <div v-if="item.isEditng">
          <input type="text" v-model="item.tempContent" />
        </div>
        <div v-else-if="item.status">
          <del style="color: gray">{{ item.content }}</del>
        </div>
        <div v-else>
          <span>{{ item.content }}</span>
        </div>
      </label>
      <template v-if="item.isEditng">
        <a href="#" @click.prevent="saveTodo(item.id)" style="color: green; text-decoration: none"
          >SAVE</a
        >
        <a href="#" @click.prevent="cancelEdit(item.id)" style="color: red; text-decoration: none"
          >CANCEL</a
        >
      </template>
      <template v-else>
        <a href="#" @click.prevent="editTodo(item.id)" style="text-decoration: none">EDIT</a>
        <a href="#" @click.prevent="deleteTodo(item.id)" style="text-decoration: none">Ｘ</a>
      </template>
    </li>
  </ul>
</template>
