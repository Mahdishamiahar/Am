<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Al.VIPZEXNET - چت هوش مصنوعی</title>
<style>
  /* ======== پس‌زمینه و تم ======== */
  body {
    margin: 0;
    font-family: "Segoe UI", sans-serif;
    background: linear-gradient(135deg, #0d1117, #1a1f29);
    color: #fff;
    display: flex;
    flex-direction: column;
    height: 100vh;
    overflow: hidden;
  }
  /* موج ملایم پس‌زمینه */
  body::before {
    content:"";
    position:absolute;
    top:0; left:0; right:0; bottom:0;
    background: radial-gradient(circle at 50% 50%, rgba(58,123,213,0.2), transparent 70%);
    animation: moveBg 15s linear infinite;
    z-index: 0;
  }
  @keyframes moveBg {
    0% {background-position:0 0;}
    50% {background-position:100px 100px;}
    100% {background-position:0 0;}
  }

  header {
    position: relative;
    z-index: 1;
    background: #161b22cc;
    backdrop-filter: blur(8px);
    padding: 12px 20px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    border-bottom: 1px solid #30363d;
  }
  header h1 { margin: 0; font-size: 18px; color: #58a6ff; }
  header select, header button {
    background: #0d1117;
    color: #fff;
    border: 1px solid #30363d;
    border-radius: 6px;
    padding: 6px 10px;
    cursor: pointer;
  }

  #chatWindow {
    flex: 1;
    overflow-y: auto;
    padding: 16px;
    display: flex;
    flex-direction: column;
    gap: 12px;
    scrollbar-width: thin;
    position: relative;
    z-index: 1;
  }

  .message-wrapper {
    display: flex;
    align-items: flex-end;
    gap: 8px;
  }
  .avatar {
    width: 36px;
    height: 36px;
    border-radius: 50%;
    flex-shrink: 0;
    background-size: cover;
    background-position: center;
  }
  .message {
    max-width: 70%;
    padding: 12px 16px;
    border-radius: 16px;
    line-height: 1.5;
    white-space: pre-wrap;
    box-shadow: 0 4px 10px rgba(0,0,0,0.3);
    word-break: break-word;
    opacity: 0;
    transform: translateY(5px);
    animation: fadeIn 0.3s ease forwards;
  }
  .user {
    background: #238636;
    color: #fff;
    border-bottom-right-radius: 4px;
    margin-left: auto;
    animation: popIn 0.2s ease forwards;
  }
  .bot {
    background: #21262d;
    color: #fff;
    border-bottom-left-radius: 4px;
    margin-right: auto;
  }
  .timestamp {
    font-size: 10px;
    color: #999;
    margin-top: 2px;
    text-align: left;
  }

  #inputArea {
    display: flex;
    padding: 12px;
    border-top: 1px solid #30363d;
    background: #161b22cc;
    backdrop-filter: blur(6px);
    position: relative;
    z-index: 1;
  }
  #userInput {
    flex: 1;
    padding: 10px 12px;
    border: none;
    border-radius: 10px;
    background: #0d1117;
    color: #fff;
    font-size: 15px;
    resize: none;
    max-height: 120px;
    line-height: 1.4;
  }
  #sendBtn {
    padding: 10px 18px;
    margin-left: 10px;
    background: #58a6ff;
    border: none;
    border-radius: 10px;
    cursor: pointer;
    color: #fff;
    font-size: 15px;
    transition: background 0.2s ease;
  }
  #sendBtn:hover { background: #1f6feb; }

  @keyframes fadeIn {
    to {opacity: 1; transform: translateY(0);}
  }
  @keyframes popIn {
    0% {transform: scale(0.8); opacity:0;}
    100% {transform: scale(1); opacity:1;}
  }

  /* حالت تایپ ربات */
  #typingIndicator {
    display: flex;
    align-items: center;
    gap: 4px;
    margin-right: auto;
    font-size: 12px;
    color: #aaa;
  }
  #typingIndicator span {
    display:inline-block;
    width:6px; height:6px;
    background:#58a6ff;
    border-radius:50%;
    animation: blink 1s infinite;
  }
  #typingIndicator span:nth-child(2) { animation-delay:0.2s; }
  #typingIndicator span:nth-child(3) { animation-delay:0.4s; }
  @keyframes blink { 0%,80%,100%{opacity:0;} 40%{opacity:1;} }

  /* ریسپانسیو */
  @media (max-width: 768px) {
    header h1 { font-size: 16px; }
    #chatWindow { padding: 12px; gap: 10px; }
    .message { max-width: 85%; font-size: 14px; padding: 10px 14px; }
    #userInput { font-size: 14px; }
    #sendBtn { font-size: 14px; padding: 8px 14px; }
  }
  @media (max-width: 480px) {
    header { flex-direction: column; align-items: flex-start; gap: 8px; }
    header select, header button { width: 100%; }
    .message { max-width: 90%; }
    #inputArea { flex-direction: column; gap: 8px; }
    #sendBtn { width: 100%; margin-left: 0; }
  }
</style>
</head>
<body>
<header>
<h1>🤖 Al.VIPZEXNET</h1>
<select id="modelSelect">
  <option value="openai">OpenAI</option>
  <option value="deepseek">DeepSeek</option>
  <option value="mistral">Mistral</option>
  <option value="openai-large">OpenAI Large (GPT-4)</option>
</select>
<button id="clearBtn">پاک کردن چت</button>
</header>

<div id="chatWindow"></div>

