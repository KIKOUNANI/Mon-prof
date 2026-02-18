<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Latyfa Shop - Générateur de Pub</title>
    <!-- Chargement des bibliothèques nécessaires -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/ccapture.js/1.0.9/ccapture.min.js"></script>
    <!-- WebM Writer pour assurer la compatibilité du format -->
    <script src="https://cdn.rawgit.com"></script>
    
    <style>
        body {
            background: #121212;
            color: white;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        .container {
            text-align: center;
            padding: 20px;
        }
        canvas {
            width: 320px; /* Taille d'affichage */
            height: 560px;
            border: 4px solid #c5a059;
            border-radius: 15px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.5);
            background: black;
        }
        .controls {
            margin-top: 20px;
        }
        .btn {
            background: linear-gradient(135deg, #c5a059 0%, #8e6d31 100%);
            color: black;
            border: none;
            padding: 15px 30px;
            border-radius: 50px;
            font-weight: bold;
            font-size: 16px;
            cursor: pointer;
            transition: transform 0.2s, box-shadow 0.2s;
            text-transform: uppercase;
        }
        .btn:hover {
            transform: scale(1.05);
            box-shadow: 0 5px 15px rgba(197, 160, 89, 0.4);
        }
        .btn:disabled {
            background: #444;
            color: #888;
            cursor: not-allowed;
        }
        .status {
            margin-top: 15px;
            font-size: 14px;
            color: #c5a059;
            height: 20px;
        }
    </style>
</head>
<body>

    <div class="container">
        <canvas id="videoCanvas" width="720" height="1280"></canvas>
        
        <div class="controls">
            <button id="recordBtn" class="btn" onclick="startRecording()">🎬 Créer la Vidéo MP4</button>
        </div>
        <div class="status" id="statusText">Prêt à l'enregistrement</div>
    </div>

    <script>
        const canvas = document.getElementById('videoCanvas');
        const ctx = canvas.getContext('2d');
        const statusText = document.getElementById('statusText');
        const recordBtn = document.getElementById('recordBtn');

        let capturer;
        let isRecording = false;

        // Configuration de la publicité
        const scenes = [
            { text: 'LATYFA SHOP', sub: 'L\'élégance à votre portée', duration: 60 },
            { text: 'BIJOUX PRESTIGE', sub: 'Éclat et Raffinement', img: 'bijou', duration: 60 },
            { text: 'SACS DE LUXE', sub: 'Le style au quotidien', img: 'sac', duration: 60 },
            { text: 'PARFUMS', sub: 'Sillages d\'exception', img: 'parfum', duration: 60 },
            { text: 'LIVRAISON RAPIDE', sub: 'Chez vous en 24h/48h', duration: 60 },
            { text: 'CODE: BIENVENUE20', sub: '-20% sur votre commande', duration: 60 },
            { text: 'CONTACTEZ-NOUS', sub: '07 09 00 28 81', duration: 60 }
        ];

        const imagesUrl = {
            logo: 'https://i.postimg.cc/qMqtFGM2/1770342273452.png',
            bijou: 'https://images.unsplash.com',
            sac: 'https://images.unsplash.com',
            parfum: 'https://images.unsplash.com'
        };

        const loadedImages = {};
        let imagesReady = false;

        // Charger les images avec gestion CORS
        async function loadAssets() {
            const promises = Object.entries(imagesUrl).map(([key, src]) => {
                return new Promise(resolve => {
                    const img = new Image();
                    img.crossOrigin = 'anonymous'; 
                    img.onload = () => {
                        loadedImages[key] = img;
                        resolve();
                    };
                    img.src = src;
                });
            });
            await Promise.all(promises);
            imagesReady = true;
            draw(0); // Dessiner la première frame
        }

        function draw(frame) {
            const totalFramesPerScene = 60;
            const sceneIdx = Math.floor(frame / totalFramesPerScene) % scenes.length;
            const scene = scenes[sceneIdx];
            const progress = (frame % totalFramesPerScene) / totalFramesPerScene;

            // 1. Fond
            ctx.fillStyle = '#1a1a1a';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            
            // Cercle décoratif animé
            ctx.beginPath();
            ctx.strokeStyle = '#c5a059';
            ctx.lineWidth = 2;
            ctx.arc(canvas.width/2, canvas.height/2, 200 + Math.sin(frame*0.05)*20, 0, Math.PI*2);
            ctx.stroke();

            // 2. Affichage Image
            ctx.save();
            const currentImg = scene.img ? loadedImages[scene.img] : loadedImages.logo;
            if (currentImg) {
                const imgSize = 400;
                ctx.globalAlpha = Math.min(progress * 2, 1);
                ctx.drawImage(currentImg, canvas.width/2 - imgSize/2, 400, imgSize, imgSize);
            }
            ctx.restore();

            // 3. Texte Principal
            ctx.fillStyle = '#c5a059';
            ctx.font = 'bold 50px Arial';
            ctx.textAlign = 'center';
            ctx.shadowBlur = 15;
            ctx.shadowColor = 'black';
            ctx.fillText(scene.text, canvas.width/2, 250);

            // 4. Sous-texte
            ctx.fillStyle = 'white';
            ctx.font = '30px Arial';
            ctx.fillText(scene.sub, canvas.width/2, 320);

            // 5. Footer constant
            ctx.fillStyle = 'rgba(255,255,255,0.5)';
            ctx.font = '20px Arial';
            ctx.fillText('www.latyfa-shop.com', canvas.width/2, canvas.height - 100);
        }

        function startRecording() {
            if (!imagesReady) return alert("Images en cours de chargement...");
            
            isRecording = true;
            recordBtn.disabled = true;
            statusText.innerText = "Génération de la vidéo en cours...";

            capturer = new CCapture({
                format: 'webm',
                framerate: 30,
                verbose: false,
                display: false
            });

            capturer.start();
            
            let frame = 0;
            const maxFrames = scenes.length * 60; // 14 secondes env.

            function animate() {
                if (frame < maxFrames) {
                    draw(frame);
                    capturer.capture(canvas);
                    statusText.innerText = `Progression : ${Math.round((frame/maxFrames)*100)}%`;
                    frame++;
                    requestAnimationFrame(animate);
                } else {
                    stopRecording();
                }
            }
            animate();
        }

        function stopRecording() {
            capturer.stop();
            capturer.save();
            isRecording = false;
            recordBtn.disabled = false;
            statusText.innerText = "✅ Vidéo prête ! Téléchargement lancé.";
        }

        loadAssets();
    </script>
</body>
</html>
