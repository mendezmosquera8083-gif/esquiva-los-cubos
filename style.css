<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Juego 3D: Runner Neón</title>
    <script src="https://code.playcanvas.com/playcanvas-stable.min.js"></script>
    <style>
        body { margin: 0; padding: 0; overflow: hidden; background-color: #000; }
        canvas { display: block; width: 100vw; height: 100vh; }
        #interfaz {
            position: absolute;
            top: 20px;
            left: 20px;
            color: white;
            font-family: system-ui, sans-serif;
            background: rgba(0, 0, 0, 0.8);
            padding: 15px;
            border-radius: 8px;
            border: 2px solid #56f5d2;
            box-shadow: 0 0 15px #56f5d244;
        }
        #mensajeOver {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            font-family: system-ui, sans-serif;
            font-size: 32px;
            font-weight: bold;
            color: #ff4757;
            background: rgba(0, 0, 0, 0.9);
            padding: 30px;
            border-radius: 10px;
            border: 3px solid #ff4757;
            display: none;
            text-align: center;
            cursor: pointer;
        }
    </style>
</head>
<body>

    <div id="interfaz">
        Puntuación: <span id="puntos">0</span><br>
        <span style="font-size: 14px; color: #a2aec9;">Usa Flechas Izquierda/Derecha para esquivar</span>
    </div>

    <div id="mensajeOver" onclick="location.reload()">
        ¡CHOQUE!<br>
        <span style="font-size:16px; color:#fff;">Haz clic para reiniciar</span>
    </div>

    <canvas id="mundo3D"></canvas>

    <script>
        const canvas = document.getElementById('mundo3D');
        const spanPuntos = document.getElementById('puntos');
        const divMensaje = document.getElementById('mensajeOver');
        
        const app = new pc.Application(canvas, { keyboard: new pc.Keyboard(window) });
        app.start();
        app.setCanvasFillMode(pc.FILLMODE_FILL_WINDOW);
        app.setCanvasResolution(pc.RESOLUTION_AUTO);
        window.addEventListener('resize', () => app.resizeCanvas());

        // 1. ESCENARIO
        const camara = new pc.Entity();
        camara.addComponent('camera', { clearColor: new pc.Color(0.05, 0.05, 0.1) });
        camara.setPosition(0, 4, 8);
        camara.lookAt(0, 0, -5);
        app.root.addChild(camara);

        const luz = new pc.Entity();
        luz.addComponent('light', { type: 'directional', color: new pc.Color(1, 1, 1), intensity: 1.5 });
        luz.setEulerAngles(45, -30, 0);
        app.root.addChild(luz);

        // Pista o carril
        const pista = new pc.Entity();
        pista.addComponent('render', { type: 'plane' });
        pista.setLocalScale(10, 1, 100); 
        pista.setPosition(0, 0, -40);
        const matPista = new pc.StandardMaterial();
        matPista.diffuse.set(0.1, 0.1, 0.2);
        matPista.update();
        pista.render.material = matPista;
        app.root.addChild(pista);

        // 2. JUGADOR
        const jugador = new pc.Entity();
        jugador.addComponent('render', { type: 'box' });
        jugador.setPosition(0, 0.5, 0);
        const matJugador = new pc.StandardMaterial();
        matJugador.diffuse.set(0.2, 0.8, 1); // Azul Neón
        matJugador.update();
        jugador.render.material = matJugador;
        app.root.addChild(jugador);

        // 3. VARIABLES DEL JUEGO
        let juegoActivo = true;
        let puntos = 0;
        let temporizadorObstaculos = 0;
        let velocidadObstaculos = 15; // Velocidad de movimiento hacia la cámara
        const obstaculos = [];

        // Material rojo para los enemigos (reutilizable)
        const matEnemigo = new pc.StandardMaterial();
        matEnemigo.diffuse.set(1, 0.2, 0.2);
        matEnemigo.update();

        // 4. BUCLE PRINCIPAL
        app.on('update', function (dt) {
            if (!juegoActivo) return;

            // Movimiento lateral del jugador
            let velLateral = 12 * dt;
            let posJugador = jugador.getPosition();
            
            if ((app.keyboard.isPressed(pc.KEY_LEFT) || app.keyboard.isPressed(pc.KEY_A)) && posJugador.x > -4) {
                jugador.translate(-velLateral, 0, 0);
            }
            if ((app.keyboard.isPressed(pc.KEY_RIGHT) || app.keyboard.isPressed(pc.KEY_D)) && posJugador.x < 4) {
                jugador.translate(velLateral, 0, 0);
            }

            // Generar nuevos obstáculos cada 0.6 segundos
            temporizadorObstaculos += dt;
            if (temporizadorObstaculos > 0.6) {
                temporizadorObstaculos = 0;
                
                const obstaculo = new pc.Entity();
                obstaculo.addComponent('render', { type: 'box' });
                obstaculo.render.material = matEnemigo;
                
                // Posición aleatoria en el eje X, muy lejos en el eje Z (-40)
                let xAleatorio = (Math.random() * 8) - 4; // Entre -4 y 4
                obstaculo.setPosition(xAleatorio, 0.5, -40);
                
                app.root.addChild(obstaculo);
                obstaculos.push(obstaculo);
            }

            // Mover obstáculos y detectar colisiones
            for (let i = obstaculos.length - 1; i >= 0; i--) {
                let obs = obstaculos[i];
                // Mover hacia el jugador (+Z)
                obs.translate(0, 0, velocidadObstaculos * dt);
                
                let posObs = obs.getPosition();

                // Colisión (Distancia simple)
                let dx = posJugador.x - posObs.x;
                let dz = posJugador.z - posObs.z;
                if (Math.abs(dx) < 1.0 && Math.abs(dz) < 1.0) {
                    juegoActivo = false;
                    divMensaje.style.display = 'block';
                }

                // Eliminar obstáculos que pasaron la cámara para no saturar memoria
                if (posObs.z > 5) {
                    obs.destroy(); // Comando de PlayCanvas para borrar entidades
                    obstaculos.splice(i, 1);
                    
                    puntos += 10;
                    spanPuntos.textContent = puntos;
                    
                    // Aumentar dificultad
                    if (puntos % 100 === 0) velocidadObstaculos += 2;
                }
            }
        });
    </script>
</body>
</html>