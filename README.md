# LE-VOYAGE-DANS-LE-MONDE
IL SAGIT D UNE PERSONAGE QUI VOYAGE A TRAVERS LE MONDE



VOICI LE LIEN DU JEU
https://es-d-89840578520261002-01a0f121-4fcc-7e56-ad74-23473c52f39d.codepen.dev/
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Air Force Cash: Donald's World Tour</title>
    <style>
        :root {
            --gold: #ffd700;
            --dark-blue: #0b1d3a;
            --light-blue: #1e3a8a;
            --bg-gray: #121212;
            --panel-bg: #1e293b;
            --text-light: #f8fafc;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background-color: var(--bg-gray);
            color: var(--text-light);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            padding: 20px;
        }

        #game-container {
            width: 100%;
            max-width: 900px;
            background-color: var(--dark-blue);
            border: 4px solid var(--gold);
            border-radius: 12px;
            box-shadow: 0 0 25px rgba(255, 215, 0, 0.3);
            overflow: hidden;
            display: flex;
            flex-direction: column;
        }

        header {
            background-color: #050e1a;
            padding: 20px;
            text-align: center;
            border-bottom: 2px solid var(--gold);
        }

        header h1 {
            color: var(--gold);
            font-size: 1.8rem;
            letter-spacing: 1px;
            text-transform: uppercase;
        }

        #stats-bar {
            display: flex;
            justify-content: space-around;
            background-color: var(--light-blue);
            padding: 15px;
            font-weight: bold;
            font-size: 1.1rem;
            border-bottom: 2px solid var(--gold);
        }

        .stat-item {
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .stat-value {
            color: var(--gold);
        }

        main {
            padding: 25px;
            display: flex;
            flex-direction: column;
            gap: 20px;
            min-height: 400px;
        }

        .level-card {
            background-color: var(--panel-bg);
            border-radius: 8px;
            padding: 20px;
            border-left: 5px solid var(--gold);
        }

        .level-title {
            color: var(--gold);
            font-size: 1.4rem;
            margin-bottom: 10px;
        }

        .level-description {
            line-height: 1.5;
            margin-bottom: 15px;
            color: #cbd5e1;
        }

        .actions-container {
            display: flex;
            flex-direction: column;
            gap: 12px;
        }

        .action-btn {
            background-color: #0f172a;
            color: white;
            border: 1px solid var(--gold);
            padding: 14px;
            border-radius: 6px;
            cursor: pointer;
            font-size: 1rem;
            text-align: left;
            transition: all 0.2s ease;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .action-btn:hover:not(:disabled) {
            background-color: var(--gold);
            color: var(--dark-blue);
            font-weight: bold;
        }

        .action-btn:disabled {
            opacity: 0.5;
            cursor: not-allowed;
            border-color: #475569;
        }

        .cost-tag {
            background-color: rgba(255, 215, 0, 0.2);
            padding: 4px 8px;
            border-radius: 4px;
            font-size: 0.9rem;
            color: var(--gold);
        }

        .action-btn:hover:not(:disabled) .cost-tag {
            background-color: var(--dark-blue);
            color: var(--gold);
        }

        #log-panel {
            background-color: #020617;
            border: 1px solid #334155;
            border-radius: 6px;
            padding: 15px;
            height: 120px;
            overflow-y: auto;
            font-family: monospace;
            font-size: 0.9rem;
            color: #4ade80;
        }

        .log-entry {
            margin-bottom: 5px;
        }

        .log-error {
            color: #f87171;
        }

        .log-success {
            color: #67e8f9;
        }

        footer {
            background-color: #050e1a;
            padding: 15px;
            text-align: center;
            font-size: 0.85rem;
            color: #64748b;
            border-top: 1px solid #1e293b;
        }
    </style>
</head>
<body>

    <div id="game-container">
        <header>
            <h1>Air Force Cash : World Tour</h1>
        </header>

        <div id="stats-bar">
            <div class="stat-item">Trésorerie: <span id="cash-display" class="stat-value">$20,000,000</span></div>
            <div class="stat-item">Carburant: <span id="fuel-display" class="stat-value">100%</span></div>
            <div class="stat-item">Niveau: <span id="level-display" class="stat-value">1 / 5</span></div>
        </div>

        <main>
            <div class="level-card">
                <h2 id="level-title" class="level-title">Chargement...</h2>
                <p id="level-description" class="level-description">Veuillez patienter...</p>
            </div>

            <div id="actions-list" class="actions-container">
                <!-- Les actions dynamiques s'afficheront ici -->
            </div>

            <div id="log-panel">
                <div class="log-entry">Bienvenue à bord d'Air Force One, Monsieur le Président. Choisis tes investissements avec soin.</div>
            </div>
        </main>

        <footer>
            Air Force Cash - Simulation & Governance Engine
        </footer>
    </div>

    <script>
        // État initial du jeu
        const gameState = {
            cash: 20000000,
            fuel: 100,
            currentLevelIndex: 0,
            completedTasks: new Set()
        };

        // Données des niveaux
        const levels = [
            {
                id: 1,
                title: "Niveau 1 : L'Escale Parisienne (France)",
                description: "Pour obtenir le droit de survol de l'Espace Schengen et privatiser une soirée au Bourget, vous devez régler les taxes aéroportuaires et embaucher du personnel d'élite.",
                tasks: [
                    {
                        id: "l1_t1",
                        text: "Payer la taxe d'atterrissage VIP au gouvernement français",
                        cost: 500000,
                        fuelCost: 10,
                        logMsg: "Taxe acquittée. Espace aérien débloqué !"
                    },
                    {
                        id: "l1_t2",
                        text: "Engager un chef étoilé pour le banquet diplomatique",
                        cost: 100000,
                        fuelCost: 0,
                        logMsg: "Le chef étoilé a préparé un repas d'exception. Diplomates conquis."
                    }
                ]
            },
            {
                id: 2,
                title: "Niveau 2 : Le Deal des Pyramides (Égypte)",
                description: "Vous souhaitez obtenir une autorisation spéciale pour survoler les Pyramides de Gizeh à basse altitude. Les autorités locales demandent une contribution aux infrastructures.",
                tasks: [
                    {
                        id: "l2_t1",
                        text: "Financer la rénovation du tarmac de l'aéroport militaire du Caire",
                        cost: 1200000,
                        fuelCost: 15,
                        logMsg: "Tarmac financé. Piste privée mise à disposition immédiatement."
                    },
                    {
                        id: "l2_t2",
                        text: "Remettre la prime d'escorte en Cash à l'équipe de sécurité locale",
                        cost: 200000,
                        fuelCost: 0,
                        logMsg: "Escorte terrestre et aérienne garantie autour d'Air Force One."
                    }
                ]
            },
            {
                id: 3,
                title: "Niveau 3 : La Négociation High-Tech (Japon)",
                description: "Air Force One a besoin de réacteurs expérimentaux produits à Tokyo pour couvrir les distances restantes sans escale technique supplémentaire.",
                tasks: [
                    {
                        id: "l3_t1",
                        text: "Acheter les brevets d'ingénierie au consortium de Tokyo",
                        cost: 3500000,
                        fuelCost: 20,
                        logMsg: "Brevets acquis. Les technologies de pointe sont transférées à bord."
                    },
                    {
                        id: "l3_t2",
                        text: "Verser des primes d'accélération aux techniciens de piste",
                        cost: 500000,
                        fuelCost: 0,
                        logMsg: "Réacteurs installés en temps record !"
                    }
                ]
            },
            {
                id: 4,
                title: "Niveau 4 : Le Sommet d'Émeraude (Brésil)",
                description: "Une escale au Brésil nécessite de sécuriser une piste privée au milieu de la jungle et d'assurer le ravitaillement complet de l'appareil.",
                tasks: [
                    {
                        id: "l4_t1",
                        text: "Financer le balisage de nuit de la piste isolée",
                        cost: 2000000,
                        fuelCost: 25,
                        logMsg: "Piste entièrement éclairée et sécurisée."
                    },
                    {
                        id: "l4_t2",
                        text: "Payer la logistique terrestre et la sécurité privée",
                        cost: 800000,
                        fuelCost: 0,
                        logMsg: "Convoyeur et sécurité VIP opérationnels."
                    }
                ]
            },
            {
                id: 5,
                title: "Niveau 5 : Le Grand Final à Dubaï (Émirats Arabes Unis)",
                description: "Dernière étape du World Tour. Vous devez remporter les enchères pour la priorité absolue d'atterrissage et privatiser le sommet international.",
                tasks: [
                    {
                        id: "l5_t1",
                        text: "Régler l'enchère finale pour la priorité d'atterrissage royale",
                        cost: 10000000,
                        fuelCost: 30,
                        logMsg: "Enchère remportée ! Air Force One atterrit sur la piste dorée."
                    },
                    {
                        id: "l5_t2",
                        text: "Rétribuer le réseau d'intermédiaires diplomatiques",
                        cost: 1500000,
                        fuelCost: 0,
                        logMsg: "Accords internationaux signés. Le World Tour est un succès absolu !"
                    }
                ]
            }
        ];

        // Formatage de la monnaie
        function formatCash(amount) {
            return '$' + amount.toLocaleString('en-US');
        }

        // Mise à jour de l'interface graphique
        function updateUI() {
            document.getElementById('cash-display').textContent = formatCash(gameState.cash);
            document.getElementById('fuel-display').textContent = gameState.fuel + '%';
            document.getElementById('level-display').textContent = `${gameState.currentLevelIndex + 1} / ${levels.length}`;

            const currentLevel = levels[gameState.currentLevelIndex];
            document.getElementById('level-title').textContent = currentLevel.title;
            document.getElementById('level-description').textContent = currentLevel.description;

            const actionsContainer = document.getElementById('actions-list');
            actionsContainer.innerHTML = '';

            // Génération des boutons d'action du niveau actuel
            let levelCompleted = true;

            currentLevel.tasks.forEach(task => {
                const isDone = gameState.completedTasks.has(task.id);
                if (!isDone) levelCompleted = false;

                const btn = document.createElement('button');
                btn.className = 'action-btn';
                btn.disabled = isDone || gameState.cash < task.cost || gameState.fuel < task.fuelCost;

                const labelSpan = document.createElement('span');
                labelSpan.textContent = isDone ? `✓ ${task.text}` : task.text;

                const costSpan = document.createElement('span');
                costSpan.className = 'cost-tag';
                
                let details = formatCash(task.cost);
                if (task.fuelCost > 0) details += ` | -${task.fuelCost}% Kérozène`;
                costSpan.textContent = isDone ? 'Payé' : details;

                btn.appendChild(labelSpan);
                btn.appendChild(costSpan);

                btn.addEventListener('click', () => executeTask(task));
                actionsContainer.appendChild(btn);
            });

            // Bouton de passage au niveau suivant si toutes les tâches sont accomplies
            if (levelCompleted) {
                const nextBtn = document.createElement('button');
                nextBtn.className = 'action-btn';
                nextBtn.style.borderColor = '#4ade80';
                nextBtn.style.color = '#4ade80';

                if (gameState.currentLevelIndex < levels.length - 1) {
                    nextBtn.innerHTML = '<span><strong>Décoller vers la destination suivante</strong></span><span class="cost-tag">Vol Air Force One</span>';
                    nextBtn.addEventListener('click', () => {
                        gameState.currentLevelIndex++;
                        addLog(`Décollage vers le Niveau ${gameState.currentLevelIndex + 1} !`, 'log-success');
                        updateUI();
                    });
                } else {
                    nextBtn.innerHTML = '<span><strong>VICTOIRE ! Le World Tour est accompli !</strong></span><span class="cost-tag">Bravo</span>';
                    nextBtn.addEventListener('click', () => {
                        alert("Félicitations ! Vous avez réalisé le Tour du Monde le plus rentable et prestigieux de l'histoire !");
                    });
                }
                actionsContainer.appendChild(nextBtn);
            }
        }

        // Exécution d'une action
        function executeTask(task) {
            if (gameState.cash >= task.cost && gameState.fuel >= task.fuelCost) {
                gameState.cash -= task.cost;
                gameState.fuel -= task.fuelCost;
                gameState.completedTasks.add(task.id);
                addLog(task.logMsg, 'log-success');
                updateUI();
            } else {
                addLog("Fonds ou carburant insuffisants pour réaliser cette transaction !", 'log-error');
            }
        }

        // Ajout d'une ligne de journal
        function addLog(message, className = '') {
            const logPanel = document.getElementById('log-panel');
            const entry = document.createElement('div');
            entry.className = `log-entry ${className}`;
            entry.textContent = `> ${message}`;
            logPanel.appendChild(entry);
            logPanel.scrollTop = logPanel.scrollHeight;
        }

        // Initialisation au chargement
        updateUI();
    </script>
</body>
</html>
