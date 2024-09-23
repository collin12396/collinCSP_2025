---
layout: schedule
title: Calendar
units: "1,2,3,4,5,6,7,8,9"
search_exclude: true
course: csp
menu: nav/home.html
---
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Interactive Calendar</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            display: flex;
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
            padding: 30px;
        }

        .calendar-header h2 {
            margin: 0;
            font-size: 2em;
        }

        .calendar-header button {
            background-color: #ffffff;
            color: #2196F3;
            border: none;
            padding: 10px;
            cursor: pointer;
            font-size: 1em;
            border-radius: 5px;
        }

        .calendar-header button:hover {
            background-color: #f0f0f0;
        }

        .calendar-days {
            display: grid;
            grid-template-columns: repeat(7, 1fr);
            padding: 30px;
            gap: 15px;
        }

        .calendar-days div {
            width: 60px;
            height: 60px;
            display: flex;
            justify-content: center;
            align-items: center;
            cursor: pointer;
            font-weight: bold;
            font-size: 1.5em;
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
            font-size: 1.2em;
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

    <script>
        const calendarDays = document.querySelector(".calendar-days");
        const monthYearDisplay = document.getElementById("month-year");
        const currentDate = new Date();
        let selectedDate = null;
        let currentYear = currentDate.getFullYear();
        let currentMonth = currentDate.getMonth();

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

                dayDiv.addEventListener("click", () => selectDate(dayDiv));
                calendarDays.appendChild(dayDiv);
            }
        }

        function selectDate(dayDiv) {
            // Deselect previous date
            if (selectedDate) {
                selectedDate.classList.remove("selected");
            }

            // Select new date
            selectedDate = dayDiv;
            selectedDate.classList.add("selected");
        }

        // Navigate to previous month
        document.getElementById("prev-month").addEventListener("click", () => {
            currentMonth--;
            if (currentMonth < 0) {
                currentMonth = 11;
                currentYear--;
            }
            renderCalendar(currentYear, currentMonth);
        });

        // Navigate to next month
        document.getElementById("next-month").addEventListener("click", () => {
            currentMonth++;
            if (currentMonth > 11) {
                currentMonth = 0;
                currentYear++;
            }
            renderCalendar(currentYear, currentMonth);
        });

        renderCalendar(currentYear, currentMonth);
    </script>
</body>
</html>
