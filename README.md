<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Tikiti - Réseau Social</title>
<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600;700&display=swap" rel="stylesheet">
<style>
body {
    font-family: 'Poppins', sans-serif;
    background: linear-gradient(135deg, #ff9a9e, #fad0c4);
    margin: 0;
    padding: 0;
    color: #333;
}
header {
    background-color: #ff6f61;
    color: white;
    padding: 15px;
    text-align: center;
    font-size: 28px;
    font-weight: bold;
    box-shadow: 0 4px 6px rgba(0,0,0,0.1);
}
nav {
    background-color: #fff;
    padding: 10px;
    display: flex;
    justify-content: center;
    gap: 20px;
    border-bottom: 1px solid #ddd;
}
nav button {
    padding: 10px 20px;
    border: none;
    background-color: #ff6f61;
    color: white;
    border-radius: 25px;
    cursor: pointer;
    font-weight: 600;
    transition: 0.3s;
}
nav button:hover {
    background-color: #ff4c3b;
}
#container {
    max-width: 700px;
    margin: 30px auto;
    background-color: white;
    padding: 25px;
    border-radius: 15px;
    box-shadow: 0 8px 20px rgba(0,0,0,0.15);
}
input, textarea {
    width: 100%;
    padding: 12px;
    margin: 10px 0;
    border-radius: 8px;
    border: 1px solid #ddd;
    font-size: 16px;
}
button.add-btn, button.send-btn, button.remove-btn {
    border: none;
    cursor: pointer;
    font-weight: 600;
    transition: 0.3s;
}
button.add-btn, button.send-btn {
    background-color: #4caf50;
    color: white;
    padding: 10px 20px;
    border-radius: 10px;
    margin-top: 5px;
}
button.add-btn:hover, button.send-btn:hover {
    background-color: #45a049;
}
h2, h3 { color: #ff6f61; }
#login, #register, #friendsSection, #chatSection { display: none; }
.friend-list { list-style: none; padding: 0; }
.friend-list li {
    padding: 10px;
    margin: 5px 0;
    background-color: #ffe6e1;
    border-radius: 10px;
    display: flex;
    justify-content: space-between;
    align-items: center;
}
.friend-list li span.name {
    cursor: pointer;
}
.friend-list li span.name:hover {
    text-decoration: underline;
}
button.remove-btn {
    background-color: #ff4c3b;
    color: white;
    padding: 5px 10px;
    border-radius: 5px;
}
button.remove-btn:hover {
    background-color: #e6392b;
}
#chatBox {
    border: 1px solid #ddd;
    border-radius: 10px;
    height: 400px;
    overflow-y: auto;
    padding: 10px;
    background-color: #fff7f5;
    margin-bottom: 10px;
}
.message {
    margin: 5px 0;
    padding: 10px;
    border-radius: 10px;
    max-width: 70%;
    word-wrap: break-word;
}
.message.self {
    background-color: #4caf50;
    color: white;
    align-self: flex-end;
    margin-left: auto;
}
.message.friend {
    background-color: #ffe6e1;
    color: #333;
    align-self: flex-start;
    margin-right: auto;
}
#chatBox div { display: flex; flex-direction: column; }
</style>
</head>
<body>

<header>Tikiti 🌴</header>

<nav>
    <button onclick="showSection('login')">Connexion</button>
    <button onclick="showSection('register')">Inscription</button>
    <button onclick="showSection('friendsSection')">Amis</button>
</nav>

<div id="container">

<!-- Login -->
<div id="login">
    <h2>Connexion</h2>
    <input type="text" id="loginUsername" placeholder="Nom d'utilisateur">
    <input type="password" id="loginPassword" placeholder="Mot de passe">
    <button onclick="login()">Se connecter</button>
</div>

<!-- Register -->
<div id="register">
    <h2>Inscription</h2>
    <input type="text" id="registerUsername" placeholder="Nom d'utilisateur">
    <input type="password" id="registerPassword" placeholder="Mot de passe">
    <button onclick="register()">S'inscrire</button>
</div>

<!-- Friends Section -->
<div id="friendsSection">
    <h2>Bienvenue, <span id="userDisplay"></span> !</h2>
    <h3>Ajouter un ami :</h3>
    <input type="text" id="friendSearch" placeholder="Tape l'identifiant de ton ami">
    <button class="add-btn" onclick="addFriend()">Ajouter</button>

    <h3>Mes amis :</h3>
    <ul class="friend-list" id="friends"></ul>
