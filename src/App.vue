<script setup>

</script>

<template>
  <div class="wrapper">
    <div class="todolist_wrapper">
      <h1 class="headline_1">ToDo</h1>
      <form class="todo_list">
        <ul>
          <li>
            <label for="task1">期末試験</label>
            <input type="checkbox" id="task1"/>
          </li>
          <li>
            <label for="task2">宿題</label>
            <input type="checkbox" id="task2"/>
          </li>
        </ul>
      </form>
      <form class="add_task_form">
      <input type="text" placeholder="新しいタスクを入力">
      <button>追加</button>
      </form>
    </div>
  </div>
</template>

<style scoped>
.wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
}

.todolist_wrapper {
  background-color: white;
  border-radius: 8px;
  box-shadow: 0 2px 4px lightblue;
  padding: 20px;
  height: 600px;
  width: 400px;
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
  background-color: lightgray; /* 灰色の背景 */
  flex-grow: 1; /* リストが増減しても灰色の部分が広がる */
  margin: 0; /* 不要なマージンを削除 */
  border-radius: 8px; /* 角を丸くする */
  margin-bottom: 20px; /* 下部のマージンを追加 */
}

.todo_list li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
  padding: 10px;
  border: 1px solid #eee;
  background-color: white;
  border-radius: 8px; /* 角を丸くする */
}

.todo_list label {
  flex-grow: 1;
  margin-right: 10px;
}

.todo_list input[type="checkbox"] {
  width: 18px;
  height: 18px;
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
</style>
