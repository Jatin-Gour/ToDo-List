# ToDo-List
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Simple ToDo List</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@400;600&display=swap');
  
  * {
    box-sizing: border-box;
  }

  body {
    background: linear-gradient(135deg, #667eea, #764ba2, #6b8dd6, #b38fec);
    background-size: 400% 400%;
    animation: gradientShift 15s ease infinite;
    font-family: 'Montserrat', sans-serif;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    min-height: 100vh;
    padding: 40px 20px;
    margin: 0;
    color: #fff;
  }

  @keyframes gradientShift {
    0% {
      background-position: 0% 50%;
    }
    50% {
      background-position: 100% 50%;
    }
    100% {
      background-position: 0% 50%;
    }
  }

  .todo-container {
    background: rgba(255 255 255 / 0.95);
    max-width: 480px;
    width: 100%;
    border-radius: 12px;
    box-shadow: 0 15px 30px rgba(0,0,0,0.25);
    padding: 30px 30px 40px;
    color: #333;
  }

  h1 {
    margin: 0 0 25px 0;
    font-weight: 600;
    font-size: 2.4rem;
    color: #212121;
    text-align: center;
  }

  .input-area {
    display: flex;
    gap: 12px;
    margin-bottom: 30px;
  }

  input[type="text"] {
    flex-grow: 1;
    padding: 14px 18px;
    border-radius: 8px;
    border: 2px solid #ddd;
    font-size: 1.1rem;
    transition: border-color 0.3s ease;
  }
  input[type="text"]:focus {
    outline: none;
    border-color: #6b8dd6;
    box-shadow: 0 0 6px rgba(107,141,214, 0.6);
  }

  button.add-btn {
    background-color: #6b8dd6;
    border: none;
    border-radius: 8px;
    padding: 14px 26px;
    font-weight: 600;
    font-size: 1.1rem;
    color: #fff;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }
  button.add-btn:hover {
    background-color: #546cb3;
  }
  button.add-btn:disabled {
    background-color: #a8c1f7;
    cursor: not-allowed;
  }

  ul.task-list {
    list-style: none;
    padding: 0;
    margin: 0;
    max-height: 400px;
    overflow-y: auto;
  }

  ul.task-list::-webkit-scrollbar {
    width: 8px;
  }
  ul.task-list::-webkit-scrollbar-thumb {
    background: #6b8dd644;
    border-radius: 10px;
  }
  ul.task-list::-webkit-scrollbar-track {
    background: transparent;
  }

  li.task-item {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 14px 18px;
    border-bottom: 1px solid #eee;
    transition: background-color 0.3s ease;
    animation: fadeInSlide 0.4s ease forwards;
  }
  li.task-item:last-child {
    border-bottom: none;
  }

  label.task-label {
    display: flex;
    align-items: center;
    gap: 16px;
    flex-grow: 1;
    cursor: pointer;
    user-select: none;
  }

  input[type="checkbox"] {
    width: 22px;
    height: 22px;
    cursor: pointer;
    accent-color: #6b8dd6;
  }

  span.task-text {
    font-size: 1.1rem;
    color: #444;
    transition: color 0.3s ease, text-decoration 0.3s ease;
  }

  li.task-item.completed span.task-text {
    text-decoration: line-through;
    color: #999;
  }

  button.delete-btn {
    background: transparent;
    border: none;
    color: #e03e3e;
    font-size: 1.3rem;
    cursor: pointer;
    padding: 4px 8px;
    border-radius: 6px;
    transition: background-color 0.2s ease;
  }
  button.delete-btn:hover {
    background-color: #e03e3e33;
  }

  
  @keyframes fadeInSlide {
    0% {
      opacity: 0;
      transform: translateX(-20px);
    }
    100% {
      opacity: 1;
      transform: translateX(0);
    }
  }

  @keyframes fadeOutSlide {
    0% {
      opacity: 1;
      transform: translateX(0);
      height: auto;
      padding-top: 14px;
      padding-bottom: 14px;
      margin-bottom: 0;
    }
    100% {
      opacity: 0;
      transform: translateX(20px);
      height: 0;
      padding-top: 0;
      padding-bottom: 0;
      margin-bottom: -18px;
    }
  }

  @media (max-width: 480px) {
    body {
      padding: 20px 12px;
    }
    .todo-container {
      padding: 25px 20px 35px;
      max-width: 100%;
    }
    input[type="text"] {
      font-size: 1rem;
      padding: 12px 14px;
    }
    button.add-btn {
      font-size: 1rem;
      padding: 12px 20px;
    }
    h1 {
      font-size: 1.8rem;
      margin-bottom: 20px;
    }
  }

</style>
</head>
<body>
  <div class="todo-container" role="main" aria-label="Simple ToDo List app">
    <h1>Simple ToDo List</h1>
    <form class="input-area" aria-label="Add new task">
      <input type="text" id="taskInput" placeholder="Add a new task..." aria-label="Task description" autocomplete="off" />
      <button type="submit" class="add-btn" aria-label="Add task button" disabled>Add</button>
    </form>
    <ul id="taskList" class="task-list" aria-live="polite" aria-relevant="additions removals"></ul>
  </div>
<script>
  (function() {
    const taskInput = document.getElementById('taskInput');
    const addBtn = document.querySelector('button.add-btn');
    const taskList = document.getElementById('taskList');

    function updateAddBtnState() {
      addBtn.disabled = taskInput.value.trim() === '';
    }

    function createTaskElement(taskText) {
      const li = document.createElement('li');
      li.className = 'task-item';

      const label = document.createElement('label');
      label.className = 'task-label';

      const checkbox = document.createElement('input');
      checkbox.type = 'checkbox';
      checkbox.setAttribute('aria-label', 'Mark task done');

      const span = document.createElement('span');
      span.className = 'task-text';
      span.textContent = taskText;

      label.appendChild(checkbox);
      label.appendChild(span);

      const deleteBtn = document.createElement('button');
      deleteBtn.className = 'delete-btn';
      deleteBtn.setAttribute('aria-label', 'Delete task');
      deleteBtn.innerHTML = '&times;';

      
      checkbox.addEventListener('change', (e) => {
        if (checkbox.checked) {
          li.classList.add('completed');
        } else {
          li.classList.remove('completed');
        }
      });

      deleteBtn.addEventListener('click', (e) => {
        e.preventDefault();
        
        li.style.animation = 'fadeOutSlide 0.5s forwards';
        li.addEventListener('animationend', () => {
          if (li.parentNode) {
            taskList.removeChild(li);
          }
        });
      });

      li.appendChild(label);
      li.appendChild(deleteBtn);

      return li;
    }

    
    function addTask(taskText) {
      const taskElement = createTaskElement(taskText);
      taskList.appendChild(taskElement);
    }

    
    document.querySelector('form').addEventListener('submit', (e) => {
      e.preventDefault();
      const newTask = taskInput.value.trim();
      if (newTask === '') return;
      addTask(newTask);
      taskInput.value = '';
      updateAddBtnState();
      taskInput.focus();
    });

    
    taskInput.addEventListener('input', updateAddBtnState);
  })();
</script>
</body>
</html>