</div>

<!-- Chat Section -->
<div id="chatSection">
    <h2>Chat avec <span id="chatWith"></span></h2>
    <div id="chatBox"></div>
    <textarea id="chatInput" placeholder="Écris ton message..."></textarea>
    <button class="send-btn" onclick="sendMessage()">Envoyer</button>
</div>

</div>

<script>
let users = [];
let currentUser = null;
let chats = {};
let currentFriend = null;

function showSection(section) {
    document.getElementById('login').style.display = 'none';
    document.getElementById('register').style.display = 'none';
    document.getElementById('friendsSection').style.display = 'none';
    document.getElementById('chatSection').style.display = 'none';
    document.getElementById(section).style.display = 'block';
}

// Inscription
function register() {
    let username = document.getElementById('registerUsername').value;
    let password = document.getElementById('registerPassword').value;
    if(username && password) {
        if(users.find(u => u.username === username)) {
            alert("Ce nom d'utilisateur est déjà pris !");
            return;
        }
        users.push({username: username, password: password, friends: []});
        alert("Inscription réussie !");
        showSection('login');
    } else {
        alert("Veuillez remplir tous les champs !");
    }
}

// Connexion
function login() {
    let username = document.getElementById('loginUsername').value;
    let password = document.getElementById('loginPassword').value;
    let user = users.find(u => u.username === username && u.password === password);
    if(user) {
        currentUser = user;
        document.getElementById('userDisplay').innerText = currentUser.username;
        renderFriends();
        showSection('friendsSection');
    } else {
        alert("Nom d'utilisateur ou mot de passe incorrect !");
    }
}

// Ajouter un ami
function addFriend() {
    let friendName = document.getElementById('friendSearch').value;
    if(!friendName) return;
    if(friendName === currentUser.username) {
        alert("Tu ne peux pas t'ajouter toi-même !");
        return;
    }
    let friend = users.find(u => u.username === friendName);
    if(friend) {
        if(!currentUser.friends.includes(friendName)) {
            currentUser.friends.push(friendName);
            alert(friendName + " a été ajouté à tes amis !");
            renderFriends();
            document.getElementById('friendSearch').value = '';
        } else {
            alert("Cet utilisateur est déjà ton ami !");
        }
    } else {
        alert("Utilisateur non trouvé !");
    }
}

// Supprimer un ami
function removeFriend(name) {
    currentUser.friends = currentUser.friends.filter(f => f !== name);
    if(currentFriend === name) currentFriend = null;
    renderFriends();
}

// Affichage amis
function renderFriends() {
    let friendsUl = document.getElementById('friends');
    friendsUl.innerHTML = '';
    currentUser.friends.forEach(f => {
        let li = document.createElement('li');
        li.innerHTML = `<span class="name">${f}</span> <button class="remove-btn" onclick="removeFriend('${f}')">Supprimer</button>`;
        li.querySelector('.name').addEventListener('click', () => openChat(f));
        friendsUl.appendChild(li);
    });
}

// Ouvrir le chat
function openChat(friend) {
    currentFriend = friend;
    document.getElementById('chatWith').innerText = friend;
    if(!chats[friend]) chats[friend] = [];
    renderChat();
    showSection('chatSection');
}

// Envoyer un message
function sendMessage() {
    let content = document.getElementById('chatInput').value;
    if(!content || !currentFriend) return;
    if(!chats[currentFriend]) chats[currentFriend] = [];
    chats[currentFriend].push({from: currentUser.username, content: content});
    document.getElementById('chatInput').value = '';
    renderChat();
}

// Affichage chat
function renderChat() {
    let chatBox = document.getElementById('chatBox');
    chatBox.innerHTML = '';
    chats[currentFriend].forEach(msg => {
        let div = document.createElement('div');
        div.className = 'message ' + (msg.from === currentUser.username ? 'self' : 'friend');
        div.innerText = msg.content;
        chatBox.appendChild(div);
    });
    chatBox.scrollTop = chatBox.scrollHeight;
}

// Initial show login
showSection('login');
</script>

</body>
</html>
