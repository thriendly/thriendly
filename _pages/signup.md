---
layout: mainlayout
title: Signup with TipSeason
permalink: /signup
---

<div class="container-fluid">
    <div class="row  justify-content-center align-items-center d-flex-row text-center h-100">
        <div class="col-12 col-md-6 col-lg-4   h-50 ">
            <div class="card shadow">
                <div class="card-body mx-auto">
                    <h3 class="card-title mt-3 text-center text-info">Welcome to Thriendly!</h3>
                    <h4 class="card-title mt-3 text-center text-secondary">It's time to grow as an Influencer.</h4>

                    <h4 class="card-title mt-3 text-center">Create Account</h4>
                    <p>
                        <a href="#" class="btn btn-block btn-danger" id="googleSignInButton">
                            <i class="fab fa-google mr-2"></i>Signup with Google</a>
                        <!--                            <a href="" class="btn btn-block btn-primary">-->
                        <!--                                <i class="fab fa-facebook-f mr-2"></i>Login via facebook</a>-->
                    </p>
                    <p class="text-muted font-weight-bold ">
                        <span>OR</span>
                    </p>
                    <form id="signupForm">
                        <div class="form-group input-group">
                            <div class="input-group-prepend">
                                <span class="input-group-text"> <i class="fa fa-envelope"></i> </span>
                            </div>
                            <input name="" class="form-control" placeholder="Email address" type="email" id="email">
                        </div>
                        <div class="form-group input-group">
                            <div class="input-group-prepend">
                                <span class="input-group-text"> <i class="fa fa-lock"></i> </span>
                            </div>
                            <input class="form-control" placeholder="Create password" type="password" id="password">
                        </div>
                        <div class="form-group">
                            <button type="submit" class="btn btn-primary btn-block"> Create Account </button>
                        </div>
                        <p class="form-group">Have an account?
                            <a href="/login">Log In</a>
                        </p>
                        <p class="form-group text-danger" id="error" style="display:none;">
                            Invalid Email/Password!
                        </p>
                    </form>
                </div>
            </div>
        </div>
    </div>
</div>


<!-- Your JavaScript code -->
<script type="module">
    // Import the functions you need from the SDKs you need
    import { initializeApp } from "https://www.gstatic.com/firebasejs/10.1.0/firebase-app.js";
    import { getAnalytics } from "https://www.gstatic.com/firebasejs/10.1.0/firebase-analytics.js";
    import { getAuth, GoogleAuthProvider, signInWithRedirect, getRedirectResult, onAuthStateChanged, createUserWithEmailAndPassword, sendEmailVerification } from "https://www.gstatic.com/firebasejs/10.1.0/firebase-auth.js";


    // Your web app's Firebase configuration
    // For Firebase JS SDK v7.20.0 and later, measurementId is optional
    const firebaseConfig = {
        apiKey: "AIzaSyCbf0LeMex9k1JkVYLEF72tlVUbAztU6Rc",
        authDomain: "thetipseason.firebaseapp.com",
        projectId: "thetipseason",
        storageBucket: "thetipseason.appspot.com",
        messagingSenderId: "987994279894",
        appId: "1:987994279894:web:b9b77c974a902068ec5e77",
        measurementId: "G-2VTGDC811Z"
    };

    // Initialize Firebase
    const app = initializeApp(firebaseConfig);
    const analytics = getAnalytics(app);

    const auth = getAuth();
    const googleProvider = new GoogleAuthProvider();

    // Get the Google Sign-In button element
    const googleSignInButton = document.getElementById("googleSignInButton");

    // Add click event listener to the Google Sign-In button
    googleSignInButton.addEventListener("click", () => {
        // Sign in with Google using the GoogleAuthProvider instance
        signInWithRedirect(auth, googleProvider)
            .catch((error) => {
                console.error("Google Sign-In error: ", error.message);
            });
    });

    // Check authentication state
    onAuthStateChanged(auth, (user) => {
        if (user) {
            // User is signed in
            console.log("User is signed in:", user.displayName);
            $("#username").html(user.displayName)
        } else {
            // User is signed out
            console.log("User is signed out.");
        }
    });

    // Function to handle email/password signup with email verification
    function signUpWithEmailPassword(email, password) {
        // Sign up with email and password
        createUserWithEmailAndPassword(auth, email, password)
            .then((userCredential) => {
                // User account created successfully
                const user = userCredential.user;
                console.log("User account created:", user.email);

                // Send email verification
                verifyUser(user);

                $("#error").text("Signup success. Check inbox for verification email!").show();
                // goHome();
            })
            .catch((error) => {
                // Handle signup error
                console.error("Sign Up error: ", error.message);
                // Display user-friendly error messages
                const errorElement = $("#error");
                switch (error.code) {
                    case "auth/email-already-in-use":
                        errorElement.text("Email registered already! Try Login").show();
                        break;
                    case "auth/invalid-email":
                        errorElement.text("Invalid email address").show();
                        break;
                    case "auth/weak-password":
                        errorElement.text("Please choose strong password!").show();
                        break;
                    default:
                        errorElement.text("Error during signup! Please try again.").show();
                }

            });
    }

    function goHome() {
        // Redirect to the secure home page
        window.location.href = "/app/aireplys";
    }

    // Function to send email verification
    function verifyUser(user) {
        sendEmailVerification(user)
            .then(() => {
                console.log("Email verification sent to:", user.email);
                // You can display a message to the user on the signup page to check their email for verification link
            })
            .catch((error) => {
                console.error("Error sending email verification:", error.message);
                // You can display an error message to the user on the signup page if needed
            });
    }

    // Get the signup form element
    const signupForm = $("#signupForm");

    // Add submit event listener to the signup form
    signupForm.on("submit", (event) => {
        event.preventDefault(); // Prevent form submission
        $("#error").hide();

        // Get email and password from the form fields
        const email = $("#email").val();
        const password = $("#password").val();

        if (!validateCredentials(email, password)) {
            // Display an error message if credentials are invalid
            $("#error").show();
            return; // Stop further processing
        }
        // Call the signUpWithEmailPassword function to create the user account
        signUpWithEmailPassword(email, password);
    });

    function validateCredentials(email, password) {
        // Check if the email and password are not empty
        if (!email || !password) {
            return false;
        }
        return true; // Credentials are valid
    }
</script>