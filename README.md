Add ocean photos website
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>像素海洋照片漂流</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: 'Arial', sans-serif;
            overflow: hidden;
            height: 100vh;
            background: #a8d8ff;
            position: relative;
        }
        
        /* 像素海洋背景 */
        .ocean {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            overflow: hidden;
            z-index: 1;
            background: 
                linear-gradient(45deg, #a8d8ff 25%, transparent 25%, transparent 75%, #a8d8ff 75%, #a8d8ff),
                linear-gradient(45deg, #a8d8ff 25%, #8bcbff 25%, #8bcbff 75%, #a8d8ff 75%, #a8d8ff);
            background-size: 60px 60px;
            background-position: 0 0, 30px 30px;
            animation: wave 4s infinite linear;
        }
        
        /* 像素波浪效果 */
        .wave {
            position: absolute;
            bottom: 0;
            left: 0;
            width: 200%;
            height: 100px;
            background: 
                repeating-linear-gradient(
                    90deg,
                    transparent,
                    transparent 20px,
                    rgba(255, 255, 255, 0.3) 20px,
                    rgba(255, 255, 255, 0.3) 40px
                );
            animation: moveWave 6s infinite linear;
            opacity: 0.6;
        }
        
        .wave2 {
            height: 80px;
            animation: moveWave 8s infinite linear;
            opacity: 0.4;
            bottom: 20px;
        }
        
        .wave3 {
            height: 60px;
            animation: moveWave 10s infinite linear;
            opacity: 0.2;
            bottom: 40px;
        }
        
        @keyframes moveWave {
            0% {
                transform: translateX(0);
            }
            100% {
                transform: translateX(-50%);
            }
        }
        
        @keyframes wave {
            0%, 100% {
                background-position: 0 0, 30px 30px;
            }
            50% {
                background-position: 0 10px, 30px 40px;
            }
        }
        
        /* 侧边栏样式 */
        .sidebar {
            position: fixed;
            top: 0;
            left: -80px;
            width: 80px;
            height: 100%;
            background: rgba(0, 0, 0, 0.9);
            transition: left 0.3s ease;
            z-index: 100;
            padding: 20px 10px;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 20px;
        }
        
        .sidebar.open {
            left: 0;
        }
        
        .toggle-btn {
            position: absolute;
            top: 20px;
            right: -50px;
            width: 50px;
            height: 50px;
            background: #000;
            border: none;
            border-radius: 0 10px 10px 0;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 24px;
            color: #fff;
        }
        
        .upload-btn {
            width: 50px;
            height: 50px;
            background: #000;
            color: white;
            border: none;
            border-radius: 50%;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 20px;
            transition: all 0.3s ease;
            box-shadow: 0 0 0 2px #fff;
        }
        
        .upload-btn:hover {
            background: #333;
            transform: scale(1.1);
        }
        
        #file-input {
            display: none;
        }
        
        /* 照片容器 */
        .photo-container {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 5;
            pointer-events: none;
        }
        
        .floating-photo {
            position: absolute;
            width: 150px;
            height: 150px;
            overflow: hidden;
            pointer-events: auto;
            cursor: move;
            animation: float 15s infinite ease-in-out;
            z-index: 10;
        }
        
        .floating-photo img {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }
        
        @keyframes float {
            0% {
                transform: translate(0, 0) rotate(0deg);
            }
            25% {
                transform: translate(20px, -15px) rotate(3deg);
            }
            50% {
                transform: translate(-15px, 10px) rotate(-2deg);
            }
            75% {
                transform: translate(10px, -5px) rotate(1deg);
            }
            100% {
                transform: translate(0, 0) rotate(0deg);
            }
        }
    </style>
</head>
<body>
    <!-- 像素海洋背景 -->
    <div class="ocean">
        <div class="wave"></div>
        <div class="wave wave2"></div>
        <div class="wave wave3"></div>
    </div>
    
    <!-- 侧边栏 -->
    <div class="sidebar" id="sidebar">
        <button class="toggle-btn" id="toggle-btn">☰</button>
        <input type="file" id="file-input" accept="image/*" multiple>
        <button class="upload-btn" id="upload-btn" title="上传图片">↑</button>
    </div>
    
    <!-- 照片容器 -->
    <div class="photo-container" id="photo-container"></div>

    <script>
        document.addEventListener('DOMContentLoaded', function() {
            const sidebar = document.getElementById('sidebar');
            const toggleBtn = document.getElementById('toggle-btn');
            const fileInput = document.getElementById('file-input');
            const uploadBtn = document.getElementById('upload-btn');
            const photoContainer = document.getElementById('photo-container');
            
            toggleBtn.addEventListener('click', function() {
                sidebar.classList.toggle('open');
            });
            
            uploadBtn.addEventListener('click', function() {
                fileInput.click();
            });
            
            fileInput.addEventListener('change', function(e) {
                const files = e.target.files;
                for (let i = 0; i < files.length; i++) {
                    const file = files[i];
                    if (file) {
                        const reader = new FileReader();
                        reader.onload = function(event) {
                            addFloatingPhoto(event.target.result);
                        };
                        reader.readAsDataURL(file);
                    }
                }
                fileInput.value = '';
                sidebar.classList.remove('open');
            });
            
            function addFloatingPhoto(imageSrc) {
                const photoDiv = document.createElement('div');
                photoDiv.className = 'floating-photo';
                const img = document.createElement('img');
                img.src = imageSrc;
                img.alt = '';
                photoDiv.appendChild(img);
                photoContainer.appendChild(photoDiv);
                
                const maxX = window.innerWidth - 150;
                const maxY = window.innerHeight - 150;
                const randomX = Math.floor(Math.random() * maxX);
                const randomY = Math.floor(Math.random() * maxY);
                
                photoDiv.style.left = `${randomX}px`;
                photoDiv.style.top = `${randomY}px`;
                
                const randomDelay = Math.random() * 5;
                const randomDuration = 10 + Math.random() * 10;
                photoDiv.style.animationDelay = `${randomDelay}s`;
                photoDiv.style.animationDuration = `${randomDuration}s`;
                
                makeElementDraggable(photoDiv);
                
                photoDiv.addEventListener('dblclick', function() {
                    photoContainer.removeChild(photoDiv);
                });
            }
            
            function makeElementDraggable(element) {
                let pos1 = 0, pos2 = 0, pos3 = 0, pos4 = 0;
                element.onmousedown = dragMouseDown;
                
                function dragMouseDown(e) {
                    e.preventDefault();
                    pos3 = e.clientX;
                    pos4 = e.clientY;
                    document.onmouseup = closeDragElement;
                    document.onmousemove = elementDrag;
                    element.style.animationPlayState = 'paused';
                }
                
                function elementDrag(e) {
                    e.preventDefault();
                    pos1 = pos3 - e.clientX;
                    pos2 = pos4 - e.clientY;
                    pos3 = e.clientX;
                    pos4 = e.clientY;
                    element.style.top = (element.offsetTop - pos2) + "px";
                    element.style.left = (element.offsetLeft - pos1) + "px";
                }
                
                function closeDragElement() {
                    document.onmouseup = null;
                    document.onmousemove = null;
                    element.style.animationPlayState = 'running';
                }
            }
        });
    </script>
</body>
</html>
