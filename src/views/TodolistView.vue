<script setup>
import { onMounted, ref, computed } from 'vue'
import { useRouter } from 'vue-router'
import axios from 'axios'
import TheTodolist from '@/components/TheTodolist.vue'

const router = useRouter()
const apiUrl = 'https://todolist-api.hexschool.io'
const token = ref('')
const user = ref({
  nickname: '',
  uid: '',
})
const todos = ref([])
const tempTodo = ref('')

onMounted(async () => {
  try {
    await fetchUserData()
    await getTodos()
  } catch (error) {
    alert(`${error.response.data.message}`)
  }
})

//驗證
const fetchUserData = async () => {
  try {
    token.value = document.cookie.replace(/(?:^|.*;\s*)customTodoToken\s*=\s*([^;]*).*$/i, '$1')
    if (token.value) {
      const res = await axios.get(`${apiUrl}/users/checkout`, {
        headers: {
          Authorization: token.value,
        },
      })
      user.value.nickname = res.data.nickname
      user.value.uid = res.data.uid
      await getTodos()
    } else {
      router.push('/')
    }
  } catch (error) {
    alert(`${error.response.data.message}`)
  }
}
//待完成數量
const PendingTodosSum = computed(() => {
  const pendingTodos = todos.value.filter((item) => !item.status)
  return pendingTodos.length
})
//登出
const signout = async () => {
  try {
    const res = await axios.post(
      `${apiUrl}/users/sign_out`,
      {},
      {
        headers: {
          Authorization: token.value,
        },
      },
    )
    document.cookie = `customTodoToken=;expires=Thu, 01 Jan 1970 00:00:00 UTC;path=;`
    token.value = null
    alert(`${res.data.message}`)
    await fetchUserData()
  } catch (error) {
    alert(`${error.response.data.message}`)
  }
}
//新增todo
const addTodo = async () => {
  try {
    const res = await axios.post(
      `${apiUrl}/todos/`,
      {
        content: tempTodo.value.trim(),
      },
      {
        headers: {
          Authorization: token.value,
        },
      },
    )
    tempTodo.value = ''
    await getTodos()
    alert(`${res.data.newTodo.content} 新增待辦成功`)
  } catch (error) {
    alert(`${error.response.data.message}`)
  }
}
//讀取todo
const getTodos = async () => {
  try {
    const res = await axios.get(`${apiUrl}/todos/`, {
      headers: {
        Authorization: token.value,
      },
    })
    const todosResult = res.data.data.map((item) => {
      return {
        ...item,
        tempContent: item.content,
        isEditing: false,
      }
    })
    todos.value = todosResult
  } catch (error) {
    alert(`${error.response.data.message}`)
  }
}
//切換待辦狀態
const changeTodoStatus = async (id) => {
  try {
    const res = await axios.patch(
      `${apiUrl}/todos/${id}/toggle`,
      {},
      {
        headers: {
          Authorization: token.value,
        },
      },
    )
    alert(`${res.data.message}`)
    await getTodos()
  } catch (error) {
    alert(`${error.response.data.message}`)
  }
}
//儲存更新清單
const saveTodo = async (id) => {
  try {
    const exitTodo = filterList.value.find((todo) => todo.id === id)
    const res = await axios.put(
      `${apiUrl}/todos/${id}`,
      {
        content: exitTodo.tempContent.trim(),
      },
      {
        headers: {
          Authorization: token.value,
        },
      },
    )
    exitTodo.isEditng = false
    alert(`${res.data.message}`)
    await getTodos()
  } catch (error) {
    alert(`${error.response.data.message}`)
  }
}
//刪除待辦
const deleteTodo = async (id) => {
  try {
    const res = await axios.delete(`${apiUrl}/todos/${id}`, {
      headers: {
        Authorization: token.value,
      },
    })
    alert(res.data.message)
    await fetchUserData()
  } catch (error) {
    alert(`${error.response.data.message}`)
  }
}
//篩選待辦
const filter = ref('all')
const filterList = computed(() => {
  if (filter.value === 'todo') {
    return todos.value.filter((todo) => todo.status === false)
  } else if (filter.value === 'done') {
    return todos.value.filter((todo) => todo.status === true)
  } else {
    return todos.value
  }
})
</script>
<template>
  <div id="todoListPage" class="bg-half">
    <nav>
      <h1><a href="#">ONLINE TODO LIST</a></h1>
      <ul>
        <li class="todo_sm">
          <a href="#"
            ><span>{{ user.nickname }}的代辦</span></a
          >
        </li>
        <li><a href="#" @click="signout">登出</a></li>
      </ul>
    </nav>
    <div class="conatiner todoListPage vhContainer">
      <div class="todoList_Content">
        <div class="inputBox">
          <input type="text" placeholder="請輸入待辦事項" v-model="tempTodo" />
          <a href="#" @click.prevent="addTodo">
            <i class="fa fa-plus">+</i>
          </a>
        </div>
        <div v-if="todos.length === 0">
          <p style="text-align: center; font-size: 16px; color: #333333; padding-top: 20px">
            目前尚無待辦事項
          </p>
        </div>
        <div class="todoList_list" v-else>
          <ul class="todoList_tab">
            <li>
              <a href="#" @click.prevent="filter = 'all'" :class="{ active: filter === 'all' }"
                >全部</a
              >
            </li>
            <li>
              <a href="#" @click.prevent="filter = 'todo'" :class="{ active: filter === 'todo' }"
                >待完成</a
              >
            </li>
            <li>
              <a href="#" @click.prevent="filter = 'done'" :class="{ active: filter === 'done' }"
                >已完成</a
              >
            </li>
          </ul>
          <div class="todoList_items">
            <TheTodolist
              :filterList="filterList"
              :token="token"
              @change-todo-status="changeTodoStatus"
              @save-todo="saveTodo"
              @delete-todo="deleteTodo"
            />
            <div class="todoList_statistics">
              <p>{{ PendingTodosSum }} 個待完成項目</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
