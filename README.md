cedrick<!doctype html>
<html lang="fr">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Restaurant - Commande en ligne</title>
  <style>
    /* ---------- Reset / Base ---------- */
    :root{
      --bg:#f7f7f9;
      --card:#ffffff;
      --accent:#e53935;
      --accent-2:#ff7043;
      --muted:#6b7280;
      --glass: rgba(255,255,255,0.7);
      --shadow: 0 6px 18px rgba(16,24,40,0.08);
      --radius:12px;
      font-family: Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
    }
    *{box-sizing:border-box}
    html,body{height:100%}
    body{
      margin:0;
      background: linear-gradient(180deg,#fff 0%,var(--bg) 100%);
      color:#111827;
      -webkit-font-smoothing:antialiased;
      -moz-osx-font-smoothing:grayscale;
      padding:24px;
    }

    /* ---------- Layout ---------- */
    .app{
      max-width:1100px;
      margin:0 auto;
      display:grid;
      grid-template-columns: 1fr 360px;
      gap:20px;
      align-items:start;
    }
    header{
      grid-column:1/-1;
      display:flex;
      justify-content:space-between;
      align-items:center;
      margin-bottom:6px;
    }
    .brand{
      display:flex;
      gap:12px;
      align-items:center;
    }
    .logo{
      width:56px;height:56px;border-radius:12px;
      background:linear-gradient(135deg,var(--accent),var(--accent-2));
      display:flex;align-items:center;justify-content:center;color:white;font-weight:700;
      box-shadow:var(--shadow);
      font-size:20px;
    }
    h1{margin:0;font-size:20px}
    p.lead{margin:0;color:var(--muted);font-size:13px}

    /* ---------- Menu / Cards ---------- */
    .menu{
      display:grid;
      gap:16px;
    }
    .category{
      background:linear-gradient(180deg,var(--glass),rgba(255,255,255,0.85));
      border-radius:var(--radius);
      padding:14px;
      box-shadow:var(--shadow);
      border:1px solid rgba(0,0,0,0.03);
    }
    .category h2{margin:0 0 8px 0;font-size:16px}
    .grid-items{
      display:grid;
      gap:10px;
      grid-template-columns: repeat(auto-fit,minmax(220px,1fr));
    }
    .item{
      display:flex;
      gap:12px;
      align-items:flex-start;
      padding:10px;
      border-radius:8px;
      background:linear-gradient(180deg,#fff,#fff);
      border:1px solid rgba(0,0,0,0.04);
    }
    .item img{
      width:88px;height:64px;object-fit:cover;border-radius:8px;
      flex-shrink:0;
    }
    .item .meta{flex:1}
    .item .meta h3{margin:0;font-size:15px}
    .item .meta p{margin:6px 0 0 0;color:var(--muted);font-size:13px}
    .price-row{display:flex;align-items:center;gap:8px;margin-top:8px}
    .price{font-weight:700}
    .btn{
      border:0;padding:8px 12px;border-radius:10px;cursor:pointer;font-weight:600;
      background:var(--accent);color:white;
      transition:transform .12s ease, box-shadow .12s ease;
    }
    .btn:active{transform:translateY(1px)}
    .btn.ghost{background:transparent;color:var(--accent);border:1px solid rgba(0,0,0,0.06)}

    /* ---------- Cart (right column) ---------- */
    .cart{
      position:sticky; top:24px;
      background:var(--card); padding:16px; border-radius:var(--radius); box-shadow:var(--shadow);
      border:1px solid rgba(0,0,0,0.04);
    }
    .cart h3{margin:0 0 12px 0}
    .cart-list{display:flex;flex-direction:column;gap:10px;max-height:44vh;overflow:auto;padding-right:6px}
    .cart-row{display:flex;align-items:center;gap:10px;justify-content:space-between}
    .cart-row .info{flex:1;min-width:0}
    .cart-row .info p{margin:0;font-weight:600}
    .cart-row .controls{display:flex;gap:6px;align-items:center}
    .qty{display:inline-flex;align-items:center;border-radius:8px;padding:4px 6px;background:#f3f4f6;border:1px solid rgba(0,0,0,0.04)}
    .total{display:flex;justify-content:space-between;font-weight:800;padding:10px 0;border-top:1px dashed rgba(0,0,0,0.06);margin-top:12px}
    .small{font-size:13px;color:var(--muted)}

    /* ---------- Form ---------- */
    form .row{display:flex;gap:8px;margin-top:8px}
    input[type="text"], input[type="tel"], textarea, select{
      width:100%;padding:10px;border-radius:8px;border:1px solid rgba(0,0,0,0.06);font-size:14px;
    }
    textarea{min-height:72px;resize:vertical}
    .submit{margin-top:12px;display:flex;gap:8px;align-items:center}
    .notice{font-size:13px;color:var(--muted);margin-top:8px}

    /* ---------- Responsive ---------- */
    @media (max-width:980px){
      .app{grid-template-columns: 1fr; padding-bottom:50px}
      .cart{position:relative;top:auto}
    }
  </style>
</head>
<body>
  <div class="app" id="app">
    <header>
      <div class="brand">
        <div class="logo">R</div>
        <div>
          <h1>Restaurant Express</h1>
          <p class="lead">Commandez en ligne — livraison ou retrait</p>
        </div>
      </div>
      <div class="actions">
        <button class="btn ghost" id="clearAll">Vider le panier</button>
      </div>
    </header>

    <!-- LEFT: Menu -->
    <main class="menu" id="menu"></main>

    <!-- RIGHT: Cart & Order form -->
    <aside class="cart" aria-labelledby="cartTitle">
      <h3 id="cartTitle">Votre commande</h3>

      <div class="cart-list" id="cartList">
        <p class="small">Aucun article dans le panier.</p>
      </div>

      <div class="total">
        <span>Sous-total</span>
        <span id="subtotal">0 €</span>
      </div>

      <div class="notice">Frais de livraison calculés au paiement.</div>

      <form id="orderForm" aria-label="Formulaire de commande">
        <div class="row" style="margin-top:12px">
          <input type="text" id="name" placeholder="Nom complet" required />
        </div>
        <div class="row">
          <input type="tel" id="phone" placeholder="Téléphone" required />
        </div>
        <div class="row">
          <select id="fulfillment">
            <option value="pickup">Retrait au restaurant</option>
            <option value="delivery">Livraison</option>
          </select>
        </div>
        <div class="row" id="addressRow" style="display:none">
          <input type="text" id="address" placeholder="Adresse (rue, ville)" />
        </div>
        <div style="margin-top:8px">
          <textarea id="notes" placeholder="Notes pour la commande (allergies, instructions)"></textarea>
        </div>

        <div class="submit">
          <button class="btn" type="submit">Passer la commande</button>
          <button class="btn ghost" type="button" id="saveLocal">Sauvegarder local</button>
        </div>
        <div id="status" class="small" style="margin-top:8px"></div>
      </form>
    </aside>
  </div>

  <script>
    /**********************
     * Données du menu (exemple)
     * En production, charge depuis une API (fetch('/api/menu'))
     **********************/
    const MENU = [
      {
        id: 'burgers',
        title: 'Burgers',
        items: [
          { id:'b1', name:'Burger Classique', desc:'Bœuf, salade, tomate, sauce maison', price:7.50, img:'https://images.unsplash.com/photo-1551782450-a2132b4ba21d?q=80&w=800&auto=format&fit=crop&crop=faces' },
          { id:'b2', name:'Cheese Burger', desc:'Bœuf, cheddar fondu, pickles', price:8.90, img:'https://images.unsplash.com/photo-1550547660-d9450f859349?q=80&w=800&auto=format&fit=crop&crop=faces' }
        ]
      },
      {
        id:'pizzas',
        title:'Pizzas',
        items:[
          { id:'p1', name:'Margherita', desc:'Tomate, mozzarella, basilic', price:9.00, img:'https://images.unsplash.com/photo-1548365328-9b0d3f5d39b2?q=80&w=800&auto=format&fit=crop&crop=faces' },
          { id:'p2', name:'Pepperoni', desc:'Pepperoni, mozzarella', price:11.20, img:'https://images.unsplash.com/photo-1601924582975-d1253bf6c0b0?q=80&w=800&auto=format&fit=crop&crop=faces' }
        ]
      },
      {
        id:'drinks',
        title:'Boissons',
        items:[
          { id:'d1', name:'Soda 33cl', desc:'Canette', price:2.00, img:'https://images.unsplash.com/photo-1571689938585-c2734d6e8d50?q=80&w=800&auto=format&fit=crop&crop=faces' },
          { id:'d2', name:'Eau 50cl', desc:'Plate', price:1.50, img:'https://images.unsplash.com/photo-1505740420928-5e560c06d30e?q=80&w=800&auto=format&fit=crop&crop=faces' }
        ]
      }
    ];

    /**********************
     * État local (panier)
     **********************/
    const state = {
      cart: {}, // key=itemId -> { item, qty }
    };

    /* ------------ Helpers ------------ */
    const formatPrice = v => v.toFixed(2).replace('.',',') + ' €';

    /* ------------ Render menu ------------ */
    const menuRoot = document.getElementById('menu');
    function renderMenu(){
      menuRoot.innerHTML = '';
      MENU.forEach(cat=>{
        const catEl = document.createElement('section');
        catEl.className = 'category';
        catEl.innerHTML = `<h2>${escapeHTML(cat.title)}</h2><div class="grid-items"></div>`;
        const grid = catEl.querySelector('.grid-items');
        cat.items.forEach(it=>{
          const itemEl = document.createElement('article');
          itemEl.className = 'item';
          itemEl.innerHTML = `
            <img alt="${escapeHTML(it.name)}" src="${it.img}" onerror="this.style.display='none'">
            <div class="meta">
              <h3>${escapeHTML(it.name)}</h3>
              <p>${escapeHTML(it.desc)}</p>
              <div class="price-row">
                <div class="price">${formatPrice(it.price)}</div>
                <div style="margin-left:auto">
                  <button class="btn" data-add="${it.id}">Ajouter</button>
                </div>
              </div>
            </div>
          `;
          grid.appendChild(itemEl);
        });
        menuRoot.appendChild(catEl);
      });
    }

    /* ------------ Cart rendering ------------ */
    const cartList = document.getElementById('cartList');
    const subtotalEl = document.getElementById('subtotal');

    function renderCart(){
      const entries = Object.values(state.cart);
      cartList.innerHTML = '';
      if(entries.length === 0){
        cartList.innerHTML = '<p class="small">Aucun article dans le panier.</p>';
      } else {
        entries.forEach(row=>{
          const r = document.createElement('div');
          r.className = 'cart-row';
          r.innerHTML = `
            <div class="info">
              <p title="${escapeHTML(row.item.name)}">${escapeHTML(row.item.name)}</p>
              <div class="small">${formatPrice(row.item.price)} x ${row.qty} = <strong>${formatPrice(row.item.price * row.qty)}</strong></div>
            </div>
            <div class="controls">
              <div class="qty" data-id="${row.item.id}">
                <button class="btn ghost" data-decr="${row.item.id}" aria-label="Réduire la quantité">−</button>
                <span style="padding:0 8px">${row.qty}</span>
                <button class="btn ghost" data-incr="${row.item.id}" aria-label="Augmenter la quantité">+</button>
              </div>
              <button class="btn ghost" data-remove="${row.item.id}" title="Supprimer">Supprimer</button>
            </div>
          `;
          cartList.appendChild(r);
        });
      }
      const subtotal = entries.reduce((s,r)=>s + r.item.price * r.qty, 0);
      subtotalEl.textContent = formatPrice(subtotal);
    }

    /* ------------ Cart operations ------------ */
    function addToCart(itemId, qty=1){
      // find item in MENU
      const item = MENU.flatMap(c=>c.items).find(i=>i.id===itemId);
      if(!item) return;
      if(state.cart[itemId]) state.cart[itemId].qty += qty;
      else state.cart[itemId] = { item: structuredClone(item), qty };
      renderCart();
    }
    function changeQty(itemId, delta){
      if(!state.cart[itemId]) return;
      state.cart[itemId].qty += delta;
      if(state.cart[itemId].qty <= 0) delete state.cart[itemId];
      renderCart();
    }
    function removeItem(itemId){
      delete state.cart[itemId];
      renderCart();
    }
    function clearCart(){
      state.cart = {};
      renderCart();
    }

    /* ------------ Event delegation for menu / cart buttons ------------ */
    document.addEventListener('click', (e)=>{
      const add = e.target.closest('[data-add]');
      if(add){
        const id = add.getAttribute('data-add');
        addToCart(id,1);
        return;
      }
      const incr = e.target.closest('[data-incr]');
      if(incr){
        changeQty(incr.getAttribute('data-incr'), 1);
        return;
      }
      const decr = e.target.closest('[data-decr]');
      if(decr){
        changeQty(decr.getAttribute('data-decr'), -1);
        return;
      }
      const rem = e.target.closest('[data-remove]');
      if(rem){
        removeItem(rem.getAttribute('data-remove'));
        return;
      }
      const clr = e.target.closest('#clearAll');
      if(clr){
        if(confirm('Vider tout le panier ?')) clearCart();
        return;
      }
    });

    /* ------------ Form behavior ------------ */
    const fulfillment = document.getElementById('fulfillment');
    const addressRow = document.getElementById('addressRow');
    fulfillment.addEventListener('change', ()=>{
      addressRow.style.display = fulfillment.value === 'delivery' ? 'block' : 'none';
    });

    // Save/Load local sample
    const saveBtn = document.getElementById('saveLocal');
    saveBtn.addEventListener('click', ()=>{
      try{
        localStorage.setItem('r_restaurant_cart', JSON.stringify(state.cart));
        alert('Panier sauvegardé localement.');
      }catch(e){alert('Impossible de sauvegarder: ' + e.message)}
    });
    // Load cart if present
    (function loadLocal(){
      try{
        const raw = localStorage.getItem('r_restaurant_cart');
        if(raw){
          const parsed = JSON.parse(raw);
          // Validate structure
          Object.keys(parsed).forEach(k=>{
            if(parsed[k] && parsed[k].item && parsed[k].qty){
              state.cart[k] = parsed[k];
            }
          });
        }
      }catch(e){}
    })();

    /* ------------ Submit order (simulated) ------------ */
    const orderForm = document.getElementById('orderForm');
    const status = document.getElementById('status');

    orderForm.addEventListener('submit', async (ev)=>{
      ev.preventDefault();
      status.textContent = '';
      const name = document.getElementById('name').value.trim();
      const phone = document.getElementById('phone').value.trim();
      const fulfillmentVal = fulfillment.value;
      const address = document.getElementById('address').value.trim();
      const notes = document.getElementById('notes').value.trim();

      if(!name || !phone){
        status.textContent = 'Merci de renseigner nom et téléphone.';
        return;
      }
      if(fulfillmentVal === 'delivery' && !address){
        status.textContent = 'Merci de renseigner l\'adresse pour la livraison.';
        return;
      }
      const items = Object.values(state.cart).map(r=>({ id:r.item.id, name:r.item.name, qty:r.qty, price:r.item.price }));

      if(items.length === 0){
        status.textContent = 'Votre panier est vide.';
        return;
      }

      const orderPayload = {
        customer: { name, phone, address: fulfillmentVal === 'delivery' ? address : null },
        fulfillment: fulfillmentVal,
        notes,
        items,
        subtotal: items.reduce((s,i)=>s + i.qty * i.price, 0),
        createdAt: new Date().toISOString()
      };

      // === SIMULATION ===
      // Ici en production: fetch('/api/orders', {method:'POST', headers:{'content-type':'application/json'}, body:JSON.stringify(orderPayload)})
      // Pour l'exemple, on simule un envoi asynchrone
      try{
        status.textContent = 'Envoi de la commande...';
        await new Promise(r=>setTimeout(r, 900)); // simulate latency
        // simulate server response
        const fakeResp = { success:true, orderId: 'CMD-' + Math.floor(Math.random()*900000 + 100000) };

        if(fakeResp.success){
          status.textContent = 'Commande passée — n° ' + fakeResp.orderId + '. Merci !';
          // Clear cart after order
          clearCart();
          // Optionally clear localStorage saved cart
          localStorage.removeItem('r_restaurant_cart');
          orderForm.reset();
          addressRow.style.display = 'none';
        } else {
          throw new Error('Erreur serveur');
        }
      }catch(err){
        status.textContent = 'Erreur lors de l\'envoi : ' + (err.message || 'unknown');
      }
    });

    /* ------------ Small utilities ------------ */
    function escapeHTML(s){
      return (s+'').replace(/[&<>"']/g, (m)=> ({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[m]));
    }

    /* ------------ Initial render ------------ */
    renderMenu();
    renderCart();
  </script>
</body>
</html>
