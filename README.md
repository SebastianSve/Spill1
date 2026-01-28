<!DOCTYPE html>
<html lang="no">
<head>
    <meta charset="UTF-8">
    <title>Romskip - Autofire & Helse</title>
    <style>
        body {
            background-color: #0d0d0d;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            color: white;
            overflow: hidden;
        }

        #spillContainer {
            position: relative;
            box-shadow: 0 0 40px rgba(0, 255, 0, 0.2);
        }

        canvas {
            background-color: #000000;
            border: 4px solid #333;
            display: block;
            border-radius: 4px;
        }

        #ui {
            position: absolute;
            top: 15px;
            left: 15px;
            font-size: 24px;
            font-weight: bold;
            pointer-events: none;
            text-shadow: 0 0 5px lime;
        }

        #startSkjerm {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            text-align: center;
            background: rgba(0, 0, 0, 0.95);
            padding: 50px;
            border: 2px solid lime;
            border-radius: 12px;
            box-shadow: 0 0 20px rgba(0, 255, 0, 0.5);
        }

        button {
            background-color: lime;
            color: black;
            border: none;
            padding: 15px 35px;
            font-size: 20px;
            font-weight: bold;
            cursor: pointer;
            margin-top: 20px;
            border-radius: 6px;
            transition: 0.2s;
        }

        button:hover {
            background-color: white;
            transform: scale(1.05);
        }

        .instruks {
            color: #aaa;
            margin-top: 20px;
            font-size: 14px;
        }
    </style>
</head>
<body>

<div id="spillContainer">
    <canvas id="canvas" width="600" height="800"></canvas>
    <div id="ui">Poeng: <span id="poengTeller">0</span></div>

    <div id="startSkjerm">
        <h1 style="color: lime; margin: 0 0 10px 0; text-transform: uppercase; letter-spacing: 2px;">Neon Defender</h1>
        <p>Fiendene har nå helse!</p>
        <p><strong>HOLD INNE MELLOMROM</strong> for å skyte kontinuerlig.</p>
        <button onclick="startSpillet()">START SPILL</button>
        <div class="instruks">Piltaster for å styre</div>
    </div>
</div>

