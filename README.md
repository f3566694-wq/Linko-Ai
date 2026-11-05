
<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Linko AI Chat</title>
<style>
*{box-sizing:border-box}
body{margin:0;background:#10111c;color:#fff;font-family:Arial,sans-serif}
.app{display:flex;height:100dvh;overflow:hidden}
.sidebar{width:260px;background:#151625;padding:17px;display:flex;flex-direction:column;border-right:1px solid #303149}
.logo{font-size:23px;font-weight:bold;padding:12px 4px 25px}
.logo span{background:#7955ff;padding:8px;border-radius:10px}
.new{background:#7955ff;color:white;padding:14px;border:0;border-radius:12px;font-weight:bold;cursor:pointer}
.history{margin-top:25px;overflow-y:auto;flex:1}
.history h4{color:#898ba4;font-size:12px}
.history-item{padding:12px;border-radius:9px;cursor:pointer;color:#c5c5d6;font-size:14px;white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
.history-item.active,.history-item:hover{background:#29283d}
.sidebar-bottom{border-top:1px solid #303149;padding-top:15px}
.sidebar-bottom button{width:100%;background:transparent;color:#ccc;padding:12px;text-align:left;border:0;cursor:pointer}
.main{flex:1;display:flex;flex-direction:column;min-width:0}
.header{height:65px;display:flex;align-items:center;justify-content:space-between;padding:0 23px;border-bottom:1px solid #303149}
.header small{color:#999}
.header button{background:#22243a;color:white;border:0;padding:10px;border-radius:9px}
.chat{flex:1;overflow-y:auto;padding:25px 18px 30px}
.welcome{max-width:650px;margin:10vh auto;text-align:center}
.welcome h1{font-size:clamp(30px,5vw,48px)}
.welcome h1 span{color:#a48bff}
.welcome p{color:#999bb2;line-height:1.6}
.suggestions{display:grid;grid-template-columns:1fr 1fr;gap:10px;margin-top:30px}
.suggestion{background:#191a29;color:white;border:1px solid #303149;padding:17px;border-radius:13px;text-align:left;cursor:pointer}
.suggestion:hover{border-color:#7955ff}
.suggestion small{display:block;color:#999;margin-top:7px}
.messages{max-width:780px;margin:auto;display:flex;flex-direction:column;gap:20px}
.message{line-height:1.7;white-space:pre-wrap;overflow-wrap:anywhere}
.user{align-self:flex-end;background:#30294c;padding:13px 17px;border-radius:17px;max-width:85%}
.bot{align-self:stretch;padding:5px 4px}
.bot-label{font-size:12px;color:#a995ff;font-weight:bold;margin-bottom:8px}
.actions{display:flex;gap:8px;margin-top:10px}
.actions button{background:#25253b;color:#bbb;border:0;border-radius:7px;padding:7px;cursor:pointer}
.composer-wrap{padding:12px 18px 20px}
.composer{max-width:780px;margin:auto;background:#1b1c2c;border:1px solid #45405f;border-radius:18px;padding:13px}
.composer textarea{width:100%;background:none;border:0;outline:0;color:white;resize:none;min-height:45px;max-height:150px;font-size:16px}
.composer-bottom{display:flex;justify-content:space-between;align-items:center}
.composer-bottom small{color:#888}
.send{background:#7955ff;color:white;width:38px;height:38px;border:0;border-radius:12px;font-size:19px;cursor:pointer}
.send:disabled{opacity:.5}
.disclaimer{text-align:center;color:#777;font-size:11px;margin-top:9px}
.mobile-new{display:none}
@media(max-width:650px){
.sidebar{width:65px;padding:10px 6px}
.logo{font-size:0;padding:10px 0 20px;text-align:center}
.logo span{font-size:20px}
.new{font-size:0;padding:12px 5px}
.new:after{content:"+";font-size:23px}
.history h4,.history-item,.sidebar-bottom{display:none}
.header{padding:0 13px}
.chat{padding:15px 12px}
.suggestions{grid-template-columns:1fr}
.composer-wrap{padding:8px 10px 12px}
.welcome{margin-top:7vh}
}
</style>
</head>
<body>
<div class="app">
<aside class="sidebar">
<div class="logo"><span>✳</span> Linko AI</div>
<button class="new" onclick="newChat()">＋ Новый чат</button>
<div class="history">
<h4>ИСТОРИЯ ЧАТОВ</h4>
<div id="history"></div>
</div>
<div class="sidebar-bottom">
<button onclick="clearHistory()">🗑 Очистить историю</button>
</div>
</aside>

<main class="main">
<header class="header">
<strong>Linko AI</strong>
<small>AI Assistant · Preview</small>
</header>

<section class="chat" id="chat">
<div class="welcome" id="welcome">
<h1>Привет! Я <span>Linko AI.</span></h1>
<p>Чем могу помочь сегодня? Задавай вопросы, создавай идеи и учись новому.</p>
<div class="suggestions">
<button class="suggestion" onclick="suggest('Помоги создать сайт')">⌘ Создать сайт<small>Разработка и программирование</small></button>
<button class="suggestion" onclick="suggest('Придумай бизнес-идею')">◇ Бизнес-идеи<small>Новые возможности</small></button>
<button class="suggestion" onclick="suggest('Объясни сложную тему')">✧ Обучение<small>Простые объяснения</small></button>
<button class="suggestion" onclick="suggest('Напиши интересную историю')">▣ Творчество<small>Истории и идеи</small></button>
</div>
</div>
<div class="messages" id="messages"></div>
</section>

<div class="composer-wrap">
<div class="composer">
<textarea id="input" rows="1" placeholder="Напиши сообщение Linko AI..."></textarea>
<div class="composer-bottom">
<small>✧ Linko AI</small>
<button class="send" id="send" onclick="sendMessage()">↑</button>
</div>
</div>
<div class="disclaimer">Linko AI может допускать ошибки. Проверяйте важную информацию.</div>
</div>
</main>
</div>

<script>
let chats=JSON.parse(localStorage.getItem("linko_chats")||"[]");
let current=null;
let busy=false;

function save(){
 localStorage.setItem("linko_chats",JSON.stringify(chats));
}

function newChat(){
 current=Date.now();
 chats.unshift({id:current,title:"Новый чат",messages:[]});
 save();
 render();
 document.getElementById("input").focus();
}

function clearHistory(){
 if(confirm("Удалить всю историю разговоров?")){
 chats=[];
 current=null;
 save();
 render();
 }
}

function render(){
 let list=document.getElementById("history");
 list.innerHTML="";
 chats.forEach(c=>{
  let item=document.createElement("div");
  item.className="history-item"+(c.id===current?" active":"");
  item.textContent=c.title;
  item.onclick=()=>{current=c.id;render()};
  list.appendChild(item);
 });
 let chat=chats.find(c=>c.id===current);
 document.getElementById("welcome").style.display=
  chat&&chat.messages.length?"none":"block";
 let box=document.getElementById("messages");
 box.innerHTML="";
 if(chat){
  chat.messages.forEach(m=>addBubble(m.text,m.role,false));
 }
 document.getElementById("chat").scrollTop=
  document.getElementById("chat").scrollHeight;
}

function addBubble(text,role,refresh=true){
 let box=document.getElementById("messages");
 let div=document.createElement("div");
 div.className="message "+(role==="user"?"user":"bot");
 if(role==="bot"){
  let label=document.createElement("div");
  label.className="bot-label";
  label.textContent="✳ LINKO AI";
  div.appendChild(label);
 }
 let content=document.createElement("div");
 content.textContent=text;
 div.appendChild(content);
 if(role==="bot"){
  let actions=document.createElement("div");
  actions.className="actions";
  let copy=document.createElement("button");
  copy.textContent="⧉ Копировать";
  copy.onclick=()=>navigator.clipboard?.writeText(text).then(()=>copy.textContent="✓ Скопировано").catch(()=>alert(text));
  actions.appendChild(copy);
  div.appendChild(actions);
 }
 box.appendChild(div);
 if(refresh)document.getElementById("chat").scrollTop=
  document.getElementById("chat").scrollHeight;
}

function suggest(text){
 document.getElementById("input").value=text;
 sendMessage();
}

async function sendMessage(){
 if(busy)return;
 let input=document.getElementById("input");
 let text=input.value.trim();
 if(!text)return;
 if(!current)newChat();
 let chat=chats.find(c=>c.id===current);
 chat.messages.push({role:"user",text});
 if(chat.messages.length===1)chat.title=text.slice(0,35);
 input.value="";
 input.style.height="45px";
 busy=true;
 document.getElementById("send").disabled=true;
 render();
 let loading=document.createElement("div");
 loading.className="message bot";
 loading.textContent="✳ Linko AI думает...";
 document.getElementById("messages").appendChild(loading);
 try{
  const response=await fetch("/api/chat",{
   method:"POST",
   headers:{"Content-Type":"application/json"},
   body:JSON.stringify({message:text})
  });
  const data=await response.json();
  if(!response.ok)throw new Error(data.detail||"Не удалось получить ответ");
  loading.remove();
  chat.messages.push({role:"bot",text:data.reply});
  save();
  render();
 }catch(error){
  loading.textContent="Не удалось получить ответ: "+error.message+
   "\n\nПроверьте подключение настоящей AI-модели.";
 }finally{
  busy=false;
  document.getElementById("send").disabled=false;
  save();
  render();
 }
}

document.getElementById("input").addEventListener("input",function(){
 this.style.height="45px";
 this.style.height=Math.min(this.scrollHeight,150)+"px";
});
document.getElementById("input").addEventListener("keydown",function(e){
 if(e.key==="Enter"&&!e.shiftKey){
  e.preventDefault();
  sendMessage();
 }
});

if(chats.length)current=chats[0].id;
render();
</script>
</body>
</html>
