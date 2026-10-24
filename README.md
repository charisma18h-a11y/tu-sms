# tu-sms
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>منصة التنبيهات الأكاديمية الموحدة</title>
    <!-- Tailwind CSS لتنسيق المظهر المريح -->
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;700&display=swap" rel="stylesheet">
    
    <!-- مكتبة OneSignal للإشعارات الفورية على الجوال -->
    <script src="https://cdn.onesignal.com/sdks/web/v16/OneSignalSDK.page.js" defer></script>
    <script>
      window.OneSignalDeferred = window.OneSignalDeferred || [];
      OneSignalDeferred.push(async function(OneSignal) {
        await OneSignal.init({
          appId: "ضع_هنا_APP_ID_الخاص_بـ_OneSignal", // استبدل هذا المعرف بعد التسجيل مجاناً في OneSignal
        });
      });
    </script>

    <style>
        body { 
            font-family: 'Cairo', sans-serif; 
            background-color: #18181b; /* خلفية داكنة دافئة مريحة للعين */
            color: #f4f4f5;
        }
    </style>
</head>
<body class="min-h-screen flex flex-col justify-between">

    <!-- الهيدر المريح -->
    <header class="bg-zinc-900 border-b border-zinc-800 py-5 px-4 text-center relative shadow-sm">
        <h1 class="text-xl font-bold text-zinc-100">المنصة الأكاديمية للتنبيهات</h1>
        <p class="text-zinc-400 text-xs mt-1">متابعة التنبيهات والجدول الدراسي لكافة المواد</p>
        
        <button onclick="openAdminModal()" class="absolute left-4 top-4 bg-zinc-800 hover:bg-zinc-700 text-xs text-zinc-300 px-3 py-1.5 rounded-lg border border-zinc-700 transition">
            🔐 الأدمن
        </button>
    </header>

    <main class="max-w-2xl w-full mx-auto p-4 flex-grow my-4">

        <!-- زر تفعيل الإشعارات الفورية -->
        <div class="bg-indigo-950/40 border border-indigo-800/50 p-4 rounded-2xl mb-6 flex justify-between items-center">
            <div>
                <h4 class="text-xs font-bold text-indigo-300">📱 تفعيل إشعارات الجوال الفورية</h4>
                <p class="text-[11px] text-zinc-400 mt-0.5">لتصلك التنبيهات والأخبار على شاشة جوالك مباشرة</p>
            </div>
            <button onclick="subscribeToNotifications()" class="bg-indigo-600 hover:bg-indigo-500 text-white text-xs px-3 py-2 rounded-xl font-bold transition shadow-md">
                تفعيل 🔔
            </button>
        </div>

        <!-- شاشة تسجيل بيانات الطالب لأول مرة -->
        <div id="studentRegisterCard" class="bg-zinc-900 p-6 rounded-2xl border border-zinc-800 shadow-xl mb-6">
            <h2 class="text-base font-bold text-zinc-200 mb-1">تسجيل البيانات الموحدة</h2>
            <p class="text-xs text-zinc-400 mb-4">أدخل اسمك ورقم جوالك ليتم تخصيص جدول التنبيهات الخاص بك.</p>
            <div class="space-y-3">
                <div>
                    <label class="block text-xs font-semibold text-zinc-400 mb-1">الاسم الثلاثي:</label>
                    <input type="text" id="regName" placeholder="مثال: خالد أحمد" class="w-full p-2.5 text-xs bg-zinc-800 border border-zinc-700 rounded-xl text-zinc-100 outline-none focus:border-indigo-500">
                </div>
                <div>
                    <label class="block text-xs font-semibold text-zinc-400 mb-1">رقم الجوال:</label>
                    <input type="tel" id="regPhone" placeholder="05xxxxxxxx" class="w-full p-2.5 text-xs bg-zinc-800 border border-zinc-700 rounded-xl text-zinc-100 outline-none focus:border-indigo-500">
                </div>
                <button onclick="registerStudent()" class="w-full bg-indigo-600 hover:bg-indigo-500 text-white font-bold py-2.5 rounded-xl transition text-xs">
                    حفظ البيانات والدخول
                </button>
            </div>
        </div>

        <!-- معلومات الطالب -->
        <div id="studentWelcomeCard" class="hidden bg-zinc-900 border border-zinc-800 p-4 rounded-2xl mb-6 flex justify-between items-center">
            <div>
                <span class="text-[10px] text-zinc-500 font-semibold block">مرحباً بك:</span>
                <h3 id="displayStudentName" class="font-bold text-zinc-200 text-sm"></h3>
                <span id="displayStudentPhone" class="text-xs text-zinc-400"></span>
            </div>
            <button onclick="resetStudentData()" class="text-xs text-red-400 hover:underline">تعديل بياناتي</button>
        </div>

        <!-- جدول التنبيهات -->
        <div id="announcementsSection" class="hidden">
            <h2 class="text-sm font-bold text-zinc-300 mb-3 flex items-center gap-2">
                📌 جدول التنبيهات والإعلانات الخاص بك
            </h2>

            <div id="announcementsList" class="space-y-3">
                <!-- يتم تعبئة التنبيهات هنا -->
            </div>
        </div>

    </main>

    <!-- مودال الأدمن -->
    <div id="adminModal" class="hidden fixed inset-0 bg-black/70 backdrop-blur-sm flex justify-center items-center p-4 z-50">
        <div class="bg-zinc-900 border border-zinc-800 max-w-lg w-full p-6 rounded-2xl shadow-2xl max-h-[90vh] overflow-y-auto">
            
            <div id="adminAuthSection">
                <h3 class="text-sm font-bold text-zinc-200 mb-4">تسجيل دخول الأدمن</h3>
                <input type="password" id="adminPassInput" placeholder="أدخل كلمة المرور" class="w-full p-2.5 text-xs bg-zinc-800 border border-zinc-700 rounded-xl mb-3 text-zinc-100 outline-none focus:border-indigo-500">
                <div class="flex gap-2">
                    <button onclick="verifyAdmin()" class="w-full bg-indigo-600 text-white py-2 rounded-xl text-xs font-bold">دخول</button>
                    <button onclick="closeAdminModal()" class="w-full bg-zinc-800 text-zinc-400 py-2 rounded-xl text-xs">إلغاء</button>
                </div>
            </div>

            <!-- لوحة تحكم الأدمن -->
            <div id="adminControls" class="hidden space-y-4">
                <div class="flex justify-between items-center border-b border-zinc-800 pb-2">
                    <h3 class="font-bold text-indigo-400 text-sm">لوحة تحكم الأدمن</h3>
                    <button onclick="closeAdminModal()" class="text-xs text-zinc-500">إغلاق ✖</button>
                </div>

                <div class="bg-zinc-800/50 p-3 rounded-xl border border-zinc-800">
                    <h4 class="text-xs font-bold text-zinc-300 mb-2">نشر تنبيه جديد وإرسال إشعار</h4>
                    
                    <input type="text" id="postSubject" placeholder="اسم المادة (مثال: فيزياء / شعبة 1820)" class="w-full p-2 text-xs bg-zinc-900 border border-zinc-700 rounded-lg mb-2 text-zinc-100">
                    <textarea id="postContent" placeholder="نص التنبيه..." class="w-full p-2 text-xs bg-zinc-900 border border-zinc-700 rounded-lg mb-2 h-20 text-zinc-100"></textarea>
                    
                    <label class="block text-xs font-bold text-zinc-400 mb-1">تحديد المستهدفين:</label>
                    <select id="targetAudience" onchange="toggleStudentSelector()" class="w-full p-2 text-xs bg-zinc-900 border border-zinc-700 rounded-lg mb-2 text-zinc-100">
                        <option value="all">إرسال لجميع الطلاب المسجلين</option>
                        <option value="specific">تحديد طلاب معينين</option>
                    </select>

                    <div id="studentsListSelector" class="hidden max-h-32 overflow-y-auto border border-zinc-700 p-2 rounded-lg bg-zinc-900 mb-2 text-xs space-y-1">
                    </div>

                    <button onclick="publishAnnouncement()" class="w-full bg-indigo-600 hover:bg-indigo-500 text-white py-2 rounded-lg text-xs font-bold transition">
                        نشر التنبيه وإرسال إشعار الجوال 📣
                    </button>
                </div>

                <div>
                    <h4 class="text-xs font-bold text-zinc-400 mb-1">الطلاب المسجلين (<span id="studentsCount">0</span>):</h4>
                    <div id="registeredStudentsDisplay" class="max-h-28 overflow-y-auto border border-zinc-800 p-2 rounded-lg bg-zinc-950 text-xs space-y-1 text-zinc-400">
                    </div>
                </div>
            </div>

        </div>
    </div>

    <footer class="text-center py-4 text-xs text-zinc-600 border-t border-zinc-800 bg-zinc-900">
        نظام التنبيهات الموحد &copy; 2026
    </footer>

    <script>
        const ADMIN_PASS = "Tu1143091278";

        let currentStudent = JSON.parse(localStorage.getItem('registered_student')) || null;
        let registeredStudents = JSON.parse(localStorage.getItem('all_students')) || [];
        let announcements = JSON.parse(localStorage.getItem('all_announcements')) || [];

        window.onload = function() {
            checkStudentStatus();
        };

        function subscribeToNotifications() {
            OneSignalDeferred.push(function(OneSignal) {
                OneSignal.Notifications.requestPermission();
            });
        }

        function checkStudentStatus() {
            if (currentStudent) {
                document.getElementById('studentRegisterCard').classList.add('hidden');
                document.getElementById('studentWelcomeCard').classList.remove('hidden');
                document.getElementById('announcementsSection').classList.remove('hidden');
                
                document.getElementById('displayStudentName').innerText = currentStudent.name;
                document.getElementById('displayStudentPhone').innerText = currentStudent.phone;
                
                renderStudentAnnouncements();
            } else {
                document.getElementById('studentRegisterCard').classList.remove('hidden');
                document.getElementById('studentWelcomeCard').classList.add('hidden');
                document.getElementById('announcementsSection').classList.add('hidden');
            }
        }

        function registerStudent() {
            const name = document.getElementById('regName').value.trim();
            const phone = document.getElementById('regPhone').value.trim();

            if (!name || !phone) {
                alert("يرجى إدخال الاسم ورقم الجوال بالكامل.");
                return;
            }

            currentStudent = { id: Date.now(), name: name, phone: phone };
            localStorage.setItem('registered_student', JSON.stringify(currentStudent));

            const exists = registeredStudents.some(s => s.phone === phone);
            if (!exists) {
                registeredStudents.push(currentStudent);
                localStorage.setItem('all_students', JSON.stringify(registeredStudents));
            }

            checkStudentStatus();
        }

        function resetStudentData() {
            localStorage.removeItem('registered_student');
            currentStudent = null;
            checkStudentStatus();
        }

        function renderStudentAnnouncements() {
            const container = document.getElementById('announcementsList');
            container.innerHTML = '';

            const studentPosts = announcements.filter(post => {
                return post.target === 'all' || (post.targetStudents && post.targetStudents.includes(currentStudent.phone));
            });

            if (studentPosts.length === 0) {
                container.innerHTML = '<p class="text-center text-zinc-500 py-6 text-xs">لا توجد تنبيهات حالية مضافة لجدولك.</p>';
                return;
            }

            studentPosts.forEach(post => {
                const card = document.createElement('div');
                card.className = "bg-zinc-900 p-4 rounded-xl border border-zinc-800 shadow-sm";
                card.innerHTML = `
                    <div class="flex justify-between items-center mb-2">
                        <span class="bg-indigo-900/60 text-indigo-300 border border-indigo-700/50 font-bold px-2.5 py-0.5 rounded-md text-[11px]">${post.subject}</span>
                        <span class="text-[10px] text-zinc-500">${post.date}</span>
                    </div>
                    <p class="text-xs text-zinc-300 leading-relaxed">${post.content}</p>
                `;
                container.appendChild(card);
            });
        }

        function openAdminModal() { document.getElementById('adminModal').classList.remove('hidden'); }
        function closeAdminModal() { document.getElementById('adminModal').classList.add('hidden'); }

        function verifyAdmin() {
            const pass = document.getElementById('adminPassInput').value;
            if (pass === ADMIN_PASS) {
                document.getElementById('adminAuthSection').classList.add('hidden');
                document.getElementById('adminControls').classList.remove('hidden');
                loadAdminData();
            } else {
                alert("كلمة المرور غير صحيحة!");
            }
        }

        function loadAdminData() {
            document.getElementById('studentsCount').innerText = registeredStudents.length;
            const listDisplay = document.getElementById('registeredStudentsDisplay');
            const selectorList = document.getElementById('studentsListSelector');
            
            listDisplay.innerHTML = '';
            selectorList.innerHTML = '';

            registeredStudents.forEach(st => {
                listDisplay.innerHTML += `<div class="p-1 border-b border-zinc-800 flex justify-between"><span>${st.name}</span> <span class="text-zinc-600">${st.phone}</span></div>`;
                selectorList.innerHTML += `
                    <label class="flex items-center gap-2 text-zinc-300">
                        <input type="checkbox" value="${st.phone}" class="student-checkbox">
                        <span>${st.name} (${st.phone})</span>
                    </label>
                `;
            });
        }

        function toggleStudentSelector() {
            const target = document.getElementById('targetAudience').value;
            const selector = document.getElementById('studentsListSelector');
            if (target === 'specific') selector.classList.remove('hidden');
            else selector.classList.add('hidden');
        }

        function publishAnnouncement() {
            const subject = document.getElementById('postSubject').value.trim();
            const content = document.getElementById('postContent').value.trim();
            const target = document.getElementById('targetAudience').value;

            if (!subject || !content) {
                alert("يرجى كتابة اسم المادة ونص التنبيه.");
                return;
            }

            let selectedPhones = [];
            if (target === 'specific') {
                const checkboxes = document.querySelectorAll('.student-checkbox:checked');
                checkboxes.forEach(cb => selectedPhones.push(cb.value));
                if (selectedPhones.length === 0) {
                    alert("يرجى اختيار طالب واحد على الأقل.");
                    return;
                }
            }

            const now = new Date();
            const dateStr = `${now.toLocaleDateString('ar-SA')} - ${now.toLocaleTimeString('ar-SA', {hour: '2-digit', minute:'2-digit'})}`;

            announcements.unshift({
                id: Date.now(),
                subject: subject,
                content: content,
                date: dateStr,
                target: target,
                targetStudents: selectedPhones
            });

            localStorage.setItem('all_announcements', JSON.stringify(announcements));
            
            alert("تم نشر التنبيه وتوجيهه لجوالات الطلاب!");
            closeAdminModal();
            checkStudentStatus();
        }
    </script>
</body>
</html>
