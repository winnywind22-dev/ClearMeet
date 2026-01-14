<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Task & Work Log</title>

<style>
/* base */
* {
  box-sizing: border-box;
  transition: all 0.2s ease;
}

body {
  font-family: Arial, sans-serif;
  background: #f4f6f8;
  margin: 0;
  padding: 20px;
}

.container {
  max-width: 900px;
  margin: auto;
  background: white;
  padding: 24px;
  border-radius: 12px;
  box-shadow: 0 8px 20px rgba(0,0,0,0.08);
}

h1 {
  text-align: center;
  margin-bottom: 20px;
}

/* form */
form {
  display: grid;
  gap: 12px;
  margin-bottom: 30px;
}

input, textarea {
  padding: 10px;
  font-size: 15px;
  border-radius: 8px;
  border: 1px solid #ccc;
}

textarea {
  min-height: 80px;
  resize: vertical;
}

input:focus, textarea:focus {
  outline: none;
  border-color: #4f46e5;
  box-shadow: 0 0 0 2px rgba(79,70,229,0.15);
}

button {
  padding: 12px;
  font-size: 16px;
  border-radius: 8px;
  border: none;
  background: #4f46e5;
  color: white;
  cursor: pointer;
}

button:hover {
  background: #4338ca;
}

button:active {
  transform: scale(0.97);
}

/* table */
table {
  width: 100%;
  border-collapse: collapse;
}

th, td {
  padding: 10px;
  border-bottom: 1px solid #ddd;
  text-align: left;
  font-size: 14px;
  vertical-align: top;
}

th {
  background: #f1f5f9;
}

tbody tr:hover {
  background: #f8fafc;
}

/* animation */
@keyframes fadeSlide {
  from {
    opacity: 0;
    transform: translateY(-8px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.new-task {
  animation: fadeSlide 0.3s ease;
}
</style>
</head>

<body>
<div class="container">
  <h1>Task & Work Log</h1>

  <form id="taskForm">
    <input type="text" id="title" placeholder="Task Title" required />
    <textarea id="content" placeholder="Task Details" required></textarea>
    <input type="text" id="notes" placeholder="Notes (optional)" />
    <button type="submit">Add Task</button>
  </form>

  <table>
    <thead>
      <tr>
        <th>Title</th>
        <th>Content</th>
        <th>Notes</th>
        <th>Date & Time</th>
      </tr>
    </thead>
    <tbody id="taskList"></tbody>
  </table>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
import { getDatabase, ref, push, onValue } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

/* 🔴 replace with real Firebase config */
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  databaseURL: "https://YOUR_PROJECT.firebaseio.com",
  projectId: "YOUR_PROJECT",
  storageBucket: "YOUR_PROJECT.appspot.com",
  messagingSenderId: "YOUR_ID",
  appId: "YOUR_APP_ID"
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);
const tasksRef = ref(db, "tasks");

const form = document.getElementById("taskForm");
const taskList = document.getElementById("taskList");

/* ADD TASK */
form.addEventListener("submit", e => {
  e.preventDefault();

  const title = document.getElementById("title").value;
  const content = document.getElementById("content").value;
  const notes = document.getElementById("notes").value || "-";
  const time = new Date().toLocaleString();

  // show immediately
  const row = document.createElement("tr");
  row.classList.add("new-task");
  row.innerHTML = `
    <td>${title}</td>
    <td>${content}</td>
    <td>${notes}</td>
    <td>${time}</td>
  `;
  taskList.prepend(row);

  // send to firebase
  try {
    push(tasksRef, { title, content, notes, time });
  } catch (e) {
    console.log("Firebase not connected");
  }

  form.reset();
});

/* LOAD FROM FIREBASE */
onValue(tasksRef, snapshot => {
  taskList.innerHTML = "";
  snapshot.forEach(child => {
    const task = child.val();
    taskList.innerHTML += `
      <tr>
        <td>${task.title}</td>
        <td>${task.content}</td>
        <td>${task.notes}</td>
        <td>${task.time}</td>
      </tr>
    `;
  });
});
</script>
</body>
</html>
