<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ALR GLOBAL | ياسمين</title>
    <style>
        :root { --bg: #000; --card: #111; --accent: #00ff00; }
        body { margin: 0; padding: 20px; background: var(--bg); color: #fff; font-family: sans-serif; display: flex; flex-direction: column; align-items: center; }
        
        /* Logo Animation */
        .alr-logo { font-size: 35px; font-weight: 900; background: linear-gradient(45deg, #ff0000, #ffff00, #00ff00, #0000ff, #8b00ff); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-size: 400%; animation: flow 5s linear infinite; margin-bottom: 20px; }
        @keyframes flow { 0% { background-position: 0% 50%; } 100% { background-position: 100% 50%; } }

        .screen { display: none; width: 100%; max-width: 400px; }
        .active { display: block; }

        /* Auth Tabs */
        .auth-tabs { display: flex; gap: 10px; margin-bottom: 20px; width: 100%; }
        .tab-btn { flex: 1; padding: 10px; background: #222; border: 1px solid #333; color: #777; cursor: pointer; border-radius: 8px; font-weight: bold; transition: 0.3s; }
        .tab-btn.active-tab { background: #fff; color: #000; border-color: #fff; }

        /* UI Elements */
        input, textarea { width: 100%; padding: 12px; margin-bottom: 10px; background: #1a1a1a; border: 1px solid #333; color: white; border-radius: 8px; box-sizing: border-box; }
        .btn { width: 100%; padding: 12px; border: none; border-radius: 25px; font-weight: bold; cursor: pointer; margin-top: 10px; }
        .btn-primary { background: white; color: black; }
        .btn-logout { background: #ff4444; color: white; }

        .product-card { background: var(--card); border: 1px solid #222; border-radius: 15px; padding: 15px; margin-bottom: 15px; }
        .price-row { display: flex; justify-content: space-between; align-items: center; }
        .price-tag { color: var(--accent); font-weight: bold; }
        
        .cart-status { position: fixed; bottom: 20px; background: rgba(255,255,255,0.1); backdrop-filter: blur(10px); padding: 10px 20px; border-radius: 30px; border: 1px solid #444; display: none; gap: 15px; align-items: center; }
    </style>
</head>
<body>

    <div class="alr-logo">ALR GLOBAL</div>

    <div id="auth-screen" class="screen active">
        <div class="auth-tabs">
            <button id="tab-login" class="tab-btn active-tab" onclick="switchTab('login')">تسجيل دخول</button>
            <button id="tab-reg" class="tab-btn" onclick="switchTab('reg')">حساب جديد</button>
        </div>

        <div id="login-fields">
            <input type="text" id="user" placeholder="الاسم أو المستخدم">
            <input type="password" id="pass" placeholder="كلمة السر">
            <button class="btn btn-primary" onclick="app.login()">دخول</button>
        </div>

        <div id="reg-fields" style="display:none">
            <input type="text" id="reg-user" placeholder="الاسم الكامل">
            <input type="text" id="reg-phone" placeholder="رقم الموبايل">
            <input type="text" id="reg-addr" placeholder="العنوان بالتفصيل">
            <input type="password" id="reg-pass" placeholder="اختر كلمة سر">
            <button class="btn btn-primary" onclick="app.register()">إنشاء الحساب</button>
        </div>
    </div>

    <div id="store-screen" class="screen">
        <div id="admin-panel" style="display: none; border: 1px dashed gold; padding: 15px; border-radius: 15px; margin-bottom: 20px;">
            <p style="color: gold;">لوحة الإدارة (ياسمين)</p>
            <input type="text" id="p-name" placeholder="اسم المنتج">
            <input type="number" id="p-price" placeholder="السعر">
            <button class="btn btn-primary" onclick="app.addProduct()">نشر</button>
        </div>

        <div id="items-list"></div>
        <button class="btn btn-logout" onclick="location.reload()">خروج</button>
    </div>

    <div id="cart-bar" class="cart-status">
        <span>🛒 <span id="c-count">0</span> | <span id="c-total">0</span> EGP</span>
        <button class="btn-primary" style="padding:5px 15px; border-radius:10px" onclick="app.checkout()">طلب</button>
    </div>

    <script>
        // وظيفة التبديل بين التبويبات
        function switchTab(type) {
            const loginFields = document.getElementById('login-fields');
            const regFields = document.getElementById('reg-fields');
            const tabLogin = document.getElementById('tab-login');
            const tabReg = document.getElementById('tab-reg');

            if (type === 'login') {
                loginFields.style.display = 'block';
                regFields.style.display = 'none';
                tabLogin.classList.add('active-tab');
                tabReg.classList.remove('active-tab');
            } else {
                loginFields.style.display = 'none';
                regFields.style.display = 'block';
                tabReg.classList.add('active-tab');
                tabLogin.classList.remove('active-tab');
            }
        }

        const app = {
            products: [], cart: [], total: 0, user: null,

            login: function() {
                const u = document.getElementById('user').value, p = document.getElementById('pass').value;
                if (u === "asd" && p === "09876u") {
                    document.getElementById('admin-panel').style.display = "block";
                    this.start("ياسمين");
                } else if (u && p) {
                    this.start(u);
                } else {
                    alert("برجاء إدخال البيانات");
                }
            },

            register: function() {
                const u = document.getElementById('reg-user').value;
                if(u) {
                    alert("تم إنشاء الحساب بنجاح!");
                    this.start(u);
                } else {
                    alert("برجاء إكمال البيانات");
                }
            },

            start: function(name) {
                this.user = name;
                document.getElementById('auth-screen').classList.remove('active');
                document.getElementById('store-screen').classList.add('active');
                document.getElementById('cart-bar').style.display = "flex";
            },

            addProduct: function() {
                const n = document.getElementById('p-name').value, pr = document.getElementById('p-price').value;
                if(n && pr) {
                    this.products.unshift({n, pr: parseFloat(pr)});
                    this.render();
                    document.getElementById('p-name').value = '';
                    document.getElementById('p-price').value = '';
                }
            },

            render: function() {
                const list = document.getElementById('items-list');
                list.innerHTML = this.products.map(p => `
                    <div class="product-card">
                        <h3>${p.n}</h3>
                        <div class="price-row">
                            <span class="price-tag">${p.pr} EGP</span>
                            <button style="border-radius:50%; width:30px; height:30px; background:white; border:none; cursor:pointer;" onclick="app.add('${p.n}', ${p.pr})">+</button>
                        </div>
                    </div>`).join('');
            },

            add: function(n, pr) {
                this.cart.push(n); this.total += pr;
                document.getElementById('c-count').innerText = this.cart.length;
                document.getElementById('c-total').innerText = this.total;
            },

            checkout: function() {
                alert(`طلب جديد من: ${this.user}\nالأصناف: ${this.cart.join(' - ')}\nالإجمالي: ${this.total} EGP\nواتساب: 01092370886`);
            }
        };
    </script>
</body>
</html>