<div id="inputArea">
<textarea id="userInput" placeholder="پیام خود را به Al.VIPZEXNET بنویسید..." rows="1"
onkeydown="if(event.key==='Enter' && !event.shiftKey){event.preventDefault();sendMessage()}"></textarea>
<button id="sendBtn" onclick="sendMessage()">ارسال</button>
</div>

<script>
let selectedModel = "openai";
const chatHistory = [];

document.getElementById("modelSelect").addEventListener("change", function () {
  selectedModel = this.value;
});

document.getElementById("clearBtn").addEventListener("click", () => {
  chatHistory.length = 0;
  document.getElementById("chatWindow").innerHTML = "";
});

function addMessage(text, sender, responseIndex = null) {
  const chatWindow = document.getElementById("chatWindow");
  const wrapper = document.createElement("div");
  wrapper.className = "message-wrapper " + sender;

  const avatar = document.createElement("div");
  avatar.className = "avatar";
  avatar.style.backgroundImage = sender === "user" 
    ? "url('https://i.imgur.com/0y0y0y0.png')" 
    : "url('https://i.imgur.com/q1bPzOZ.png')";

  const msgDiv = document.createElement("div");
  msgDiv.className = "message " + sender;
  msgDiv.textContent = text;

  const timeDiv = document.createElement("div");
  timeDiv.className = "timestamp";
  const now = new Date();
  timeDiv.textContent = now.getHours()+":"+now.getMinutes().toString().padStart(2,'0');

  if (sender === "user") {
    msgDiv.style.cursor = "pointer";
    msgDiv.title = "برای ویرایش کلیک کنید";
    msgDiv.addEventListener("click", () => editMessage(msgDiv, responseIndex));
    wrapper.appendChild(msgDiv);
    wrapper.appendChild(avatar);
  } else {
    wrapper.appendChild(avatar);
    wrapper.appendChild(msgDiv);
  }

  wrapper.appendChild(timeDiv);
  chatWindow.appendChild(wrapper);
  chatWindow.scrollTop = chatWindow.scrollHeight;
}

function editMessage(msgDiv, responseIndex) {
  const originalText = msgDiv.textContent;
  const input = document.createElement("textarea");
  input.value = originalText;
  input.style.width = "100%";
  input.style.fontSize = "14px";
  input.style.borderRadius = "8px";
  input.style.padding = "6px";
  input.style.boxSizing = "border-box";

  msgDiv.replaceWith(input);
  input.focus();

  input.addEventListener("keydown", async (e) => {
    if (e.key === "Enter" && !e.shiftKey) {
      e.preventDefault();
      const newText = input.value;
      chatHistory[responseIndex].user = newText;

      const newMsg = document.createElement("div");
      newMsg.className = "message user";
      newMsg.textContent = newText;
      newMsg.style.cursor = "pointer";
      newMsg.title = "برای ویرایش کلیک کنید";
      newMsg.addEventListener("click", () => editMessage(newMsg, responseIndex));
      input.replaceWith(newMsg);

      await updateBotResponse(responseIndex);
    } else if (e.key === "Escape") {
      const originalMsg = document.createElement("div");
      originalMsg.className = "message user";
      originalMsg.textContent = originalText;
      originalMsg.style.cursor = "pointer";
      originalMsg.title = "برای ویرایش کلیک کنید";
      originalMsg.addEventListener("click", () => editMessage(originalMsg, responseIndex));
      input.replaceWith(originalMsg);
    }
  });
}

async function updateBotResponse(index) {
  const loadingMsg = document.createElement("div");
  loadingMsg.className = "message bot";
  loadingMsg.textContent = "⌛ بروزرسانی پاسخ Al.VIPZEXNET...";
  document.getElementById("chatWindow").appendChild(loadingMsg);

  try {
    const formData = new FormData();
    formData.append("userid", "user123");
    formData.append("prompt", chatHistory[index].user);
    formData.append("model", selectedModel);

    const res = await fetch("https://api2.api-code.ir/gpt-save-v2/", {
      method: "POST",
      body: formData
    });
    const data = await res.text();

    loadingMsg.remove();
    chatHistory[index].bot = data;
    addMessage(data, "bot", index);
  } catch (err) {
    loadingMsg.remove();
    addMessage("❌ خطا در بروزرسانی پاسخ Al.VIPZEXNET", "bot", index);
  }
}

async function sendMessage() {
  const input = document.getElementById("userInput");
  const text = input.value.trim();
  if (!text) return;

  const index = chatHistory.length;
  chatHistory.push({ user: text, bot: "" });

  addMessage(text, "user", index);
  input.value = "";
  input.style.height = "auto";

  // تایپ ایندیکیتور ربات
  const typingIndicator = document.createElement("div");
  typingIndicator.id = "typingIndicator";
  typingIndicator.innerHTML = "<span></span><span></span><span></span> Al.VIPZEXNET در حال تایپ ...";
  document.getElementById("chatWindow").appendChild(typingIndicator);
  document.getElementById("chatWindow").scrollTop = document.getElementById("chatWindow").scrollHeight;

  try {
    const formData = new FormData();
    formData.append("userid", "user123");
    formData.append("prompt", text);
    formData.append("model", selectedModel);

    const res = await fetch("https://api2.api-code.ir/gpt-save-v2/", {
      method: "POST",
      body: formData
    });
    const data = await res.text();

    typingIndicator.remove();
    chatHistory[index].bot = data;
    addMessage(data, "bot", index);
  } catch (err) {
    typingIndicator.remove();
    addMessage("❌ خطا در دریافت پاسخ از Al.VIPZEXNET", "bot", index);
  }
}
</script>
</body>
</html>
