<script>
  import {
    db
  } from "./firebase.js";
  //signup
  let state = true;
  //gets the user input and signs user up
  function signup() {
    //check if the passowords match
    if (
      document.getElementById("pass").value !=
      document.getElementById("pass2").value
    ) {
      window.alert("Password does not match!");
    } else {
      var userName = document.getElementById("fName").value;
      var userEmail = document.getElementById("email").value;
      var userPass = document.getElementById("pass").value;
      //when the user email already exists in the database
      firebase
        .auth()
        .createUserWithEmailAndPassword(userEmail, userPass)
        .catch(function (error) {
          // Handle Errors here.
          var errorCode = error.code;
          var errorMessage = error.message;
          window.alert("Error : " + errorMessage);
          state = false;
        });
        //signs user up
      firebase.auth().onAuthStateChanged(function (user) {
        if (state) {
          console.log(user.uid);
          //stores the user info into database
          if (user) {
            db.collection("users")
              .doc(user.uid)
              .set({
                name: userName,
                email: user.email
              }, {
                merge: true
              });
            document.getElementById("loggedIn").style.display = "block";

          } else {
            // User is signed out.
            // ...
          }
        }
      });
    }
  }
</script>

<style>
  main {
    text-align: center;
    background-color: #F7EAC5;
    height: 100%;
    margin: 0px;
  }

  button {
    background-color: #EE8152;
    color: #F7EAC5;
    border-radius: 7px;
    border: none;
    padding: 10px;
    margin-top: 20px;
    font-size: 15pt;
    width: 50%;
  }

  #loginButton {
    font-size: 20pt;

  }

  h2 {
    width: 100%;
    height: 30px;
    font-family: arial;
    padding-top: 10px;
    font-size: 20pt;
  }

  #fName {
    width: 50%;
    height: 40px;
    display: flex;
    margin: 0px auto;
    margin-top: 50px;
    font-size: 18pt;
  }

  #email {
    width: 50%;
    height: 40px;
    display: flex;
    margin: 0px auto;
    margin-top: 20px;
    font-size: 18pt;

  }

  #pass {
    width: 50%;
    height: 40px;
    display: flex;
    margin: 0px auto;
    margin-top: 20px;
    font-size: 18pt;
  }

  #pass2 {
    width: 50%;
    height: 40px;
    display: flex;
    margin: 0px auto;
    margin-top: 20px;
    font-size: 15pt;
  }

  #loggedIn {
    margin-top: 40px;
    padding: -2px;
    display: none;
  }

  #loggedInfo {
    font-size: 15pt;
    font-family: arial;
    color: #900c3f;
  }
</style>

<svelte:head>

  <script src="https://www.gstatic.com/firebasejs/7.14.2/firebase-firestore.js">

  </script>
</svelte:head>
<main>
  <h2>Signup with WorkToMate</h2>
  <input id="fName" placeholder="Full Name" type="text" />
  <input id="email" placeholder="Email Address" type="text" />
  <input id="pass" placeholder="Password" type="password" />
  <input id="pass2" placeholder="Confirm Password" type="password" />
  <button on:click={signup}>
    <div id="loginButton">Signup</div>
  </button>
  <div id="loggedIn">
    <p id="loggedInfo">Signup successful, you are now logged in.</p>
    <a href="/home">
      <button id="loggedInBtn">Go To Home</button>
    </a>
  </div>
</main>
