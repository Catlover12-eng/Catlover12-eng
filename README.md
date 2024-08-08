<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Reality Show Simulator</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div class="container">
        <h1>Reality Show Simulator</h1>

        <!-- Setup Page -->
        <div id="setup-page">
            <h2>Setup Contestants</h2>
            <label for="num-contestants">Number of Contestants:</label>
            <input type="number" id="num-contestants" min="2">
            <button onclick="setContestants()">Set Number of Contestants</button>
            
            <div id="contestants-input" style="display:none;">
                <h3>Enter Contestant Names:</h3>
                <div id="contestants-list"></div>
                <button onclick="submitNames()">Submit Names</button>
            </div>

            <div id="teams-input" style="display:none;">
                <h3>Assign Contestants to Teams:</h3>
                <div id="teams-list"></div>
                <button onclick="submitTeams()">Submit Teams</button>
            </div>
        </div>

        <!-- Challenge Page -->
        <div id="challenge-page" style="display:none;">
            <h2>Challenge Details</h2>
            <textarea id="challenge-details" placeholder="Describe the challenge..."></textarea>
            <h3>Challenge Outcome:</h3>
            <label for="winning-team">Winning Team:</label>
            <select id="winning-team"></select>
            <label for="losing-team">Losing Team:</label>
            <select id="losing-team"></select>
            <button onclick="submitChallenge()">Submit Challenge Outcome</button>

            <div id="elimination-section" style="display:none;">
                <h3>Vote to Eliminate:</h3>
                <label for="eliminated-player">Eliminate Player:</label>
                <select id="eliminated-player"></select>
                <button onclick="submitElimination()">Submit Elimination</button>
            </div>

            <div id="confessions-section" style="display:none;">
                <h3>Confessions:</h3>
                <div id="confessions-list"></div>
                <button onclick="submitConfessions()">Submit Confessions</button>
            </div>
        </div>

        <!-- Results Page -->
        <div id="results-page" style="display:none;">
            <h2>Round Summary</h2>
            <div id="round-summary"></div>
            <button onclick="startNextRound()">Start Next Round</button>
        </div>

        <!-- Final Page -->
        <div id="final-page" style="display:none;">
            <h2>Final Winner</h2>
            <div id="final-winner"></div>
            <button onclick="startNewGame()">Start a New Game</button>
        </div>
    </div>

    <script src="script.js"></script>
</body>
</html>body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    background-color: #f4f4f4;
}

.container {
    width: 80%;
    max-width: 800px;
    background: #fff;
    padding: 20px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

h1 {
    text-align: center;
}

button {
    margin-top: 10px;
    padding: 10px 15px;
    background-color: #007bff;
    color: #fff;
    border: none;
    border-radius: 5px;
    cursor: pointer;
}

button:hover {
    background-color: #0056b3;
}

textarea {
    width: 100%;
    height: 100px;
    margin-top: 10px;
    padding: 10px;
    border-radius: 5px;
    border: 1px solid #ddd;
}
let contestants = [];
let teams = { team1: [], team2: [] };
let currentChallenge = '';
let currentRound = 0;

function setContestants() {
    const numContestants = document.getElementById('num-contestants').value;
    contestants = [];
    document.getElementById('contestants-list').innerHTML = '';
    for (let i = 0; i < numContestants; i++) {
        const input = document.createElement('input');
        input.type = 'text';
        input.id = 'contestant-' + i;
        input.placeholder = 'Contestant ' + (i + 1);
        document.getElementById('contestants-list').appendChild(input);
        document.getElementById('contestants-list').appendChild(document.createElement('br'));
    }
    document.getElementById('contestants-input').style.display = 'block';
}

function submitNames() {
    const numContestants = document.getElementById('num-contestants').value;
    contestants = [];
    for (let i = 0; i < numContestants; i++) {
        const name = document.getElementById('contestant-' + i).value;
        if (name) contestants.push(name);
    }
    if (contestants.length > 0) {
        document.getElementById('teams-input').style.display = 'block';
        setupTeams();
    }
}

function setupTeams() {
    document.getElementById('teams-list').innerHTML = '';
    contestants.forEach((contestant, index) => {
        const select = document.createElement('select');
        select.id = 'team-' + index;
        select.innerHTML = `
            <option value="team1">Team 1</option>
            <option value="team2">Team 2</option>
        `;
        document.getElementById('teams-list').appendChild(select);
        document.getElementById('teams-list').appendChild(document.createElement('br'));
    });
}

function submitTeams() {
    teams.team1 = [];
    teams.team2 = [];
    contestants.forEach((contestant, index) => {
        const team = document.getElementById('team-' + index).value;
        if (team === 'team1') {
            teams.team1.push(contestant);
        } else {
            teams.team2.push(contestant);
        }
    });
    document.getElementById('setup-page').style.display = 'none';
    document.getElementById('challenge-page').style.display = 'block';
    loadTeams();
}

function loadTeams() {
    document.getElementById('winning-team').innerHTML = `
        <option value="team1">Team 1</option>
        <option value="team2">Team 2</option>
    `;
    document.getElementById('losing-team').innerHTML = `
        <option value="team1">Team 1</option>
        <option value="team2">Team 2</option>
    `;
    document.getElementById('eliminated-player').innerHTML = '';
    contestants.forEach(contestant => {
        const option = document.createElement('option');
        option.value = contestant;
        option.textContent = contestant;
        document.getElementById('eliminated-player').appendChild(option);
    });
}

function submitChallenge() {
    currentChallenge = document.getElementById('challenge-details').value;
    document.getElementById('elimination-section').style.display = 'block';
    document.getElementById('confessions-section').style.display = 'block';
}

function submitElimination() {
    const eliminatedPlayer = document.getElementById('eliminated-player').value;
    contestants = contestants.filter(contestant => contestant !== eliminatedPlayer);
    if (contestants.length <= 1) {
        document.getElementById('final-winner').textContent = `The winner is ${contestants[0]}!`;
        document.getElementById('challenge-page').style.display = 'none';
        document.getElementById('final-page').style.display = 'block';
    } else {
        document.getElementById('round-summary').textContent = `Round ${++currentRound} completed. ${eliminatedPlayer} was eliminated.`;
        document.getElementById('challenge-page').style.display = 'none';
        document.getElementById('results-page').style.display = 'block';
    }
}

function submitConfessions() {
    // Handle confession submissions if needed
}

function startNextRound() {
    document.getElementById('results-page').style.display = 'none';
    document.getElementById('challenge-page').style.display = 'block';
}

function startNewGame() {
    location.reload();
}
