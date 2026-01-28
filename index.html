<!DOCTYPE html>
<html lang="no">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Neon Defender - Skyt Fiendene</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            background-color: #050505;
            color: white;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            overflow: hidden; /* Hindre scrolling */
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }

        #gameContainer {
            position: relative;
            box-shadow: 0 0 50px rgba(0, 255, 255, 0.2);
        }

        canvas {
            background-color: #111;
            border: 2px solid #333;
            border-radius: 4px;
            display: block;
        }

        #ui-layer {
            position: absolute;
            top: 20px;
            left: 20px;
            pointer-events: none; /* Lar oss klikke gjennom UI */
            font-size: 20px;
            font-weight: bold;
            text-shadow: 0 0 5px cyan;
        }

        #gameOverScreen {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            text-align: center;
            background: rgba(0, 0, 0, 0.85);
            padding: 40px;
            border: 2px solid cyan;
            border-radius: 10px;
            display: none; /* Skjult som standard */
            box-shadow: 0 0 20px cyan;
        }

        h1 { margin-top: 0; color: #ff0055; text-shadow: 0 0 10px #ff0055; }
        
        button {
            background: cyan;
            border: none;
            padding: 15px 30px;
            font-size: 18px;
            font-weight: bold;
            cursor: pointer;
            border-radius: 5px;
            transition: 0.2s;
            margin-top: 20px;
        }

        button:hover {
            background: white;
            box-shadow: 0 0 15px cyan;
        }

        .controls-hint {
            position: absolute;
            bottom: 10px;
            width: 100%;
            text-align: center;
            color: #666;
            font-size: 14px;
            pointer-events: none;
        }
    </style>
</head>
<body>

    <div id="gameContainer">
        <canvas id="spillCanvas" width="600" height="800"></canvas>
        
        <div id="ui-layer">
            <div>Poeng: <span id="scoreDisplay">0</span></div>
            <div style="font-size: 14px; color: #aaa; margin-top: 5px;">Høyeste score: <span id="highScoreDisplay">0</span></div>
            <div id="powerUpText" style="color: yellow; margin-top: 10px; opacity: 0;">TRIPPELSKUDD!</div>
        </div>

        <div id="gameOverScreen">
            <h1>GAME OVER</h1>
            <p>Du fikk <span id="finalScore">0</span> poeng</p>
            <button onclick="startSpill()">Prøv igjen</button>
        </div>
        
        <div class="controls-hint">Bruk PILTASTER for å flytte • MELLOMROM for å skyte</div>
    </div>

<script>
    const canvas = document.getElementById('spillCanvas');
    const ctx = canvas.getContext('2d');
    
    // Spilltilstand
    let spillErIgang = false;
    let poeng = 0;
    let frameCount = 0;
    let highScore = localStorage.getItem('neonDefenderHighScore') || 0;
    document.getElementById('highScoreDisplay').innerText = highScore;

    // Input håndtering
    const keys = {};
    window.addEventListener('keydown', e => keys[e.code] = true);
    window.addEventListener('keyup', e => {
        keys[e.code] = false;
        if (e.code === 'Space' && spillErIgang) {
            spiller.skyt();
        }
    });

    // Klasser
    class Spiller {
        constructor() {
            this.width = 40;
            this.height = 40;
            this.x = canvas.width / 2 - this.width / 2;
            this.y = canvas.height - 60;
            this.color = '#00ffff';
            this.speed = 7;
            this.powerUpType = null;
            this.powerUpTimer = 0;
            this.harSkjold = false;
        }

        update() {
            // Bevegelse
            if (keys['ArrowLeft'] && this.x > 0) this.x -= this.speed;
            if (keys['ArrowRight'] && this.x + this.width < canvas.width) this.x += this.speed;

            // Powerup timer
            if (this.powerUpTimer > 0) {
                this.powerUpTimer--;
                if (this.powerUpTimer <= 0) {
                    this.powerUpType = null;
                    document.getElementById('powerUpText').style.opacity = 0;
                }
            }
        }

        draw() {
            ctx.save();
            ctx.shadowBlur = 15;
            ctx.shadowColor = this.color;
            ctx.fillStyle = this.color;
            
            // Tegn skip (trekant)
            ctx.beginPath();
            ctx.moveTo(this.x + this.width/2, this.y);
            ctx.lineTo(this.x + this.width, this.y + this.height);
            ctx.lineTo(this.x, this.y + this.height);
            ctx.fill();

            // Tegn skjold hvis aktivt
            if (this.harSkjold) {
                ctx.strokeStyle = 'rgba(0, 100, 255, 0.7)';
                ctx.lineWidth = 3;
                ctx.beginPath();
                ctx.arc(this.x + this.width/2, this.y + this.height/2, 35, 0, Math.PI * 2);
                ctx.stroke();
            }
            ctx.restore();
        }

        skyt() {
            if (this.powerUpType === 'trippel') {
                skuddListe.push(new Skudd(this.x + this.width / 2, this.y, 0));
                skuddListe.push(new Skudd(this.x + this.width / 2, this.y, -1)); // Venstre
                skuddListe.push(new Skudd(this.x + this.width / 2, this.y, 1));  // Høyre
            } else {
                skuddListe.push(new Skudd(this.x + this.width / 2, this.y, 0));
            }
        }
        
        aktiverPowerUp(type) {
            if (type === 'skjold') {
                this.harSkjold = true;
                visTekst("SKJOLD AKTIVERT", "#0088ff");
            } else if (type === 'trippel') {
                this.powerUpType = 'trippel';
                this.powerUpTimer = 600; // Varer i ca 10 sekunder (60fps)
                visTekst("TRIPPELSKUDD!", "#ffff00");
            }
        }
    }

    class Skudd {
        constructor(x, y, dx) {
            this.x = x;
            this.y = y;
            this.radius = 4;
            this.speed = 10;
            this.dx = dx * 2; // Spredning
            this.markedForDeletion = false;
        }
        update() {
            this.y -= this.speed;
            this.x += this.dx;
            if (this.y < 0) this.markedForDeletion = true;
        }
        draw() {
            ctx.save();
            ctx.shadowBlur = 10;
            ctx.shadowColor = 'yellow';
            ctx.fillStyle = '#ffff00';
            ctx.beginPath();
            ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2);
            ctx.fill();
            ctx.restore();
        }
    }

    class Fiende {
        constructor() {
            this.width = 40;
            this.height = 40;
            this.x = Math.random() * (canvas.width - this.width);
            this.y = -this.height;
            // Fiender blir raskere jo flere poeng du har
            this.speed = Math.random() * 2 + 2 + (poeng / 500); 
            this.color = '#ff0055';
            this.markedForDeletion = false;
        }
        update() {
            this.y += this.speed;
            if (this.y > canvas.height) {
                this.markedForDeletion = true;
                // Trekk poeng hvis fienden slipper forbi? Nei, la oss holde det enkelt.
            }
        }
        draw() {
            ctx.save();
            ctx.shadowBlur = 10;
            ctx.shadowColor = this.color;
            ctx.fillStyle = this.color;
            ctx.fillRect(this.x, this.y, this.width, this.height);
            
            // Øyne for å se slem ut
            ctx.fillStyle = 'black';
            ctx.fillRect(this.x + 8, this.y + 10, 8, 8);
            ctx.fillRect(this.x + 24, this.y + 10, 8, 8);
            ctx.restore();
        }
    }

    class Partikkel {
        constructor(x, y, color) {
            this.x = x;
            this.y = y;
            this.size = Math.random() * 5 + 2;
            this.speedX = Math.random() * 6 - 3;
            this.speedY = Math.random() * 6 - 3;
            this.color = color;
            this.markedForDeletion = false;
            this.life = 1.0; // Opacity
        }
        update() {
            this.x += this.speedX;
            this.y += this.speedY;
            this.life -= 0.02;
            if (this.life <= 0) this.markedForDeletion = true;
        }
        draw() {
            ctx.save();
            ctx.globalAlpha = this.life;
            ctx.fillStyle = this.color;
            ctx.beginPath();
            ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
            ctx.fill();
            ctx.restore();
        }
    }
    
    class PowerUpItem {
        constructor() {
            this.size = 20;
            this.x = Math.random() * (canvas.width - this.size);
            this.y = -this.size;
            this.speed = 3;
            this.type = Math.random() > 0.5 ? 'skjold' : 'trippel';
            this.color = this.type === 'skjold' ? '#0088ff' : '#ffff00';
            this.markedForDeletion = false;
            this.angle = 0;
        }
        update() {
            this.y += this.speed;
            this.angle += 0.1;
            if (this.y > canvas.height) this.markedForDeletion = true;
        }
        draw() {
            ctx.save();
            ctx.translate(this.x + this.size/2, this.y + this.size/2);
            ctx.rotate(this.angle);
            ctx.fillStyle = this.color;
            ctx.shadowBlur = 15;
            ctx.shadowColor = this.color;
            ctx.fillRect(-this.size/2, -this.size/2, this.size, this.size);
            ctx.restore();
            
            // Tegn ikon (S eller T)
            ctx.fillStyle = 'black';
            ctx.font = 'bold 12px Arial';
            ctx.textAlign = 'center';
            ctx.textBaseline = 'middle';
            ctx.fillText(this.type === 'skjold' ? 'S' : 'T', this.x + this.size/2, this.y + this.size/2);
        }
    }

    // Variabler for lister
    let spiller;
    let skuddListe = [];
    let fiendeListe = [];
    let partikkelListe = [];
    let powerUpListe = [];
    let fiendeTimer = 0;

    function startSpill() {
        spiller = new Spiller();
        skuddListe = [];
        fiendeListe = [];
        partikkelListe = [];
        powerUpListe = [];
        poeng = 0;
        document.getElementById('scoreDisplay').innerText = poeng;
        document.getElementById('gameOverScreen').style.display = 'none';
        spillErIgang = true;
        animate();
    }

    function lagEksplosjon(x, y, color) {
        for (let i = 0; i < 10; i++) {
            partikkelListe.push(new Partikkel(x, y, color));
        }
    }
    
    function visTekst(tekst, farge) {
        const el = document.getElementById('powerUpText');
        el.innerText = tekst;
        el.style.color = farge;
        el.style.opacity = 1;
        el.style.textShadow = `0 0 10px ${farge}`;
    }

    function sjekkKollisjon(rect1, rect2) {
        return (
            rect1.x < rect2.x + rect2.width &&
            rect1.x + rect1.width > rect2.x &&
            rect1.y < rect2.y + rect2.height &&
            rect1.y + rect1.height > rect2.y
        );
    }

    function avsluttSpill() {
        spillErIgang = false;
        document.getElementById('gameOverScreen').style.display = 'block';
        document.getElementById('finalScore').innerText = poeng;
        
        if (poeng > highScore) {
            highScore = poeng;
            localStorage.setItem('neonDefenderHighScore', highScore);
            document.getElementById('highScoreDisplay').innerText = highScore;
        }
    }

    function animate() {
        if (!spillErIgang) return;
        
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        
        // --- Spiller ---
        spiller.update();
        spiller.draw();

        // --- Skudd ---
        skuddListe.forEach(skudd => {
            skudd.update();
            skudd.draw();
        });
        skuddListe = skuddListe.filter(s => !s.markedForDeletion);

        // --- Fiender ---
        fiendeTimer++;
        // Spawn rate: Raskere jo høyere score (men aldri raskere enn hvert 20. frame)
        let spawnRate = Math.max(20, 60 - Math.floor(poeng / 50));
        
        if (fiendeTimer % spawnRate === 0) {
            fiendeListe.push(new Fiende());
        }

        fiendeListe.forEach(fiende => {
            fiende.update();
            fiende.draw();

            // Sjekk om fiende treffer spiller
            if (sjekkKollisjon(spiller, fiende)) {
                if (spiller.harSkjold) {
                    spiller.harSkjold = false;
                    fiende.markedForDeletion = true;
                    lagEksplosjon(fiende.x + fiende.width/2, fiende.y + fiende.height/2, 'cyan');
                } else {
                    avsluttSpill();
                }
            }

            // Sjekk om skudd treffer fiende
            skuddListe.forEach(skudd => {
                // Enkel sirkel-til-rektangel kollisjon approksimering
                if (
                    skudd.x > fiende.x && 
                    skudd.x < fiende.x + fiende.width &&
                    skudd.y > fiende.y && 
                    skudd.y < fiende.y + fiende.height
                ) {
                    fiende.markedForDeletion = true;
                    skudd.markedForDeletion = true;
                    poeng += 10;
                    document.getElementById('scoreDisplay').innerText = poeng;
                    lagEksplosjon(fiende.x + fiende.width/2, fiende.y + fiende.height/2, '#ff0055');
                }
            });
        });
        fiendeListe = fiendeListe.filter(f => !f.markedForDeletion);

        // --- PowerUps ---
        // 0.2% sjanse hver frame for en powerup
        if (Math.random() < 0.002) {
            powerUpListe.push(new PowerUpItem());
        }
        
        powerUpListe.forEach(p => {
            p.update();
            p.draw();
            
            // Kollisjon med spiller (enkel boks)
            if (
                spiller.x < p.x + p.size &&
                spiller.x + spiller.width > p.x &&
                spiller.y < p.y + p.size &&
                spiller.y + spiller.height > p.y
            ) {
                spiller.aktiverPowerUp(p.type);
                p.markedForDeletion = true;
            }
        });
        powerUpListe = powerUpListe.filter(p => !p.markedForDeletion);

        // --- Partikler ---
        partikkelListe.forEach(p => {
            p.update();
            p.draw();
        });
        partikkelListe = partikkelListe.filter(p => !p.markedForDeletion);

        requestAnimationFrame(animate);
    }

    // Start spillet ved innlasting
    startSpill();

</script>
</body>
</html>
