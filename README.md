
let loading = document.createElement("div");
loading.className = "message bot";
loading.innerHTML = `
  <div class="bot-label">✳ LINKO AI</div>
  <div class="thinking">
    <span>Linko AI анализирует вопрос</span>
    <span class="dots"><i></i><i></i><i></i></span>
  </div>
`;
document.getElementById("messages").appendChild(loading);

.thinking {
  display: flex;
  align-items: center;
  gap: 9px;
  color: #bdb4e8;
  font-size: 14px;
}

.dots {
  display: flex;
  gap: 4px;
}

.dots i {
  width: 6px;
  height: 6px;
  background: #a48bff;
  border-radius: 50%;
  animation: thinking 1s infinite ease-in-out;
}

.dots i:nth-child(2) {
  animation-delay: .15s;
}

.dots i:nth-child(3) {
  animation-delay: .3s;
}

@keyframes thinking {
  0%, 60%, 100% {
    transform: translateY(0);
    opacity: .4;
  }
  30% {
    transform: translateY(-6px);
    opacity: 1;
  }
}

