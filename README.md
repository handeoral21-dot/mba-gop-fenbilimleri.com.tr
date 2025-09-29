# mba-gop-fenbilimleri.com.tr
<!doctype html>
<html lang="tr">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Bilim Elçileri - Uygulama</title>
  <style>
    :root{--bg:#f3f6fb;--card:#fff;--accent:#0b6ef6;--muted:#6b7280;--admin:#ff8c42;--student:#ffd34d}
    *{box-sizing:border-box;font-family:Inter, system-ui, Arial}
    body{margin:0;background:var(--bg);color:#111}
    /* Login */
    .center{min-height:100vh;display:flex;align-items:center;justify-content:center;padding:20px}
    .login-card{width:360px;background:var(--card);padding:20px;border-radius:12px;box-shadow:0 8px 30px rgba(10,20,40,0.08)}
    .title{font-size:20px;margin:0 0 12px}
    label{font-size:13px;color:var(--muted)}
    input[type=text],input[type=password],select,textarea{width:100%;padding:10px;border-radius:8px;border:1px solid #e6e9ef;margin-top:6px;margin-bottom:12px}
    button{padding:10px 14px;border-radius:8px;border:0;background:var(--accent);color:white;cursor:pointer}

    /* App layout */
    .app{display:flex;height:100vh}
    .left{width:220px;background:#f8fafc;padding:18px;border-right:1px solid #e6e9ef;overflow:auto}
    .main{flex:1;padding:18px;display:flex;flex-direction:column}
    .topbar{display:flex;align-items:center;justify-content:space-between;margin-bottom:12px}
    .brand{font-weight:700;font-size:18px}
    .userBox{text-align:right}
    .userName{font-weight:600}
    .roleBadge{display:inline-block;padding:6px 10px;border-radius:999px;margin-top:6px}

    .content{display:flex;gap:14px;flex:1}
    .centerPanel{flex:1;background:var(--card);padding:16px;border-radius:12px;overflow:auto}
    .rightPanel{width:340px;background:var(--card);padding:12px;border-radius:12px}

    .menuItem{padding:8px 10px;border-radius:8px;margin-bottom:6px;cursor:pointer}
    .menuItem:hover{background:#eef3ff}
    .menuTitle{font-weight:600;margin-bottom:8px}

    /* Messages */
    .messagesList{max-height:480px;overflow:auto}
    .msgItem{padding:10px;border-radius:8px;border:1px solid #eee;margin-bottom:8px;cursor:pointer}
    .msgItem:hover{background:#f7fbff}
    .msgHeader{font-weight:700}
    .msgBody{margin-top:8px}

    .controlsRow{display:flex;gap:8px;align-items:center}
    .small{font-size:13px;color:var(--muted)}
    .fileInput{margin-top:6px}

    /* Presentation / projects */
    .cardItem{padding:10px;border-radius:8px;border:1px solid #eee;margin-bottom:8px}
    .center-empty{color:var(--muted);text-align:center;padding:20px}

    footer{font-size:13px;color:var(--muted);text-align:center;padding:10px}

    .top-actions{display:flex;gap:8px}

    @media (max-width:900px){.left{display:none}.rightPanel{display:none}}
  </style>
</head>
<body>

<div id="loginView" class="center">
  <div class="login-card">
    <div class="title">Bilim Elçileri</div>
    <div class="small">Lütfen kullanıcı adı ve şifre ile giriş yapın</div>
    <label>Kullanıcı adı</label>
    <input id="loginUser" type="text" placeholder="kullanıcı adı" />
    <label>Şifre</label>
    <input id="loginPass" type="password" placeholder="şifre" />
    <div style="display:flex;gap:8px;align-items:center;justify-content:flex-end">
      <button id="loginBtn">Giriş</button>
    </div>
    <div style="margin-top:12px;font-size:13px;color:var(--muted)">Hazır hesap: <strong>mert884</strong> / <strong>234987</strong> (Yönetici)</div>
  </div>
</div>

<div id="appView" class="app" style="display:none">
  <div class="left">
    <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:12px">
      <div class="menuTitle">Seçenekler</div>
      <button id="logoutBtn" style="background:#ef4444;padding:6px 8px;border-radius:8px">Çıkış</button>
    </div>
    <div id="leftMenu"></div>
    <hr />
    <div class="menuTitle">Hızlı İşlemler</div>
    <div id="quickActions"></div>
  </div>

  <div class="main">
    <div class="topbar">
      <div class="brand">Bilim Elçileri</div>
      <div class="userBox">
        <div id="displayName" class="userName">Ad Soyad</div>
        <div id="roleBadge" class="roleBadge">Rol</div>
      </div>
    </div>

    <div class="content">
      <div class="centerPanel" id="centerPanel">
        <div id="centerContent"></div>
      </div>

      <div class="rightPanel">
        <div class="menuTitle">Gelen Mesajlar</div>
        <div id="inboxList" class="messagesList"></div>
      </div>
    </div>

    <footer>Bilim Elçileri - Demo Uygulama</footer>
  </div>
</div>

<script>
// ---------- Kalıcı veri: localStorage ----------
const STORAGE_KEY = 'bilim_elcileri_data_v1';
function loadState(){
  const raw = localStorage.getItem(STORAGE_KEY);
  if(raw) return JSON.parse(raw);
  return null;
}
function saveState(state){ localStorage.setItem(STORAGE_KEY, JSON.stringify(state)); }

// ---------- Başlangıç state ----------
let state = loadState() || {
  users:[
    {username:'mert884', password:'234987', name:'Mert Yılmaz', role:'Yönetici', color:'--admin', visits:0, viewed:[]},
    {username:'ayse101', password:'1234', name:'Ayşe Demir', role:'Öğrenci', color:'--student', visits:0, viewed:[]},
    {username:'ali202', password:'0000', name:'Ali Vural', role:'Öğrenci', color:'--student', visits:0, viewed:[]}
  ],
  messages:[
    {id:1, from:'Ayşe Demir', to:'Mert Yılmaz', text:'Merhaba, projem hazırlandı mı?', files:[]},
    {id:2, from:'Ali Vural', to:'Mert Yılmaz', text:'Sunum için yardıma ihtiyacım var.', files:[]}
  ],
  projects:[],
  presentations:[]
};

// save initial
saveState(state);

let currentUser = null; // object
let selectedRecipient = null;

function $(id){return document.getElementById(id)}
function show(view){ $('loginView').style.display = view==='login'?'flex':'none'; $('appView').style.display = view==='app'?'flex':'none'; }

// ---------- Login ----------
$('loginBtn').addEventListener('click', ()=>{
  const u = $('loginUser').value.trim();
  const p = $('loginPass').value;
  const user = state.users.find(x=>x.username===u && x.password===p);
  if(!user){ alert('Kullanıcı adı veya şifre yanlış'); return; }
  currentUser = user;
  currentUser.visits = (currentUser.visits||0) + 1;
  saveState(state);
  renderApp();
  show('app');
});
$('logoutBtn').addEventListener('click', ()=>{ if(confirm('Çıkış yapmak istiyor musunuz?')){ currentUser=null; show('login'); } });

function renderApp(){
  $('displayName').innerText = currentUser.name;
  const badge = $('roleBadge');
  badge.innerText = currentUser.role;
  badge.style.background = currentUser.role === 'Yönetici' ? 'var(--admin)' : 'var(--student)';
  // left menu
  const leftMenu = $('leftMenu'); leftMenu.innerHTML = '';
  const quick = $('quickActions'); quick.innerHTML = '';
  function addItem(text, handler){ const d=document.createElement('div'); d.className='menuItem'; d.innerText=text; d.onclick=handler; leftMenu.appendChild(d);} 
  if(currentUser.role==='Yönetici'){
    addItem('Projeler', ()=>showProjectsAdmin());
    addItem('Sunumlar', ()=>showPresentations());
    addItem('Kişi Ekle', ()=>showAddPerson());
    addItem('Kim Ne Kadar Girmiş', ()=>showUsage());
    addItem('Kim Neye Bakmış', ()=>showViewed());
    quick.innerHTML = '<div class="small">Yönetici Hızlı</div>';
  } else {
    addItem('Sunumlar', ()=>showPresentations());
    addItem('Proje Gönder', ()=>showSendProject());
    quick.innerHTML = '<div class="small">Öğrenci Hızlı</div>';
  }
  showInboxCenter();
  renderInboxList();
}

// ---------- Inbox ----------
function renderInboxList(){
  const list = $('inboxList'); list.innerHTML='';
  const myMsgs = state.messages.filter(m=>m.to===currentUser.name || m.from===currentUser.name).sort((a,b)=>b.id-a.id);
  myMsgs.forEach(m=>{
    const el = document.createElement('div'); el.className='msgItem';
    el.innerHTML = `<div class="msgHeader">${m.from} → ${m.to}</div><div class="small">${m.text.slice(0,80)}${m.text.length>80?'...':''}</div>`;
    el.onclick = ()=> openMessage(m.id);
    list.appendChild(el);
  });
}

function showInboxCenter(){
  const c = $('centerContent'); c.innerHTML = '';
  const top = document.createElement('div'); top.style.display='flex'; top.style.justifyContent='space-between'; top.style.alignItems='center'; top.style.marginBottom='10px';
  if(currentUser.role==='Yönetici'){
    const btn = document.createElement('button'); btn.innerText='Mesajlar'; btn.onclick = ()=> showMessagesUI(); top.appendChild(btn);
  } else {
    top.innerHTML = '<div class="menuTitle">Gelen Mesajlar</div>';
  }
  c.appendChild(top);

  const inboxBox = document.createElement('div'); inboxBox.className='messagesList';
  const myMsgs = state.messages.filter(m=>m.to===currentUser.name).sort((a,b)=>b.id-a.id);
  if(myMsgs.length===0) inboxBox.innerHTML='<div class="center-empty">Henüz mesaj yok</div>';
  myMsgs.forEach(m=>{
    const el = document.createElement('div'); el.className='msgItem'; el.innerHTML=`<div class="msgHeader">${m.from}</div><div class="small">${m.text.slice(0,100)}${m.text.length>100?'...':''}</div>`;
    el.onclick = ()=> openMessage(m.id);
    inboxBox.appendChild(el);
  });
  c.appendChild(inboxBox);
}

// ---------- Messages UI (for admins shows send area) ----------
function showMessagesUI(){
  const c = $('centerContent'); c.innerHTML = '';
  const topArea = document.createElement('div'); topArea.style.marginBottom='12px';
  topArea.innerHTML = `
    <div style="display:flex;gap:8px;margin-bottom:8px" class="top-actions">
      <input id="recipientSearch" type="text" placeholder="Kime (isim veya kullanıcı adı)" />
      <button id="searchRecipientBtn">Ara</button>
      <button id="clearRecipient">Temizle</button>
    </div>
    <div style="margin-bottom:8px">Seçili kişi: <strong id="selectedRecipient">(yok)</strong></div>
    <div style="margin-bottom:8px"><textarea id="composeText" rows="5" placeholder="Mesaj yaz..."></textarea></div>
    <div><input id="fileUpload" class="fileInput" type="file" multiple /></div>
    <div style="display:flex;gap:8px;margin-top:8px"><button id="sendMsgBtn">Gönder</button><button id="backBtn">Mesaj Listesine Dön</button></div>
  `;
  c.appendChild(topArea);

  const msgsDiv = document.createElement('div'); msgsDiv.className='messagesList';
  state.messages.slice().reverse().forEach(m=>{
    const el = document.createElement('div'); el.className='msgItem'; el.innerHTML=`<div class="msgHeader">${m.from} → ${m.to}</div><div class="small">${m.text.slice(0,80)}</div>`;
    el.onclick = ()=> openMessage(m.id);
    msgsDiv.appendChild(el);
  });
  c.appendChild(msgsDiv);

  $('backBtn').onclick = ()=> showInboxCenter();
  $('searchRecipientBtn').onclick = searchRecipient;
  $('clearRecipient').onclick = ()=>{ selectedRecipient=null; $('selectedRecipient').innerText='(yok)'; };
  $('sendMsgBtn').onclick = sendComposedMessage;
}

function searchRecipient(){
  const q = $('recipientSearch').value.trim().toLowerCase();
  if(!q) return alert('Aranacak isim girin');
  const found = state.users.find(u=>u.name.toLowerCase().includes(q) || u.username.toLowerCase().includes(q));
  if(!found) return alert('Kullanıcı bulunamadı');
  selectedRecipient = found;
  $('selectedRecipient').innerText = `${found.name} (${found.username})`;
}

function sendComposedMessage(){
  const text = $('composeText').value.trim();
  if(!selectedRecipient) return alert('Alıcı seçin');
  const input = $('fileUpload');
  const files = Array.from(input.files).map(f=>f.name);
  const msg = {id:Date.now(), from: currentUser.name, to: selectedRecipient.name, text, files};
  state.messages.push(msg);
  saveState(state);
  alert('Mesaj gönderildi');
  $('composeText').value=''; $('recipientSearch').value=''; $('selectedRecipient').innerText='(yok)'; selectedRecipient=null; $('fileUpload').value='';
  renderInboxList();
  showMessagesUI();
}

function openMessage(id){
  const m = state.messages.find(x=>x.id===id);
  if(!m) return;
  const c = $('centerContent'); c.innerHTML='';
  const box = document.createElement('div');
  box.innerHTML = `
    <div style="font-weight:800;margin-bottom:6px">Gönderen: ${m.from}</div>
    <div style="color:var(--muted);margin-bottom:12px">Alıcı: ${m.to}</div>
    <div style="border-top:1px dashed #eee;padding-top:10px">${m.text.replace(/
/g,'<br>')}</div>
  `;
  if(m.files && m.files.length) box.innerHTML += `<div style="margin-top:10px"><strong>Ekler:</strong><ul>${m.files.map(f=>`<li>${f}</li>`).join('')}</ul></div>`;
  box.innerHTML += '<div style="margin-top:12px"><button id="backToMessages">Mesajlara Dön</button></div>';
  c.appendChild(box);
  $('backToMessages').onclick = ()=> { if(currentUser.role==='Yönetici') showMessagesUI(); else showInboxCenter(); };
}

// ---------- Projects & Presentations ----------
function showProjectsAdmin(){
  const c=$('centerContent'); c.innerHTML='';
  const header = document.createElement('div'); header.className='menuTitle'; header.innerText='Projeler'; c.appendChild(header);
  const newBtn = document.createElement('button'); newBtn.innerText='Kod Yap / Gönder'; newBtn.onclick = ()=> showCodeComposer(); c.appendChild(newBtn);

  const list = document.createElement('div'); list.style.marginTop='12px';
  if(state.projects.length===0) list.innerHTML='<div class="center-empty">Henüz proje yok</div>';
  state.projects.forEach(p=>{ const el=document.createElement('div'); el.className='cardItem'; el.innerHTML=`<div><strong>${p.owner}</strong></div><div class="small">${p.code.slice(0,120)}${p.code.length>120?'...':''}</div><div style="margin-top:6px"><button data-id="${p.id}">Görüntüle</button></div>`; el.querySelector('button').onclick = ()=> viewProject(p.id); list.appendChild(el); });
  c.appendChild(list);
}

function showCodeComposer(){
  const c=$('centerContent'); c.innerHTML='';
  const ta = document.createElement('textarea'); ta.rows=10; ta.placeholder='Kod veya proje açıklaması yazın...'; ta.style.width='100%';
  const sendBtn = document.createElement('button'); sendBtn.innerText='Gönder Kodu';
  const toDesignBtn = document.createElement('button'); toDesignBtn.innerText='Hazıra Al (Sunum tasarla)'; toDesignBtn.style.marginLeft='8px';
  c.appendChild(ta); c.appendChild(document.createElement('div'));
  c.appendChild(sendBtn); c.appendChild(toDesignBtn);
  sendBtn.onclick = ()=>{ state.projects.push({id:Date.now(), owner:currentUser.name, code:ta.value, status:'gönderildi'}); saveState(state); alert('Proje gönderildi'); showProjectsAdmin(); }
  toDesignBtn.onclick = ()=>{ const code = ta.value; const pid = Date.now(); const designed = `<div style="padding:20px"><h2>Sunum (Otomatik Tasarım)</h2><pre>${escapeHtml(code)}</pre></div>`; state.projects.push({id:pid, owner:currentUser.name, code, status:'tasarlandı'}); state.presentations.push({id:pid, title:'(Başlıksız) '+pid, code, designedHtml:designed, owner:currentUser.name}); saveState(state); alert('Sunum tasarlandı ve hazırlandı. Başlık vererek yayınlayabilirsiniz.'); showPresentations(); }
}

function escapeHtml(s){ return (s||'').toString().replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;'); }

function showPresentations(){
  const c=$('centerContent'); c.innerHTML='';
  const header = document.createElement('div'); header.className='menuTitle'; header.innerText='Sunumlar'; c.appendChild(header);
  // if admin allow publish
  if(currentUser.role==='Yönetici'){
    const publishArea = document.createElement('div'); publishArea.style.marginBottom='10px';
    publishArea.innerHTML = `<div style="margin-bottom:8px"><strong>Yeni yayınla (başlık ile)</strong></div><input id="newTitle" placeholder="Başlık girin" /><button id="publishBtn" style="margin-left:8px">Yayınla</button>`;
    c.appendChild(publishArea);
    $('publishBtn')?.addEventListener('click', ()=>{ const t=$('newTitle').value.trim(); if(!t) return alert('Başlık girin'); // find last tasarlandı
      const p = state.presentations.find(x=>x.title.startsWith('(Başlıksız)'));
      if(!p) return alert('Yayınlanacak hazır sunum yok'); p.title = t; saveState(state); alert('Sunum yayınlandı'); showPresentations(); });
  }

  const list = document.createElement('div');
  if(state.presentations.length===0) list.innerHTML='<div class="center-empty">Henüz sunum yok</div>';
  state.presentations.forEach(p=>{
    const el = document.createElement('div'); el.className='cardItem';
    el.innerHTML = `<div><strong>${p.title}</strong></div><div class="small">${p.owner}</div><div style="margin-top:6px"><button data-id="${p.id}">Göster</button></div>`;
    el.querySelector('button').onclick = ()=> showPresentation(p.id);
    list.appendChild(el);
  });
  c.appendChild(list);
}

function showPresentation(id){
  const p = state.presentations.find(x=>x.id===id);
  if(!p) return alert('Sunum bulunamadı');
  const c=$('centerContent'); c.innerHTML = '';
  const header = document.createElement('div'); header.style.marginBottom='8px'; header.innerHTML = `<div style="display:flex;justify-content:space-between;align-items:center"><div><strong>${p.title}</strong></div><div><button id="exitShow">Çık</button></div></div>`;
  c.appendChild(header);
  const area = document.createElement('div'); area.innerHTML = p.designedHtml || `<pre>${escapeHtml(p.code)}</pre>`;
  c.appendChild(area);
  $('exitShow').onclick = ()=> showPresentations();
}

function viewProject(id){ const p = state.projects.find(x=>x.id===id); if(!p) return alert('Proje yok'); const c=$('centerContent'); c.innerHTML = `<div class="menuTitle">Proje - ${p.owner}</div><pre>${escapeHtml(p.code)}</pre><div style="margin-top:8px"><button onclick="(function(){document.getElementById('centerContent').innerHTML='';showProjectsAdmin();})()">Geri</button></div>`; }

// Admin - Kişi Ekle
function showAddPerson(){
  const c=$('centerContent'); c.innerHTML='';
  c.innerHTML = `<div class="menuTitle">Kişi Ekle</div>
    <label>İsim Soyisim</label><input id="newName" type="text" />
    <label>Kategori (Öğrenci / Yönetici)</label><select id="newRole"><option>Öğrenci</option><option>Yönetici</option></select>
    <label>Kullanıcı adı</label><input id="newUsername" type="text" />
    <label>Şifre</label><input id="newPass" type="text" />
    <div style="margin-top:8px"><button id="addPersonBtn">Kişi Ekle</button></div>`;
  $('addPersonBtn').onclick = ()=>{
    const name=$('newName').value.trim(); const role=$('newRole').value; const username=$('newUsername').value.trim(); const pass=$('newPass').value;
    if(!name||!username||!pass) return alert('Tüm alanları doldurun');
    state.users.push({username, password:pass, name, role, color: role==='Yönetici'?'--admin':'--student', visits:0, viewed:[]});
    saveState(state);
    alert('Kişi eklendi');
    showAddPerson();
  }
}

// Admin usage / viewed
function showUsage(){
  const c=$('centerContent'); c.innerHTML=''; c.innerHTML = '<div class="menuTitle">Kim Ne Kadar Girmiş</div>';
  const list = document.createElement('div');
  state.users.filter(u=>u.role==='Öğrenci').forEach(s=>{
    const el = document.createElement('div'); el.className='cardItem'; el.innerHTML = `<div><strong>${s.name}</strong></div><div class="small">Bu hafta giriş: ${s.visits||0} kez</div>`; list.appendChild(el);
  });
  c.appendChild(list);
}

function showViewed(){
  const c=$('centerContent'); c.innerHTML=''; c.innerHTML = '<div class="menuTitle">Kim Neye Bakmış</div>';
  const list = document.createElement('div');
  state.users.filter(u=>u.role==='Öğrenci').forEach(s=>{
    const el = document.createElement('div'); el.className='cardItem'; el.innerHTML = `<div><strong>${s.name}</strong></div><div class="small">Baktıkları: ${(s.viewed||[]).join(', ') || 'Henüz yok'}</div>`; list.appendChild(el);
  });
  c.appendChild(list);
}

// Öğrenci - Proje Gönder
function showSendProject(){
  const c=$('centerContent'); c.innerHTML='';
  // select admin
  const admins = state.users.filter(u=>u.role==='Yönetici');
  c.innerHTML = `<div class="menuTitle">Proje Gönder</div><label>Kime gönderilecek</label><select id="targetAdmin">${admins.map(a=>`<option value="${a.username}">${a.name} (${a.username})</option>`).join('')}</select><label>Proje açıklaması / kod</label><textarea id="projCode" rows="8"></textarea><div style="margin-top:8px"><input id="projFile" type="file" /></div><div style="margin-top:8px"><button id="sendProjBtn">Gönder</button></div>`;
  $('sendProjBtn').onclick = ()=>{ const code=$('projCode').value.trim(); const target=$('targetAdmin').value; if(!code) return alert('Yazın'); const admin = state.users.find(u=>u.username===target); state.projects.push({id:Date.now(), owner:currentUser.name, code, status:'öğrenci gönderdi', to:admin.name}); saveState(state); alert('Projeniz gönderildi'); showInboxCenter(); }
}

// track view
function markViewed(name,item){ if(currentUser.role==='Öğrenci'){ currentUser.viewed = currentUser.viewed||[]; if(!currentUser.viewed.includes(item)) currentUser.viewed.push(item); saveState(state); } }

// expose viewProject
window.viewProject = viewProject;
function viewProject(id){ const p = state.projects.find(x=>x.id===id); if(!p) return alert('Proje yok'); const c=$('centerContent'); c.innerHTML = `<div class="menuTitle">Proje - ${p.owner}</div><pre>${escapeHtml(p.code)}</pre><div style="margin-top:8px"><button onclick="(function(){document.getElementById('centerContent').innerHTML='';renderApp();})()">Geri</button></div>`; }

// ---------- Başlat ----------
show('login');

</script>
</body>
</html>
