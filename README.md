# pakatkat
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Graphing Calculator</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/mathjs/10.6.4/math.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js@3.7.1"></script>
    <style>
        body { font-family: Arial, sans-serif; text-align: center; background: #f4f4f4; }
        .draggable { position: absolute; top: 20px; left: 20px; padding: 10px; background: white; border-radius: 5px; box-shadow: 2px 2px 10px rgba(0,0,0,0.1); cursor: move; }
        canvas { background: white; }
    </style>
</head>
<body>
    <div class="draggable" id="inputPanel">
        <label>Enter Function (e.g., x^2): </label>
        <input type="text" id="equation" value="x^2">
        <button onclick="plotGraph()">Plot</button>
    </div>
    <canvas id="graphCanvas" width="800" height="500"></canvas>
    
    <script>
        let ctx = document.getElementById('graphCanvas').getContext('2d');
        let chart;

        function plotGraph() {
            let expr = document.getElementById("equation").value;
            let xValues = [], yValues = [];
            
            for (let x = -10; x <= 10; x += 0.1) {
                xValues.push(x);
                try {
                    yValues.push(math.evaluate(expr, { x }));
                } catch (err) {
                    console.error("Math Evaluation Error:", err);
                    yValues.push(null);
                }
            }
            
            if (chart) chart.destroy();
            chart = new Chart(ctx, {
                type: 'line',
                data: {
                    labels: xValues,
                    datasets: [{ label: expr, data: yValues, borderColor: 'blue', borderWidth: 2, fill: false }]
                },
                options: { scales: { x: { type: 'linear', position: 'bottom' } } }
            });
        }

        // Draggable Panel
        let panel = document.getElementById("inputPanel");
        panel.onmousedown = function(event) {
            let shiftX = event.clientX - panel.getBoundingClientRect().left;
            let shiftY = event.clientY - panel.getBoundingClientRect().top;
            function moveAt(pageX, pageY) {
                panel.style.left = pageX - shiftX + 'px';
                panel.style.top = pageY - shiftY + 'px';
            }
            function onMouseMove(event) { moveAt(event.pageX, event.pageY); }
            document.addEventListener('mousemove', onMouseMove);
            document.onmouseup = function() { document.removeEventListener('mousemove', onMouseMove); document.onmouseup = null; };
        };
        panel.ondragstart = function() { return false; };
    </script>
</body>
</html>
