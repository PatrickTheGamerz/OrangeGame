<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Universal Dynamics Lab - Pro Edition</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/matter-js/0.19.0/matter.min.js"></script>
    <style>
        :root {
            --bg: #010103;
            --panel: rgba(6, 6, 10, 0.98);
            --accent: #00ff88;
            --math-blue: #00d2ff;
            --force-red: #ff3366;
            --border: rgba(255, 255, 255, 0.08);
            --text-dim: #94a3b8;
            --theory-gold: #ffcc00;
        }

        body {
            margin: 0; padding: 0;
            background: var(--bg);
            color: #f1f5f9;
            font-family: 'Inter', -apple-system, system-ui, sans-serif;
            overflow: hidden;
            display: flex;
            flex-direction: row;
            height: 100vh;
            width: 100vw;
        }

        /* Viewport */
        #viewport {
            flex-grow: 1;
            position: relative;
            background: radial-gradient(circle at 50% 50%, #0a0a1a 0%, #010103 100%);
            overflow: hidden;
            touch-action: none;
        }

        /* Sidebar */
        #sidebar {
            width: 420px;
            background: var(--panel);
            border-left: 1px solid var(--border);
            display: flex;
            flex-direction: column;
            padding: 0;
            box-sizing: border-box;
            overflow-y: auto;
            box-shadow: -15px 0 50px rgba(0,0,0,0.9);
            z-index: 100;
            transition: width 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }

        /* Responsive Scaling for Mobile */
        @media (max-width: 900px) {
            body { flex-direction: column; }
            #sidebar { 
                width: 100%; 
                height: 45%; 
                border-left: none; 
                border-top: 1px solid var(--border); 
            }
            #viewport { height: 55%; flex-grow: 0; }
            .header-main { padding: 15px 20px; }
            .section { padding: 15px 20px; }
            .stat-overlay { top: 15px !important; left: 15px !important; font-size: 8px !important; }
        }

        .header-main {
            padding: 24px;
            border-bottom: 1px solid var(--border);
            display: flex;
            flex-direction: column;
            gap: 15px;
            flex-shrink: 0;
        }

        .top-row {
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        h1 {
            font-size: 11px;
            letter-spacing: 4px;
            text-transform: uppercase;
            color: var(--accent);
            margin: 0;
            font-weight: 900;
        }

        .toggle-container {
            display: flex;
            background: #000;
            padding: 3px;
            border-radius: 8px;
            border: 1px solid var(--border);
            width: 100%;
        }

        .toggle-btn {
            flex: 1;
            padding: 8px;
            font-size: 9px;
            border-radius: 6px;
            cursor: pointer;
            text-transform: uppercase;
            text-align: center;
            transition: 0.3s;
            font-weight: 800;
            color: #555;
        }

        .toggle-btn.active { background: #1a1a1a; color: var(--accent); }
        .toggle-btn.theory-active { color: var(--theory-gold); }

        .section {
            padding: 20px 24px;
            border-bottom: 1px solid var(--border);
            position: relative;
        }

        .section-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 15px;
        }

        .section-title {
            font-size: 9px;
            text-transform: uppercase;
            letter-spacing: 2px;
            font-weight: 800;
            color: var(--text-dim);
        }

        /* Tooltip & Theory Blocks */
        .info-icon {
            width: 18px;
            height: 18px;
            background: rgba(255,255,255,0.05);
            border: 1px solid var(--border);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 9px;
            font-weight: bold;
            cursor: help;
            color: var(--accent);
        }

        .tooltip-box {
            position: absolute;
            top: 50px;
            right: 24px;
            width: 280px;
            background: #050508;
            border: 1px solid var(--accent);
            padding: 16px;
            border-radius: 8px;
            font-size: 11px;
            line-height: 1.6;
            display: none;
            z-index: 2000;
            box-shadow: 0 20px 40px rgba(0,0,0,1);
        }

        .theory-block {
            display: none;
            background: rgba(255, 204, 0, 0.03);
            border-left: 2px solid var(--theory-gold);
            padding: 12px;
            border-radius: 0 4px 4px 0;
            font-size: 11px;
            line-height: 1.5;
            color: #ddd;
        }

        .theory-block b { color: var(--theory-gold); }

        /* Normal UI Controls */
        .control-block { display: block; }
        .control-row { margin-bottom: 14px; }
        .label-val { display: flex; justify-content: space-between; font-size: 11px; margin-bottom: 6px; }
        .unit { font-family: 'JetBrains Mono', monospace; color: var(--accent); font-weight: bold; }
        input[type=range] { width: 100%; accent-color: var(--accent); height: 4px; cursor: pointer; background: rgba(255,255,255,0.1); border-radius: 2px; }

        .btn-group { display: grid; grid-template-columns: repeat(3, 1fr); gap: 6px; margin-top: 10px; }
        button {
            background: rgba(255,255,255,0.03);
            border: 1px solid var(--border);
            color: white;
            padding: 10px;
            border-radius: 4px;
            font-size: 9px;
            text-transform: uppercase;
            font-weight: 700;
            cursor: pointer;
            transition: 0.2s;
            -webkit-tap-highlight-color: transparent;
        }
        button:hover { background: rgba(0, 255, 136, 0.1); border-color: var(--accent); }
        button:active { transform: scale(0.96); }
        button.active { background: var(--accent); color: #000; }

        /* View Mode Switch */
        .view-mode-pill {
            display: flex;
            background: #000;
            border-radius: 20px;
            padding: 2px;
            border: 1px solid var(--border);
            margin-bottom: 10px;
        }
        .view-mode-btn {
            flex: 1;
            padding: 6px;
            font-size: 9px;
            border-radius: 18px;
            text-align: center;
            cursor: pointer;
            text-transform: uppercase;
            font-weight: 800;
            color: #444;
        }
        .view-mode-btn.active { background: #111; color: var(--accent); }

        /* Analysis */
        #inspector-content {
            background: rgba(0,0,0,0.4);
            border-radius: 8px;
            padding: 15px;
            font-size: 11px;
            border: 1px dashed var(--border);
        }
        .eq-display {
            background: #000;
            padding: 10px;
            border-radius: 6px;
            margin-top: 10px;
            font-family: 'JetBrains Mono', monospace;
            color: var(--math-blue);
            font-size: 12px;
            border-left: 3px solid var(--accent);
        }

        .stat-overlay {
            position: absolute;
            top: 25px;
            left: 25px;
            font-size: 10px;
            color: var(--text-dim);
            pointer-events: none;
            display: flex;
            flex-direction: column;
            gap: 4px;
            text-shadow: 0 2px 4px rgba(0,0,0,0.8);
        }
    </style>
</head>
<body>

    <div id="viewport">
        <div class="stat-overlay">
            <div>CHAMBER: <span class="unit">ATMOSPHERIC SEAL</span></div>
            <div>RESOLUTION: <span class="unit">1px = 2cm</span></div>
            <div id="sim-info">ENVIRONMENT: <span class="unit">VACUUM</span></div>
        </div>
    </div>

    <div id="sidebar">
        <div class="header-main">
            <div class="top-row">
                <h1>Dynamics Lab</h1>
                <div class="view-mode-pill" style="width: 140px; margin-bottom: 0;">
                    <div class="view-mode-btn active" id="vm-phys" onclick="setViewMode('physical')">Phys</div>
                    <div class="view-mode-btn" id="vm-math" onclick="setViewMode('mathematical')">Math</div>
                </div>
            </div>
            
            <div class="toggle-container">
                <div class="toggle-btn active" id="gui-normal" onclick="setInterfaceMode('normal')">Controls</div>
                <div class="toggle-btn" id="gui-theory" onclick="setInterfaceMode('theory')">Explanation</div>
            </div>
        </div>

        <!-- 01. MASS -->
        <div class="section">
            <div class="section-header">
                <span class="section-title">01. Inertial Mass</span>
                <div class="info-icon" onmouseover="toggleInfo('m-tip', true)" onmouseout="toggleInfo('m-tip', false)">!</div>
            </div>
            <div class="tooltip-box" id="m-tip">
                Mass (m) measures inertia. Higher mass resists acceleration more strongly.
            </div>
            
            <div class="control-block" id="m-ctrl">
                <div class="control-row">
                    <div class="label-val"><span>Specimen Mass</span> <span class="unit" id="val-mass">10 kg</span></div>
                    <input type="range" id="input-mass" min="1" max="1000" step="1" value="10">
                </div>
                <div class="btn-group">
                    <button onclick="setTool('box')" id="t-box">Cube</button>
                    <button onclick="setTool('ball')" id="t-ball">Ball</button>
                    <button onclick="setTool(null)" id="t-none" class="active">Point</button>
                </div>
            </div>
            
            <div class="theory-block" id="m-theo">
                <b>Mass (m)</b>: A scalar property representing the amount of matter. It determines how much an object resists changes in motion. 
                Formula: m = F / a. <br><br>
                <b>Inertia</b>: The tendency of an object to remain at rest or in uniform motion.
            </div>
        </div>

        <!-- 02. FORCE -->
        <div class="section">
            <div class="section-header">
                <span class="section-title">02. Force Vectors</span>
                <div class="info-icon" onmouseover="toggleInfo('f-tip', true)" onmouseout="toggleInfo('f-tip', false)">!</div>
            </div>
            <div class="tooltip-box" id="f-tip">
                Forces are vectors (magnitude and direction). ΣF is the sum of all vectors.
            </div>

            <div class="control-block" id="f-ctrl">
                <div class="control-row">
                    <div class="label-val"><span>Gravity (g)</span> <span class="unit" id="val-g">9.81 m/s²</span></div>
                    <input type="range" id="input-g" min="-20" max="40" step="0.01" value="9.81">
                </div>
                <button onclick="clearWorld()" style="width:100%; border-color: var(--force-red); color: var(--force-red);">Purge Chamber</button>
            </div>

            <div class="theory-block" id="f-theo">
                <b>Force (F)</b>: An interaction that changes the motion of an object. Measured in Newtons (N). <br><br>
                <b>Resultant Force (ΣF)</b>: The vector sum of all forces (Gravity + Drag + User Input). <br><br>
                <b>Newton's 1st Law</b>: Objects maintain velocity unless ΣF is non-zero.
            </div>
        </div>

        <!-- 03. KINETICS -->
        <div class="section">
            <div class="section-header">
                <span class="section-title">03. Kinetics & Friction</span>
                <div class="info-icon" onmouseover="toggleInfo('k-tip', true)" onmouseout="toggleInfo('k-tip', false)">!</div>
            </div>
            <div class="tooltip-box" id="k-tip">
                Kinetics deals with objects in motion. Drag simulates air or fluid resistance.
            </div>

            <div class="control-block" id="k-ctrl">
                <div class="control-row">
                    <div class="label-val"><span>Flow Speed</span> <span class="unit" id="val-time">1.0x</span></div>
                    <input type="range" id="input-time" min="0" max="2" step="0.05" value="1.0">
                </div>
                <div class="control-row">
                    <div class="label-val"><span>Air Viscosity</span> <span class="unit" id="val-drag">0.01</span></div>
                    <input type="range" id="input-drag" min="0" max="1" step="0.01" value="0.01">
                </div>
            </div>

            <div class="theory-block" id="k-theo">
                <b>Velocity (v)</b>: Displacement over time (m/s). <br><br>
                <b>Acceleration (a)</b>: Rate of change of velocity. <br><br>
                <b>Viscosity (b)</b>: The 'thickness' of the medium. Generates Drag Force: Fd = -b * v.
            </div>
        </div>

        <!-- ANALYSIS -->
        <div class="section" style="border-bottom: none;">
            <div class="section-header">
                <span class="section-title">Live Analysis</span>
            </div>
            <div id="inspector-content">
                <p style="opacity: 0.5">Select a specimen...</p>
            </div>
        </div>
    </div>

    <script>
        const { Engine, Render, Runner, Bodies, Composite, Mouse, MouseConstraint, Vector, Body, Events } = Matter;

        // Configuration
        const PX_TO_M = 0.02; // 50px = 1m
        const FPS = 60;

        const viewport = document.getElementById('viewport');
        const engine = Engine.create();
        const world = engine.world;
        
        const render = Render.create({
            element: viewport,
            engine: engine,
            options: {
                width: viewport.clientWidth,
                height: viewport.clientHeight,
                wireframes: false,
                background: 'transparent',
                pixelRatio: window.devicePixelRatio || 1
            }
        });

        Render.run(render);
        const runner = Runner.create();
        Runner.run(runner, engine);

        // --- CHAMBER WALLS ---
        let boundaries = [];
        function buildChamber() {
            Composite.remove(world, boundaries);
            const w = viewport.clientWidth;
            const h = viewport.clientHeight;
            const t = 200; // Thicker walls for higher realism/stability
            const wallProps = { 
                isStatic: true, 
                render: { fillStyle: '#050505' }, 
                friction: 0.3, 
                restitution: 0.4,
                label: 'Wall'
            };

            boundaries = [
                Bodies.rectangle(w/2, h + t/2 - 10, w + 1000, t, wallProps), // Floor
                Bodies.rectangle(w/2, -t/2 + 10, w + 1000, t, wallProps),   // Ceiling
                Bodies.rectangle(-t/2 + 10, h/2, t, h + 1000, wallProps),   // Left
                Bodies.rectangle(w + t/2 - 10, h/2, t, h + 1000, wallProps) // Right
            ];
            Composite.add(world, boundaries);
        }
        buildChamber();

        // Interaction
        const mouse = Mouse.create(render.canvas);
        const mouseConstraint = MouseConstraint.create(engine, {
            mouse: mouse,
            constraint: { stiffness: 0.1, render: { visible: false } }
        });
        Composite.add(world, mouseConstraint);

        // App State
        let currentTool = null;
        let selectedBody = null;
        let viewMode = 'physical';
        let interfaceMode = 'normal';

        function setInterfaceMode(mode) {
            interfaceMode = mode;
            document.querySelectorAll('.toggle-btn').forEach(b => b.classList.remove('active', 'theory-active'));
            const activeBtn = document.getElementById('gui-' + mode);
            activeBtn.classList.add('active');
            if (mode === 'theory') activeBtn.classList.add('theory-active');

            const controls = document.querySelectorAll('.control-block');
            const theory = document.querySelectorAll('.theory-block');

            if (mode === 'normal') {
                controls.forEach(c => c.style.display = 'block');
                theory.forEach(t => t.style.display = 'none');
            } else {
                controls.forEach(c => c.style.display = 'none');
                theory.forEach(t => t.style.display = 'block');
            }
        }

        function setViewMode(mode) {
            viewMode = mode;
            document.querySelectorAll('.view-mode-btn').forEach(b => b.classList.remove('active'));
            document.getElementById('vm-' + (mode === 'physical' ? 'phys' : 'math')).classList.add('active');
            
            render.options.wireframes = (mode === 'mathematical');
            Composite.allBodies(world).forEach(b => {
                if(!b.isStatic) {
                    b.render.opacity = (mode === 'mathematical' ? 0.2 : 1);
                }
            });
        }

        function setTool(t) {
            currentTool = t;
            document.querySelectorAll('.btn-group button').forEach(b => b.classList.remove('active'));
            const id = t ? 't-' + t : 't-none';
            document.getElementById(id).classList.add('active');
        }

        function toggleInfo(id, show) { document.getElementById(id).style.display = show ? 'block' : 'none'; }

        function clearWorld() {
            const statics = Composite.allBodies(world).filter(b => b.isStatic);
            Composite.clear(world, false, true);
            Composite.add(world, [...statics, mouseConstraint]);
            selectedBody = null;
        }

        // Handle Spawning
        viewport.addEventListener('mousedown', (e) => {
            if (mouseConstraint.body) {
                selectedBody = mouseConstraint.body;
                return;
            }
            if (!currentTool) return;

            const pos = mouse.position;
            const m = parseFloat(document.getElementById('input-mass').value);
            const col = `hsl(${Math.random() * 360}, 65%, 50%)`;
            
            const opt = { 
                mass: m, 
                frictionAir: parseFloat(document.getElementById('input-drag').value),
                restitution: 0.5,
                friction: 0.2,
                render: { 
                    fillStyle: col, 
                    strokeStyle: '#ffffff', 
                    lineWidth: 2,
                    opacity: viewMode === 'mathematical' ? 0.2 : 1
                } 
            };

            let b;
            if (currentTool === 'box') b = Bodies.rectangle(pos.x, pos.y, 44, 44, opt);
            if (currentTool === 'ball') b = Bodies.circle(pos.x, pos.y, 22, opt);

            if (b) {
                Composite.add(world, b);
                selectedBody = b;
            }
        });

        // Frame Logic
        Events.on(render, 'afterRender', () => {
            const ctx = render.context;
            const bodies = Composite.allBodies(world);
            const ratio = window.devicePixelRatio || 1;
            
            const gVal = parseFloat(document.getElementById('input-g').value);
            world.gravity.y = gVal / 9.81;
            engine.timing.timeScale = parseFloat(document.getElementById('input-time').value);

            bodies.forEach(body => {
                if (body.isStatic) return;

                const pos = body.position;
                const weight = body.mass * (world.gravity.y * 9.81);
                const airDrag = Vector.mult(body.velocity, -body.frictionAir * 50);
                
                let mouseForce = { x: 0, y: 0 };
                if (mouseConstraint.body === body) {
                    const delta = Vector.sub(mouse.position, body.position);
                    mouseForce = Vector.mult(delta, 0.1 * body.mass);
                }

                const ΣF = Vector.add(Vector.add({ x: 0, y: weight }, airDrag), mouseForce);

                // Draw Vectors with HiDPI support
                if (viewMode === 'mathematical' || body === selectedBody) {
                    drawVector(ctx, pos, ΣF, 0.4, 'var(--force-red)', 'ΣF');
                    if (viewMode === 'mathematical') {
                        drawVector(ctx, pos, {x: ΣF.x, y:0}, 0.4, 'rgba(255,51,102,0.4)', 'ΣFx');
                        drawVector(ctx, pos, {x: 0, y:ΣF.y}, 0.4, 'rgba(255,51,102,0.4)', 'ΣFy');
                    }
                    drawVector(ctx, pos, body.velocity, 8, 'var(--math-blue)', 'v');
                }

                // Analysis Panel
                if (body === selectedBody) {
                    const v = Vector.magnitude(body.velocity) * PX_TO_M * FPS;
                    const f = Vector.magnitude(ΣF);
                    const a = f / body.mass;

                    document.getElementById('inspector-content').innerHTML = `
                        <div style="margin-bottom:8px">
                            <span class="unit">VECTORS</span><br>
                            Velocity: ${v.toFixed(2)} m/s<br>
                            Acceleration: ${a.toFixed(2)} m/s²<br>
                            Net Force: ${f.toFixed(1)} N
                        </div>
                        <div class="eq-display">
                            a = F / m<br>
                            ${a.toFixed(2)} = ${f.toFixed(1)} / ${body.mass}
                        </div>
                    `;
                }
            });

            // UI Label Sync
            document.getElementById('val-mass').innerText = document.getElementById('input-mass').value + " kg";
            document.getElementById('val-g').innerText = gVal.toFixed(2) + " m/s²";
            document.getElementById('val-time').innerText = engine.timing.timeScale.toFixed(2) + "x";
            document.getElementById('val-drag').innerText = document.getElementById('input-drag').value;
            
            const b = parseFloat(document.getElementById('input-drag').value);
            document.getElementById('sim-info').innerHTML = b > 0.1 ? "ENVIRONMENT: <span style='color:#ffaa00'>FLUID DYNAMICS</span>" : "ENVIRONMENT: <span class='unit'>NEAR-VACUUM</span>";
        });

        function drawVector(ctx, start, vec, scale, col, label) {
            const m = Vector.magnitude(vec);
            if (m < 0.5) return;

            const end = { x: start.x + vec.x * scale, y: start.y + vec.y * scale };

            ctx.save();
            ctx.beginPath();
            ctx.strokeStyle = col;
            ctx.lineWidth = (label === 'ΣF' || label === 'v') ? 2.5 : 1.2;
            ctx.setLineDash((label.includes('x') || label.includes('y')) ? [4, 4] : []);
            ctx.moveTo(start.x, start.y);
            ctx.lineTo(end.x, end.y);
            ctx.stroke();
            ctx.setLineDash([]);

            // Arrow
            const ang = Math.atan2(vec.y, vec.x);
            ctx.beginPath();
            ctx.fillStyle = col;
            ctx.moveTo(end.x, end.y);
            ctx.lineTo(end.x - 7 * Math.cos(ang - 0.45), end.y - 7 * Math.sin(ang - 0.45));
            ctx.lineTo(end.x - 7 * Math.cos(ang + 0.45), end.y - 7 * Math.sin(ang + 0.45));
            ctx.fill();

            if (viewMode === 'mathematical') {
                ctx.shadowColor = 'rgba(0,0,0,0.8)';
                ctx.shadowBlur = 4;
                ctx.font = 'bold 11px JetBrains Mono, monospace';
                ctx.fillStyle = col;
                ctx.fillText(label, end.x + 8, end.y + 4);
            }
            ctx.restore();
        }

        // Debounced Resize
        let resizeTimer;
        window.addEventListener('resize', () => {
            clearTimeout(resizeTimer);
            resizeTimer = setTimeout(() => {
                render.canvas.width = viewport.clientWidth;
                render.canvas.height = viewport.clientHeight;
                render.options.width = viewport.clientWidth;
                render.options.height = viewport.clientHeight;
                buildChamber();
            }, 100);
        });

    </script>
</body>
</html>
