# tu-sms
 <!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>إعلانات شعبة 1820 - د. محمد الطويرقي</title>
    <!-- Tailwind CSS للتصميم السريع والأنيق -->
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Cairo', sans-serif; }
    </style>
</head>
<body class="bg-gray-50 text-gray-800 min-h-screen flex flex-col justify-between">

    <!-- الهيدر -->
    <header class="bg-indigo-600 text-white shadow-md py-6 px-4 text-center">
        <h1 class="text-2xl font-bold">إعلانات شعبة 1820</h1>
        <p class="text-indigo-200 text-sm mt-1">إشراف: د. محمد الطويرقي | جامعة الطائف</p>
    </header>

    <!-- المحتوى الرئيسي -->
    <main class="max-w-2xl w-full mx-auto p-4 flex-grow my-6">
        
        <!-- زر لوحة التحكم الخاصة بالمشرف -->
        <div class="flex justify-between items-center mb-6">
            <h2 class="text-lg font-semibold text-gray-700">التنبيهات والإعلانات النشطة</h2>
            <button onclick="toggleAdminPanel()" class="text-xs bg-gray-200 hover:bg-gray-300 text-gray-700 px-3 py-1.5 rounded-lg transition">
                ⚙️ إدارة الإعلانات
            </button>
        </div>

        <!-- لوحة الإضافة (مخفية افتراضياً) -->
        <div id="adminPanel" class="hidden bg-white p-4 rounded-xl shadow-sm border border-indigo-100 mb-6">
            <h3 class="text-sm font-bold text-indigo-600 mb-2">إضافة إعلان جديد</h3>
            <input type="password" id="adminPassword" placeholder="كلمة المرور (1820)" class="w-full p-2 text-sm border rounded-lg mb-2 focus:ring-2 focus:ring-indigo-500 outline-none">
            <textarea id="newAnnouncementText" placeholder="اكتب نص الإعلان أو التنبيه هنا..." class="w-full p-2 text-sm border rounded-lg mb-2 h-24 focus:ring-2 focus:ring-indigo-500 outline-none"></textarea>
            <button onclick="addAnnouncement()" class="w-full bg-indigo-600 hover:bg-indigo-700 text-white text-sm py-2 rounded-lg font-medium transition">نشر الإعلان فوراً</button>
        </div>

        <!-- قائمة الإعلانات -->
        <div id="announcementsList" class="space-y-4">
            <!-- سيتم تعبئة الإعلانات تلقائياً هنا -->
        </div>

    </main>

    <!-- الفوتر -->
    <footer class="text-center py-4 text-xs text-gray-400 border-t bg-white">
        تم تصميم المنصة لتسهيل التواصل الأكاديمي لشعبة 1820 &copy; 2026
    </footer>

    <!-- سكربت التشغيل والتخزين -->
    <script>
        // إعلانات افتراضية أولية
        let announcements = JSON.parse(localStorage.getItem('section_1820_posts')) || [
            {
                id: 1,
                date: "اليوم، 05:30 م",
                text: "السلام عليكم ورحمة الله وبركاته، تنبيه وتذكير هام لطلاب شعبة 1820 (د. محمد الطويرقي)."
            }
        ];

        function renderAnnouncements() {
            const listContainer = document.getElementById('announcementsList');
            listContainer.innerHTML = '';
            
            if (announcements.length === 0) {
                listContainer.innerHTML = '<p class="text-center text-gray-400 py-8">لا توجد إعلانات حالياً.</p>';
                return;
            }

            announcements.forEach((item, index) => {
                const card = document.createElement('div');
                card.className = "bg-white p-4 rounded-xl shadow-sm border border-gray-100 relative";
                card.innerHTML = `
                    <div class="flex justify-between items-start mb-2">
                        <span class="text-xs text-indigo-500 font-semibold bg-indigo-50 px-2 py-0.5 rounded">إعلان رسمي</span>
                        <span class="text-xs text-gray-400">${item.date}</span>
                    </div>
                    <p class="text-gray-700 text-sm leading-relaxed whitespace-pre-line">${item.text}</p>
                `;
                listContainer.appendChild(card);
            });
        }

        function toggleAdminPanel() {
            const panel = document.getElementById('adminPanel');
            panel.classList.toggle('hidden');
        }

        function addAnnouncement() {
            const pass = document.getElementById('adminPassword').value;
            const text = document.getElementById('newAnnouncementText').value;

            if (pass !== "1820") {
                alert("كلمة المرور غير صحيحة!");
                return;
            }

            if (!text.trim()) {
                alert("يرجى كتابة نص الإعلان أولاً.");
                return;
            }

            const now = new Date();
            const dateStr = `${now.toLocaleDateString('ar-SA')} - ${now.toLocaleTimeString('ar-SA', {hour: '2-digit', minute:'2-digit'})}`;

            announcements.unshift({
                id: Date.now(),
                date: dateStr,
                text: text
            });

            localStorage.setItem('section_1820_posts', JSON.stringify(announcements));
            document.getElementById('newAnnouncementText').value = '';
            document.getElementById('adminPassword').value = '';
            document.getElementById('adminPanel').classList.add('hidden');
            
            renderAnnouncements();
            alert("تم نشر الإعلان بنجاح!");
        }

        // تشغيل عرض الإعلانات عند فتح الصفحة
        renderAnnouncements();
    </script>
</body>
</html>
