---
layout: post
title: Calendar
permalink: /cspcalendar/
---
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Interactive Calendar with Tasks</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-color: #f4f4f9;
        }

        .calendar {
            width: 600px;
            background-color: white;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            overflow: hidden;
        }

        .calendar-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            background-color: #2196F3;
            color: white;
            padding: 20px;
        }

        .calendar-header h2 {
            margin: 0;
            font-size: 1.5em;
        }

        .calendar-header button {
            background-color: #ffffff;
            color: #2196F3;
            border: none;
            padding: 10px;
            cursor: pointer;
            font-size: 0.9em;
            border-radius: 5px;
        }

        .calendar-header button:hover {
            background-color: #f0f0f0;
        }

        .calendar-days {
            display: grid;
            grid-template-columns: repeat(7, 1fr);
            padding: 20px;
            gap: 10px;
        }

        .calendar-days div {
            width: 50px;
            height: 50px;
            display: flex;
            justify-content: center;
            align-items: center;
            cursor: pointer;
            font-weight: bold;
            font-size: 1em;
            color: #333;
            transition: background-color 0.2s;
        }

        .calendar-days div:hover {
            background-color: #eee;
        }

        .calendar-days div.selected {
            background-color: #2196F3;
            color: white;
        }

        .calendar-days div.current-day {
            background-color: #ff9800;
            color: white;
        }

        .day-header {
            background-color: #ddd;
            font-size: 1.1em;
        }

        /* Modal styling */
        .modal {
            display: none;
            position: fixed;
            z-index: 1;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.5);
            justify-content: center;
            align-items: center;
        }

        .modal-content {
            background-color: white;
            padding: 20px;
            border-radius: 10px;
            width: 400px;
            text-align: center;
        }

        .modal-content input,
        .modal-content button {
            margin: 10px;
            padding: 10px;
            width: 90%;
        }

        /* Task display box */
        .task-box {
            width: 600px;
            margin-top: 20px;
            padding: 20px;
            background-color: white;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }

        .task-box h3 {
            font-size: 1.2em;
            margin-bottom: 10px;
        }

        .task-list {
            list-style-type: none;
            padding: 0;
            max-height: 150px;
            overflow-y: auto;
        }

        .task-list li {
            padding: 10px;
            background-color: #f9f9f9;
            margin-bottom: 10px;
            border-radius: 5px;
            font-size: 0.9em;
        }

        .task-list li span {
            font-weight: bold;
        }
    </style>
