# Neural Network Animation

```markdown
# Neural Network Animation

This project creates an interactive neural network animation using HTML5 Canvas and JavaScript. Nodes in the network move dynamically, pulsate, and react to the user's mouse movements, creating a visually appealing and interactive experience.

## Demo

![Neural Network Animation](path/to/demo-image.gif)

## Features

- Interactive nodes that move away from the cursor.
- Dynamic connections between nodes that vary in opacity based on distance.
- Nodes with gradient colors and pulsation effect.
- Smooth animation with friction applied to node movements.

## Requirements

- A modern web browser with HTML5 and JavaScript support.

## Usage

1. **Clone the repository**:

   ```bash
   git clone https://github.com/your-username/neural-network-animation.git
   ```

2. **Navigate to the project directory**:

   ```bash
   cd neural-network-animation
   ```

3. **Open `index.html` in your web browser**:

   You can simply open the `index.html` file in any modern web browser to view the animation.

## Code Explanation

### HTML

The HTML structure includes a canvas element where the animation will be rendered:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Neural Network Animation</title>
    <style>
        body, html {
            margin: 0;
            padding: 0;
            height: 100%;
            display: flex;
            justify-content: center;
            align-items: center;
            background: radial-gradient(circle, #1a1a1a, #000);
            overflow: hidden;
            font-family: Arial, sans-serif;
        }
        canvas {
            display: block;
            width: 100%;
            height: 100%;
        }
    </style>
</head>
<body>
    <canvas id="neuralNetCanvas"></canvas>
    <script src="script.js"></script>
</body>
</html>
```

### JavaScript

The JavaScript code, located in `script.js`, handles the creation, animation, and interaction of the nodes and connections.

1. **Setup Canvas**:

   ```javascript
   const canvas = document.getElementById('neuralNetCanvas');
   const ctx = canvas.getContext('2d');

   function resizeCanvas() {
       canvas.width = window.innerWidth;
       canvas.height = window.innerHeight;
   }

   window.addEventListener('resize', resizeCanvas);
   resizeCanvas();
   ```

2. **Create Nodes**:

   Nodes are created with random positions, speeds, and sizes. They also have a pulsation effect.

   ```javascript
   const nodes = [];
   const nodeCount = 100;

   function createNodes() {
       for (let i = 0; i < nodeCount; i++) {
           nodes.push({
               x: Math.random() * canvas.width,
               y: Math.random() * canvas.height,
               baseRadius: Math.random() * 3 + 2,
               radius: Math.random() * 3 + 2,
               speedX: (Math.random() - 0.5) * 2,
               speedY: (Math.random() - 0.5) * 2,
               pulseOffset: Math.random() * 2 * Math.PI
           });
       }
   }

   createNodes();
   ```

3. **Update and Draw Nodes**:

   Nodes move and react to the mouse position. They also pulsate and stay within the canvas bounds.

   ```javascript
   let mouse = { x: canvas.width / 2, y: canvas.height / 2 };

   canvas.addEventListener('mousemove', event => {
       const rect = canvas.getBoundingClientRect();
       mouse.x = event.clientX - rect.left;
       mouse.y = event.clientY - rect.top;
   });

   function updateNodes() {
       nodes.forEach(node => {
           const dx = node.x - mouse.x;
           const dy = node.y - mouse.y;
           const distance = Math.hypot(dx, dy);
           const maxMouseEffectDistance = 150;

           if (distance < maxMouseEffectDistance) {
               const angle = Math.atan2(dy, dx);
               const force = (maxMouseEffectDistance - distance) / maxMouseEffectDistance;
               node.speedX += Math.cos(angle) * force * 1;
               node.speedY += Math.sin(angle) * force * 1;
           }

           node.speedX *= 0.9;
           node.speedY *= 0.9;

           node.x += node.speedX;
           node.y += node.speedY;

           node.radius = node.baseRadius + Math.sin(node.pulseOffset) * 0.1;
           node.pulseOffset += 0.1;

           if (node.x < 0 || node.x > canvas.width) node.speedX *= -1;
           if (node.y < 0 || node.y > canvas.height) node.speedY *= -1;
       });
   }

   function drawNodes() {
       ctx.clearRect(0, 0, canvas.width, canvas.height);
       nodes.forEach(node => {
           ctx.beginPath();
           const gradient = ctx.createRadialGradient(node.x, node.y, 0, node.x, node.y, node.radius);
           gradient.addColorStop(0, '#0ff');
           gradient.addColorStop(1, '#00f');
           ctx.arc(node.x, node.y, node.radius, 0, Math.PI * 2);
           ctx.fillStyle = gradient;
           ctx.fill();
       });
   }
   ```

4. **Connect Nodes**:

   Connections between nodes are drawn based on their distance, with a fading effect.

   ```javascript
   const maxDistance = 150;

   function connectNodes() {
       ctx.shadowBlur = 10;
       ctx.shadowColor = '#fff';
       for (let i = 0; i < nodes.length; i++) {
           for (let j = i + 1; j < nodes.length; j++) {
               const distance = Math.hypot(nodes[i].x - nodes[j].x, nodes[i].y - nodes[j].y);
               if (distance < maxDistance) {
                   ctx.beginPath();
                   ctx.moveTo(nodes[i].x, nodes[i].y);
                   ctx.lineTo(nodes[j].x, nodes[j].y);
                   ctx.strokeStyle = `rgba(255, 255, 255, ${1 - distance / maxDistance})`;
                   ctx.lineWidth = 1;
                   ctx.stroke();
               }
           }
       }
       ctx.shadowBlur = 0;
   }
   ```

5. **Animation Loop**:

   The `animate` function updates and draws the nodes, and then requests the next animation frame.

   ```javascript
   function animate() {
       updateNodes();
       drawNodes();
       connectNodes();
       requestAnimationFrame(animate);
   }

   animate();
   ```

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

```