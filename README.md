# tu-sms

<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>المنصة الأكاديمية للتنبيهات والجدول</title>
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;700&display=swap" rel="stylesheet">
    <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>

    <!-- مكتبة OneSignal للإشعارات الفورية على الجوال -->
    <script src="https://cdn.onesignal.com/sdks/web/v16/OneSignalSDK.page.js" defer></script>
    <script>
      window.OneSignalDeferred = window.OneSignalDeferred || [];
      OneSignalDeferred.push(async function(OneSignal) {
        await OneSignal.init({
          appId: "ضع_هنا_APP_ID_الخاص_بـ_OneSignal", // استبدل هذا النص بمعرف OneSignal الخاص بك لاحقاً
        });
      });
    </script>

    <style>
        body { font-family: 'Cairo', sans-serif; background-color: #18181b; color: #f4f4f5; }
    </style>
</head>
<body class="min-h-screen flex flex-col justify-between">

    <header class="bg-zinc-900 border-b border-zinc-800 py-5 px-4 text-center relative shadow-sm">
        <h1 class="text-xl font-bold text-zinc-100">المنصة الأكاديمية الموحدة</h1>
        <p class="text-zinc-400 text-xs mt-1">متابعة التنبيهات والجدول الدراسي لكافة المواد</p>
        <button onclick="openAdminModal()" class="absolute left-4 top-4 bg-zinc-800 hover:bg-zinc-700 text-xs text-zinc-300 px-3 py-1.5 rounded-lg border border-zinc-700 transition">
            🔐 الأدمن
        </button>
    </header>

    <main class="max-w-2xl w-full mx-auto p-4 flex-grow my-4">

        <!-- زر تفعيل الإشعارات الفورية (تم إصلاحه وتفعيله) -->
        <div class="bg-indigo-950/40 border border-indigo-800/50 p-4 rounded-2xl mb-6 flex justify-between items-center shadow-lg">
            <div>
                <h4 class="text-xs font-bold text-indigo-300">📱 تفعيل إشعارات الجوال الفورية</h4>
                <p class="text-[11px] text-zinc-400 mt-0.5">اضغط لتصلك التنبيهات على شاشة جوالك مباشرة</p>
            </div>
            <button onclick="subscribeNotifications()" class="bg-indigo-600 hover:bg-indigo-500 text-white text-xs px-4 py-2.5 rounded-xl font-bold transition shadow-md">
                تفعيل 🔔
            </button>
        </div>

        <!-- تسجيل الطالب -->
        <div id="studentRegisterCard" class="bg-zinc-900 p-6 rounded-2xl border border-zinc-800 shadow-xl mb-6">
            <h2 class="text-base font-bold text-zinc-200 mb-1">تسجيل البيانات في النظام</h2>
            <p class="text-xs text-zinc-400 mb-4">أدخل اسمك ورقم جوالك لربط جدول التنبيهات بك.</p>
            <div class="space-y-3">
                <input type="text" id="regName" placeholder="الاسم الثلاثي" class="w-full p-2.5 text-xs bg-zinc-800 border border-zinc-700 rounded-xl text-zinc-100 outline-none">
                <input type="tel" id="regPhone" placeholder="رقم الجوال (05xxxxxxxx)" class="w-full p-2.5 text-xs bg-zinc-800 border border-zinc-700 rounded-xl text-zinc-100 outline-none">
                <button onclick="registerStudentToSupabase()" class="w-full bg-indigo-600 hover:bg-indigo-500 text-white font-bold py-2.5 rounded-xl text-xs">
                    حفظ البيانات والدخول
                </button>
            </div>
        </div>

        <!-- ترحيب بالطالب -->
        <div id="studentWelcomeCard" class="hidden bg-zinc-900 border border-zinc-800 p-4 rounded-2xl mb-6 flex justify-between items-center">
            <div>
                <span class="text-[10px] text-zinc-500 font-semibold block">مرحباً بك:</span>
                <h3 id="displayStudentName" class="font-bold text-zinc-200 text-sm"></h3>
                <span id="displayStudentPhone" class="text-xs text-zinc-400"></span>
            </div>
            <button onclick="logoutStudent()" class="text-xs text-red-400 hover:underline">تعديل بياناتي</button>
        </div>

        <!-- التنبيهات -->
        <div id="announcementsSection" class="hidden">
            <h2 class="text-sm font-bold text-zinc-300 mb-3">📌 جدول التنبيهات والإعلانات الخاص بك</h2>
            <div id="announcementsList" class="space-y-3"></div>
        </div>

    </main>

    <!-- مودال الأدمن -->
    <div id="adminModal" class="hidden fixed inset-0 bg-black/70 backdrop-blur-sm flex justify-center items-center p-4 z-50">
        <div class="bg-zinc-900 border border-zinc-800 max-w-lg w-full p-6 rounded-2xl shadow-2xl max-h-[90vh] overflow-y-auto">
            
            <div id="adminAuthSection">
                <h3 class="text-sm font-bold text-zinc-200 mb-4">تسجيل دخول الأدمن</h3>
                <input type="password" id="adminPassInput" placeholder="أدخل كلمة المرور" class="w-full p-2.5 text-xs bg-zinc-800 border border-zinc-700 rounded-xl mb-3 text-zinc-100 outline-none">
                <button onclick="verifyAdmin()" class="w-full bg-indigo-600 text-white py-2 rounded-xl text-xs font-bold">دخول</button>
            </div>

            <div id="adminControls" class="hidden space-y-4">
                <div class="flex justify-between items-center border-b border-zinc-800 pb-2">
                    <h3 class="font-bold text-indigo-400 text-sm">لوحة تحكم الأدمن</h3>
                    <button onclick="closeAdminModal()" class="text-xs text-zinc-500">إغلاق ✖</button>
                </div>

                <div class="bg-zinc-800/50 p-3 rounded-xl border border-zinc-800">
                    <h4 class="text-xs font-bold text-zinc-300 mb-2">إضافة تنبيه جديد</h4>
                    <input type="text" id="postSubject" placeholder="اسم المادة (مثال: فيزياء / شعبة 1820)" class="w-full p-2 text-xs bg-zinc-900 border border-zinc-700 rounded-lg mb-2 text-zinc-100">
                    <textarea id="postContent" placeholder="نص التنبيه..." class="w-full p-2 text-xs bg-zinc-900 border border-zinc-700 rounded-lg mb-2 h-20 text-zinc-100"></textarea>
                    <button onclick="publishAnnouncementToSupabase()" class="w-full bg-indigo-600 text-white py-2 rounded-lg text-xs font-bold">نشر التنبيه للجميع 📣</button>
                </div>

                <div>
                    <h4 class="text-xs font-bold text-zinc-400 mb-1">الطلاب المسجلين بالسحابة:</h4>
                    <div id="registeredStudentsDisplay" class="max-h-28 overflow-y-auto border border-zinc-800 p-2 rounded-lg bg-zinc-950 text-xs space-y-1 text-zinc-400"></div>
                </div>
            </div>
        </div>
    </div>

    <script>
        const ADMIN_PASS = "Tu1143091278";

        // بيانات Supabase الخاصة بك التي أرسلتها:
        const SUPABASE_URL = 'https://hzkoanvnkwwdrpaxgnhj.supabase.co';
        const SUPABASE_KEY = 'sb_publishable_f0KBqcr-RA1pxNf9tlGsXg_SeyrDKPr'; 
        
        const _supabase = supabase.createClient(SUPABASE_URL, SUPABASE_KEY);

        let currentStudent = JSON.parse(localStorage.getItem('current_student')) || null;

        window.onload = function() {
            checkStudent();
            loadAnnouncements();
        };

        // دالة تفعيل الإشعارات الفورية
        function subscribeNotifications() {
            if (typeof OneSignalDeferred !== 'undefined') {
                OneSignalDeferred.push(async function(OneSignal) {
                    try {
                        await OneSignal.Notifications.requestPermission();
                        alert("تم إرسال طلب إذن الإشعارات بنجاح! 🔔");
                    } catch (err) {
                        alert("تأكد من إعداد OneSignal App ID لاستلام الإشعارات على الجوال.");
                    }
                });
            } else {
                alert("نظام الإشعارات يحمل حالياً، حاول مرة أخرى.");
            }
        }

        function checkStudent() {
            if (currentStudent) {
                document.getElementById('studentRegisterCard').classList.add('hidden');
                document.getElementById('studentWelcomeCard').classList.remove('hidden');
                document.getElementById('announcementsSection').classList.remove('hidden');
                document.getElementById('displayStudentName').innerText = currentStudent.name;
                document.getElementById('displayStudentPhone').innerText = currentStudent.phone;
            }
        }

        async function registerStudentToSupabase() {
            const name = document.getElementById('regName').value.trim();
            const phone = document.getElementById('regPhone').value.trim();

            if (!name || !phone) {
                alert("الرجاء إدخال الاسم ورقم الجوال.");
                return;
            }

            try {
                const { data, error } = await _supabase.from('students').insert([{ name, phone }]);
                if (error) {
                    alert("خطأ من قاعدة البيانات: " + error.message);
                    return;
                }

                currentStudent = { name, phone };
                localStorage.setItem('current_student', JSON.stringify(currentStudent));
                checkStudent();
                alert("تم تسجيل بياناتك بنجاح في النظام السحابي!");
            } catch (err) {
                alert("حدث خطأ في الاتصال بالسحابة.");
            }
        }

        function logoutStudent() {
            localStorage.removeItem('current_student');
            location.reload();
        }

        async function loadAnnouncements() {
            try {
                const { data, error } = await _supabase.from('announcements').select('*').order('id', { ascending: false });
                if (error) return;

                const container = document.getElementById('announcementsList');
                container.innerHTML = '';

                if (!data || data.length === 0) {
                    container.innerHTML = '<p class="text-center text-zinc-500 py-6 text-xs">لا توجد تنبيهات حالية.</p>';
                    return;
                }

                data.forEach(post => {
                    container.innerHTML += `
                        <div class="bg-zinc-900 p-4 rounded-xl border border-zinc-800">
                            <div class="flex justify-between items-center mb-2">
                                <span class="bg-indigo-900/60 text-indigo-300 border border-indigo-700/50 font-bold px-2.5 py-0.5 rounded-md text-[11px]">${post.subject}</span>
                                <span class="text-[10px] text-zinc-500">${post.date}</span>
                            </div>
                            <p class="text-xs text-zinc-300 leading-relaxed">${post.content}</p>
                        </div>
                    `;
                });
            } catch (e) {}
        }

        function openAdminModal() { document.getElementById('adminModal').classList.remove('hidden'); }
        function closeAdminModal() { document.getElementById('adminModal'].classList.add('hidden'); }

        function verifyAdmin() {
            const pass = document.getElementById('adminPassInput').value;
            if (pass === ADMIN_PASS) {
                document.getElementById('adminAuthSection').classList.add('hidden');
                document.getElementById('adminControls').classList.remove('hidden');
                loadAdminStudents();
            } else {
                alert("كلمة المرور غير صحيحة!");
            }
        }

        async function loadAdminStudents() {
            const { data } = await _supabase.from('students').select('*');
            const listDisplay = document.getElementById('registeredStudentsDisplay');
            listDisplay.innerHTML = '';
            if, (data) {
                data.forEach(st => {
                    listDisplay.innerHTML += `<div class="p-1 border-b border-zinc-800 flex justify-between"><span>${st.name}</span> <span class="text-zinc-600">${st.phone}</span></div>`;
                });
            }
        }

        async function publishAnnouncementToSupabase() {
            const subject = document.getElementById('postSubject').value.trim();
            const content = document.getElementById('postContent').value.trim();

            if (!subject || !content) {
                alert("يرجى إدخال المادة ونص التنبيه.");
                return;
            }

            const now = new Date();
            const dateStr = `${now.toLocaleDateString('ar-SA')} - ${now.toLocaleTimeString('ar-SA', {hour: '2-digit', minute:'2-digit'})}`;

            const { error } = await _supabase.from('announcements').insert([{ subject, content, date: dateStr, target: 'all' }]);
            if (error) {
                alert("خطأ في النشر: " + error.message);
                return;
            }

            alert("تم نشر التنبيه بنجاح في السحابة!");
            closeAdminModal();
            loadAnnouncements();
        }
    </script>
</body>
</html>
