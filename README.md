# Anki
mkdir -p content layouts/shortcodes layouts/partials static/css static/images static/audios && echo "baseURL = 'https://exemplo.com'
languageCode = 'pt-br'
title = 'A Torre de Anw'
theme = []

[markup.goldmark.renderer]
  unsafe = true" > hugo.toml && echo '<div class="anw-box">
  <div class="anw-avatar">
    <img src="/images/anw-{{ .Get "humor" | default "neutro" }}.svg" alt="Ratinho Anw">
  </div>
  <div class="anw-balao">
    <strong class="anw-nome">Anw (Torre de Babel):</strong>
    <p>{{ .Inner | markdownify }}</p>
  </div>
</div>' > layouts/shortcodes/anw.html && echo '<div class="flashcard-container" id="card-{{ .Get "id" }}">
  <div class="card-inner" id="inner-{{ .Get "id" }}">
    <div class="card-front" id="front-{{ .Get "id" }}">
      <span class="card-label">Palavra</span>
      <p class="card-word">{{ .Get "frente" }}</p>
      <button class="btn-reveal" onclick="revealCard('\''{{ .Get "id" }}'\'')">Ver Tradução</button>
    </div>
    <div class="card-back" id="back-{{ .Get "id" }}" style="display: none;">
      <span class="card-label">Tradução / Significado</span>
      <p class="card-meaning">{{ .Get "verso" }}</p>
      {{ if .Get "audio" }}
      <div class="audio-player">
        <audio id="audio-player-{{ .Get "id" }}" src="/audios/{{ .Get "audio" }}"></audio>
        <button class="btn-audio" onclick="document.getElementById('\''audio-player-{{ .Get "id" }}'\'').play()">🔊 Ouvir Pronúncia</button>
      </div>
      {{ end }}
      <div class="card-explicacao">{{ .Inner | markdownify }}</div>
      <div class="srs-buttons">
        <button class="btn-srs btn-errei" onclick="rateCard('\''{{ .Get "id" }}'\'', 1)">Errei ❌</button>
        <button class="btn-srs btn-acertei" onclick="rateCard('\''{{ .Get "id" }}'\'', 5)">Acertei! 🎉</button>
      </div>
    </div>
  </div>
</div>' > layouts/shortcodes/flashcard.html && echo '<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>{{ .Title }}</title>
    <link rel="stylesheet" href="/css/style.css">
</head>
<body>
    <main style="max-width: 600px; margin: 0 auto; padding: 20px;">
        {{ .Content }}
    </main>
    <script>
    function revealCard(id) {
        document.getElementById(`front-${id}`).style.display = '\''none'\'';
        document.getElementById(`back-${id}`).style.display = '\''block'\'';
    }
    function rateCard(id, score) {
        let cardData = JSON.parse(localStorage.getItem(`srs-${id}`)) || { interval: 1, repetition: 0, ease: 2.5 };
        if (score < 3) { cardData.repetition = 0; cardData.interval = 1; }
        else {
            if (cardData.repetition === 0) cardData.interval = 1;
            elif (cardData.repetition === 1) cardData.interval = 4;
            else cardData.interval = Math.round(cardData.interval * cardData.ease);
            cardData.repetition++;
        }
        let targetDate = new Date();
        targetDate.setDate(targetDate.getDate() + cardData.interval);
        cardData.nextReview = targetDate.toISOString().split('\''T'\'')[0];
        localStorage.setItem(`srs-${id}`, JSON.stringify(cardData));
        const container = document.getElementById(`card-${id}`);
        container.innerHTML = `<div class="anw-box"><div><span class="anw-nome">Anw Guardou o Livro!</span><p>Anotei na biblioteca de Babel. Próxima revisão em: <strong>${cardData.nextReview}</strong>.</p></div></div>`;
    }
    document.addEventListener("DOMContentLoaded", function() {
        const today = new Date().toISOString().split('\''T'\'')[0];
        document.querySelectorAll('\''.flashcard-container'\'').forEach(card => {
            const id = card.id.replace('\''card-'\'', '\'''\'');
            const data = JSON.parse(localStorage.getItem(`srs-${id}`));
            if (data && data.nextReview > today) {
                card.innerHTML = `<div class="anw-box" style="border-left-color: #2e7d32;"><div class="anw-nome" style="color: #2e7d32;">Revisão em dia!</div><p>Anw diz: Você já domina esta palavra por enquanto. Próxima revisão: <strong>${data.nextReview}</strong>.</p></div>`;
            }
        });
    });
    </script>
</body>
</html>' > layouts/_default/single.html && cp layouts/_default/single.html layouts/_default/list.html && cp layouts/_default/single.html layouts/_default/home.html && echo '/* --- DESIGN DA TORRE DE ANW --- */
body { font-family: sans-serif; background-color: #fdfbf7; color: #333; }
.anw-box { display: flex; align-items: center; background-color: #fcf9f2; border-left: 5px solid #d4af37; border-radius: 8px; padding: 15px; margin: 20px 0; box-shadow: 0 4px 12px rgba(0,0,0,0.04); }
.anw-avatar { flex-shrink: 0; width: 60px; height: 60px; margin-right: 15px; background: #eee; border-radius: 50%; }
.anw-nome { color: #8b5a2b; font-size: 0.85rem; text-transform: uppercase; font-weight: bold; display: block; }
.flashcard-container { background: #fff; border: 2px solid #e6dfd3; border-radius: 12px; padding: 20px; margin: 20px 0; text-align: center; box-shadow: 0 6px 16px rgba(0,0,0,0.03); }
.card-word { font-size: 2rem; font-weight: bold; margin: 10px 0; }
.card-meaning { font-size: 1.8rem; font-weight: bold; color: #2e7d32; margin: 10px 0; }
.btn-reveal { background: #8b5a2b; color: white; border: none; padding: 10px 20px; font-weight: bold; border-radius: 6px; cursor: pointer; }
.srs-buttons { display: flex; justify-content: center; gap: 10px; margin-top: 15px; }
.btn-srs { border: none; padding: 10px 20px; font-weight: bold; color: white; border-radius: 6px; cursor: pointer; }
.btn-errei { background: #d32f2f; } .btn-acertei { background: #2e7d32; }
.audio-player { margin: 10px 0; }
.btn-audio { background: #e6dfd3; color: #5d3e1b; border: none; padding: 5px 12px; border-radius: 15px; cursor: pointer; }' > static/css/style.css && echo '---
title: "A Torre de Babel de Anw"
---

Olá! Este é o meu site de idiomas inspirado no Omniglot.

{{< anw humor="estudioso" >}}
Seja bem-vindo à minha biblioteca na Torre de Babel! Aqui vamos aprender línguas com repetição espaçada.
{{< /anw >}}

Tente adivinhar a palavra abaixo:

{{< flashcard id="de-schaden" frente="Schadenfreude" verso="Alegria com o azar alheio" >}}
Junção de Schaden (dano) + Freude (alegria).
{{< /flashcard >}}' > content/_index.md && echo "🚀 Estrutura do Hugo criada com sucesso de uma vez só!"