<script>
    const canvas = document.getElementById('canvas');
    const ctx = canvas.getContext('2d');
    const startSkjerm = document.getElementById('startSkjerm');
    const poengTeller = document.getElementById('poengTeller');

    // Spillvariabler
    let spillErIgang = false;
    let poeng = 0;
    let fiendeTimer = 0;
    
    // Lister
    let skuddListe = [];
    let fiendeListe = [];
    let partikler = [];

    // Tastatur input
    const keys = {
        ArrowLeft: false,
        ArrowRight: false,
        Space: false
    };

    window.addEventListener('keydown', (e) => {
        if(e.code === 'ArrowLeft') keys.ArrowLeft = true;
        if(e.code === 'ArrowRight') keys.ArrowRight = true;
        if(e.code === 'Space') keys.Space = true;
    });

    window.addEventListener('keyup', (e) => {
        if(e.code === 'ArrowLeft') keys.ArrowLeft = false;
        if(e.code === 'ArrowRight') keys.ArrowRight = false;
        if(e.code === 'Space') keys.Space = false;
    });

    // SPILLER OBJEKTET
    const spiller = {
        x: 275,
        y: 700,
        width: 50,
        height: 50,
        farge: '#00FF00',
        speed: 7,
        skuddTimer: 0, // Teller ned for neste skudd (Cool-down)

        update: function() {
            // Bevegelse
            if (keys.ArrowLeft && this.x > 0) this.x -= this.speed;
            if (keys.ArrowRight && this.x + this.width < canvas.width) this.x += this.speed;

            // Skyting (Kontinuerlig)
            if (keys.Space) {
                if (this.skuddTimer <= 0) {
                    this.skyt();
                    this.skuddTimer = 8; // Antall frames ventetid mellom hvert skudd (lavere tall = raskere skyting)
                }
            }
            if (this.skuddTimer > 0) this.skuddTimer--;
        },

        draw: function() {
            ctx.save();
            ctx.shadowBlur = 15;
            ctx.shadowColor = this.farge;
            ctx.fillStyle = this.farge;
            
            ctx.beginPath();
            ctx.moveTo(this.x + this.width / 2, this.y);
            ctx.lineTo(this.x + this.width, this.y + this.height);
            ctx.lineTo(this.x + this.width / 2, this.y + this.height - 10); // Liten innhugg bak
            ctx.lineTo(this.x, this.y + this.height);
            ctx.closePath();
            ctx.fill();
            ctx.restore();
        },

        skyt: function() {
            skuddListe.push({
                x: this.x + this.width / 2 - 3,
                y: this.y,
                width: 6,
                height: 20,
                color: '#FFFF00',
                speed: 15
            });
        }
    };

    function startSpillet() {
        spiller.x = canvas.width / 2 - 25;
        poeng = 0;
        skuddListe = [];
        fiendeListe = [];
        partikler = [];
        poengTeller.innerText = "0";
        spillErIgang = true;
        startSkjerm.style.display = 'none';
        gameLoop();
    }

    function gameOver() {
        spillErIgang = false;
        startSkjerm.innerHTML = `
            <h1 style="color: #ff3333;">GAME OVER</h1>
            <p style="font-size: 20px;">Poeng: <strong style="color: white;">${poeng}</strong></p>
            <button onclick="startSpillet()">PRØV IGJEN</button>
        `;
        startSkjerm.style.display = 'block';
    }

    function lagEksplosjon(x, y, farge, antall = 8) {
        for(let i=0; i<antall; i++){
            partikler.push({
                x: x,
                y: y,
                vx: (Math.random() - 0.5) * 12,
                vy: (Math.random() - 0.5) * 12,
                life: 1.0,
                color: farge
            });
        }
    }

    function gameLoop() {
        if (!spillErIgang) return;

        ctx.clearRect(0, 0, canvas.width, canvas.height);

        // --- Spiller ---
        spiller.update();
        spiller.draw();

        // --- Skudd ---
        for (let i = skuddListe.length - 1; i >= 0; i--) {
            let s = skuddListe[i];
            s.y -= s.speed;
            
            ctx.fillStyle = s.color;
            ctx.shadowBlur = 10;
            ctx.shadowColor = s.color;
            ctx.fillRect(s.x, s.y, s.width, s.height);
            ctx.shadowBlur = 0;

            if (s.y < 0) skuddListe.splice(i, 1);
        }

        // --- Fiender ---
        fiendeTimer++;
        if (fiendeTimer > 45) { // Spawn rate
            let hp = Math.floor(Math.random() * 4) + 2; // Tilfeldig liv mellom 2 og 5
            
            fiendeListe.push({
                x: Math.random() * (canvas.width - 50),
                y: -50,
                width: 50,
                height: 50,
                speed: 3 + (poeng / 200),
                color: '#ff3333',
                maxHp: hp,
                hp: hp,
                hitTimer: 0 // For blinke-effekt når truffet
            });
            fiendeTimer = 0;
        }

        for (let i = fiendeListe.length - 1; i >= 0; i--) {
            let f = fiendeListe[i];
            f.y += f.speed;
            if(f.hitTimer > 0) f.hitTimer--;

            // Tegn Fiende
            ctx.fillStyle = f.hitTimer > 0 ? '#ffffff' : f.color; // Blink hvit hvis truffet
            ctx.fillRect(f.x, f.y, f.width, f.height);
            
            // Ansikt
            ctx.fillStyle = 'black';
            ctx.fillRect(f.x + 10, f.y + 15, 10, 10);
            ctx.fillRect(f.x + 30, f.y + 15, 10, 10);
            ctx.fillRect(f.x + 15, f.y + 35, 20, 5);

            // --- HELSEBAR ---
            let barWidth = f.width;
            let barHeight = 6;
            let barX = f.x;
            let barY = f.y - 12;

            // Bakgrunn (Rød)
            ctx.fillStyle = '#550000';
            ctx.fillRect(barX, barY, barWidth, barHeight);
            
            // Forgrunn (Grønn) - Prosent av gjenværende liv
            let hpProsent = f.hp / f.maxHp;
            ctx.fillStyle = hpProsent > 0.5 ? '#00ff00' : (hpProsent > 0.25 ? '#ffff00' : '#ff0000');
            ctx.fillRect(barX, barY, barWidth * hpProsent, barHeight);

            // Kollisjon Spiller <-> Fiende
            if (f.x < spiller.x + spiller.width &&
                f.x + f.width > spiller.x &&
                f.y < spiller.y + spiller.height &&
                f.y + f.height > spiller.y) {
                gameOver();
            }

            // Kollisjon Skudd <-> Fiende
            for (let j = skuddListe.length - 1; j >= 0; j--) {
                let s = skuddListe[j];
                if (s.x < f.x + f.width &&
                    s.x + s.width > f.x &&
                    s.y < f.y + f.height &&
                    s.y + s.height > f.y) {
                    
                    // Treff!
                    f.hp--;
                    f.hitTimer = 3; // Blinker hvit i 3 frames
                    skuddListe.splice(j, 1); // Fjern skuddet
                    
                    lagEksplosjon(s.x, s.y, 'yellow', 2); // Liten gnist

                    // Sjekk om fiende er død
                    if (f.hp <= 0) {
                        lagEksplosjon(f.x + f.width/2, f.y + f.height/2, 'orange', 10);
                        fiendeListe.splice(i, 1);
                        poeng += 10;
                        poengTeller.innerText = poeng;
                    }
                    break; 
                }
            }

            if (f.y > canvas.height) fiendeListe.splice(i, 1);
        }

        // --- Partikler ---
        for (let i = partikler.length - 1; i >= 0; i--) {
            let p = partikler[i];
            p.x += p.vx;
            p.y += p.vy;
            p.life -= 0.05;

            ctx.globalAlpha = p.life;
            ctx.fillStyle = p.color;
            ctx.beginPath();
            ctx.arc(p.x, p.y, 3, 0, Math.PI*2);
            ctx.fill();
            ctx.globalAlpha = 1.0;

            if (p.life <= 0) partikler.splice(i, 1);
        }

        requestAnimationFrame(gameLoop);
    }
</script>

</body>
</html>
