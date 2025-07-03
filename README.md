<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>AR Профорієнтаційний Квест - КДПУ</title>
    <script src="https://aframe.io/releases/1.0.4/aframe.min.js"></script>
    <script src="https://unpkg.com/aframe-look-at-component@0.8.0/dist/aframe-look-at-component.min.js"></script>
    <script src="https://raw.githack.com/AR-js-org/AR.js/master/aframe/build/aframe-ar-nft.js"></script>
    <style>
        body {
            margin: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            overflow: hidden;
            background: linear-gradient(135deg, #1e3c72 0%, #2a5298 100%);
        }
        
        .quest-ui {
            position: absolute;
            top: 20px;
            left: 20px;
            z-index: 1000;
            background: rgba(255, 255, 255, 0.95);
            padding: 20px;
            border-radius: 15px;
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
            max-width: 320px;
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.2);
        }
        
        .quest-ui h2 {
            margin: 0 0 15px 0;
            color: #1e3c72;
            font-size: 20px;
            font-weight: bold;
        }
        
        .quest-ui p {
            margin: 8px 0;
            color: #444;
            font-size: 14px;
            line-height: 1.4;
        }
        
        .quest-progress {
            position: absolute;
            top: 20px;
            right: 20px;
            z-index: 1000;
            background: rgba(46, 125, 50, 0.9);
            color: white;
            padding: 15px;
            border-radius: 10px;
            font-weight: bold;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
        }
        
        .quest-controls {
            position: absolute;
            bottom: 30px;
            left: 50%;
            transform: translateX(-50%);
            z-index: 1000;
            display: flex;
            gap: 15px;
            flex-wrap: wrap;
            justify-content: center;
        }
        
        .quest-btn {
            background: linear-gradient(45deg, #2196F3, #21CBF3);
            border: none;
            padding: 12px 20px;
            border-radius: 25px;
            cursor: pointer;
            font-size: 14px;
            font-weight: bold;
            color: white;
            box-shadow: 0 4px 15px rgba(33, 150, 243, 0.4);
            transition: all 0.3s ease;
            min-width: 120px;
        }
        
        .quest-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 20px rgba(33, 150, 243, 0.6);
        }
        
        .quest-btn.secondary {
            background: linear-gradient(45deg, #FF9800, #F57C00);
            box-shadow: 0 4px 15px rgba(255, 152, 0, 0.4);
        }
        
        .quest-btn.secondary:hover {
            box-shadow: 0 6px 20px rgba(255, 152, 0, 0.6);
        }
        
        .station-indicator {
            position: absolute;
            bottom: 100px;
            left: 50%;
            transform: translateX(-50%);
            z-index: 1000;
            background: rgba(0, 0, 0, 0.8);
            color: white;
            padding: 10px 20px;
            border-radius: 20px;
            font-size: 16px;
            display: none;
        }
        
        .arjs-loader {
            height: 100%;
            width: 100%;
            position: absolute;
            top: 0;
            left: 0;
            background-color: rgba(30, 60, 114, 0.9);
            z-index: 9999;
            display: flex;
            justify-content: center;
            align-items: center;
            flex-direction: column;
        }
        
        .arjs-loader div {
            text-align: center;
            font-size: 1.5em;
            color: white;
            margin: 10px 0;
        }
        
        .loading-spinner {
            border: 4px solid rgba(255, 255, 255, 0.3);
            border-top: 4px solid white;
            border-radius: 50%;
            width: 40px;
            height: 40px;
            animation: spin 1s linear infinite;
            margin-bottom: 20px;
        }
        
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
    </style>
</head>
<body>
    <div class="quest-ui">
        <h2>🎯 AR Профорієнтаційний Квест</h2>
        <p><strong>📍 Локація:</strong> КДПУ</p>
        <p><strong>🎮 Завдання:</strong> Знайти та дослідити AR-об'єкти</p>
        <p><strong>⭐ Станція 1:</strong> Кафедра інформатики (ліворуч)</p>
        <p><strong>⭐ Станція 2:</strong> Кафедра математики (праворуч)</p>
        <p><strong>💡 Підказка:</strong> Клікніть на об'єкти для отримання інформації</p>
    </div>

    <div class="quest-progress">
        <div id="progress-text">Прогрес: 0/2 станцій</div>
        <div id="score-text">Бали: 0</div>
    </div>

    <div class="station-indicator" id="station-indicator">
        🎯 Станція знайдена!
    </div>

    <div class="quest-controls">
        <button class="quest-btn" onclick="startQuest()">🚀 Почати квест</button>
        <button class="quest-btn secondary" onclick="resetQuest()">🔄 Скинути</button>
        <button class="quest-btn" onclick="showHint()">💡 Підказка</button>
        <button class="quest-btn secondary" onclick="toggleDebug()">🔧 Налагодження</button>
    </div>

    <!-- AR.js loader -->
    <div class="arjs-loader" id="arjs-loader">
        <div class="loading-spinner"></div>
        <div>Завантаження AR-технологій...</div>
        <div style="font-size: 0.8em; opacity: 0.8;">Переконайтесь, що камера увімкнена</div>
    </div>

    <a-scene 
        vr-mode-ui="enabled: false" 
        embedded
        arjs="sourceType: webcam; debugUIEnabled: false; trackingMethod: best;"
        renderer="logarithmicDepthBuffer: true; colorManagement: true; sortObjects: true;"
        background="color: #1a1a2e; transparent: true">
        
        <!-- Глобальне освітлення -->
        <a-light type="ambient" color="#404040" intensity="0.6"></a-light>
        <a-light type="directional" color="#ffffff" intensity="0.8" position="2 4 3"></a-light>
        <a-light type="point" color="#4FC3F7" intensity="0.5" position="-5 3 -2"></a-light>
        <a-light type="point" color="#FF7043" intensity="0.5" position="5 3 -2"></a-light>

        <!-- Ліва станція - кафедра інформатики -->
        <a-entity id="leftStation" 
                  position="-4 0 -3"
                  class="quest-station"
                  data-station="informatics">
            
            <!-- Основна модель - куб з голографічним ефектом -->
            <a-entity id="leftModel" 
                      position="0 1.5 0"
                      animation__hover="property: rotation; to: 0 360 0; dur: 3000; loop: true; pauseEvents: mouseleave; startEvents: mouseenter">
                
                <a-box id="informaticsBox"
                       color="#1E88E5" 
                       metalness="0.8" 
                       roughness="0.2"
                       width="1.5" 
                       height="1.5" 
                       depth="1.5"
                       animation__float="property: position; to: 0 0.3 0; dur: 2500; dir: alternate; loop: true; easing: easeInOutSine"
                       class="interactive-object">
                    
                    <!-- Внутрішній світловий куб -->
                    <a-box color="#64B5F6" 
                           width="0.8" 
                           height="0.8" 
                           depth="0.8"
                           opacity="0.6"
                           animation__pulse="property: scale; to: 1.2 1.2 1.2; dur: 1500; dir: alternate; loop: true">
                    </a-box>
                </a-box>
            </a-entity>
            
            <!-- Інформаційна панель -->
            <a-plane position="0 3 0" 
                     rotation="0 0 0"
                     width="3" 
                     height="1.5" 
                     color="#1565C0" 
                     opacity="0.9"
                     look-at="[camera]">
                <a-text value="КАФЕДРА ІНФОРМАТИКИ&#10;🔬 Програмування&#10;🌐 Веб-технології&#10;🤖 Штучний інтелект" 
                        position="0 0 0.01" 
                        align="center" 
                        color="#FFFFFF"
                        width="4"
                        font="kelsonsans"
                        animation__glow="property: material.color; to: #E3F2FD; dur: 2000; dir: alternate; loop: true">
                </a-text>
            </a-plane>
            
            <!-- Платформа станції -->
            <a-cylinder position="0 0 0" 
                        radius="2" 
                        height="0.2" 
                        color="#0D47A1" 
                        opacity="0.8"
                        animation__spin="property: rotation; to: 0 360 0; dur: 20000; loop: true">
                
                <!-- Індикатори навколо платформи -->
                <a-torus position="0 0.15 0" 
                         color="#2196F3" 
                         radius="1.8" 
                         radius-tubular="0.1"
                         animation__orbit="property: rotation; to: 0 -360 0; dur: 8000; loop: true">
                </a-torus>
            </a-cylinder>
        </a-entity>

        <!-- Права станція - Кафедра математики -->
        <a-entity id="rightStation" 
                  position="4 0 -3"
                  class="quest-station"
                  data-station="mathematics">
            
            <!-- Основна модель - сфера з математичними символами -->
            <a-entity id="rightModel" 
                      position="0 1.5 0"
                      animation__hover="property: scale; to: 1.3 1.3 1.3; dur: 500; pauseEvents: mouseleave; startEvents: mouseenter">
                
                <a-sphere id="mathematicsSphere"
                          color="#D32F2F" 
                          metalness="0.6" 
                          roughness="0.3"
                          radius="0.8"
                          animation__bounce="property: position; to: 0 0.5 0; dur: 2000; dir: alternate; loop: true; easing: easeInOutBounce"
                          class="interactive-object">
                    
                    <!-- Математичні символи навколо сфери -->
                    <a-text value="π" position="1.2 0 0" color="#FFD54F" width="8" look-at="[camera]"></a-text>
                    <a-text value="∑" position="-1.2 0 0" color="#FFD54F" width="8" look-at="[camera]"></a-text>
                    <a-text value="∫" position="0 1.2 0" color="#FFD54F" width="8" look-at="[camera]"></a-text>
                    <a-text value="∞" position="0 -1.2 0" color="#FFD54F" width="8" look-at="[camera]"></a-text>
                </a-sphere>
            </a-entity>
            
            <!-- Інформаційна панель -->
            <a-plane position="0 3 0" 
                     rotation="0 0 0"
                     width="3" 
                     height="1.5" 
                     color="#C62828" 
                     opacity="0.9"
                     look-at="[camera]">
                <a-text value="КАФЕДРА МАТЕМАТИКИ&#10;📐 Геометрія&#10;📊 Статистика&#10;🔢 Алгебра" 
                        position="0 0 0.01" 
                        align="center" 
                        color="#FFFFFF"
                        width="4"
                        font="kelsonsans"
                        animation__flicker="property: material.color; to: #FFEBEE; dur: 1800; dir: alternate; loop: true">
                </a-text>
            </a-plane>
            
            <!-- Платформа станції -->
            <a-cylinder position="0 0 0" 
                        radius="2" 
                        height="0.2" 
                        color="#B71C1C" 
                        opacity="0.8"
                        animation__spin="property: rotation; to: 0 -360 0; dur: 25000; loop: true">
                
                <!-- Орбітальні кільця -->
                <a-torus position="0 0.15 0" 
                         color="#FF5722" 
                         radius="1.5" 
                         radius-tubular="0.08"
                         rotation="45 0 0"
                         animation__orbit1="property: rotation; to: 45 360 0; dur: 6000; loop: true">
                </a-torus>
                <a-torus position="0 0.15 0" 
                         color="#FF9800" 
                         radius="1.2" 
                         radius-tubular="0.06"
                         rotation="0 45 90"
                         animation__orbit2="property: rotation; to: 360 45 90; dur: 5000; loop: true">
                </a-torus>
            </a-cylinder>
        </a-entity>

        <!-- Декоративні елементи середовища -->
        <a-plane position="0 -0.5 -5" 
                 rotation="-90 0 0" 
                 width="20" 
                 height="20" 
                 color="#263238" 
                 opacity="0.3"
                 material="roughness: 0.8; metalness: 0.1">
        </a-plane>

        <!-- Атмосферні частинки -->
        <a-entity id="particles">
            <a-sphere position="-6 2 -4" radius="0.05" color="#81C784" 
                      animation__float1="property: position; to: -6 4 -4; dur: 4000; dir: alternate; loop: true; easing: easeInOutSine">
            </a-sphere>
            <a-sphere position="6 3 -2" radius="0.05" color="#FFB74D" 
                      animation__float2="property: position; to: 6 5 -2; dur: 3500; dir: alternate; loop: true; easing: easeInOutSine">
            </a-sphere>
            <a-sphere position="0 4 -8" radius="0.05" color="#BA68C8" 
                      animation__float3="property: position; to: 0 6 -8; dur: 3200; dir: alternate; loop: true; easing: easeInOutSine">
            </a-sphere>
        </a-entity>

        <!-- Камера з курсором -->
        <a-entity id="cameraRig" position="0 1.6 0">
            <a-camera id="mainCamera" 
                      look-controls="pointerLockEnabled: true"
                      wasd-controls="acceleration: 15; fly: false"
                      cursor="rayOrigin: mouse">
                <a-cursor 
                    geometry="primitive: ring; radiusInner: 0.015; radiusOuter: 0.025"
                    material="color: #4FC3F7; shader: flat"
                    position="0 0 -0.5"
                    animation__click="property: scale; startEvents: click; to: 0.7 0.7 0.7; dur: 150; dir: alternate; easing: easeInQuad">
                </a-cursor>
            </a-camera>
        </a-entity>
    </a-scene>

    <script>
        // Квест змінні
        let questStarted = false;
        let stationsVisited = 0;
        let totalScore = 0;
        let debugMode = false;
        
        // Дані станцій
        const stationData = {
            informatics: {
                name: "Кафедра інформатики",
                description: "Вивчення програмування, веб-технологій, штучного інтелекту та комп'ютерних наук",
                points: 50,
                tasks: ["Розв'яжи алгоритмічну задачу", "Створи веб-сторінку", "Програмуй на Python"]
            },
            mathematics: {
                name: "Кафедра математики", 
                description: "Дослідження алгебри, геометрії, математичного аналізу та статистики",
                points: 50,
                tasks: ["Розв'яжи рівняння", "Побудуй графік функції", "Обчисли інтеграл"]
            }
        };

        // Ініціалізація
        document.addEventListener('DOMContentLoaded', function() {
            // Приховати loader після завантаження
            setTimeout(() => {
                document.getElementById('arjs-loader').style.display = 'none';
            }, 2000);
            
            setupEventListeners();
            console.log('AR Профорієнтаційний квест ініціалізовано');
        });

        function setupEventListeners() {
            // Обробка кліків по моделях
            document.getElementById('informaticsBox').addEventListener('click', () => {
                handleStationClick('informatics');
            });
            
            document.getElementById('mathematicsSphere').addEventListener('click', () => {
                handleStationClick('mathematics');
            });
            
            // Hover ефекти
            const interactiveObjects = document.querySelectorAll('.interactive-object');
            interactiveObjects.forEach(obj => {
                obj.addEventListener('mouseenter', function() {
                    this.setAttribute('animation__hover-glow', {
                        property: 'material.emissive',
                        to: '#222222',
                        dur: 300
                    });
                });
                
                obj.addEventListener('mouseleave', function() {
                    this.setAttribute('animation__hover-glow', {
                        property: 'material.emissive',
                        to: '#000000',
                        dur: 300
                    });
                });
            });
        }

        function handleStationClick(stationType) {
            if (!questStarted) {
                alert('🎯 Спочатку почніть квест!');
                return;
            }
            
            const station = stationData[stationType];
            const stationElement = document.querySelector(`[data-station="${stationType}"]`);
            
            if (stationElement.classList.contains('visited')) {
                alert('📍 Ця станція вже пройдена!');
                return;
            }
            
            // Відзначити станцію як пройдену
            stationElement.classList.add('visited');
            stationsVisited++;
            totalScore += station.points;
            
            // Показати інформацію про станцію
            showStationInfo(station);
            
            // Оновити прогрес
            updateProgress();
            
            // Анімація успіху
            showStationIndicator();
            
            // Перевірка завершення квесту
            if (stationsVisited >= 2) {
                setTimeout(() => {
                    alert(`🎉 Квест завершено!\n🏆 Загальний бал: ${totalScore}\n🎯 Ви успішно дослідили всі станції!`);
                }, 1000);
            }
        }

        function showStationInfo(station) {
            const infoText = `
🎯 Станція: ${station.name}
📝 Опис: ${station.description}
⭐ Бали: +${station.points}
📋 Завдання: ${station.tasks.join(', ')}
            `;
            
            alert(infoText);
        }

        function showStationIndicator() {
            const indicator = document.getElementById('station-indicator');
            indicator.style.display = 'block';
            
            setTimeout(() => {
                indicator.style.display = 'none';
            }, 3000);
        }

        function updateProgress() {
            document.getElementById('progress-text').textContent = `Прогрес: ${stationsVisited}/2 станцій`;
            document.getElementById('score-text').textContent = `Бали: ${totalScore}`;
        }

        // Функції управління квестом
        function startQuest() {
            questStarted = true;
            stationsVisited = 0;
            totalScore = 0;
            
            // Скинути стан станцій
            document.querySelectorAll('.quest-station').forEach(station => {
                station.classList.remove('visited');
            });
            
            updateProgress();
            alert('🚀 Квест розпочато! Знайдіть та клікніть на AR-об\'єкти для збору інформації.');
        }

        function resetQuest() {
            questStarted = false;
            stationsVisited = 0;
            totalScore = 0;
            
            document.querySelectorAll('.quest-station').forEach(station => {
                station.classList.remove('visited');
            });
            
            updateProgress();
            alert('🔄 Квест скинуто. Натисніть "Почати квест" для нового проходження.');
        }

        function showHint() {
            const hints = [
                "💡 Підказка: Клікніть на синій куб ліворуч для вивчення інформатики",
                "💡 Підказка: Червона сфера праворуч розповість про математику",
                "💡 Підказка: Наведіть курсор на об'єкти для анімації",
                "💡 Підказка: Кожна станція дає 50 балів"
            ];
            
            const randomHint = hints[Math.floor(Math.random() * hints.length)];
            alert(randomHint);
        }

        function toggleDebug() {
            debugMode = !debugMode;
            
            if (debugMode) {
                console.log('Debug mode enabled');
                console.log('Stations visited:', stationsVisited);
                console.log('Total score:', totalScore);
                console.log('Quest started:', questStarted);
                alert('🔧 Режим налагодження увімкнено (перевірте консоль)');
            } else {
                alert('🔧 Режим налагодження вимкнено');
            }
        }

        // Симуляція GPS координат (як у прикладі з документа)
        function simulateGPSTracking() {
            // Координати Криворізького ДПУ: 47.908387, 33.411848
            console.log('GPS tracking simulation for Kryvyi Rih State Pedagogical University');
            console.log('Coordinates: 47.908387, 33.411848');
        }

        // Запуск GPS симуляції
        simulateGPSTracking();
    </script>
</body>
</html>
