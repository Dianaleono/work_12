<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>WebXR Сцена з Двома Моделями</title>
    <script src="https://aframe.io/releases/1.0.4/aframe.min.js"></script>
    <script src="https://unpkg.com/aframe-look-at-component@0.8.0/dist/aframe-look-at-component.min.js"></script>
    <style>
        body {
            margin: 0;
            font-family: Arial, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            overflow: hidden;
        }
        
        .ui-overlay {
            position: absolute;
            top: 20px;
            left: 20px;
            z-index: 1000;
            background: rgba(255, 255, 255, 0.9);
            padding: 15px;
            border-radius: 10px;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.3);
        }
        
        .ui-overlay h3 {
            margin: 0 0 10px 0;
            color: #333;
            font-size: 18px;
        }
        
        .ui-overlay p {
            margin: 5px 0;
            color: #666;
            font-size: 14px;
        }
        
        .controls {
            position: absolute;
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            z-index: 1000;
            display: flex;
            gap: 10px;
        }
        
        .control-btn {
            background: rgba(255, 255, 255, 0.9);
            border: none;
            padding: 10px 15px;
            border-radius: 25px;
            cursor: pointer;
            font-size: 14px;
            font-weight: bold;
            color: #333;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
            transition: all 0.3s ease;
        }
        
        .control-btn:hover {
            background: white;
            transform: translateY(-2px);
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.3);
        }
    </style>
