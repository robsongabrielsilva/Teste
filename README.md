<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Checklist Funcional Semanal</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      background: linear-gradient(to bottom, #e0f7fa, #ffffff);
      margin: 0;
      padding: 0;
    }
    header {
      background: #1976d2;
      color: white;
      padding: 15px;
      text-align: center;
      box-shadow: 0 4px 8px rgba(0,0,0,0.1);
    }
    .container {
      padding: 20px;
    }
    .day-selector {
      display: flex;
      justify-content: space-around;
      margin-bottom: 20px;
      flex-wrap: wrap;
    }
    .day-button {
      padding: 10px 15px;
      border: none;
      background: #2196F3;
      color: white;
      cursor: pointer;
      border-radius: 10px;
      margin: 5px;
      box-shadow: 2px 2px 5px rgba(0,0,0,0.2);
    }
    .checklist-section {
      margin-bottom: 30px;
      border-radius: 10px;
      padding: 15px;
      color: white;
      box-shadow: 2px 2px 10px rgba(0,0,0,0.15);
    }
    .manha { background: linear-gradient(to right, #81d4fa, #b3e5fc); }
    .tarde { background: linear-gradient(to right, #ffd54f, #ffb74d); }
    .noite { background: linear-gradient(to right, #37474f, #263238); }

    h2 {
      margin-top: 0;
    }
    .task {
      display: flex;
      align-items: center;
      margin: 8px 0;
      background: white;
      padding: 8px;
      border-radius: 8px;
      box-shadow: 0 2px 5px rgba(0,0,0,0.1);
    }
    .task input[type="text"] {
      flex: 1;
      margin-left: 10px;
      padding: 5px;
      border: none;
      border-radius: 4px;
      font-size: 14px;
    }
    .task input[type="checkbox"] {
      width: 20px;
      height: 20px;
    }
    .buttons {
      display: flex;
      gap: 5px;
      margin-left: 10px;
    }
    .buttons button {
      padding: 4px 6px;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }
    .add-btn { background-color: #4CAF50; color: white; margin-top: 10px; padding: 6px 10px; border-radius: 6px; }
    .del-btn { background-color: #f44336; color: white; }
    .move-btn { background-color: #FF9800; color: white; }
  </style>
</head>
<body>
  <header>
    <h1>Checklist Funcional Semanal</h1>
  </header>
  <div class="container">
    <div class="day-selector" id="daySelector"></div>
    <div id="checklistContainer"></div>
  </div>
  <script>
    const days = ['Domingo','Segunda','Terça','Quarta','Quinta','Sexta','Sábado'];
    const periods = ['Manhã','Tarde','Noite'];
    let currentDay = new Date().getDay();

    const daySelector = document.getElementById('daySelector');
    const checklistContainer = document.getElementById('checklistContainer');

    function loadChecklist(dayIndex) {
      currentDay = dayIndex;
      checklistContainer.innerHTML = '';

      periods.forEach(period => {
        const section = document.createElement('div');
        section.classList.add('checklist-section');
        section.classList.add(period.toLowerCase());

        const title = document.createElement('h2');
        title.textContent = `${period} - ${days[dayIndex]}`;
        section.appendChild(title);

        const taskList = document.createElement('div');
        taskList.id = `${days[dayIndex]}-${period}`;

        const savedTasks = JSON.parse(localStorage.getItem(taskList.id) || '[]');
        savedTasks.forEach(task => {
          taskList.appendChild(createTaskElement(task.text, task.done, taskList.id));
        });

        const addBtn = document.createElement('button');
        addBtn.textContent = 'Adicionar Tarefa';
        addBtn.className = 'add-btn';
        addBtn.onclick = () => {
          taskList.appendChild(createTaskElement('', false, taskList.id));
          saveTasks(taskList.id);
        };

        section.appendChild(taskList);
        section.appendChild(addBtn);
        checklistContainer.appendChild(section);
      });
    }

    function createTaskElement(text = '', done = false, storageKey) {
      const taskDiv = document.createElement('div');
      taskDiv.className = 'task';

      const checkbox = document.createElement('input');
      checkbox.type = 'checkbox';
      checkbox.checked = done;
      checkbox.onchange = () => saveTasks(storageKey);

      const input = document.createElement('input');
      input.type = 'text';
      input.value = text;
      input.onchange = () => saveTasks(storageKey);

      const btnGroup = document.createElement('div');
      btnGroup.className = 'buttons';

      const delBtn = document.createElement('button');
      delBtn.className = 'del-btn';
      delBtn.textContent = 'X';
      delBtn.onclick = () => {
        taskDiv.remove();
        saveTasks(storageKey);
      };

      const moveBtn = document.createElement('button');
      moveBtn.className = 'move-btn';
      moveBtn.textContent = 'Mover';
      moveBtn.onclick = () => moveTask(taskDiv, storageKey);

      btnGroup.appendChild(moveBtn);
      btnGroup.appendChild(delBtn);

      taskDiv.appendChild(checkbox);
      taskDiv.appendChild(input);
      taskDiv.appendChild(btnGroup);

      return taskDiv;
    }

    function saveTasks(storageKey) {
      const taskList = document.getElementById(storageKey);
      const tasks = Array.from(taskList.children).map(task => {
        const input = task.querySelector('input[type="text"]');
        const checkbox = task.querySelector('input[type="checkbox"]');
        return { text: input.value, done: checkbox.checked };
      });
      localStorage.setItem(storageKey, JSON.stringify(tasks));
    }

    function moveTask(taskElement, fromKey) {
      const destPeriod = prompt('Mover para: Manhã, Tarde ou Noite?');
      if (!periods.includes(destPeriod)) return alert('Período inválido.');

      const toKey = `${days[currentDay]}-${destPeriod}`;
      const destination = document.getElementById(toKey);
      destination.appendChild(taskElement);
      saveTasks(fromKey);
      saveTasks(toKey);
    }

    days.forEach((day, i) => {
      const btn = document.createElement('button');
      btn.className = 'day-button';
      btn.textContent = day;
      btn.onclick = () => loadChecklist(i);
      daySelector.appendChild(btn);
    });

    loadChecklist(currentDay);
  </script>
</body>
</html>


