<!doctype html>
<html lang="tr">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Sunum Değerlendirme</title>
<style>
  body {font-family:Arial,sans-serif;background:#f6f8fb;color:#0b1220;display:flex;flex-direction:column;align-items:center;padding:40px}
  h1{margin-bottom:10px}
  input[type=text]{padding:10px;font-size:16px;border-radius:6px;border:1px solid #ccc;width:200px;margin-bottom:20px}
  .stars{display:flex;gap:8px;margin-bottom:20px}
  .star{font-size:40px;cursor:pointer;user-select:none;opacity:0.3}
  .star.active{opacity:1;color:gold}
  button{padding:10px 16px;font-size:16px;border:none;border-radius:6px;background:#0b77d1;color:white;cursor:pointer}
  .stats{margin-top:20px;font-size:18px}
</style>
</head>
<body>

<h1>Sunum Değerlendirme</h1>
<input type="text" id="nameInput" placeholder="İsminizi girin...">

<div class="stars" id="stars">
  <span class="star" data-value="1">☆</span>
  <span class="star" data-value="2">☆</span>
  <span class="star" data-value="3">☆</span>
  <span class="star" data-value="4">☆</span>
  <span class="star" data-value="5">☆</span>
</div>

<button id="sendBtn">Oy Gönder</button>

<div class="stats">
  Katılımcı: <span id="count">0</span> | Ortalama: <span id="avg">—</span>
</div>

<script>
const LS_KEY = 'simple_ratings';
let ratings = JSON.parse(localStorage.getItem(LS_KEY)) || [];

const nameInput = document.getElementById('nameInput');
const stars = Array.from(document.querySelectorAll('.star'));
const sendBtn = document.getElementById('sendBtn');
const countEl = document.getElementById('count');
const avgEl = document.getElementById('avg');

let selected = 0;

function updateStarsUI(v){
  stars.forEach(s=>{
    const val = Number(s.dataset.value);
    if(val<=v){ s.classList.add('active'); s.textContent='★'; }
    else { s.classList.remove('active'); s.textContent='☆'; }
  });
}

stars.forEach(s=>{
  s.addEventListener('click', ()=>{ selected = Number(s.dataset.value); updateStarsUI(selected); });
  s.addEventListener('mouseover', ()=> updateStarsUI(Number(s.dataset.value)));
});
document.querySelector('.stars').addEventListener('mouseleave', ()=> updateStarsUI(selected));

function renderStats(){
  countEl.textContent = ratings.length;
  if(ratings.length===0) avgEl.textContent='—';
  else {
    const sum = ratings.reduce((a,r)=>a+r.score,0);
    const avg = Math.round((sum/ratings.length)*100)/100;
    avgEl.textContent = avg;
  }
}
renderStats();

sendBtn.addEventListener('click', ()=>{
  const name = (nameInput.value||'').trim();
  if(!name){ alert('İsim boş olamaz'); return; }
  if(selected<1 || selected>5){ alert('1–5 yıldız seçin'); return; }
  ratings.push({name,score:selected});
  localStorage.setItem(LS_KEY,JSON.stringify(ratings));
  nameInput.value=''; selected=0; updateStarsUI(0);
  renderStats();
  alert('Oyunuz kaydedildi!');
});
</script>

</body>
</html>
