<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8" />
  <title>WWE2K26</title>
  <style>
    body { background: #111; color: white; text-align: center; font-family: Arial; }
    canvas { background: #222; margin-top: 20px; border: 3px solid #555; }
  </style>
</head>
<body>
  <h1>WWE2K26</h1>
  <p>Steuerung: A/D = links/rechts, W = springen, J = Schlag, K = Tritt, P = Pin</p>

  <canvas id="game" width="900" height="500"></canvas>

  <script>
    const canvas = document.getElementById("game");
    const ctx = canvas.getContext("2d");

    const ring = { x: 100, y: 300, w: 700, h: 150 };

    // Spieler und Gegner HP angepasst
    const player = { x: 200, y: 260, w: 60, h: 80, color: "cyan", vx: 0, vy: 0, jumping: false, hp: 5000, pinActive: false, pinCounter: 0 };
    const enemy = { x: 650, y: 260, w: 60, h: 80, color: "red", hp: 5000, hitFlash: 0, attackCooldown: 0, pinCooldown: 0, pinActive: false, pinCounter: 0 };
    const referee = { x: 450, y: 260, w: 40, h: 80, color: "white", direction: 1 };

    const gravity = 0.6;
    const keys = {};

    const PLAYER_DAMAGE = 50; // Spieler Schaden bleibt 50
    const ENEMY_DAMAGE = 400; // Gegner macht jetzt 400 Schaden

    let gameOver = false;
    let gameMessage = '';

    document.addEventListener("keydown", e => keys[e.key] = true);
    document.addEventListener("keyup", e => keys[e.key] = false);

    function update() {
      if (gameOver) return;

      // Spieler Bewegung
      player.vx = 0;
      if (keys["a"]) player.vx = -5;
      if (keys["d"]) player.vx = 5;
      if (keys["w"] && !player.jumping) { player.vy = -12; player.jumping = true; }

      // Spieler Angriff: exakt PLAYER_DAMAGE Schaden
      if (keys["j"] || keys["k"]) attack(player, enemy, PLAYER_DAMAGE);

      // Spieler Pin starten
      if (keys["p"] && !player.pinActive) { player.pinActive = true; player.pinCounter = 0; }

      // Spieler Pin zählen & Kickout prüfen
      if (player.pinActive) handlePin(player, enemy, 'SIEG!');

      // Spieler Physik
      player.vy += gravity;
      player.x += player.vx;
      player.y += player.vy;
      if (player.y > 260) { player.y = 260; player.vy = 0; player.jumping = false; }

      // Gegner-KI: auf Spieler zu, greift automatisch an (ENEMY_DAMAGE)
      if (enemy.hp > 0) {
        if (Math.abs(enemy.x - player.x) > 5) {
          enemy.x += enemy.x < player.x ? 2 : -2;
        } else if (enemy.attackCooldown <= 0) {
          player.hp -= ENEMY_DAMAGE;
          enemy.attackCooldown = 50;
        }
        enemy.attackCooldown--;

        // Gegner-Pin automatisch starten, wenn Spieler HP < 3500
        if (!enemy.pinActive && Math.abs(enemy.x - player.x) < 50 && player.hp < 3500 && enemy.pinCooldown <= 0) {
          enemy.pinActive = true;
          enemy.pinCounter = 0;
          enemy.pinCooldown = 200;
        }
        enemy.pinCooldown--;
      }

      // Gegner Pin zählen & Kickout prüfen
      if (enemy.pinActive) handlePin(enemy, player, 'NIEDERLAGE!');

      // Ringrichter bewegen
      referee.x += referee.direction * 1;
      if (referee.x < ring.x + 50 || referee.x + referee.w > ring.x + ring.w - 50) referee.direction *= -1;
      if (enemy.hitFlash > 0) enemy.hitFlash--;
    }

    function attack(attacker, target, dmg) {
      if (attacker.x + attacker.w > target.x && attacker.x < target.x + target.w) {
        target.hp -= dmg;
        target.hitFlash = 5;
      }
    }

    function handlePin(fighter, target, winMessage) {
      fighter.pinCounter += 0.02;

      if (Math.floor(fighter.pinCounter) > Math.floor(fighter.pinCounter - 0.02)) {
        let hpRatio = target.hp / 5000;
        let kickChance = 0;
        if (hpRatio > 0.6) kickChance = 0.25;
        else if (hpRatio > 0.3) kickChance = 0.4;
        else if (hpRatio > 0) kickChance = 0.65;
        else kickChance = 0.8;

        if (Math.random() < kickChance && fighter.pinCounter < 3) {
          fighter.pinActive = false;
          gameMessage = "KICKOUT!";
          setTimeout(() => { gameMessage = ''; }, 1000);
          return;
        }
      }

      if (fighter.pinCounter >= 3) {
        fighter.pinActive = false;
        gameMessage = winMessage;
        gameOver = true;
      }
    }

    function drawAudience() {
      for (let i = 0; i < 100; i++) {
        const x = i * 9 + Math.random() * 5;
        const y = 50 + Math.sin(i) * 5;
        ctx.fillStyle = Math.random() < 0.01 ? "white" : "#333";
        ctx.beginPath(); ctx.arc(x, y, 4, 0, Math.PI * 2); ctx.fill();
      }
    }

    function drawRing() { ctx.fillStyle = "#444"; ctx.fillRect(ring.x, ring.y, ring.w, ring.h); ctx.strokeStyle = "white"; ctx.lineWidth = 4; ctx.strokeRect(ring.x, ring.y, ring.w, ring.h); }

    function drawHP(x, y, hp, w) { const hpRatio = hp / 5000; let hpColor = hpRatio > 0.6 ? "green" : hpRatio > 0.3 ? "orange" : "red"; ctx.fillStyle = hpColor; ctx.fillRect(x, y, w * hpRatio, 10); ctx.strokeStyle = "white"; ctx.strokeRect(x, y, w, 10); }

    function draw() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      drawAudience(); drawRing();

      ctx.fillStyle = player.color; ctx.fillRect(player.x, player.y, player.w, player.h); drawHP(player.x, player.y - 15, player.hp, player.w);
      ctx.fillStyle = enemy.hitFlash > 0 ? "yellow" : enemy.color; ctx.fillRect(enemy.x, enemy.y, enemy.w, enemy.h); drawHP(enemy.x, enemy.y - 15, enemy.hp, enemy.w);

      ctx.fillStyle = referee.color; ctx.fillRect(referee.x, referee.y, referee.w, referee.h);

      if (player.pinActive) ctx.fillText("Pin: " + Math.floor(player.pinCounter + 1), 400, 50);
      if (enemy.pinActive) ctx.fillText("Enemy Pin: " + Math.floor(enemy.pinCounter + 1), 400, 80);

      if (gameMessage) { ctx.fillStyle = "yellow"; ctx.font = "40px Arial"; ctx.fillText(gameMessage, 350, 200); }

      ctx.fillStyle = "white"; ctx.font = "16px Arial";
      ctx.fillText("Enemy HP: " + Math.floor(enemy.hp), 20, 20);
      ctx.fillText("Player HP: " + player.hp, 20, 40);
    }

    function loop() { update(); draw(); requestAnimationFrame(loop); }
    loop();
  </script>
</body>
</html>
