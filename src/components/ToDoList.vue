<script setup>
import { reactive, ref } from "@vue/reactivity";

// ToDoリストの初期値
const toDolist = reactive([
  {
    title: "プログラミングテスト",
    isDone: false
  },
  {
    title: "ランニング",
    isDone: true
  },
]);

const changeColor = (index) => {
  toDolist[index].isDone = !toDolist[index].isDone;
};

// ドラッグ中のタスクを追跡するための変数
let draggedItemIndex = null;

const onDragStart = (index) => {
  draggedItemIndex = index;
};

const onDragOver = (event) => {
  event.preventDefault(); // ドロップを許可するためにデフォルト動作をキャンセル
};

const onDrop = (index) => {
  if (draggedItemIndex !== null && draggedItemIndex !== index) {
    const draggedItem = toDolist[draggedItemIndex];
    toDolist.splice(draggedItemIndex, 1); // 元の位置から削除
    toDolist.splice(index, 0, draggedItem); // 新しい位置に挿入
    draggedItemIndex = null; // リセット
  }
};

// 新しいタスクのタイトルを保持用の変数
const newTask = ref('');

const addList = () =>{
  if (newTask.value.trim() !=='') {
    toDolist.push({
      title: newTask.value,
      isDone: false
    });
    newTask.value = '';
  }
};
</script>

<template>
  <div class="wrapper">
    <div class="todolist_wrapper">
      <h1 class="headline_1">ToDo</h1>
      <form class="todo_list">
        <ul>
          <li
              v-for="(task, index) in toDolist"
              :key="index"
              :class="{'list-changeColor' : task.isDone }"
              @click="changeColor(index)"
              draggable="true"
              @dragstart="onDragStart(index)"
              @dragover="onDragOver"
              @drop="onDrop(index)"
          >
            <label for="task {{index}}">{{ task.title }}</label>
            <input v-if="task.isDone" type="checkbox" checked>
            <input v-else type="checkbox">
          </li>
        </ul>
      </form>
      <form class="add_task_form" @submit.prevent="addList">
        <input v-model="newTask" type="text" placeholder="新しいタスクを入力">
        <button type="submit">追加</button>
      </form>
    </div>
  </div>
</template>

<style scoped>
.wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
}

.todolist_wrapper {
  background-color: white;
  border-radius: 8px;
  box-shadow: 0 2px 4px lightblue;
  padding: 20px;
  height: 90vh;
  width: 80vh;
  display: flex;
  flex-direction: column;
}

.headline_1 {
  text-align: center;
  color: #333;
  margin-top: 0;
  margin-bottom: 20px;
}

.todo_list {
  flex-grow: 1;
  display: flex;
  flex-direction: column;
}

.todo_list ul {
  list-style-type: none;
  padding: 20px;
  overflow-y: auto;
  background-color: lightgray;
  flex-grow: 1;
  margin: 0;
  border-radius: 8px;
  margin-bottom: 20px;
}

.todo_list li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
  padding: 10px;
  border: 1px solid #eee;
  background-color: white;
  border-radius: 8px;
  cursor: move; /* カーソルをドラッグ中に変更 */
}

.todo_list label {
  flex-grow: 1;
  margin-right: 15px;
}

.todo_list input[type="checkbox"] {
  width: 17px;
  height: 17px;
}

.add_task_form {
  margin-top: auto;
  display: flex;
  gap: 10px;
}

.add_task_form input[type="text"] {
  flex-grow: 1;
  padding: 8px;
  border: 1px solid #ddd;
  border-radius: 4px;
}

.add_task_form button {
  padding: 8px 15px;
  background-color: cadetblue;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.add_task_form button:hover {
  background-color: darkcyan;
}

.list-changeColor {
  background-color: powderblue !important;
  border: 1px solid powderblue !important;
  color: white !important;
}
</style>
