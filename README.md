<!doctype html>
<html lang="ko">
<head>
    <meta charset="utf-8" />
    <!-- 📱 모바일 배율 수정 -->
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0" />
    <title>야시장 메뉴 - 장바구니</title>
    <style>
        :root {
            --accent: #d9534f;
            --bg: #fafafa;
            --card: #fff;
            --muted: #666
        }

        * { box-sizing: border-box }

        body {
            font-family: system-ui, -apple-system, "Noto Sans KR", "Segoe UI", Roboto, "Helvetica Neue", Arial;
            margin: 0;
            background: var(--bg);
            color: #222
        }

        header {
            background: linear-gradient(90deg,#ffefef,#fff);
            padding: 20px 16px;
            box-shadow: 0 1px 4px rgba(0,0,0,0.04)
        }

        .container {
            max-width: 980px;
            margin: 24px auto;
            padding: 0 16px
        }

        h1 { margin: 0; font-size: 1.3rem }

        .layout {
            display: grid;
            grid-template-columns: 1fr 340px;
            gap: 20px;
            margin-top: 18px
        }

        @media (max-width:900px) {
            .layout { grid-template-columns: 1fr; }
            .sidebar { order: 2 }
        }

        .card {
            background: var(--card);
            border-radius: 12px;
            padding: 16px;
            box-shadow: 0 6px 18px rgba(0,0,0,0.04)
        }

        .menu-section { margin-bottom: 12px }
        .menu-title {
            display: flex; align-items: center; justify-content: space-between;
            margin-bottom: 8px
        }

        .menu-grid {
            display: grid;
            grid-template-columns: repeat(2,1fr);
            gap: 10px
        }

        @media (max-width:480px) {
            .menu-grid { grid-template-columns: 1fr }
        }

        .item {
            padding: 10px;
            border: 1px solid #f0f0f0;
            border-radius: 8px;
            display: flex;
            flex-direction: column;
            gap: 8px
        }
        .item .row { display: flex; justify-content: space-between; align-items: center }
        .item .name { font-weight: 600 }

        .price { font-weight: 700; color: var(--accent) }

        .btn {
            appearance: none; border: 0; padding: 6px 10px;
            border-radius: 6px; cursor: pointer;
            font-size: 0.85rem;
        }
        .btn-primary { background: var(--accent); color: #fff }
        .btn-ghost { background: #f6f6f6 }

        .sidebar h2 { margin-top: 0 }

        .cart-list {
            display: flex; flex-direction: column; gap: 10px;
            max-height: 380px; overflow: auto; padding-right: 4px
        }

        .cart-item {
            display: flex; justify-content: space-between;
            gap: 8px; align-items: center;
            border: 1px dashed #eee;
            padding: 10px; border-radius: 8px
        }

        .qty {
            display: inline-flex; align-items: center;
            border-radius: 6px; overflow: hidden
        }
        .qty button {
            border: 0; padding: 6px 8px;
            background: #f2f2f2; cursor: pointer
        }

        .summary { margin-top: 12px }
        .total-row {
            display: flex; justify-content: space-between; align-items: center;
            font-size: 1.1rem; font-weight: 700
        }

        .small { font-size: 0.86rem; color: var(--muted) }

        .checkout-box {
            display:none;
            margin-top: 15px;
            padding: 12px;
            border: 2px solid var(--accent);
            border-radius: 10px;
            background: #fff8f8;
        }
        .checkout-box h3 { margin-top:0; color: var(--accent); }

        /* ✅ 안내문구 스타일 (검정색 + 2초 깜빡임) */
        .notice-text {
            font-size: 1.1rem;
            font-weight: bold;
            color: #000;
            margin-top: 10px;
            text-align: center;
            animation: blink 2s step-start infinite;
        }
        @keyframes blink {
            50% { opacity: 0; }
        }

        /* ✅ 계좌번호 복사 버튼 스타일 */
        .account-box {
            display: flex;
            align-items: center;
            gap: 8px;
            flex-wrap: wrap;
        }
    </style>
</head>
<body>
    <header>
        <div class="container">
            <h1>기계공학 야시장 메뉴 가격 계산페이지</h1>
            <p class="small">메뉴를 클릭하여 장바구니에 담고 수량을 조절하여 가격을 확인하세요. 
            확인된 가격으로 결제를 하시고 직원에게 확인을 받으시면 주문이 완료됩니다.</p>
        </div>
    </header>

    <main class="container layout">
        <section class="main-area">
            <div class="card">
                <div class="menu-section">
                    <div class="menu-title"><strong>주요 메뉴</strong></div>
                    <div class="menu-grid" id="main-menu"></div>
                </div>

                <div class="menu-section">
                    <div class="menu-title"><strong>사이드</strong></div>
                    <div class="menu-grid" id="side-menu"></div>
                </div>

                <div class="menu-section">
                    <div class="menu-title"><strong>추가메뉴</strong></div>
                    <div class="menu-grid" id="add-menu"></div>
                </div>
            </div>
        </section>

        <aside class="sidebar">
            <div class="card">
                <h2>장바구니</h2>
                <div class="cart-list" id="cart-list">
                    <p class="small">아직 담긴 메뉴가 없습니다.</p>
                </div>
                <div class="summary">
                    <div class="total-row"><span>합계</span><span id="total">0원</span></div>
                    <div style="display:flex;gap:8px;margin-top:10px">
                        <button class="btn btn-primary" id="checkout">결제하기</button>
                        <button class="btn btn-ghost" id="clear-cart">비우기</button>
                    </div>
                </div>

                <!-- ✅ 결제 안내 박스 -->
                <div class="checkout-box" id="checkout-box">
                    <h3>결제 안내</h3>
                    <p>입금 금액: <span id="final-amount"></span></p>
                    <div class="account-box">
                        <p style="margin:0">입금 계좌번호: <strong id="bank-account">국민은행 123456-78-987654</strong></p>
                        <button class="btn btn-ghost" id="copy-account">복사</button>
                    </div>
                    <p class="notice-text">[이체 후 직원에게 주문 상품과 이체 완료를 확인해주세요.]</p>
                </div>
            </div>
        </aside>
    </main>

    <footer>
        제공: 기계공학과 야시장
    </footer>

    <script>
    document.addEventListener("DOMContentLoaded", () => {
        const MENUS = {
            main: [
                { id: 'dakbal', name: '닭발', price: 8000 },
                { id: 'set_full', name: '닭발+주먹밥+계란찜세트', price: 12000 }
            ],
            side: [
                { id: 'karaage', name: '가라아게', price: 5000 },
                { id: 'concheese', name: '콘치즈', price: 5000 },
                { id: 'kimchijeon', name: '김치전', price: 5000 },
                { id: 'hwangtaepo', name: '황태포', price: 5000 },
                { id: 'fries', name: '감자튀김', price: 5000 },
                { id: 'muksabal', name: '묵사발', price: 5000 }
            ],
            add: [
                { id: 'steamed_egg', name: '계란찜', price: 3000 },
                { id: 'jumeokbap', name: '주먹밥', price: 2000 },
                { id: 'drink', name: '음료', price: 2000 }
            ]
        };

        const formatKRW = v => v.toLocaleString('ko-KR') + '원';

        function createMenuCard(item) {
            const el = document.createElement('div');
            el.className = 'item';
            el.innerHTML = `
                <div class="row"><div class="name">${item.name}</div><div class="price">${formatKRW(item.price)}</div></div>
                <div style="display:flex;gap:8px;justify-content:space-between;align-items:center">
                  <div class="small">설명 추가 가능</div>
                  <button class="btn btn-primary add-btn" data-id="${item.id}" data-name="${item.name}" data-price="${item.price}">담기</button>
                </div>
            `;
            return el;
        }

        function renderMenus() {
            const main = document.getElementById('main-menu');
            const side = document.getElementById('side-menu');
            const add = document.getElementById('add-menu');
            MENUS.main.forEach(i => main.appendChild(createMenuCard(i)));
            MENUS.side.forEach(i => side.appendChild(createMenuCard(i)));
            MENUS.add.forEach(i => add.appendChild(createMenuCard(i)));
        }

        // Cart management
        let cart = {}; 
        const cartListEl = document.getElementById('cart-list');
        const totalEl = document.getElementById('total');

        function saveCart() {
            localStorage.setItem('ys_cart', JSON.stringify(cart));
        }
        function loadCart() {
            const raw = localStorage.getItem('ys_cart');
            if (raw) cart = JSON.parse(raw);
        }

        function updateCartUI() {
            cartListEl.innerHTML = '';
            const keys = Object.keys(cart);
            if (keys.length === 0) {
                cartListEl.innerHTML = '<p class="small">아직 담긴 메뉴가 없습니다.</p>';
                totalEl.textContent = formatKRW(0);
                return;
            }
            let sum = 0;
            keys.forEach(k => {
                const it = cart[k];
                sum += it.price * it.qty;
                const node = document.createElement('div');
                node.className = 'cart-item';
                node.innerHTML = `
                  <div style="flex:1">
                    <div style="font-weight:700">${it.name}</div>
                    <div class="small">${formatKRW(it.price)} x ${it.qty} = ${formatKRW(it.price * it.qty)}</div>
                  </div>
                  <div style="display:flex;flex-direction:column;gap:6px;align-items:flex-end">
                    <div class="qty">
                      <button class="dec" data-id="${it.id}">-</button>
                      <div style="padding:6px 10px">${it.qty}</div>
                      <button class="inc" data-id="${it.id}">+</button>
                    </div>
                    <button class="btn btn-ghost remove" data-id="${it.id}">삭제</button>
                  </div>
                `;
                cartListEl.appendChild(node);
            });
            totalEl.textContent = formatKRW(sum);

            // attach listeners
            cartListEl.querySelectorAll('.inc').forEach(b => b.addEventListener('click', e => {
                const id = e.currentTarget.dataset.id;
                cart[id].qty += 1; saveCart(); updateCartUI();
            }));
            cartListEl.querySelectorAll('.dec').forEach(b => b.addEventListener('click', e => {
                const id = e.currentTarget.dataset.id;
                if (cart[id].qty > 1) { cart[id].qty -= 1; } else { delete cart[id]; }
                saveCart(); updateCartUI();
            }));
            cartListEl.querySelectorAll('.remove').forEach(b => b.addEventListener('click', e => {
                const id = e.currentTarget.dataset.id; delete cart[id]; saveCart(); updateCartUI();
            }));
        }

        function addToCart(id, name, price) {
            if (cart[id]) cart[id].qty += 1;
            else cart[id] = { id, name, price, qty: 1 };
            saveCart(); updateCartUI();
        }

        // init
        renderMenus();
        loadCart();
        updateCartUI();

        // add button events
        document.body.addEventListener("click", e => {
            if (e.target.classList.contains("add-btn")) {
                const el = e.target;
                addToCart(el.dataset.id, el.dataset.name, Number(el.dataset.price));
            }
        });

        document.getElementById('clear-cart').addEventListener('click', () => {
            if (confirm('장바구니를 비우시겠습니까?')) { cart = {}; saveCart(); updateCartUI(); }
            document.getElementById('checkout-box').style.display = "none"; // 결제 안내 숨김
        });

        // ✅ 결제하기 버튼 클릭 시
        document.getElementById('checkout').addEventListener('click', () => {
            const keys = Object.keys(cart);
            if (keys.length === 0) { alert('장바구니가 비어 있습니다.'); return; }
            const sum = keys.reduce((s, k) => s + cart[k].price * cart[k].qty, 0);
            
            document.getElementById('final-amount').textContent = formatKRW(sum);
            document.getElementById('checkout-box').style.display = "block";
        });

        // ✅ 계좌번호 복사하기 기능
        document.getElementById('copy-account').addEventListener('click', () => {
            const account = document.getElementById('bank-account').innerText;
            navigator.clipboard.writeText(account).then(() => {
                alert("계좌번호가 복사되었습니다: " + account);
            }).catch(err => {
                alert("복사에 실패했습니다. 수동으로 복사해주세요.");
            });
        });
    });
    </script>
</body>
</html>
