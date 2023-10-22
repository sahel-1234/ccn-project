<script>
import { initializeApp } from "firebase/app";
import {
    GoogleAuthProvider,
    getAuth,
    signInWithPopup,
    signOut,
    onAuthStateChanged,
  } from "firebase/auth";
import { onMount } from "svelte";

import firebaseConfig from "$lib/firebaseConfig.js";

  let app, auth;
  let userEmail, isUserLoggedIn, userName;
  isUserLoggedIn = false;

  onMount(() => {
    app = initializeApp(firebaseConfig);
    auth = getAuth(app);

    onAuthStateChanged(auth, (user) => {
      if (user) {
        userEmail = user.email;
        isUserLoggedIn = true;
        userName = user.displayName;
      } else {
        console.log("No user is signed in.");
      }
    });
  });

  const provider = new GoogleAuthProvider();
</script>

<div class="hero min-h-screen bg-base-200">
  <div class="hero-content text-center">
    <div class="max-w-md">
      <h1 class="text-5xl font-bold mb-8">Welcome!!!!</h1>
      {#if isUserLoggedIn}
        <h1 class="text-2xl font-bold mb-8">
          Hello <span class="text-purple-400">{userName}</span>
        </h1>
      {/if}
      {#if !isUserLoggedIn}
        <p class="py-6 mb-8">
          Please Login with your Google account to get started.
        </p>
      {/if}
      {#if !isUserLoggedIn}
        <button
          class="btn btn-primary"
          on:click={() => {
            signInWithPopup(auth, provider)
              .then((result) => {
                const user = result.user;
                console.log(user);
              })
              .catch((error) => {
                console.log(error);
                const errorMessage = error.message;
              });
          }}
        >
          Login <img src="/google.svg" height="30" width="30" /></button
        >
      {/if}
      {#if isUserLoggedIn}
        <button
          class="btn btn-primary"
          on:click={() => {
            signOut(auth).then(() => {
              isUserLoggedIn = false;
            });
          }}
        >
          Logout
        </button>
      {/if}
    </div>
  </div>
</div>
