<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>LuckyDraw — Lottery-style Game (Demo)</title>
  <meta name="description" content="A non-monetary lottery-style game demo website (frontend template). For entertainment only." />
  <style>
    :root{--accent:#ef4444;--bg:#0f172a;--card:#0b1220;--muted:#94a3b8}
    *{box-sizing:border-box}
    html,body{height:100%;margin:0;font-family:Inter,system-ui,Segoe UI,Roboto,'Helvetica Neue',Arial;background:linear-gradient(180deg,#071029 0%, #071c2b 100%);color:#e6eef8}
    .container{max-width:1000px;margin:32px auto;padding:20px}
    header{display:flex;align-items:center;justify-content:space-between;margin-bottom:24px}
    .brand{display:flex;align-items:center;gap:12px}
    .logo{width:56px;height:56px;border-radius:10px;background:linear-gradient(135deg,var(--accent),#f97316);display:flex;align-items:center;justify-content:center;font-weight:700;color:#071022}
    h1{font-size:20px;margin:0}
    p.lead{margin:0;color:var(--muted)}
    .grid{display:grid;grid-template-columns:1fr 340px;gap:18px}
    .card{background:rgba(255,255,255,0.03);padding:18px;border-radius:12px;box-shadow:0 6px 18px rgba(2,6,23,0.6)}

    /* Ticket list */
    .tickets{display:flex;flex-direction:column;gap:12px}
    .ticket{display:flex;justify-content:space-between;align-items:center;padding:12px;border-radius:10px;background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));}
    .numbers{display:flex;gap:8px}
    .num{width:36px;height:36px;border-radius:8px;background:#071633;display:flex;align-items:center;justify-content:center;font-weight:700}
    .muted{color:var(--muted);font-size:13px}

    /* controls */
    .controls{display:flex;gap:10px;flex-wrap:wrap}
    button{cursor:pointer;padding:10px 14px;border-radius:8px;border:0;background:linear-gradient(90deg,var(--accent),#f97316);color:#071022;font-weight:600}
    .ghost{background:transparent;border:1px solid rgba(255,255,255,0.06);color:#dbeafe}

    /* draw area */
    .draw-numbers{display:flex;gap:12px;align-items:center}
    .ball{width:64px;height:64px;border-radius:999px;background:linear-gradient(180deg,#fff,#f3f4f6);color:#071022;display:flex;align-items:center;justify-content:center;font-weight:800;font-size:22px;box-shadow:0 6px 20px rgba(0,0,0,0.4)}

    /* winners */
    .winners{display:flex;flex-direction:column;gap:8px}
    footer{margin-top:22px;color:var(--muted);font-size:13px;text-align:center}

    /* responsive */
    @media (max-width:880px){.grid{grid-template-columns:1fr;}.logo{width:48px;height:48px}.brand h1{font-size:18px}}
  </style>
</head>
<body>
  <div class="container">
    <header>
      <div class="brand">
        <div class="logo">LD</div>
        <div>
          <h1>LuckyDraw — Demo</h1>
          <p class="lead">A non-monetary lottery-style game UI. For entertainment only.</p>
        </div>
      </div>
      <div>
        <button id="newTicketBtn">Generate Quick Ticket</button>
      </div>
    </header>

    <main class="grid">
      <section class="card">
        <h2 style="margin-top:0">Your Tickets</h2>
        <p class="muted">Create up to 10 demo tickets and try the draw. No real money involved.</p>

        <div style="height:12px"></div>
        <div class="controls" style="margin-bottom:14px">
          <button id="buyTicket">Buy Ticket (Demo)</button>
          <button class="ghost" id="autoPick">Auto Pick Numbers</button>
          <button class="ghost" id="clearTickets">Clear Tickets</button>
        </div>

        <div class="tickets" id="ticketsList">
          <!-- tickets inserted here -->
        </div>

        <div style="height:14px"></div>
        <div class="card" style="background:transparent;padding:0;border-radius:0;box-shadow:none">
          <h3 style="margin:6px 0">Last Draw</h3>
          <div class="draw-numbers" id="lastDrawArea">
            <!-- drawn balls -->
          </div>
          <div style="height:12px"></div>
          <div class="muted" id="lastDrawWhen"></div>
          <div style="height:10px"></div>
          <div class="controls">
            <button id="drawBtn">Run Draw</button>
            <button class="ghost" id="quickDraw">Quick Draw</button>
          </div>
        </div>

      </section>

      <aside class="card">
        <h3>Draw Results & Winners</h3>
        <div style="height:10px"></div>
        <div class="winners" id="winnersList">
          <div class="muted">No draws yet. Run a draw to see winners.</div>
        </div>

        <div style="height:18px"></div>
        <h4 style="margin-bottom:6px">How it works (demo)</h4>
        <p class="muted" style="font-size:13px;line-height:1.4">This is a client-side demo. Tickets are generated in your browser. Draws are randomized locally and no real payments occur. To convert this into a real product you'd need a backend, payment provider, user accounts, and to follow gambling laws in your jurisdiction.</p>

        <div style="height:18px"></div>
        <h4 style="margin-bottom:6px">Quick actions</h4>
        <div class="controls" style="flex-direction:column">
          <button id="fillDemo">Fill Demo Tickets</button>
          <button class="ghost" id="exportJSON">Export Tickets (JSON)</button>
        </div>
      </aside>

    </main>

    <footer>
      <div class="muted">Demo site — no real money. Customize the code and add a server for production use.</div>
    </footer>
  </div>

  <script>
    // --- Simple client-only lottery demo ---
    const tickets = [];
    const ticketsListEl = document.getElementById('ticketsList');
    const winnersListEl = document.getElementById('winnersList');
    const lastDrawArea = document.getElementById('lastDrawArea');
    const lastDrawWhen = document.getElementById('lastDrawWhen');

    function renderTickets(){
      ticketsListEl.innerHTML = '';
      if(tickets.length===0){
        ticketsListEl.innerHTML = '<div class="muted">No tickets yet. Generate a ticket to play.</div>';
        return;
      }
      tickets.forEach((t,idx)=>{
        const el = document.createElement('div'); el.className='ticket';
        el.innerHTML = `
          <div>
            <div style="font-weight:700">Ticket #${idx+1} <span class="muted" style="font-weight:400;margin-left:8px">(${t.owner || 'You'})</span></div>
            <div class="muted" style="font-size:13px">Created: ${new Date(t.created).toLocaleString()}</div>
          </div>
          <div class="numbers">${t.numbers.map(n=>`<div class="num">${n}</div>`).join('')}</div>
        `;
        ticketsListEl.appendChild(el);
      });
    }

    function quickPick(){
      const nums = new Set();
      while(nums.size<6){ nums.add(Math.floor(Math.random()*49)+1); }
      return Array.from(nums).sort((a,b)=>a-b);
    }

    function addTicket(owner){
      if(tickets.length>=10){ alert('Ticket limit reached (10) — clear some to add more.'); return; }
      tickets.push({numbers: quickPick(), created: Date.now(), owner: owner||'You', id: Math.random().toString(36).slice(2,9)});
      renderTickets();
    }

    document.getElementById('newTicketBtn').addEventListener('click', ()=> addTicket());
    document.getElementById('buyTicket').addEventListener('click', ()=> addTicket());
    document.getElementById('autoPick').addEventListener('click', ()=>{
      if(tickets.length===0) addTicket('Auto'); else { tickets.forEach(t=>t.numbers=quickPick()); renderTickets(); }
    });
    document.getElementById('clearTickets').addEventListener('click', ()=>{ if(confirm('Clear all demo tickets?')){ tickets.splice(0); renderTickets(); winnersListEl.innerHTML='<div class="muted">No draws yet. Run a draw to see winners.</div>'; lastDrawArea.innerHTML=''; lastDrawWhen.textContent=''; }});

    document.getElementById('fillDemo').addEventListener('click', ()=>{ for(let i=0;i<6;i++) addTicket('DemoPlayer'+(i+1)); });
    document.getElementById('exportJSON').addEventListener('click', ()=>{
      const data = JSON.stringify(tickets, null, 2);
      const blob = new Blob([data], {type:'application/json'});
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a'); a.href = url; a.download = 'tickets.json'; a.click(); URL.revokeObjectURL(url);
    });

    function renderDraw(balls){
      lastDrawArea.innerHTML='';
      balls.forEach(b=>{ const d = document.createElement('div'); d.className='ball'; d.textContent=b; lastDrawArea.appendChild(d); });
      lastDrawWhen.textContent = 'Drawn at ' + new Date().toLocaleString();
    }

    function runDraw(){
      if(tickets.length===0){ if(!confirm('No tickets exist. Run draw anyway?')) return; }
      // draw 6 unique balls 1..49
      const balls = []; const s=new Set();
      while(s.size<6){ s.add(Math.floor(Math.random()*49)+1); }
      const drawn = Array.from(s).sort((a,b)=>a-b);
      renderDraw(drawn);

      // check winners: match count
      const winners = tickets.map(t=>{
        const matches = t.numbers.filter(n=>drawn.includes(n)).length; return {...t, matches};
      }).filter(t=>t.matches>0).sort((a,b)=>b.matches-a.matches);

      winnersListEl.innerHTML='';
      if(winners.length===0){ winnersListEl.innerHTML='<div class="muted">No winners this draw.</div>' }
      winners.forEach(w=>{
        const row = document.createElement('div'); row.style.display='flex'; row.style.justifyContent='space-between'; row.style.alignItems='center'; row.style.padding='8px'; row.style.borderRadius='8px';
        row.style.background='linear-gradient(180deg, rgba(255,255,255,0.01), rgba(255,255,255,0.00))';
        row.innerHTML = `<div><div style="font-weight:700">${w.owner}</div><div class="muted" style="font-size:13px">Ticket ${w.id}</div></div><div style="text-align:right"><div style="font-weight:800">Matches: ${w.matches}</div><div class="muted">${w.numbers.join(', ')}</div></div>`;
        winnersListEl.appendChild(row);
      });

      // Save last draw in localStorage
      localStorage.setItem('lastDraw', JSON.stringify({drawn, time:Date.now(), winners}));
    }

    document.getElementById('drawBtn').addEventListener('click', ()=>{
      // run a visually interesting draw: animate balls then reveal
      const animBalls = [];
      lastDrawArea.innerHTML='';
      for(let i=0;i<6;i++){ const b = document.createElement('div'); b.className='ball'; b.textContent='?'; lastDrawArea.appendChild(b); }
      // simple reveal sequence
      let step=0; const revealInterval = setInterval(()=>{
        const ballsEls = Array.from(lastDrawArea.children);
        ballsEls[step].textContent = Math.floor(Math.random()*49)+1; step++;
        if(step>=6){ clearInterval(revealInterval); setTimeout(runDraw, 600); }
      }, 160);
    });

    document.getElementById('quickDraw').addEventListener('click', runDraw);

    // load saved draw
    (function init(){
      const saved = localStorage.getItem('lastDraw');
      if(saved){ try{ const o = JSON.parse(saved); renderDraw(o.drawn); lastDrawWhen.textContent='Last draw: '+new Date(o.time).toLocaleString(); }catch(e){} }
      renderTickets();
    })();

    // helpful dev: auto add a demo ticket on first open
    if(!localStorage.getItem('seenDemo')){ addTicket('You'); localStorage.setItem('seenDemo','1'); }
  </script>
</body>
</html>