</head>
<body>

    <div class="calendar">
        <div class="calendar-header">
            <button id="prev-month">Prev</button>
            <h2 id="month-year"></h2>
            <button id="next-month">Next</button>
        </div>
        <div class="calendar-days">
            <div class="day-header">Sun</div>
            <div class="day-header">Mon</div>
            <div class="day-header">Tue</div>
            <div class="day-header">Wed</div>
            <div class="day-header">Thu</div>
            <div class="day-header">Fri</div>
            <div class="day-header">Sat</div>
        </div>
    </div>

    <!-- Task display box below the calendar -->
    <div class="task-box">
        <h3>Tasks for <span id="task-date">Select a date</span></h3>
        <ul class="task-list" id="taskList">
            <!-- Task items will be added here -->
        </ul>
    </div>

    <!-- Modal for adding tasks -->
    <div id="taskModal" class="modal">
        <div class="modal-content">
            <h3>Add Task</h3>
            <input type="text" id="taskName" placeholder="Task Name">
            <button id="addTaskButton">Add Task</button>
            <button id="closeModal">Cancel</button>
        </div>
    </div>

    <script>
        const calendarDays = document.querySelector(".calendar-days");
        const monthYearDisplay = document.getElementById("month-year");
        const taskList = document.getElementById("taskList");
        const taskDateDisplay = document.getElementById("task-date");
        const currentDate = new Date();
        let selectedDate = null;
        // Load tasks from localStorage or initialize an empty object
        let tasks = JSON.parse(localStorage.getItem("tasks")) || {}; 
        let currentYear = currentDate.getFullYear();
        let currentMonth = currentDate.getMonth();

        const taskModal = document.getElementById("taskModal");
        const taskNameInput = document.getElementById("taskName");
        const addTaskButton = document.getElementById("addTaskButton");
        const closeModalButton = document.getElementById("closeModal");

        function renderCalendar(year, month) {
            // Reset calendar days
            calendarDays.innerHTML = `
                <div class="day-header">Sun</div>
                <div class="day-header">Mon</div>
                <div class="day-header">Tue</div>
                <div class="day-header">Wed</div>
                <div class="day-header">Thu</div>
                <div class="day-header">Fri</div>
                <div class="day-header">Sat</div>
            `;

            // Set the header
            const monthNames = [
                "January", "February", "March", "April", "May", "June", 
                "July", "August", "September", "October", "November", "December"
            ];
            monthYearDisplay.textContent = `${monthNames[month]} ${year}`;

            const firstDay = new Date(year, month, 1).getDay();
            const lastDate = new Date(year, month + 1, 0).getDate();

            // Padding days for the previous month
            for (let i = 0; i < firstDay; i++) {
                const emptyDiv = document.createElement("div");
                calendarDays.appendChild(emptyDiv);
            }

            // Actual days of the month
            for (let day = 1; day <= lastDate; day++) {
                const dayDiv = document.createElement("div");
                dayDiv.textContent = day;

                // Highlight current day
                if (year === currentDate.getFullYear() && month === currentDate.getMonth() && day === currentDate.getDate()) {
                    dayDiv.classList.add("current-day");
                }

                dayDiv.addEventListener("click", () => selectDate(dayDiv, day));
                calendarDays.appendChild(dayDiv);
            }
        }

        function selectDate(dayDiv, day) {
            // Deselect previous date
            if (selectedDate) {
                selectedDate.classList.remove("selected");
            }

            // Select new date
            selectedDate = dayDiv;
            selectedDate.classList.add("selected");

            // Update task display box
            const dateKey = `${currentYear}-${currentMonth + 1}-${day}`;
            taskDateDisplay.textContent = `${currentMonth + 1}/${day}/${currentYear}`;
            displayTasks(dateKey);

            // Open modal for task input
            taskModal.style.display = "flex";
        }

        function displayTasks(dateKey) {
            taskList.innerHTML = ''; // Clear current tasks
            const tasksForDate = tasks[dateKey] || [];

            tasksForDate.forEach(task => {
                const li = document.createElement('li');
                li.textContent = task;
                taskList.appendChild(li);
            });
        }

        addTaskButton.addEventListener("click", () => {
            const taskName = taskNameInput.value.trim();
            if (taskName && selectedDate) {
                const dateKey = `${currentYear}-${currentMonth + 1}-${selectedDate.textContent}`;
                if (!tasks[dateKey]) {
                    tasks[dateKey] = [];
                }
                tasks[dateKey].push(taskName);
                // Save tasks to localStorage
                localStorage.setItem("tasks", JSON.stringify(tasks)); 
                displayTasks(dateKey);
                taskNameInput.value = ''; // Clear input
                taskModal.style.display = "none"; // Close modal
            }
        });

        closeModalButton.addEventListener("click", () => {
            taskModal.style.display = "none"; // Close modal
        });

        document.getElementById("prev-month").addEventListener("click", () => {
            currentMonth--;
            if (currentMonth < 0) {
                currentMonth = 11;
                currentYear--;
            }
            renderCalendar(currentYear, currentMonth);
        });

        document.getElementById("next-month").addEventListener("click", () => {
            currentMonth++;
            if (currentMonth > 11) {
                currentMonth = 0;
                currentYear++;
            }
            renderCalendar(currentYear, currentMonth);
        });

        // Initial rendering of the calendar
        renderCalendar(currentYear, currentMonth);
    </script>

</body>
</html>
