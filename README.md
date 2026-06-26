# Daily-planner-
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Study Analytics with Labels</title>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-100 dark:bg-gray-900 p-4 transition-colors duration-300">
    <div class="max-w-md mx-auto bg-white dark:bg-gray-800 rounded-2xl shadow-xl p-6">
        <h1 class="text-xl font-bold text-gray-800 dark:text-white mb-4 text-center">Study Analytics</h1>

        <div class="bg-gradient-to-r from-purple-500 to-indigo-600 p-4 rounded-xl text-white mb-6">
            <p class="text-xs opacity-80 uppercase tracking-widest">Today's Focus Score</p>
            <div id="focusScore" class="text-4xl font-bold">0/100</div>
            <div id="feedback" class="text-sm mt-1 italic">Start your day!</div>
        </div>

        <input type="date" id="datePicker" class="w-full p-2 mb-4 border rounded-lg dark:bg-gray-700 dark:text-white dark:border-gray-600">
        
        <div id="checklist" class="space-y-1 mb-6 max-h-64 overflow-y-auto pr-2"></div>

        <div class="border-t pt-4">
            <h2 class="text-sm font-semibold text-gray-700 dark:text-gray-300 mb-4">Weekly Trend</h2>
            <div id="chartContainer" class="flex items-end justify-between h-24 gap-1"></div>
        </div>
    </div>

    <script>
        const baseTasks = ["No Daily Class Miss", "Daily Class Notes", "Daily DDP Solve", "Maths (1 Ex.)", "Physics (15 Numericals)", "Chemistry (5 Reactions)", "Daily Quiz (1 Test)", "Daily Revision MCQ Solve", "Calculation Speed Practice", "Revision", "Skill Development", "English Speaking Practice", "Error Notebook Updated", "HC Verma", "Daily 1 Chapter PCB", "Daily 1 Chapter SST", "Daily Next Day Plan", "No Bad Habits"];
        
        const datePicker = document.getElementById('datePicker');
        datePicker.value = new Date().toISOString().split('T')[0];

        function calculateScore(date) {
            const data = JSON.parse(localStorage.getItem(`study_data_${date}`) || '[]');
            return Math.round((data.length / baseTasks.length) * 100);
        }

        function render() {
            const checklist = document.getElementById('checklist');
            checklist.innerHTML = '';
            const savedData = JSON.parse(localStorage.getItem(`study_data_${datePicker.value}`) || '[]');
            
            baseTasks.forEach(task => {
                const div = document.createElement('div');
                div.className = "flex items-center space-x-2 p-1.5 hover:bg-gray-50 dark:hover:bg-gray-700 rounded cursor-pointer";
                div.innerHTML = `<input type="checkbox" ${savedData.includes(task) ? 'checked' : ''} class="w-4 h-4 accent-indigo-600"> <label class="text-xs dark:text-gray-200">${task}</label>`;
                div.onclick = () => { div.querySelector('input').checked = !div.querySelector('input').checked; save(); };
                checklist.appendChild(div);
            });
            updateUI();
        }

        function updateUI() {
            const score = calculateScore(datePicker.value);
            document.getElementById('focusScore').innerText = `${score}/100`;
            const feedback = score > 80 ? "Excellent Work!" : score > 50 ? "Good Job!" : "Keep Pushing!";
            document.getElementById('feedback').innerText = feedback;
            renderChart();
        }

        function renderChart() {
            const container = document.getElementById('chartContainer');
            container.innerHTML = '';
            const days = ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'];
            
            for (let i = 6; i >= 0; i--) {
                const d = new Date();
                d.setDate(d.getDate() - i);
                const dateStr = d.toISOString().split('T')[0];
                const score = calculateScore(dateStr);
                
                const wrapper = document.createElement('div');
                wrapper.className = "flex flex-col items-center flex-1";
                wrapper.innerHTML = `
                    <div class="bg-indigo-400 w-full rounded-t transition-all" style="height: ${score > 0 ? score * 0.8 : 4}px" title="${dateStr}: ${score}%"></div>
                    <span class="text-[10px] text-gray-500 mt-1">${days[d.getDay()]}</span>
                `;
                container.appendChild(wrapper);
            }
        }

        function save() {
            const checked = Array.from(document.querySelectorAll('input:checked')).map(i => i.nextElementSibling.innerText);
            localStorage.setItem(`study_data_${datePicker.value}`, JSON.stringify(checked));
            updateUI();
        }

        datePicker.onchange = render;
        render();
    </script>
</body>
</html>

```
