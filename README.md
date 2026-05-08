# Ex03 To-Do List using JavaScript
## Date:08.05.2026

## AIM
To create a To-do Application with all features using JavaScript.

## ALGORITHM
### STEP 1
Build the HTML structure (index.html).

### STEP 2
Style the App (style.css).

### STEP 3
Plan the features the To-Do App should have.

### STEP 4
Create a To-do application using Javascript.

### STEP 5
Add functionalities.

### STEP 6
Test the App.

### STEP 7
Open the HTML file in a browser to check layout and functionality.

### STEP 8
Fix styling issues and refine content placement.

### STEP 9
Deploy the website.

### STEP 10
Upload to GitHub Pages for free hosting.

## PROGRAM

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Advanced Todo App</title>

  <style>
    *{
      margin:0;
      padding:0;
      box-sizing:border-box;
      font-family:Arial, Helvetica, sans-serif;
    }

    body{
      background:linear-gradient(135deg,#667eea,#764ba2);
      min-height:100vh;
      display:flex;
      justify-content:center;
      align-items:flex-start;
      padding:40px 15px;
    }

    .container{
      width:100%;
      max-width:700px;
      background:#fff;
      border-radius:20px;
      padding:25px;
      box-shadow:0 10px 30px rgba(0,0,0,0.2);
    }

    h1{
      text-align:center;
      margin-bottom:20px;
      color:#333;
    }

    .input-section{
      display:flex;
      gap:10px;
      margin-bottom:20px;
    }

    .input-section input{
      flex:1;
      padding:12px;
      border:2px solid #ddd;
      border-radius:10px;
      font-size:16px;
    }

    .input-section button{
      padding:12px 20px;
      border:none;
      background:#667eea;
      color:white;
      border-radius:10px;
      cursor:pointer;
      font-size:16px;
      transition:0.3s;
    }

    .input-section button:hover{
      background:#5a67d8;
    }

    .filters{
      display:flex;
      justify-content:center;
      gap:10px;
      margin-bottom:20px;
      flex-wrap:wrap;
    }

    .filters button{
      padding:8px 14px;
      border:none;
      border-radius:8px;
      background:#eee;
      cursor:pointer;
      transition:0.3s;
    }

    .filters button.active{
      background:#667eea;
      color:white;
    }

    .todo{
      background:#f7f7f7;
      padding:15px;
      border-radius:12px;
      display:flex;
      justify-content:space-between;
      align-items:center;
      margin-bottom:12px;
      transition:0.3s;
    }

    .todo.completed{
      opacity:0.7;
      text-decoration:line-through;
    }

    .todo-left{
      display:flex;
      align-items:center;
      gap:12px;
      flex:1;
    }

    .todo-text{
      font-size:17px;
      word-break:break-word;
    }

    .actions{
      display:flex;
      gap:8px;
    }

    .actions button{
      border:none;
      padding:8px 10px;
      border-radius:8px;
      cursor:pointer;
      color:white;
    }

    .edit-btn{
      background:#f6ad55;
    }

    .delete-btn{
      background:#e53e3e;
    }

    .stats{
      margin-top:20px;
      text-align:center;
      color:#444;
      font-weight:bold;
    }

    .clear-btn{
      width:100%;
      margin-top:20px;
      padding:12px;
      border:none;
      background:#e53e3e;
      color:white;
      border-radius:10px;
      cursor:pointer;
      font-size:16px;
    }

    .empty{
      text-align:center;
      color:#777;
      margin-top:20px;
    }

    @media(max-width:500px){
      .input-section{
        flex-direction:column;
      }
    }
  </style>
</head>

<body>

  <div class="container">
    <h1>📝 Todo Application</h1>

    <div class="input-section">
      <input type="text" id="todoInput" placeholder="Enter your task..." />
      <button onclick="addTodo()">Add</button>
    </div>

    <div class="filters">
      <button class="filter active" onclick="filterTodos('all')">All</button>
      <button class="filter" onclick="filterTodos('completed')">Completed</button>
      <button class="filter" onclick="filterTodos('pending')">Pending</button>
    </div>

    <div id="todoList"></div>

    <div class="stats" id="stats"></div>

    <button class="clear-btn" onclick="clearCompleted()">
      Clear Completed
    </button>
  </div>

  <script>
    let todos = JSON.parse(localStorage.getItem("todos")) || [];
    let currentFilter = "all";

    function saveTodos() {
      localStorage.setItem("todos", JSON.stringify(todos));
    }

    function addTodo() {
      const input = document.getElementById("todoInput");
      const text = input.value.trim();

      if (text === "") {
        alert("Please enter a task");
        return;
      }

      todos.push({
        id: Date.now(),
        text,
        completed: false
      });

      input.value = "";
      saveTodos();
      renderTodos();
    }

    function renderTodos() {
      const todoList = document.getElementById("todoList");
      todoList.innerHTML = "";

      let filteredTodos = todos;

      if (currentFilter === "completed") {
        filteredTodos = todos.filter(todo => todo.completed);
      } else if (currentFilter === "pending") {
        filteredTodos = todos.filter(todo => !todo.completed);
      }

      if (filteredTodos.length === 0) {
        todoList.innerHTML = `
          <div class="empty">No tasks found 😴</div>
        `;
      }

      filteredTodos.forEach(todo => {
        const div = document.createElement("div");
        div.className = `todo ${todo.completed ? "completed" : ""}`;

        div.innerHTML = `
          <div class="todo-left">
            <input type="checkbox"
              ${todo.completed ? "checked" : ""}
              onchange="toggleTodo(${todo.id})"
            />

            <span class="todo-text">${todo.text}</span>
          </div>

          <div class="actions">
            <button class="edit-btn" onclick="editTodo(${todo.id})">
              Edit
            </button>

            <button class="delete-btn" onclick="deleteTodo(${todo.id})">
              Delete
            </button>
          </div>
        `;

        todoList.appendChild(div);
      });

      updateStats();
    }

    function toggleTodo(id) {
      todos = todos.map(todo => {
        if (todo.id === id) {
          return {
            ...todo,
            completed: !todo.completed
          };
        }
        return todo;
      });

      saveTodos();
      renderTodos();
    }

    function deleteTodo(id) {
      todos = todos.filter(todo => todo.id !== id);

      saveTodos();
      renderTodos();
    }

    function editTodo(id) {
      const todo = todos.find(todo => todo.id === id);

      const updatedText = prompt("Edit your task:", todo.text);

      if (updatedText !== null && updatedText.trim() !== "") {
        todo.text = updatedText.trim();

        saveTodos();
        renderTodos();
      }
    }

    function filterTodos(filter) {
      currentFilter = filter;

      document.querySelectorAll(".filter").forEach(btn => {
        btn.classList.remove("active");
      });

      event.target.classList.add("active");

      renderTodos();
    }

    function clearCompleted() {
      todos = todos.filter(todo => !todo.completed);

      saveTodos();
      renderTodos();
    }

    function updateStats() {
      const total = todos.length;
      const completed = todos.filter(todo => todo.completed).length;
      const pending = total - completed;

      document.getElementById("stats").innerHTML = `
        Total: ${total} | Completed: ${completed} | Pending: ${pending}
      `;
    }

    document.getElementById("todoInput")
      .addEventListener("keypress", function(e) {
        if (e.key === "Enter") {
          addTodo();
        }
      });

    renderTodos();
  </script>

</body>
</html>
```


## OUTPUT
<img width="1915" height="959" alt="image" src="https://github.com/user-attachments/assets/df789854-4b52-4b81-88ec-ed16bf4f032e" />



## RESULT
The program for creating To-do list using JavaScript is executed successfully.
