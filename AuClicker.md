<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <title>People Online</title>
  <style>
    body { font-family: sans-serif; text-align: center; margin-top: 3rem; }
    #count { font-size: 3rem; font-weight: bold; }
  </style>
  <!-- Firebase SDKs -->
  <script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>
</head>
<body>
  <h1>People online right now:</h1>
  <div id="count">0</div>

  <script>
    // Replace with your Firebase project config
    const firebaseConfig = {
      apiKey: "YOUR_API_KEY",
      authDomain: "YOUR_PROJECT.firebaseapp.com",
      databaseURL: "https://YOUR_PROJECT.firebaseio.com",
      projectId: "YOUR_PROJECT",
      storageBucket: "YOUR_PROJECT.appspot.com",
      messagingSenderId: "YOUR_SENDER_ID",
      appId: "YOUR_APP_ID"
    };

    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    const connectedRef = db.ref(".info/connected");
    const presenceRef = db.ref("presence");
    const myRef = presenceRef.push();

    connectedRef.on("value", (snap) => {
      if (snap.val() === true) {
        myRef.onDisconnect().remove();
        myRef.set(true);
      }
    });

    presenceRef.on("value", (snap) => {
      document.getElementById("count").textContent = snap.numChildren();
    });
  </script>
</body>
</html>
