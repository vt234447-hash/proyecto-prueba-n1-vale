# proyecto-prueba-n1-vale
Descubre los secretos ocultos en la oscuridad. Encuentra las pistas y resuelve los acertijos... si te atreves
<!doctype html>
<html lang="es" class="h-full">
 <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Enigmas en la Oscuridad</title>
  <script src="/_sdk/data_sdk.js"></script>
  <script src="/_sdk/element_sdk.js"></script>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
        body {
            box-sizing: border-box;
        }
        
        @import url('https://fonts.googleapis.com/css2?family=Creepster&family=Nosifer&family=Special+Elite&display=swap');
        
        @keyframes flicker {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.7; }
        }
        
        @keyframes pulse-glow {
            0%, 100% { box-shadow: 0 0 20px var(--glow-color); }
            50% { box-shadow: 0 0 40px var(--glow-color); }
        }
        
        @keyframes shake {
            0%, 100% { transform: translateX(0); }
            25% { transform: translateX(-5px); }
            75% { transform: translateX(5px); }
        }
        
        @keyframes fade-in {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }
        
        @keyframes blood-drip {
            0% { transform: translateY(-100%); opacity: 0; }
            50% { opacity: 1; }
            100% { transform: translateY(100%); opacity: 0; }
        }
        
        .flicker { animation: flicker 3s infinite; }
        .pulse-glow { animation: pulse-glow 2s infinite; }
        .shake { animation: shake 0.5s; }
        .fade-in { animation: fade-in 0.8s ease-out; }
        
        .clickable-object {
            cursor: pointer;
            transition: all 0.3s ease;
        }
        
        .clickable-object:hover {
            transform: scale(1.1);
            filter: brightness(1.3);
        }
        
        .found {
            animation: pulse-glow 1s;
            --glow-color: #00ff00;
        }
        
        .wrong {
            animation: shake 0.5s;
        }
        
        .blood-text {
            text-shadow: 2px 2px 4px rgba(139, 0, 0, 0.8);
        }
        
        input:focus {
            outline: none;
            box-shadow: 0 0 20px rgba(220, 38, 38, 0.5);
        }
        
        .toast {
            position: fixed;
            top: 20px;
            right: 20px;
            padding: 16px 24px;
            border-radius: 8px;
            font-weight: bold;
            z-index: 1000;
            animation: fade-in 0.3s ease-out;
        }
        
        .toast.success {
            background: linear-gradient(135deg, #065f46 0%, #047857 100%);
            color: #d1fae5;
        }
        
        .toast.error {
            background: linear-gradient(135deg, #7f1d1d 0%, #991b1b 100%);
            color: #fecaca;
        }
    </style>
  <style>@view-transition { navigation: auto; }</style>
 </head>
 <body class="h-full">
  <div id="app" class="w-full h-full"></div>
  <script>
        const defaultConfig = {
            background_color: "#0a0a0a",
            surface_color: "#1a1a1a",
            text_color: "#e5e5e5",
            primary_action_color: "#dc2626",
            secondary_action_color: "#7f1d1d",
            font_family: "Special Elite",
            font_size: 16,
            game_title: "Enigmas en la Oscuridad",
            intro_text: "Descubre los secretos ocultos en la oscuridad. Encuentra las pistas y resuelve los acertijos... si te atreves.",
            start_button: "Comenzar Pesadilla",
            hint_button: "Revelar Pista",
            submit_button: "Descubrir Verdad"
        };

        const riddles = [
            {
                id: "riddle_1",
                question: "Camino sin piernas, lloro sin ojos. Donde quiera que voy, la muerte me sigue. ¿Qué soy?",
                answer: "nube",
                hint: "Mira hacia arriba en el cielo nocturno...",
                room: "habitacion_1",
                clueObject: "window"
            },
            {
                id: "riddle_2",
                question: "Tengo ciudades sin casas, bosques sin árboles y ríos sin agua. ¿Qué soy?",
                answer: "mapa",
                hint: "Busca entre los papeles antiguos sobre el escritorio...",
                room: "habitacion_2",
                clueObject: "desk"
            },
            {
                id: "riddle_3",
                question: "Cuanto más me quitas, más grande me vuelvo. ¿Qué soy?",
                answer: "agujero",
                hint: "Observa las paredes agrietadas con atención...",
                room: "habitacion_3",
                clueObject: "wall"
            },
            {
                id: "riddle_4",
                question: "Siempre corro pero nunca camino, tengo boca pero nunca hablo, tengo cama pero nunca duermo. ¿Qué soy?",
                answer: "rio",
                hint: "El sonido del agua resuena en las tuberías oxidadas...",
                room: "habitacion_4",
                clueObject: "pipes"
            },
            {
                id: "riddle_5",
                question: "Me alimentas y vivo, me das de beber y muero. ¿Qué soy?",
                answer: "fuego",
                hint: "Las velas titilantes guardan el secreto...",
                room: "habitacion_5",
                clueObject: "candles"
            }
        ];

        let currentRiddleIndex = 0;
        let clueFound = false;
        let userProgress = [];
        let isLoading = false;

        const dataHandler = {
            onDataChanged(data) {
                userProgress = data;
                updateProgressDisplay();
            }
        };

        function showToast(message, type = 'success') {
            const existingToast = document.querySelector('.toast');
            if (existingToast) {
                existingToast.remove();
            }

            const toast = document.createElement('div');
            toast.className = `toast ${type}`;
            toast.textContent = message;
            document.body.appendChild(toast);

            setTimeout(() => {
                toast.style.opacity = '0';
                setTimeout(() => toast.remove(), 300);
            }, 3000);
        }

        function updateProgressDisplay() {
            const progressEl = document.getElementById('progress-display');
            if (progressEl) {
                const completed = userProgress.filter(p => p.completed).length;
                progressEl.textContent = `Acertijos Resueltos: ${completed}/${riddles.length}`;
            }
        }

        function getRoomSVG(roomType, clueObject) {
            const rooms = {
                habitacion_1: `
                    <svg viewBox="0 0 800 600" class="w-full h-full">
                        <rect width="800" height="600" fill="#1a1a1a"/>
                        <rect x="50" y="400" width="700" height="200" fill="#2d2d2d"/>
                        <rect x="150" y="150" width="200" height="250" fill="#0a0a0a" stroke="#4a4a4a" stroke-width="4" class="clickable-object" data-object="window"/>
                        <path d="M 250 150 L 250 400" stroke="#4a4a4a" stroke-width="4"/>
                        <circle cx="250" cy="250" r="40" fill="#1e3a5f" opacity="0.3" class="flicker"/>
                        <rect x="500" y="250" width="120" height="180" fill="#3a3a3a"/>
                        <rect x="520" y="270" width="80" height="140" fill="#2a2a2a" class="clickable-object" data-object="door"/>
                        <circle cx="600" cy="340" r="8" fill="#8b4513"/>
                        <rect x="600" y="380" width="100" height="80" fill="#4a3a2a" class="clickable-object" data-object="table"/>
                    </svg>
                `,
                habitacion_2: `
                    <svg viewBox="0 0 800 600" class="w-full h-full">
                        <rect width="800" height="600" fill="#1a1a1a"/>
                        <rect x="50" y="400" width="700" height="200" fill="#2d2d2d"/>
                        <rect x="100" y="200" width="180" height="200" fill="#4a3a2a" class="clickable-object" data-object="desk"/>
                        <rect x="120" y="180" width="140" height="20" fill="#3a2a1a"/>
                        <rect x="140" y="220" width="40" height="30" fill="#d4af37" opacity="0.6" class="clickable-object" data-object="papers"/>
                        <rect x="200" y="240" width="60" height="80" fill="#2a2a2a"/>
                        <rect x="500" y="150" width="200" height="280" fill="#3a3a3a" class="clickable-object" data-object="bookshelf"/>
                        <rect x="520" y="170" width="30" height="40" fill="#8b0000"/>
                        <rect x="560" y="170" width="30" height="40" fill="#006400"/>
                        <rect x="600" y="170" width="30" height="40" fill="#00008b"/>
                    </svg>
                `,
                habitacion_3: `
                    <svg viewBox="0 0 800 600" class="w-full h-full">
                        <rect width="800" height="600" fill="#1a1a1a"/>
                        <rect x="50" y="400" width="700" height="200" fill="#2d2d2d"/>
                        <path d="M 100 100 Q 150 120 200 100 T 300 100" stroke="#3a3a3a" stroke-width="3" fill="none" class="clickable-object" data-object="wall"/>
                        <circle cx="250" cy="150" r="20" fill="#1a1a1a" class="clickable-object" data-object="hole"/>
                        <circle cx="450" cy="200" r="15" fill="#1a1a1a" class="clickable-object" data-object="hole"/>
                        <rect x="600" y="300" width="100" height="120" fill="#5a4a3a" class="clickable-object" data-object="chair"/>
                        <rect x="610" y="280" width="80" height="20" fill="#4a3a2a"/>
                        <ellipse cx="400" cy="500" rx="80" ry="20" fill="#0a0a0a" opacity="0.5"/>
                    </svg>
                `,
                habitacion_4: `
                    <svg viewBox="0 0 800 600" class="w-full h-full">
                        <rect width="800" height="600" fill="#1a1a1a"/>
                        <rect x="50" y="400" width="700" height="200" fill="#2d2d2d"/>
                        <rect x="100" y="100" width="40" height="300" fill="#4a4a4a" class="clickable-object" data-object="pipes"/>
                        <rect x="110" y="150" width="20" height="200" fill="#6a6a6a"/>
                        <circle cx="120" cy="180" r="8" fill="#8b4513"/>
                        <path d="M 120 200 Q 120 220 130 230" stroke="#4a4a4a" stroke-width="8" fill="none"/>
                        <rect x="500" y="250" width="150" height="100" fill="#3a3a3a" class="clickable-object" data-object="sink"/>
                        <ellipse cx="575" cy="280" rx="40" ry="30" fill="#1a1a1a"/>
                        <path d="M 575 260 L 575 240" stroke="#6a6a6a" stroke-width="6"/>
                    </svg>
                `,
                habitacion_5: `
                    <svg viewBox="0 0 800 600" class="w-full h-full">
                        <rect width="800" height="600" fill="#1a1a1a"/>
                        <rect x="50" y="400" width="700" height="200" fill="#2d2d2d"/>
                        <g class="clickable-object" data-object="candles">
                            <rect x="200" y="320" width="15" height="80" fill="#f5deb3"/>
                            <ellipse cx="207.5" cy="315" rx="10" ry="5" fill="#ff6b35" class="flicker"/>
                            <rect x="300" y="300" width="15" height="100" fill="#f5deb3"/>
                            <ellipse cx="307.5" cy="295" rx="10" ry="5" fill="#ff6b35" class="flicker"/>
                            <rect x="400" y="310" width="15" height="90" fill="#f5deb3"/>
                            <ellipse cx="407.5" cy="305" rx="10" ry="5" fill="#ff6b35" class="flicker"/>
                        </g>
                        <circle cx="600" cy="200" r="80" fill="#2a2a2a" class="clickable-object" data-object="mirror"/>
                        <ellipse cx="600" cy="200" rx="60" ry="70" fill="#1a1a1a" opacity="0.5"/>
                    </svg>
                `
            };
            return rooms[roomType] || rooms.habitacion_1;
        }

        function handleObjectClick(event) {
            if (isLoading) return;
            
            const target = event.target.closest('.clickable-object');
            if (!target) return;

            const clickedObject = target.getAttribute('data-object');
            const currentRiddle = riddles[currentRiddleIndex];

            if (clickedObject === currentRiddle.clueObject) {
                clueFound = true;
                target.classList.add('found');
                showToast('¡Pista encontrada! La verdad se revela...', 'success');
                
                const hintEl = document.getElementById('hint-text');
                const hintBtn = document.getElementById('hint-button');
                if (hintEl && hintBtn) {
                    hintEl.textContent = currentRiddle.hint;
                    hintEl.classList.remove('hidden');
                    hintBtn.disabled = true;
                    hintBtn.style.opacity = '0.5';
                }
            } else {
                target.classList.add('wrong');
                showToast('Ese no es el lugar correcto... Sigue buscando.', 'error');
                setTimeout(() => target.classList.remove('wrong'), 500);
            }
        }

        function showHint() {
            if (isLoading) return;
            
            const hintEl = document.getElementById('hint-text');
            const currentRiddle = riddles[currentRiddleIndex];
            
            if (hintEl && !clueFound) {
                hintEl.textContent = `Pista general: ${currentRiddle.hint}`;
                hintEl.classList.remove('hidden');
            }
        }

        async function submitAnswer() {
            if (isLoading) return;
            
            const input = document.getElementById('answer-input');
            const answer = input.value.trim().toLowerCase();
            const currentRiddle = riddles[currentRiddleIndex];

            if (!answer) {
                showToast('Debes escribir una respuesta...', 'error');
                return;
            }

            isLoading = true;
            const submitBtn = document.getElementById('submit-button');
            if (submitBtn) {
                submitBtn.disabled = true;
                submitBtn.textContent = 'Verificando...';
            }

            if (answer === currentRiddle.answer) {
                showToast('¡Correcto! Has descubierto la verdad...', 'success');
                
                const existingProgress = userProgress.find(p => p.riddle_id === currentRiddle.id);
                
                if (!existingProgress) {
                    const result = await window.dataSdk.create({
                        riddle_id: currentRiddle.id,
                        completed: true,
                        attempts: 1,
                        completed_at: new Date().toISOString()
                    });

                    if (!result.isOk) {
                        showToast('Error al guardar progreso', 'error');
                    }
                }

                setTimeout(() => {
                    if (currentRiddleIndex < riddles.length - 1) {
                        currentRiddleIndex++;
                        clueFound = false;
                        renderGameScreen();
                    } else {
                        renderVictoryScreen();
                    }
                    isLoading = false;
                }, 1500);
            } else {
                showToast('Respuesta incorrecta... Intenta de nuevo.', 'error');
                input.classList.add('wrong');
                setTimeout(() => {
                    input.classList.remove('wrong');
                    isLoading = false;
                    if (submitBtn) {
                        submitBtn.disabled = false;
                        submitBtn.textContent = window.elementSdk?.config?.submit_button || defaultConfig.submit_button;
                    }
                }, 500);
            }
        }

        function renderMenuScreen() {
            const config = window.elementSdk?.config || defaultConfig;
            const customFont = config.font_family || defaultConfig.font_family;
            const baseSize = config.font_size || defaultConfig.font_size;
            const backgroundColor = config.background_color || defaultConfig.background_color;
            const textColor = config.text_color || defaultConfig.text_color;
            const primaryColor = config.primary_action_color || defaultConfig.primary_action_color;
            
            const app = document.getElementById('app');
            app.style.background = `linear-gradient(135deg, ${backgroundColor} 0%, #000000 100%)`;
            
            app.innerHTML = `
                <div class="h-full flex items-center justify-center p-8">
                    <div class="max-w-2xl w-full text-center fade-in" style="font-family: '${customFont}', 'Creepster', cursive;">
                        <h1 class="text-6xl mb-8 blood-text flicker" style="font-size: ${baseSize * 3.5}px; color: ${primaryColor};">
                            ${config.game_title || defaultConfig.game_title}
                        </h1>
                        <p class="text-xl mb-12 opacity-90" style="font-size: ${baseSize * 1.25}px; color: ${textColor};">
                            ${config.intro_text || defaultConfig.intro_text}
                        </p>
                        <button id="start-button" 
                                class="px-12 py-4 rounded-lg font-bold text-xl transition-all hover:scale-105 pulse-glow"
                                style="background: linear-gradient(135deg, ${primaryColor} 0%, #991b1b 100%); 
                                       color: ${textColor}; 
                                       font-size: ${baseSize * 1.25}px;
                                       --glow-color: ${primaryColor};">
                            ${config.start_button || defaultConfig.start_button}
                        </button>
                    </div>
                </div>
            `;

            document.getElementById('start-button').addEventListener('click', () => {
                currentRiddleIndex = 0;
                clueFound = false;
                renderGameScreen();
            });
        }

        function renderGameScreen() {
            const config = window.elementSdk?.config || defaultConfig;
            const customFont = config.font_family || defaultConfig.font_family;
            const baseSize = config.font_size || defaultConfig.font_size;
            const backgroundColor = config.background_color || defaultConfig.background_color;
            const surfaceColor = config.surface_color || defaultConfig.surface_color;
            const textColor = config.text_color || defaultConfig.text_color;
            const primaryColor = config.primary_action_color || defaultConfig.primary_action_color;
            const secondaryColor = config.secondary_action_color || defaultConfig.secondary_action_color;
            
            const currentRiddle = riddles[currentRiddleIndex];
            const app = document.getElementById('app');
            app.style.background = backgroundColor;
            
            app.innerHTML = `
                <div class="h-full flex flex-col p-6" style="font-family: '${customFont}', 'Special Elite', monospace;">
                    <div class="mb-4 flex justify-between items-center">
                        <div id="progress-display" style="color: ${textColor}; font-size: ${baseSize}px;">
                            Acertijos Resueltos: ${userProgress.filter(p => p.completed).length}/${riddles.length}
                        </div>
                        <div style="color: ${primaryColor}; font-size: ${baseSize}px;" class="flicker">
                            Acertijo ${currentRiddleIndex + 1} de ${riddles.length}
                        </div>
                    </div>
                    
                    <div class="flex-1 grid grid-cols-2 gap-6">
                        <div class="rounded-lg p-6 flex flex-col" style="background: ${surfaceColor}; border: 2px solid ${secondaryColor};">
                            <h2 class="text-2xl mb-4 blood-text" style="color: ${primaryColor}; font-size: ${baseSize * 1.5}px;">
                                La Habitación Maldita
                            </h2>
                            <div class="flex-1 rounded-lg overflow-hidden" style="background: #0a0a0a; border: 2px solid ${secondaryColor};">
                                ${getRoomSVG(currentRiddle.room, currentRiddle.clueObject)}
                            </div>
                            <p class="mt-4 text-center opacity-75" style="color: ${textColor}; font-size: ${baseSize * 0.85}px;">
                                Haz clic en los objetos para encontrar la pista oculta...
                            </p>
                        </div>
                        
                        <div class="rounded-lg p-6 flex flex-col" style="background: ${surfaceColor}; border: 2px solid ${secondaryColor};">
                            <h2 class="text-2xl mb-6 blood-text" style="color: ${primaryColor}; font-size: ${baseSize * 1.5}px;">
                                El Acertijo
                            </h2>
                            <p class="text-xl mb-6 leading-relaxed" style="color: ${textColor}; font-size: ${baseSize * 1.25}px;">
                                ${currentRiddle.question}
                            </p>
                            
                            <button id="hint-button" 
                                    class="mb-4 px-6 py-3 rounded-lg font-bold transition-all hover:scale-105"
                                    style="background: ${secondaryColor}; color: ${textColor}; font-size: ${baseSize}px;">
                                ${config.hint_button || defaultConfig.hint_button}
                            </button>
                            
                            <div id="hint-text" class="hidden mb-6 p-4 rounded-lg" style="background: rgba(220, 38, 38, 0.2); color: ${textColor}; font-size: ${baseSize * 0.9}px; border: 1px solid ${primaryColor};">
                            </div>
                            
                            <input id="answer-input" 
                                   type="text" 
                                   placeholder="Escribe tu respuesta..."
                                   class="mb-4 px-6 py-3 rounded-lg font-bold"
                                   style="background: #0a0a0a; color: ${textColor}; border: 2px solid ${secondaryColor}; font-size: ${baseSize}px;">
                            
                            <button id="submit-button" 
                                    class="px-8 py-4 rounded-lg font-bold text-xl transition-all hover:scale-105 pulse-glow"
                                    style="background: linear-gradient(135deg, ${primaryColor} 0%, #991b1b 100%); 
                                           color: ${textColor}; 
                                           font-size: ${baseSize * 1.25}px;
                                           --glow-color: ${primaryColor};">
                                ${config.submit_button || defaultConfig.submit_button}
                            </button>
                        </div>
                    </div>
                </div>
            `;

            const roomSvg = app.querySelector('svg');
            if (roomSvg) {
                roomSvg.addEventListener('click', handleObjectClick);
            }

            const hintBtn = document.getElementById('hint-button');
            if (hintBtn) {
                hintBtn.addEventListener('click', showHint);
            }

            const submitBtn = document.getElementById('submit-button');
            if (submitBtn) {
                submitBtn.addEventListener('click', submitAnswer);
            }

            const answerInput = document.getElementById('answer-input');
            if (answerInput) {
                answerInput.addEventListener('keypress', (e) => {
                    if (e.key === 'Enter') {
                        submitAnswer();
                    }
                });
            }
        }

        function renderVictoryScreen() {
            const config = window.elementSdk?.config || defaultConfig;
            const customFont = config.font_family || defaultConfig.font_family;
            const baseSize = config.font_size || defaultConfig.font_size;
            const backgroundColor = config.background_color || defaultConfig.background_color;
            const textColor = config.text_color || defaultConfig.text_color;
            const primaryColor = config.primary_action_color || defaultConfig.primary_action_color;
            
            const app = document.getElementById('app');
            app.style.background = `linear-gradient(135deg, ${backgroundColor} 0%, #1a1a1a 100%)`;
            
            app.innerHTML = `
                <div class="h-full flex items-center justify-center p-8">
                    <div class="max-w-2xl w-full text-center fade-in" style="font-family: '${customFont}', 'Nosifer', cursive;">
                        <h1 class="text-6xl mb-8 blood-text pulse-glow" style="font-size: ${baseSize * 3.5}px; color: ${primaryColor}; --glow-color: ${primaryColor};">
                            ¡VICTORIA!
                        </h1>
                        <p class="text-2xl mb-8" style="color: ${textColor}; font-size: ${baseSize * 1.5}px;">
                            Has desentrañado todos los misterios...
                        </p>
                        <p class="text-xl mb-12 opacity-75" style="color: ${textColor}; font-size: ${baseSize * 1.25}px;">
                            Pero la oscuridad nunca descansa. ¿Te atreves a volver?
                        </p>
                        <button id="restart-button" 
                                class="px-12 py-4 rounded-lg font-bold text-xl transition-all hover:scale-105"
                                style="background: linear-gradient(135deg, ${primaryColor} 0%, #991b1b 100%); 
                                       color: ${textColor}; 
                                       font-size: ${baseSize * 1.25}px;">
                            Volver al Inicio
                        </button>
                    </div>
                </div>
            `;

            document.getElementById('restart-button').addEventListener('click', renderMenuScreen);
        }

        async function onConfigChange(config) {
            const currentScreen = document.getElementById('app').querySelector('h1');
            if (!currentScreen) return;

            const customFont = config.font_family || defaultConfig.font_family;
            const baseSize = config.font_size || defaultConfig.font_size;
            const backgroundColor = config.background_color || defaultConfig.background_color;
            const surfaceColor = config.surface_color || defaultConfig.surface_color;
            const textColor = config.text_color || defaultConfig.text_color;
            const primaryColor = config.primary_action_color || defaultConfig.primary_action_color;
            const secondaryColor = config.secondary_action_color || defaultConfig.secondary_action_color;

            document.getElementById('app').style.fontFamily = `'${customFont}', 'Special Elite', monospace`;

            const titleElements = document.querySelectorAll('h1, h2');
            titleElements.forEach(el => {
                el.style.fontFamily = `'${customFont}', 'Creepster', cursive`;
                if (el.tagName === 'H1') el.style.fontSize = `${baseSize * 3.5}px`;
                if (el.tagName === 'H2') el.style.fontSize = `${baseSize * 1.5}px`;
                el.style.color = primaryColor;
            });

            const paragraphs = document.querySelectorAll('p');
            paragraphs.forEach(p => {
                p.style.color = textColor;
                const computedSize = window.getComputedStyle(p).fontSize;
                if (computedSize.includes('1.25')) p.style.fontSize = `${baseSize * 1.25}px`;
                else if (computedSize.includes('0.85')) p.style.fontSize = `${baseSize * 0.85}px`;
                else p.style.fontSize = `${baseSize}px`;
            });

            const buttons = document.querySelectorAll('button');
            buttons.forEach(btn => {
                btn.style.color = textColor;
                if (btn.id === 'submit-button' || btn.id === 'start-button' || btn.id === 'restart-button') {
                    btn.style.background = `linear-gradient(135deg, ${primaryColor} 0%, #991b1b 100%)`;
                    btn.style.fontSize = `${baseSize * 1.25}px`;
                } else {
                    btn.style.background = secondaryColor;
                    btn.style.fontSize = `${baseSize}px`;
                }
            });

            const surfaces = document.querySelectorAll('[style*="background"]');
            surfaces.forEach(el => {
                if (el.id === 'app') {
                    if (currentScreen.textContent.includes('VICTORIA') || currentScreen.textContent.includes(config.game_title || defaultConfig.game_title)) {
                        el.style.background = `linear-gradient(135deg, ${backgroundColor} 0%, #000000 100%)`;
                    } else {
                        el.style.background = backgroundColor;
                    }
                } else if (el.classList.contains('rounded-lg') && el.classList.contains('p-6')) {
                    el.style.background = surfaceColor;
                    el.style.borderColor = secondaryColor;
                }
            });

            const input = document.getElementById('answer-input');
            if (input) {
                input.style.color = textColor;
                input.style.borderColor = secondaryColor;
                input.style.fontSize = `${baseSize}px`;
            }

            const gameTitle = document.querySelector('h1');
            if (gameTitle && gameTitle.textContent !== '¡VICTORIA!') {
                gameTitle.textContent = config.game_title || defaultConfig.game_title;
            }

            const introText = document.querySelector('p.text-xl');
            if (introText && !introText.classList.contains('mb-8')) {
                introText.textContent = config.intro_text || defaultConfig.intro_text;
            }

            const startBtn = document.getElementById('start-button');
            if (startBtn) startBtn.textContent = config.start_button || defaultConfig.start_button;

            const hintBtn = document.getElementById('hint-button');
            if (hintBtn) hintBtn.textContent = config.hint_button || defaultConfig.hint_button;

            const submitBtn = document.getElementById('submit-button');
            if (submitBtn && !isLoading) submitBtn.textContent = config.submit_button || defaultConfig.submit_button;
        }

        async function init() {
            const sdkResult = await window.dataSdk.init(dataHandler);
            if (!sdkResult.isOk) {
                console.error('Failed to initialize Data SDK');
            }

            if (window.elementSdk) {
                window.elementSdk.init({
                    defaultConfig,
                    onConfigChange,
                    mapToCapabilities: (config) => ({
                        recolorables: [
                            {
                                get: () => config.background_color || defaultConfig.background_color,
                                set: (value) => {
                                    config.background_color = value;
                                    window.elementSdk.setConfig({ background_color: value });
                                }
                            },
                            {
                                get: () => config.surface_color || defaultConfig.surface_color,
                                set: (value) => {
                                    config.surface_color = value;
                                    window.elementSdk.setConfig({ surface_color: value });
                                }
                            },
                            {
                                get: () => config.text_color || defaultConfig.text_color,
                                set: (value) => {
                                    config.text_color = value;
                                    window.elementSdk.setConfig({ text_color: value });
                                }
                            },
                            {
                                get: () => config.primary_action_color || defaultConfig.primary_action_color,
                                set: (value) => {
                                    config.primary_action_color = value;
                                    window.elementSdk.setConfig({ primary_action_color: value });
                                }
                            },
                            {
                                get: () => config.secondary_action_color || defaultConfig.secondary_action_color,
                                set: (value) => {
                                    config.secondary_action_color = value;
                                    window.elementSdk.setConfig({ secondary_action_color: value });
                                }
                            }
                        ],
                        borderables: [],
                        fontEditable: {
                            get: () => config.font_family || defaultConfig.font_family,
                            set: (value) => {
                                config.font_family = value;
                                window.elementSdk.setConfig({ font_family: value });
                            }
                        },
                        fontSizeable: {
                            get: () => config.font_size || defaultConfig.font_size,
                            set: (value) => {
                                config.font_size = value;
                                window.elementSdk.setConfig({ font_size: value });
                            }
                        }
                    }),
                    mapToEditPanelValues: (config) => new Map([
                        ["game_title", config.game_title || defaultConfig.game_title],
                        ["intro_text", config.intro_text || defaultConfig.intro_text],
                        ["start_button", config.start_button || defaultConfig.start_button],
                        ["hint_button", config.hint_button || defaultConfig.hint_button],
                        ["submit_button", config.submit_button || defaultConfig.submit_button]
                    ])
                });
            }

            renderMenuScreen();
        }

        init();
    </script>
 <script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'9c7634ae65eec712',t:'MTc2OTk5ODc4OS4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
fetch("https://midominio.com/juego", {
  method: "POST", // ← Esto es el método HTTP
  body: JSON.stringify({ puntuacion: 100 }),
  headers: { "Content-Type": "application/json" }
});