</head>
<body>
    <div class="ui-overlay">
        <h3>🎯 WebXR Профорієнтаційна Сцена</h3>
        <p>📍 Ліворуч: Синій куб (Факультет інформатики)</p>
        <p>📍 Праворуч: Червона сфера (Кафедра математики)</p>
        <p>🎮 Використовуйте миш або VR контролери для взаємодії</p>
    </div>

    <div class="controls">
        <button class="control-btn" onclick="animateLeft()">🔄 Анімація ліва</button>
        <button class="control-btn" onclick="animateRight()">🔄 Анімація права</button>
        <button class="control-btn" onclick="resetScene()">↺ Скинути</button>
    </div>

    <a-scene 
        vr-mode-ui="enabled: true" 
        embedded 
        background="color: #87CEEB"
        fog="type: linear; color: #87CEEB; near: 10; far: 100">
        
        <!-- Освітлення -->
        <a-light type="ambient" color="#404040" intensity="0.5"></a-light>
        <a-light type="directional" color="#ffffff" intensity="0.8" position="5 5 5"></a-light>
        <a-light type="point" color="#ffaa00" intensity="0.7" position="0 5 0"></a-light>

        <!-- Модель ліворуч - Куб (представляє факультет інформатики) -->
        <a-entity id="leftModel" 
                  position="-5 1 -3"
                  animation__hover="property: rotation; to: 0 360 0; dur: 4000; loop: true; pauseEvents: mouseleave; startEvents: mouseenter">
            
            <!-- Основний куб -->
            <a-box id="leftBox"
                   color="#4A90E2" 
                   metalness="0.3" 
                   roughness="0.4"
                   width="2" 
                   height="2" 
                   depth="2"
                   animation__float="property: position; to: 0 0.5 0; dur: 2000; dir: alternate; loop: true; easing: easeInOutSine"
                   class="interactive-object">
            </a-box>
            
            <!-- Текст над кубом -->
            <a-text value="ІНФОРМАТИКА" 
                    position="0 1.8 0" 
                    align="center" 
                    color="#FFFFFF"
                    width="8"
                    look-at="[camera]"
                    animation__pulse="property: scale; to: 1.1 1.1 1.1; dur: 1500; dir: alternate; loop: true">
            </a-text>
            
            <!-- Індикатор взаємодії -->
            <a-ring position="0 -1.5 0" 
                    radius-inner="0.8" 
                    radius-outer="1" 
                    color="#4A90E2" 
                    opacity="0.7"
                    rotation="-90 0 0"
                    animation__spin="property: rotation; to: -90 360 0; dur: 3000; loop: true">
            </a-ring>
        </a-entity>

        <!-- Модель праворуч - Сфера (представляє кафедру математики) -->
        <a-entity id="rightModel" 
                  position="5 1 -3"
                  animation__hover="property: scale; to: 1.2 1.2 1.2; dur: 500; pauseEvents: mouseleave; startEvents: mouseenter">
            
            <!-- Основна сфера -->
            <a-sphere id="rightSphere"
                      color="#E74C3C" 
                      metalness="0.5" 
                      roughness="0.2"
                      radius="1.2"
                      animation__bounce="property: position; to: 0 0.8 0; dur: 1800; dir: alternate; loop: true; easing: easeInOutBounce"
                      class="interactive-object">
            </a-sphere>
            
            <!-- Текст над сферою -->
            <a-text value="МАТЕМАТИКА" 
                    position="0 2.2 0" 
                    align="center" 
                    color="#FFFFFF"
                    width="8"
                    look-at="[camera]"
                    animation__glow="property: material.color; to: #FFD700; dur: 2000; dir: alternate; loop: true">
            </a-text>
            
            <!-- Орбітальні елементи -->
            <a-torus position="0 0 0" 
                     color="#F39C12" 
                     radius="2" 
                     radius-tubular="0.1"
                     rotation="45 0 0"
                     animation__orbit="property: rotation; to: 45 360 0; dur: 5000; loop: true">
            </a-torus>
            
            <a-torus position="0 0 0" 
                     color="#9B59B6" 
                     radius="1.5" 
                     radius-tubular="0.08"
                     rotation="0 45 90"
                     animation__orbit2="property: rotation; to: 360 45 90; dur: 4000; loop: true">
            </a-torus>
        </a-entity>

        <!-- Підлога -->
        <a-plane position="0 0 -4" 
                 rotation="-90 0 0" 
                 width="20" 
                 height="20" 
                 color="#2C3E50" 
                 opacity="0.8"
                 material="roughness: 0.8">
        </a-plane>

        <!-- Декоративні елементи -->
        <a-cylinder position="-8 0.5 -6" 
                    radius="0.3" 
                    height="1" 
                    color="#34495E"
                    animation__sway="property: rotation; to: 0 0 10; dur: 3000; dir: alternate; loop: true">
        </a-cylinder>
        
        <a-cylinder position="8 0.5 -6" 
                    radius="0.3" 
                    height="1" 
                    color="#34495E"
                    animation__sway2="property: rotation; to: 0 0 -10; dur: 2500; dir: alternate; loop: true">
        </a-cylinder>

        <!-- Частинки в повітрі -->
        <a-entity id="particles">
            <a-sphere position="-3 3 -2" radius="0.05" color="#FFD700" 
                      animation__float1="property: position; to: -3 4 -2; dur: 3000; dir: alternate; loop: true">
            </a-sphere>
            <a-sphere position="2 4 -1" radius="0.05" color="#E67E22" 
                      animation__float2="property: position; to: 2 5 -1; dur: 2500; dir: alternate; loop: true">
            </a-sphere>
            <a-sphere position="0 3.5 -5" radius="0.05" color="#8E44AD" 
                      animation__float3="property: position; to: 0 4.5 -5; dur: 2800; dir: alternate; loop: true">
            </a-sphere>
        </a-entity>

        <!-- Камера з курсором для взаємодії -->
        <a-entity id="cameraRig" position="0 1.6 0">
            <a-camera id="camera" 
                      look-controls 
                      wasd-controls="acceleration: 10"
                      cursor="rayOrigin: mouse">
                <a-cursor 
                    geometry="primitive: ring; radiusInner: 0.02; radiusOuter: 0.03"
                    material="color: #FFFFFF; shader: flat"
                    position="0 0 -1"
                    animation__click="property: scale; startEvents: click; to: 0.8 0.8 0.8; dur: 150; dir: alternate; easing: easeInQuad">
                </a-cursor>
            </a-camera>
        </a-entity>
    </a-scene>

    <script>
        // Глобальні змінні для керування анімаціями
        let leftAnimating = false;
        let rightAnimating = false;

        // Функція анімації лівої моделі
        function animateLeft() {
            const leftModel = document.querySelector('#leftModel');
            const leftBox = document.querySelector('#leftBox');
            
            if (!leftAnimating) {
                leftAnimating = true;
                
                // Комплексна анімація
                leftModel.setAttribute('animation__jump', {
                    property: 'position',
                    to: '-5 3 -3',
                    dur: 800,
                    easing: 'easeOutBounce'
                });
                
                leftBox.setAttribute('animation__color', {
                    property: 'color',
                    to: '#E74C3C',
                    dur: 800,
                    dir: 'alternate'
                });
                
                // Повернення до початкового стану
                setTimeout(() => {
                    leftModel.setAttribute('animation__return', {
                        property: 'position',
                        to: '-5 1 -3',
                        dur: 800,
                        easing: 'easeInBounce'
                    });
                    leftAnimating = false;
                }, 1600);
            }
        }

        // Функція анімації правої моделі
        function animateRight() {
            const rightModel = document.querySelector('#rightModel');
            const rightSphere = document.querySelector('#rightSphere');
            
            if (!rightAnimating) {
                rightAnimating = true;
                
                // Обертання та зміна кольору
                rightModel.setAttribute('animation__spin', {
                    property: 'rotation',
                    to: '0 720 0',
                    dur: 2000,
                    easing: 'easeInOutQuart'
                });
                
                rightSphere.setAttribute('animation__morph', {
                    property: 'color',
                    to: '#4A90E2',
                    dur: 1000,
                    dir: 'alternate'
                });
                
                setTimeout(() => {
                    rightAnimating = false;
                }, 2000);
            }
        }

        // Функція скидання сцени
        function resetScene() {
            const leftModel = document.querySelector('#leftModel');
            const rightModel = document.querySelector('#rightModel');
            const leftBox = document.querySelector('#leftBox');
            const rightSphere = document.querySelector('#rightSphere');
            
            // Скидання позицій та кольорів
            leftModel.setAttribute('position', '-5 1 -3');
            rightModel.setAttribute('position', '5 1 -3');
            rightModel.setAttribute('rotation', '0 0 0');
            
            leftBox.setAttribute('color', '#4A90E2');
            rightSphere.setAttribute('color', '#E74C3C');
            
            leftAnimating = false;
            rightAnimating = false;
        }

        // Додавання звукових ефектів (симуляція)
        document.addEventListener('DOMContentLoaded', function() {
            const interactiveObjects = document.querySelectorAll('.interactive-object');
            
            interactiveObjects.forEach(obj => {
                obj.addEventListener('mouseenter', function() {
                    console.log('Об\'єкт активовано:', this.id);
                    // Тут можна додати звукові ефекти
                });
                
                obj.addEventListener('click', function() {
                    console.log('Клік по об\'єкту:', this.id);
                    // Тут можна додати логіку квесту
                });
            });
        });

        // Симуляція профорієнтаційної інформації
        function showInfo(modelType) {
            const info = modelType === 'left' ? 
                'Факультет інформатики - вивчення програмування, веб-технологій, штучного інтелекту' :
                'Кафедра математики - алгебра, геометрія, математичний аналіз, статистика';
            
            alert(info);
        }

        // Обробка подій для показу інформації
        document.querySelector('#leftBox').addEventListener('click', () => showInfo('left'));
        document.querySelector('#rightSphere').addEventListener('click', () => showInfo('right'));
    </script>
</body>
</html>
