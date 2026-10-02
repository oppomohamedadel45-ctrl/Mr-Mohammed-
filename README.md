<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>منصة المعلم الذكية - النظام الشامل المطور</title>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>
    
    <style>
        :root {
            --primary: #1e3a8a;
            --secondary: #0f172a;
            --accent: #2563eb;
            --bg: #f8fafc;
            --card: #ffffff;
            --text: #1e293b;
            --success: #16a34a;
            --danger: #dc2626;
            --warning: #d97706;
        }

        * { box-sizing: border-box; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; margin: 0; padding: 0; }
        body { 
            background: linear-gradient(135deg, #0f172a 0%, #1e293b 100%); 
            color: var(--text); 
            min-height: 100vh; 
            padding: 20px;
        }

        .container { max-width: 950px; margin: 0 auto; background: var(--card); padding: 30px; border-radius: 16px; box-shadow: 0 15px 35px rgba(0,0,0,0.5); border-top: 6px solid var(--accent); }
        
        h1, h2, h3 { text-align: center; color: var(--primary); margin-bottom: 15px; }

        .form-group { margin-bottom: 15px; text-align: right; }
        label { display: block; margin-bottom: 6px; font-weight: bold; color: var(--secondary); font-size: 14px; }
        input, select, textarea { width: 100%; padding: 12px; border: 1.5px solid #cbd5e1; border-radius: 8px; font-size: 15px; outline: none; transition: 0.2s; background: #fff; }
        input:focus, textarea:focus, select:focus { border-color: var(--accent); box-shadow: 0 0 8px rgba(37, 99, 235, 0.25); }
        
        .btn { width: 100%; padding: 12px; border: none; border-radius: 8px; font-size: 16px; font-weight: bold; cursor: pointer; transition: 0.2s; background: var(--primary); color: white; margin-top: 10px; box-shadow: 0 4px 10px rgba(0,0,0,0.1); }
        .btn:hover { background: var(--accent); transform: translateY(-1px); }
        .btn-success { background: var(--success); }
        .btn-success:hover { background: #15803d; }
        .btn-danger { background: var(--danger); }
        .btn-danger:hover { background: #b91c1c; }
        .btn-warning { background: var(--warning); color: #fff; }
        .btn-warning:hover { background: #b45309; }

        .nav-buttons { display: grid; grid-template-columns: repeat(2, 1fr); gap: 8px; margin-bottom: 25px; }
        .nav-btn { padding: 10px; background: #e2e8f0; border: none; border-radius: 8px; font-size: 14px; font-weight: bold; cursor: pointer; color: var(--primary); transition: 0.3s; text-align: center; }
        .nav-btn:hover, .nav-btn.active { background: var(--primary); color: white; }

        .card-box { background: #f8fafc; border: 1px solid #e2e8f0; padding: 20px; border-radius: 12px; margin-bottom: 20px; box-shadow: inset 0 2px 4px rgba(0,0,0,0.02); }
        .timer-box { font-size: 1.3rem; font-weight: bold; color: var(--danger); text-align: center; margin-bottom: 15px; background: #fee2e2; padding: 12px; border-radius: 8px; border: 1px solid #fca5a5; }
        
        .hidden { display: none !important; }
        .flex-row { display: flex; gap: 10px; }
        
        table { width: 100%; border-collapse: collapse; margin-top: 10px; background: white; border-radius: 8px; overflow: hidden; }
        th, td { padding: 12px; text-align: center; border-bottom: 1px solid #e2e8f0; font-size: 14px; }
        th { background: var(--primary); color: white; }

        .question-card-builder { background: #ffffff; border: 2px solid var(--accent); padding: 20px; border-radius: 12px; margin-bottom: 20px; position: relative; box-shadow: 0 4px 12px rgba(0,0,0,0.05); }
    </style>
</head>
<body>

<div class="container">
    <!-- شاشة الحظر التام -->
    <div id="banned-screen" class="hidden" style="text-align: center; padding: 40px;">
        <h1 style="color: var(--danger); font-size: 2.5rem;">🚫 تم حظر حسابك وجهازك نهائياً!</h1>
        <p style="font-size: 1.2rem; color: #555; margin-top: 20px;">لقد تجاوزت الحد المسموح من المحاولات الخاطئة (سواء في تسجيل الدخول أو كلمة سر الإدارة). تم حظر هذا الجهاز تماماً ولن يُسمح لك بفتح المنصة مرة أخرى.</p>
    </div>

    <!-- شاشة تسجيل الدخول الرئيسية -->
    <div id="login-screen">
        <h1 style="color: var(--primary);">منصة المعلم الذكية 🎓</h1>
        <p style="text-align: center; color: #555; margin-bottom: 20px;">النظام السحابي الموحد لإدارة المدرسين والطلاب</p>
        
        <div style="display: flex; gap: 10px; margin-bottom: 20px; justify-content: center; flex-wrap: wrap;">
            <button class="btn" id="tab-btn-student" onclick="switchLoginMode('student')" style="width: auto; padding: 10px 20px; background: var(--primary);">بوابة الطالب 👨‍🎓</button>
            <button class="btn" id="tab-btn-teacher" onclick="switchLoginMode('teacher')" style="width: auto; padding: 10px 20px; background: #64748b;">دخول مدرس 👨‍🏫</button>
            <button class="btn" id="tab-btn-admin" onclick="switchLoginMode('admin')" style="width: auto; padding: 10px 20px; background: #b91c1c;">👑 الليدر العام (الإدارة)</button>
        </div>

        <div id="box-student-login" class="card-box" style="text-align: right; max-width: 480px; margin: 0 auto;">
            <h3 style="color: var(--secondary); margin-bottom: 15px; text-align: center;">👤 تسجيل دخول أو إنشاء حساب طالب</h3>
            <div class="form-group"><label>الاسم الثلاثي:</label><input type="text" id="student-auth-name" placeholder="محمد أحمد محمود..."></div>
            <div class="form-group"><label>رقم الهاتف (أرقام فقط):</label><input type="tel" id="student-auth-phone" placeholder="01012345678" oninput="this.value = this.value.replace(/[^0-9]/g, '')" maxlength="11"></div>
            <div class="form-group"><label>الرقم السري:</label><input type="password" id="student-auth-pass" placeholder="اكتب الرقم السري الخاص بك..."></div>
            <button class="btn btn-success" onclick="handleStudentAuth()">دخول للمنصة 🚀</button>
        </div>

        <div id="box-teacher-login" class="card-box hidden" style="text-align: right; max-width: 450px; margin: 0 auto;">
            <h3 style="color: var(--secondary); margin-bottom: 15px; text-align: center;">👨‍🏫 بوابة المدرسين</h3>
            <div class="form-group"><label>كود المعلم (Teacher ID):</label><input type="text" id="teacher-login-id" placeholder="مثال: TCH-101"></div>
            <div class="form-group"><label>الرقم السري (Password):</label><input type="password" id="teacher-login-pass" placeholder="الرقم السري الخاص بك..."></div>
            <button class="btn btn-success" onclick="handleTeacherLogin()">دخول لوحة التحكم ⚙</button>
        </div>

        <div id="box-admin-login" class="card-box hidden" style="text-align: right; max-width: 600px; margin: 0 auto; border: 2px solid var(--danger);">
            <h3 style="color: var(--danger); margin-bottom: 15px; text-align: center;">👑 لوحة تحكم الليدر العام</h3>
            <div id="admin-auth-box">
                <div class="form-group"><label>كلمة سر الليدر العام:</label><input type="password" id="admin-pass-input" placeholder="أدخل كلمة سر الإدارة..."></div>
                <button class="btn btn-danger" onclick="verifyAdminAccess()">دخول للإدارة المطلقة 🔓</button>
            </div>
            <div id="admin-panel-content" class="hidden" style="margin-top: 15px;">
                <h4 style="color: var(--primary); margin-bottom: 10px;">➕ إضافة مدرس جديد للنظام:</h4>
                <div class="form-group"><label>اسم المدرس:</label><input type="text" id="new-t-name" placeholder="مثال: أستاذ المادة..."></div>
                <div class="form-group"><label>المادة / التخصص:</label><input type="text" id="new-t-subject" placeholder="مثال: جيولوجيا"></div>
                <div class="form-group"><label>كود المدرس (Teacher ID):</label><input type="text" id="new-t-code" placeholder="مثال: TCH-106"></div>
                <div class="form-group"><label>الرقم السري:</label><input type="password" id="new-t-pass" placeholder="الرقم السري..."></div>
                <button class="btn btn-success" onclick="addNewTeacher()">إضافة مدرس جديد 🚀</button>
                <hr style="margin: 20px 0;">
                <h4 style="color: var(--primary);">👨‍🏫 إدارة المدرسين:</h4>
                <div id="admin-teachers-list" style="max-height: 180px; overflow-y: auto; margin-top: 10px; border: 1px solid #cbd5e1; padding: 10px; border-radius: 8px;"></div>
            </div>
        </div>
    </div>

    <!-- لوحة تحكم المدرس المستقل -->
    <div id="teacher-dashboard" class="hidden">
        <h1 id="teacher-dash-title">لوحة تحكم المعلم ⚙</h1>
        <p style="text-align:center; color: var(--success); font-weight:bold; margin-bottom:20px;" id="teacher-welcome-msg">أهلاً بك.</p>
        
        <div class="card-box" style="border: 2px solid var(--warning); background: #fffbeb;">
            <h3>⏳ طلبات انضمام الطلاب المعلقة لمادتك</h3>
            <div id="pending-students-list" style="margin-top: 15px; max-height: 250px; overflow-y: auto;"><p>لا توجد طلبات معلقة.</p></div>
        </div>

        <div class="card-box" style="border: 2px solid var(--accent); background: #f0fdf4;">
            <h3>📝 تصحيح الامتحانات المقالية (إجابات الصور)</h3>
            <div id="pending-essays-list" style="margin-top: 15px; max-height: 350px; overflow-y: auto;"><p>لا توجد إجابات مقالية تنتظر التصحيح.</p></div>
        </div>

        <div class="card-box">
            <h3>✍️ بناء امتحان احترافي (اختياري + مقالي برفع صور)</h3>
            <div class="form-group" style="margin-top: 15px;"><label>عنوان الامتحان:</label><input type="text" id="exam-title-input" placeholder="مثال: امتحان الشهر الأول..."></div>
            <div id="dynamic-questions-container"></div>
            <div class="flex-row" style="margin-bottom: 20px;">
                <button class="btn btn-warning" onclick="addNewQuestionField('mcq')">➕ إضافة سؤال اختياري</button>
                <button class="btn btn-success" onclick="addNewQuestionField('essay')">➕ إضافة سؤال مقالي (برفع صورة)</button>
            </div>
            <div class="form-group"><label>وقت الامتحان بالدقائق:</label><input type="number" id="exam-time" value="30" min="1" max="240"></div>
            <button class="btn btn-success" onclick="publishExam()" style="background: var(--primary);">نشر الامتحان لطلابك المقبولين فقط 🚀</button>
        </div>

        <div class="card-box">
            <h3>📋 قائمة طلابك المقبولين</h3>
            <div id="teacher-students-management-list" style="max-height: 200px; overflow-y: auto;"><p>جاري التحميل...</p></div>
        </div>
        <button class="btn btn-danger" onclick="logoutSession()">تسجيل الخروج الآمن</button>
    </div>

    <!-- لوحة الطالب الرئيسية -->
    <div id="student-dashboard" class="hidden">
        <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px;">
            <h3>أهلاً بك يا بطل، <span id="welcome-name" style="color: var(--primary);"></span> 🎯</h3>
            <button class="btn btn-danger" style="width: auto; padding: 6px 12px;" onclick="logoutSession()">خروج</button>
        </div>

        <div class="nav-buttons">
            <button class="nav-btn active" onclick="switchStudentTab('teachers-list')">قائمة المدرسين 👨‍🏫</button>
            <button class="nav-btn" onclick="switchStudentTab('my-exams')">امتحاناتي ونتائجي ✅</button>
        </div>

        <div id="st-tab-teachers-list" class="tab-pane">
            <div class="card-box">
                <h3>📚 جميع المدرسين المتاحين في المنصة</h3>
                <p style="font-size: 13px; color: #555; margin-bottom: 15px;">قم بطلب الانضمام للمدرس المطلوب، ولن تتمكن من رؤية امتحاناته إلا بعد موافقته.</p>
                <div id="all-teachers-directory"></div>
            </div>
        </div>

        <div id="st-tab-my-exams" class="tab-pane hidden">
            <div class="card-box">
                <label>اختر المدرس الذي تم قبولك معه لعرض امتحاناته:</label>
                <select id="student-approved-teachers-select" onchange="loadExamsForSelectedTeacher()" style="margin-bottom: 15px;">
                    <option value="">-- اختر مدرساً معتمداً --</option>
                </select>
            </div>
            <div id="active-exam-container"><p style="text-align: center; color: #666;">اختر مدرساً لعرض الامتحانات المتاحة...</p></div>
            
            <div class="card-box" style="margin-top: 25px;">
                <h3 style="color: var(--primary); margin-bottom: 15px;">📊 سجل امتحاناتي السابقة ودرجاتي</h3>
                <div id="student-history-table-container">
                    <p style="text-align: center; color: #666;">جاري تحميل سجل النتائج...</p>
                </div>
            </div>
        </div>
    </div>

    <!-- شاشة أداء الامتحان -->
    <div id="exam-taking-screen" class="hidden">
        <div class="timer-box">الوقت المتبقي: <span id="timer-display">00:00</span></div>
        <div class="card-box">
            <h2 id="exam-view-title" style="margin:0;">عنوان الامتحان</h2>
            <div style="margin: 10px 0; font-weight: bold; color: var(--accent);"><span id="question-counter">السؤال 1 من 1</span></div>
            <p id="exam-view-text" style="font-size: 1.2rem; margin: 15px 0; font-weight: bold; line-height: 1.6;"></p>
            
            <div id="exam-question-img-container" style="text-align: center; margin: 15px 0; display: none;">
                <img id="exam-question-img" src="" alt="صورة توضيحية للسؤال" style="max-width: 100%; max-height: 250px; border-radius: 8px; border: 2px solid #cbd5e1;">
            </div>

            <div id="exam-question-content-box"></div>

            <div class="flex-row" style="margin-top: 25px;">
                <button class="btn" style="background: #64748b;" onclick="prevQuestion()">⬅ السابق</button>
                <button class="btn" onclick="nextQuestion()">التالي ➡</button>
            </div>
            <button class="btn btn-success" id="submit-exam-btn" style="margin-top: 15px;" onclick="submitExamPipeline()">تسليم الامتحان نهائياً 📩</button>
        </div>
    </div>

    <!-- شاشة عرض النتيجة المنفصلة بشكل أنيق -->
    <div id="exam-result-screen" class="hidden" style="text-align: center; padding: 20px;">
        <div class="card-box" style="max-width: 600px; margin: 0 auto; border: 3px solid var(--success);">
            <h1 style="color: var(--success); margin-bottom: 10px;">🎉 تم تسليم الامتحان بنجاح!</h1>
            <p style="font-size: 1.1rem; color: #555; margin-bottom: 20px;">هذه هي نتيجة امتحانك الحالية:</p>
            <div style="background: #f0fdf4; padding: 20px; border-radius: 12px; border: 1.5px solid #86efac; margin-bottom: 20px;">
                <h3 id="res-exam-title" style="color: var(--primary); margin-bottom: 10px;">اسم الامتحان</h3>
                <div style="font-size: 2.2rem; font-weight: bold; color: var(--success); margin: 15px 0;">
                    <span id="res-score-val">0</span> / <span id="res-total-val">0</span>
                </div>
                <div style="font-size: 1.3rem; font-weight: bold; color: var(--accent);">
                    النسبة المئوية: <span id="res-percent-val">0%</span>
                </div>
            </div>
            <p id="res-status-msg" style="font-weight: bold; color: var(--warning); margin-bottom: 20px;"></p>
            <button class="btn btn-success" onclick="returnToStudentDashboard()" style="max-width: 250px; margin: 0 auto;">العودة للوحة الرئيسية 🏠</button>
        </div>
    </div>
</div>

<script>
    const firebaseConfig = {
        databaseURL: "https://mr-mohammed-default-rtdb.firebaseio.com"
    };
    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    const defaultTeachers = {
        "TCH-101": { name: "أستاذ الكيمياء", subject: "الكيمياء", password: "Chem@2026_#1", role: "teacher" },
        "TCH-102": { name: "أستاذ الفيزياء", subject: "الفيزياء", password: "Phys@2026_#2", role: "teacher" },
        "TCH-103": { name: "أستاذ الأحياء", subject: "الأحياء", password: "Bio@2026_#3", role: "teacher" },
        "TCH-104": { name: "أستاذ اللغة العربية", subject: "اللغة العربية", password: "Arab@2026_#4", role: "teacher" }
    };

    let currentUser = null;
    let currentExamData = null;
    let timerInterval = null;
    let timeLeft = 0;
    let currentQIndex = 0;
    let studentAnswers = {}; 

    window.onload = function() {
        db.ref('teachers').once('value', (snapshot) => {
            if (!snapshot.exists()) {
                db.ref('teachers').set(defaultTeachers);
            }
        });

        const isBanned = localStorage.getItem('smart_platform_banned');
        if (isBanned === 'true') {
            triggerBanState();
            return;
        }

        const savedUser = localStorage.getItem('smart_platform_multiteacher_user');
        if (savedUser) {
            currentUser = JSON.parse(savedUser);
            db.ref('banned_users/' + currentUser.phone).once('value', (snap) => {
                if (snap.exists()) {
                    triggerBanState();
                    return;
                }
                if (currentUser.role === 'teacher') {
                    document.getElementById('login-screen').classList.add('hidden');
                    document.getElementById('teacher-dashboard').classList.remove('hidden');
                    loadTeacherDashboardData();
                } else if (currentUser.role === 'student') {
                    enterStudentDashboard();
                }
            });
        }
    };

    function triggerBanState() {
        localStorage.setItem('smart_platform_banned', 'true');
        document.body.innerHTML = `
            <div style="text-align: center; padding: 60px; font-family: Tahoma;">
                <h1 style="color: #dc2626; font-size: 2.5rem;">🚫 تم حظر وصولك للمنصة نهائياً!</h1>
                <p style="font-size: 1.2rem; color: #555; margin-top: 20px;">لقد تجاوزت الحد المسموح من محاولات الدخول الخاطئة (أو محاولة الدخول لحساب الإدارة). تم حظر هذا الجهاز ورقم الهاتف ولن يفتح معك الرابط بعد الآن.</p>
            </div>
        `;
    }

    function switchLoginMode(mode) {
        document.getElementById('box-student-login').classList.add('hidden');
        document.getElementById('box-teacher-login').classList.add('hidden');
        document.getElementById('box-admin-login').classList.add('hidden');
        
        document.getElementById('tab-btn-student').style.background = '#64748b';
        document.getElementById('tab-btn-teacher').style.background = '#64748b';
        document.getElementById('tab-btn-admin').style.background = '#64748b';

        if (mode === 'student') {
            document.getElementById('box-student-login').classList.remove('hidden');
            document.getElementById('tab-btn-student').style.background = 'var(--primary)';
        } else if (mode === 'teacher') {
            document.getElementById('box-teacher-login').classList.remove('hidden');
            document.getElementById('tab-btn-teacher').style.background = 'var(--primary)';
        } else if (mode === 'admin') {
            document.getElementById('box-admin-login').classList.remove('hidden');
            document.getElementById('tab-btn-admin').style.background = 'var(--danger)';
            loadAdminTeachers();
        }
    }

    function verifyAdminAccess() {
        const pass = document.getElementById('admin-pass-input').value.trim();
        if (pass === 'Mohamed opo9067rtypro hkjlpo') { 
            localStorage.setItem('admin_failed_attempts', '0');
            document.getElementById('admin-auth-box').classList.add('hidden');
            document.getElementById('admin-panel-content').classList.remove('hidden');
            loadAdminTeachers();
        } else {
            let currentAttempts = parseInt(localStorage.getItem('admin_failed_attempts') || '0') + 1;
            localStorage.setItem('admin_failed_attempts', currentAttempts);

            if (currentAttempts >= 3) {
                if (currentUser && currentUser.phone) {
                    db.ref('banned_users/' + currentUser.phone).set({ phone: currentUser.phone, name: currentUser.name, reason: 'Admin brute-force', timestamp: Date.now() });
                }
                triggerBanState();
            } else {
                alert(`❌ كلمة سر الليدر العام غير صحيحة! متبقي لديك (${3 - currentAttempts}) محاولات قبل حظر الجهاز نهائياً.`);
            }
        }
    }

    function addNewTeacher() {
        const name = document.getElementById('new-t-name').value.trim();
        const subject = document.getElementById('new-t-subject').value.trim();
        const code = document.getElementById('new-t-code').value.trim();
        const password = document.getElementById('new-t-pass').value.trim();

        if (!name || !code || !password) { alert('برجاء استكمال بيانات المدرس!'); return; }
        db.ref('teachers/' + code).set({ name, subject, code, password, role: 'teacher' }).then(() => {
            alert('✅ تمت إضافة المدرس بنجاح!');
            document.getElementById('new-t-name').value = '';
            document.getElementById('new-t-subject').value = '';
            document.getElementById('new-t-code').value = '';
            document.getElementById('new-t-pass').value = '';
            loadAdminTeachers();
        });
    }

    function loadAdminTeachers() {
        db.ref('teachers').on('value', (snapshot) => {
            const data = snapshot.val() || defaultTeachers;
            const container = document.getElementById('admin-teachers-list');
            let html = '';
            Object.keys(data).forEach(code => {
                let t = data[code];
                html += `
                    <div style="display: flex; justify-content: space-between; align-items: center; padding: 6px; border-bottom: 1px solid #eee; font-size:14px;">
                        <div><b>${t.name}</b> (${t.subject}) - كود: ${code}</div>
                        <button class="btn btn-danger" style="width: auto; padding: 2px 8px; font-size: 11px; margin:0;" onclick="adminDeleteTeacher('${code}')">حذف</button>
                    </div>
                `;
            });
            container.innerHTML = html;
        });
    }

    function adminDeleteTeacher(code) {
        if (confirm('هل أنت متأكد من حذف هذا المدرس؟')) {
            db.ref('teachers/' + code).remove().then(() => alert('تم الحذف.'));
        }
    }

    function handleTeacherLogin() {
        const code = document.getElementById('teacher-login-id').value.trim();
        const pass = document.getElementById('teacher-login-pass').value.trim();
        if (!code || !pass) { alert('أدخل كود المعلم والرقم السري!'); return; }

        db.ref('teachers/' + code).once('value', (snapshot) => {
            const t = snapshot.val();
            if (t && t.password === pass) {
                currentUser = { ...t, id: code };
                localStorage.setItem('smart_platform_multiteacher_user', JSON.stringify(currentUser));
                document.getElementById('login-screen').classList.add('hidden');
                document.getElementById('teacher-dashboard').classList.remove('hidden');
                loadTeacherDashboardData();
            } else {
                alert('❌ كود المعلم أو الرقم السري غير صحيح!');
            }
        });
    }

    function handleStudentAuth() {
        const name = document.getElementById('student-auth-name').value.trim();
        const phone = document.getElementById('student-auth-phone').value.trim();
        const password = document.getElementById('student-auth-pass').value.trim();

        if (!name || name.split(' ').length < 3) { alert('يجب كتابة الاسم الثلاثي إجبارياً!'); return; }
        if (!phone || phone.length < 10) { alert('أدخل رقم هاتف صحيح (10 أرقام على الأقل)!'); return; }
        if (!password) { alert('أدخل الرقم السري!'); return; }

        db.ref('banned_users/' + phone).once('value', (bSnap) => {
            if (bSnap.exists()) {
                triggerBanState();
                return;
            }

            db.ref('students_accounts/' + phone).once('value', (snapshot) => {
                let acc = snapshot.val();
                if (acc) {
                    if (acc.password === password) {
                        db.ref('failed_attempts/' + phone).remove();
                        currentUser = { ...acc, phone, role: 'student' };
                        localStorage.setItem('smart_platform_multiteacher_user', JSON.stringify(currentUser));
                        enterStudentDashboard();
                    } else {
                        db.ref('failed_attempts/' + phone).transaction((currentAttempts) => {
                            return (currentAttempts || 0) + 1;
                        }, (error, committed, snapshotVal) => {
                            let attempts = snapshotVal || 1;
                            if (attempts >= 3) {
                                db.ref('banned_users/' + phone).set({ phone, name, timestamp: Date.now() }).then(() => {
                                    triggerBanState();
                                });
                            } else {
                                alert(`❌ الرقم السري غير صحيح! متبقي لديك (${3 - attempts}) محاولات قبل حظر الحساب نهائياً.`);
                            }
                        });
                    }
                } else {
                    const newAcc = { name, password, role: 'student', createdAt: Date.now() };
                    db.ref('students_accounts/' + phone).set(newAcc).then(() => {
                        currentUser = { ...newAcc, phone };
                        localStorage.setItem('smart_platform_multiteacher_user', JSON.stringify(currentUser));
                        alert('✅ تم إنشاء حسابك بنجاح!');
                        enterStudentDashboard();
                    });
                }
            });
        });
    }

    function logoutSession() {
        localStorage.removeItem('smart_platform_multiteacher_user');
        location.reload();
    }

    function loadTeacherDashboardData() {
        document.getElementById('teacher-dash-title').innerText = `لوحة تحكم المعلم: ${currentUser.name}`;
        document.getElementById('teacher-welcome-msg').innerText = `المادة: ${currentUser.subject} | كود: ${currentUser.id}`;

        db.ref('pending_students/' + currentUser.id).on('value', (snapshot) => {
            const data = snapshot.val();
            const container = document.getElementById('pending-students-list');
            if (!data) { container.innerHTML = '<p>لا توجد طلبات انضمام معلقة.</p>'; return; }

            let html = '';
            Object.keys(data).forEach(studentPhone => {
                let s = data[studentPhone];
                html += `
                    <div style="background: white; padding: 12px; border-radius: 8px; margin-bottom: 10px; border: 1.5px solid #cbd5e1; display: flex; justify-content: space-between; align-items: center;">
                        <div><b>${s.name}</b> (هاتف: ${studentPhone})</div>
                        <div class="flex-row">
                            <button class="btn btn-success" style="padding: 4px 10px;" onclick="approveStudent('${studentPhone}', '${s.name}')">موافقة ✅</button>
                            <button class="btn btn-danger" style="padding: 4px 10px;" onclick="rejectStudent('${studentPhone}')">رفض ❌</button>
                        </div>
                    </div>
                `;
            });
            container.innerHTML = html;
        });

        db.ref('pending_essays_grading/' + currentUser.id).on('value', (snapshot) => {
            const data = snapshot.val();
            const container = document.getElementById('pending-essays-list');
            if (!data) { container.innerHTML = '<p>لا توجد إجابات مقالية تنتظر التصحيح.</p>'; return; }

            let html = '';
            Object.keys(data).forEach(examKey => {
                let submissions = data[examKey];
                Object.keys(submissions).forEach(studentPhone => {
                    let sub = submissions[studentPhone];
                    let safeExamKey = examKey.replace(/\./g, '_');
                    html += `
                        <div style="background: white; padding: 15px; border-radius: 8px; margin-bottom: 15px; border: 2px solid var(--accent);">
                            <p><b>الطالب:</b> ${sub.studentName} | <b>الامتحان:</b> ${sub.examTitle}</p>
                            <p style="margin-top: 5px;"><b>السؤال المقالي:</b> ${sub.questionText}</p>
                            ${sub.questionImage ? `<div style="margin:5px 0;"><img src="${sub.questionImage}" style="max-height:80px; border-radius:4px;"></div>` : ''}
                            
                            <p style="margin-top: 10px; font-weight: bold; color: var(--primary);">📷 صورة إجابة الطالب:</p>
                            <div style="margin: 5px 0; background: #f8fafc; padding: 10px; border-radius: 8px; border: 1px dashed #cbd5e1; text-align: center;">
                                ${sub.studentImageAnswer ? `<a href="${sub.studentImageAnswer}" target="_blank"><img src="${sub.studentImageAnswer}" style="max-height: 220px; border-radius: 6px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);"></a><br><small style="color:var(--accent);">اضغط على الصورة لتكبيرها</small>` : '<span style="color:var(--danger);">لم يقم الطالب برفع صورة للإجابة!</span>'}
                            </div>
                            
                            <div class="form-group" style="margin-top: 10px;">
                                <label>الدرجة الممنوحة:</label>
                                <input type="number" id="grade_${studentPhone}_${safeExamKey}" placeholder="مثال: 5" min="0" max="20" style="width: 150px;">
                            </div>
                            <div class="form-group">
                                <label>تعليق المعلم / ملاحظات:</label>
                                <textarea id="feedback_${studentPhone}_${safeExamKey}" placeholder="اكتب تعليقاً للطالب..." rows="2"></textarea>
                            </div>
                            <button class="btn btn-success" onclick="submitEssayGrade('${examKey}', '${studentPhone}', '${sub.examTitle}', ${sub.mcqScore || 0}, ${sub.maxTotal || 10})">اعتماد الدرجة وإرسالها للطالب ✅</button>
                        </div>
                    `;
                });
            });
            container.innerHTML = html;
        });

        db.ref('approved_students/' + currentUser.id).on('value', (snapshot) => {
            const data = snapshot.val();
            const container = document.getElementById('teacher-students-management-list');
            if (!data) { container.innerHTML = '<p>لا يوجد طلاب مقبولون حالياً.</p>'; return; }

            let html = '';
            Object.keys(data).forEach(phone => {
                let s = data[phone];
                html += `
                    <div style="display: flex; justify-content: space-between; align-items: center; padding: 8px; border-bottom: 1px solid #eee;">
                        <div><b>${s.name}</b> <span style="font-size: 12px; color: #555;">(${phone})</span></div>
                        <button class="btn btn-danger" style="width: auto; padding: 4px 10px; font-size: 12px; margin:0;" onclick="removeStudent('${phone}')">إلغاء القبول 🚫</button>
                    </div>
                `;
            });
            container.innerHTML = html;
        });
    }

    function approveStudent(phone, name) {
        db.ref(`approved_students/${currentUser.id}/${phone}`).set({ name, phone }).then(() => {
            db.ref(`pending_students/${currentUser.id}/${phone}`).remove();
            alert('✅ تم قبول الطالب بنجاح!');
        });
    }

    function rejectStudent(phone) {
        db.ref(`pending_students/${currentUser.id}/${phone}`).remove();
    }

    function removeStudent(phone) {
        if (confirm('هل تريد إزالة الطالب من مادتك؟')) {
            db.ref(`approved_students/${currentUser.id}/${phone}`).remove();
        }
    }

    function submitEssayGrade(examKey, studentPhone, examTitle, mcqScore, maxTotal) {
        let safeExamKey = examKey.replace(/\./g, '_');
        let gradeInput = document.getElementById(`grade_${studentPhone}_${safeExamKey}`).value;
        let feedbackInput = document.getElementById(`feedback_${studentPhone}_${safeExamKey}`).value.trim();
        let essayScore = parseFloat(gradeInput);

        if (isNaN(essayScore)) { alert('أدخل درجة صحيحة!'); return; }

        db.ref(`exam_results/${studentPhone}/${safeExamKey}`).once('value', (snap) => {
            let existingRecord = snap.val() || { examTitle, subject: currentUser.subject, teacherName: currentUser.name, mcqScore, maxTotal };
            let finalMaxTotal = existingRecord.maxTotal || maxTotal;
            let totalScore = (existingRecord.mcqScore || mcqScore) + essayScore;
            let percentage = Math.round((totalScore / finalMaxTotal) * 100);

            const finalRecord = {
                ...existingRecord,
                essayScore,
                totalScore,
                maxTotal: finalMaxTotal,
                percentage,
                feedback: feedbackInput,
                status: 'مصحح بالكامل ✅',
                timestamp: Date.now()
            };

            db.ref(`exam_results/${studentPhone}/${safeExamKey}`).set(finalRecord).then(() => {
                db.ref(`pending_essays_grading/${currentUser.id}/${examKey}/${studentPhone}`).remove().then(() => {
                    alert('✅ تمت اعتماد درجة المقالي وتحديث النتيجة للطالب بنجاح!');
                });
            });
        });
    }

    function addNewQuestionField(type = 'mcq') {
        const container = document.getElementById('dynamic-questions-container');
        const qCount = container.children.length + 1;
        const qCard = document.createElement('div');
        qCard.className = 'question-card-builder';
        qCard.dataset.qtype = type;

        if (type === 'mcq') {
            qCard.innerHTML = `
                <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 12px;">
                    <h4 style="color: var(--primary);">سؤال اختياري رقم (<span class="q-number">${qCount}</span>)</h4>
                    <button type="button" class="btn btn-danger" style="width: auto; padding: 5px 12px;" onclick="this.closest('.question-card-builder').remove()">حذف 🗑️</button>
                </div>
                <div class="form-group"><label>نص السؤال:</label><textarea class="m-q-text" rows="2"></textarea></div>
                <div class="form-group"><label>📷 صورة توضيحية (اختياري):</label><input type="file" class="m-q-img-file" accept="image/*" onchange="previewQImg(this)"><input type="hidden" class="m-q-img-data"><div class="img-preview-box"></div></div>
                <div class="form-group"><input type="text" class="m-opt-0" placeholder="الخيار الأول"></div>
                <div class="form-group"><input type="text" class="m-opt-1" placeholder="الخيار الثاني"></div>
                <div class="form-group"><input type="text" class="m-opt-2" placeholder="الخيار الثالث"></div>
                <div class="form-group"><input type="text" class="m-opt-3" placeholder="الخيار الرابع"></div>
                <div class="form-group"><label>الإجابة الصحيحة:</label><select class="m-correct"><option value="0">الخيار الأول</option><option value="1">الخيار الثاني</option><option value="2">الخيار الثالث</option><option value="3">الخيار الرابع</option></select></div>
            `;
        } else {
            qCard.innerHTML = `
                <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 12px;">
                    <h4 style="color: var(--success);">سؤال مقالي (برفع صورة) رقم (<span class="q-number">${qCount}</span>)</h4>
                    <button type="button" class="btn btn-danger" style="width: auto; padding: 5px 12px;" onclick="this.closest('.question-card-builder').remove()">حذف 🗑️</button>
                </div>
                <div class="form-group"><label>نص السؤال المقالي:</label><textarea class="m-q-text" rows="2" placeholder="اكتب السؤال أو اطلب رسم أو حل مسألة..."></textarea></div>
                <div class="form-group"><label>📷 صورة توضيحية للسؤال (اختياري):</label><input type="file" class="m-q-img-file" accept="image/*" onchange="previewQImg(this)"><input type="hidden" class="m-q-img-data"><div class="img-preview-box"></div></div>
            `;
        }
        container.appendChild(qCard);
    }

    function previewQImg(input) {
        if (input.files && input.files[0]) {
            const reader = new FileReader();
            reader.onload = function(e) {
                const b64 = e.target.result;
                const card = input.closest('.question-card-builder');
                card.querySelector('.m-q-img-data').value = b64;
                card.querySelector('.img-preview-box').innerHTML = `<img src="${b64}" style="max-height: 80px; border-radius: 6px; margin-top:5px;">`;
            };
            reader.readAsDataURL(input.files[0]);
        }
    }

    function publishExam() {
        const title = document.getElementById('exam-title-input').value.trim();
        const duration = parseInt(document.getElementById('exam-time').value) * 60;
        const questionCards = document.querySelectorAll('.question-card-builder');
        if (!title || questionCards.length === 0) { alert('أدخل عنوان الامتحان وأضف سؤالاً واحداً على الأقل!'); return; }

        let questionsList = [];
        let maxTotal = 0;
        questionCards.forEach((card) => {
            let type = card.dataset.qtype;
            let text = card.querySelector('.m-q-text').value.trim();
            let image = card.querySelector('.m-q-img-data').value;
            if (type === 'mcq') {
                maxTotal += 10;
                let opts = [
                    card.querySelector('.m-opt-0').value.trim(),
                    card.querySelector('.m-opt-1').value.trim(),
                    card.querySelector('.m-opt-2').value.trim(),
                    card.querySelector('.m-opt-3').value.trim()
                ];
                let correct = parseInt(card.querySelector('.m-correct').value);
                questionsList.push({ type: 'mcq', text, image, options: opts, correct });
            } else {
                maxTotal += 10;
                questionsList.push({ type: 'essay', text, image });
            }
        });

        const examData = {
            id: 'exam_' + Date.now(), title, teacherId: currentUser.id,
            teacherName: currentUser.name, subject: currentUser.subject,
            questions: questionsList, duration, maxTotal, timestamp: Date.now()
        };

        db.ref('active_exams/' + currentUser.id).set(examData).then(() => {
            alert('🚀 تم نشر الامتحان لطلابك المقبولين بنجاح!');
        });
    }

    function enterStudentDashboard() {
        document.getElementById('login-screen').classList.add('hidden');
        document.getElementById('student-dashboard').classList.remove('hidden');
        document.getElementById('welcome-name').innerText = currentUser.name;
        loadAllTeachersDirectory();
        loadStudentApprovedTeachersDropdown();
        loadStudentExamsHistory();
    }

    function switchStudentTab(tabId) {
        document.querySelectorAll('#student-dashboard .tab-pane').forEach(el => el.classList.add('hidden'));
        document.querySelectorAll('#student-dashboard .nav-btn').forEach(el => el.classList.remove('active'));
        document.getElementById('st-tab-' + tabId).classList.remove('hidden');
        event.currentTarget.classList.add('active');
    }

    function loadAllTeachersDirectory() {
        db.ref('teachers').once('value', (tSnap) => {
            const teachers = tSnap.val() || defaultTeachers;
            db.ref('approved_students').once('value', (appSnap) => {
                const approvedData = appSnap.val() || {};
                db.ref('pending_students').once('value', (pendSnap) => {
                    const pendingData = pendSnap.val() || {};

                    let html = '';
                    Object.keys(teachers).forEach(tId => {
                        let t = teachers[tId];
                        let isApproved = approvedData[tId] && approvedData[tId][currentUser.phone];
                        let isPending = pendingData[tId] && pendingData[tId][currentUser.phone];

                        let statusBtn = '';
                        if (isApproved) {
                            statusBtn = `<span style="color: var(--success); font-weight: bold;">معتمد ومفعل ✅</span>`;
                        } else if (isPending) {
                            statusBtn = `<span style="color: var(--warning); font-weight: bold;">الطلب قيد المراجعة ⏳</span>`;
                        } else {
                            statusBtn = `<button class="btn btn-success" style="width: auto; padding: 6px 15px;" onclick="requestJoinTeacher('${tId}')">طلب انضمام 📩</button>`;
                        }

                        html += `
                            <div style="display: flex; justify-content: space-between; align-items: center; padding: 12px; border-bottom: 1px solid #eee;">
                                <div><b>${t.name}</b> - المادة: <span style="color:var(--accent);">${t.subject}</span></div>
                                <div>${statusBtn}</div>
                            </div>
                        `;
                    });
                    document.getElementById('all-teachers-directory').innerHTML = html;
                });
            });
        });
    }

    function requestJoinTeacher(teacherId) {
        db.ref(`pending_students/${teacherId}/${currentUser.phone}`).set({
            name: currentUser.name, phone: currentUser.phone, timestamp: Date.now()
        }).then(() => {
            alert('✅ تم إرسال طلب الانضمام للمدرس بنجاح!');
            loadAllTeachersDirectory();
        });
    }

    function loadStudentApprovedTeachersDropdown() {
        db.ref('approved_students').once('value', (snapshot) => {
            const allApproved = snapshot.val() || {};
            db.ref('teachers').once('value', (tSnap) => {
                const teachers = tSnap.val() || defaultTeachers;
                let optionsHTML = '<option value="">-- اختر مدرساً معتمداً --</option>';

                Object.keys(allApproved).forEach(tId => {
                    if (allApproved[tId][currentUser.phone]) {
                        let t = teachers[tId] || { name: tId, subject: '' };
                        optionsHTML += `<option value="${tId}">${t.name} (${t.subject})</option>`;
                    }
                });
                document.getElementById('student-approved-teachers-select').innerHTML = optionsHTML;
            });
        });
    }

    function loadExamsForSelectedTeacher() {
        const teacherId = document.getElementById('student-approved-teachers-select').value;
        const container = document.getElementById('active-exam-container');
        if (!teacherId) { container.innerHTML = '<p>اختر مدرساً لعرض الامتحانات.</p>'; return; }

        db.ref('active_exams/' + teacherId).once('value', (snapshot) => {
            currentExamData = snapshot.val();
            if (!currentExamData) { container.innerHTML = '<p style="text-align: center; color: #666;">لا توجد امتحانات جديدة منشورة من هذا المدرس حالياً.</p>'; return; }

            container.innerHTML = `
                <div class="card-box" style="border: 2px solid var(--accent);">
                    <h3 style="color: var(--primary);">${currentExamData.title}</h3>
                    <p style="margin: 10px 0; color: #555;">المادة: ${currentExamData.subject} | المدة: ${Math.floor(currentExamData.duration / 60)} دقائق</p>
                    <button class="btn btn-success" onclick="startExamView()">ابدأ الامتحان الآن 🚀</button>
                </div>
            `;
        });
    }

    function loadStudentExamsHistory() {
        db.ref('exam_results/' + currentUser.phone).on('value', (snapshot) => {
            const data = snapshot.val();
            const container = document.getElementById('student-history-table-container');
            if (!data) {
                container.innerHTML = '<p style="text-align: center; color: #666;">لم تقم بأي امتحانات مسجلة حتى الآن.</p>';
                return;
            }

            let html = `
                <table>
                    <thead>
                        <tr>
                            <th>اسم الامتحان</th>
                            <th>المادة</th>
                            <th>المعلم</th>
                            <th>الدرجة</th>
                            <th>النسبة</th>
                            <th>الحالة</th>
                            <th>ملاحظات المعلم</th>
                        </tr>
                    </thead>
                    <tbody>
            `;

            Object.keys(data).forEach(examKey => {
                let res = data[examKey];
                let score = res.totalScore !== undefined ? res.totalScore : (res.mcqScore || 0);
                let max = res.maxTotal || 10;
                let perc = res.percentage !== undefined ? res.percentage + '%' : Math.round((score/max)*100) + '%';
                html += `
                    <tr>
                        <td><b>${res.examTitle}</b></td>
                        <td>${res.subject}</td>
                        <td>${res.teacherName}</td>
                        <td><span style="color:var(--success); font-weight:bold;">${score} / ${max}</span></td>
                        <td><span style="color:var(--accent); font-weight:bold;">${perc}</span></td>
                        <td><span style="color:var(--primary);">${res.status || 'قيد تصحيح المقالي ⏳'}</span></td>
                        <td>${res.feedback || 'لا توجد ملاحظات'}</td>
                    </tr>
                `;
            });

            html += `</tbody></table>`;
            container.innerHTML = html;
        });
    }

    function startExamView() {
        document.getElementById('student-dashboard').classList.add('hidden');
        document.getElementById('exam-taking-screen').classList.remove('hidden');
        document.getElementById('exam-view-title').innerText = currentExamData.title;
        currentQIndex = 0;
        studentAnswers = {};
        renderExamQuestion();
        timeLeft = currentExamData.duration;
        startTimer();
    }

    function renderExamQuestion() {
        const q = currentExamData.questions[currentQIndex];
        document.getElementById('question-counter').innerText = `السؤال ${currentQIndex + 1} من ${currentExamData.questions.length} (${q.type === 'essay' ? 'مقالي برفع صورة' : 'اختياري'})`;
        document.getElementById('exam-view-text').innerText = q.text;

        const imgContainer = document.getElementById('exam-question-img-container');
        const imgElement = document.getElementById('exam-question-img');
        if (q.image) {
            imgElement.src = q.image;
            imgContainer.style.display = 'block';
        } else {
            imgContainer.style.display = 'none';
        }

        const contentBox = document.getElementById('exam-question-content-box');
        if (q.type === 'mcq') {
            let html = '';
            q.options.forEach((opt, idx) => {
                if (!opt) return;
                let checked = studentAnswers[currentQIndex] === idx ? 'checked' : '';
                html += `
                    <label style="display: block; padding: 12px; margin: 10px 0; background: white; border: 1.5px solid #cbd5e1; border-radius: 8px; cursor: pointer;">
                        <input type="radio" name="st_mcq" value="${idx}" ${checked} onchange="studentAnswers[${currentQIndex}] = ${idx}" style="margin-left: 10px;"> ${opt}
                    </label>
                `;
            });
            contentBox.innerHTML = html;
        } else {
            let savedImg = studentAnswers[currentQIndex] || '';
            contentBox.innerHTML = `
                <div class="form-group" style="background: #f1f5f9; padding: 15px; border-radius: 8px;">
                    <label>📷 قم برفع صورة إجابتك على هذا السؤال المقالي:</label>
                    <input type="file" accept="image/*" onchange="uploadStudentEssayAnswer(this, ${currentQIndex})">
                    <div style="margin-top: 10px;" id="essay_preview_${currentQIndex}">
                        ${savedImg ? `<img src="${savedImg}" style="max-height: 150px; border-radius: 6px; border: 1px solid #ccc;">` : ''}
                    </div>
                </div>
            `;
        }
    }

    function uploadStudentEssayAnswer(input, qIdx) {
        if (input.files && input.files[0]) {
            const reader = new FileReader();
            reader.onload = function(e) {
                let b64 = e.target.result;
                studentAnswers[qIdx] = b64;
                document.getElementById(`essay_preview_${qIdx}`).innerHTML = `<img src="${b64}" style="max-height: 150px; border-radius: 6px; border: 1px solid #ccc;">`;
            };
            reader.readAsDataURL(input.files[0]);
        }
    }

    function nextQuestion() {
        if (currentQIndex < currentExamData.questions.length - 1) { currentQIndex++; renderExamQuestion(); }
        else { alert('هذا هو السؤال الأخير.'); }
    }

    function prevQuestion() {
        if (currentQIndex > 0) { currentQIndex--; renderExamQuestion(); }
    }

    function startTimer() {
        clearInterval(timerInterval);
        timerInterval = setInterval(() => {
            let m = Math.floor(timeLeft / 60);
            let s = timeLeft % 60;
            document.getElementById('timer-display').innerText = `${m.toString().padStart(2,'0')}:${s.toString().padStart(2,'0')}`;
            if (timeLeft <= 0) {
                clearInterval(timerInterval);
                alert('انتهى الوقت!');
                submitExamPipeline();
            }
            timeLeft--;
        }, 1000);
    }

    function submitExamPipeline() {
        clearInterval(timerInterval);
        let mcqScore = 0;
        let hasEssay = false;
        let safeExamKey = currentExamData.id.replace(/\./g, '_');
        let maxTotal = currentExamData.maxTotal || (currentExamData.questions.length * 10);

        currentExamData.questions.forEach((q, idx) => {
            if (q.type === 'mcq') {
                if (studentAnswers[idx] === q.correct) mcqScore += 10;
            } else if (q.type === 'essay') {
                hasEssay = true;
                let essayPayload = {
                    studentName: currentUser.name,
                    examTitle: currentExamData.title,
                    questionText: q.text,
                    questionImage: q.image || '',
                    studentImageAnswer: studentAnswers[idx] || '',
                    mcqScore: mcqScore,
                    maxTotal: maxTotal,
                    timestamp: Date.now()
                };
                db.ref(`pending_essays_grading/${currentExamData.teacherId}/${currentExamData.id}/${currentUser.phone}`).set(essayPayload);
            }
        });

        let totalScore = mcqScore;
        let percentage = Math.round((totalScore / maxTotal) * 100);

        const initialResult = {
            examTitle: currentExamData.title,
            teacherName: currentExamData.teacherName,
            subject: currentExamData.subject,
            mcqScore: mcqScore,
            totalScore: totalScore,
            maxTotal: maxTotal,
            percentage: percentage,
            status: hasEssay ? 'قيد تصحيح المقالي ⏳' : 'مصحح بالكامل ✅',
            timestamp: Date.now()
        };

        db.ref(`exam_results/${currentUser.phone}/${safeExamKey}`).set(initialResult).then(() => {
            document.getElementById('exam-taking-screen').classList.add('hidden');
            document.getElementById('exam-result-screen').classList.remove('hidden');
            document.getElementById('res-exam-title').innerText = currentExamData.title;
            document.getElementById('res-score-val').innerText = totalScore;
            document.getElementById('res-total-val').innerText = maxTotal;
            document.getElementById('res-percent-val').innerText = percentage + '%';
            document.getElementById('res-status-msg').innerText = hasEssay ? 'ملاحظة: تم احتساب درجة الأسئلة الاختيارية فوريًا، وبانتظار تصعيد المعلم لأسئلة المقالي.' : 'تم تصحيح امتحانك بالكامل واعتماد النتيجة بنجاح!';
        });
    }

    function returnToStudentDashboard() {
        document.getElementById('exam-result-screen').classList.add('hidden');
        enterStudentDashboard();
    }
</script>

</body>
</html>
