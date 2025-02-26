<script setup>
import exampleImage from '@/assets/logo.jpg';
import backgroundVideo from '@/assets/form-video.mp4';
import { ref } from "vue";

// Reactive variables for input values
const email = ref('');
const number = ref('');

// Reactive variables for dynamic classes
const isVideoVisible = ref(false); // Controls the visibility of the video
const isEffectActive = ref(false); // Controls the .effect class on .page

// Function to validate and restrict input to integers
const validateInteger = (event) => {
  number.value = event.target.value.replace(/\D/g, ''); // Remove non-digit characters
};

// Function to handle the login button click
const handleLogin = async () => {
  console.log('Email:', email.value);
  console.log('Number:', number.value);

  try {
    // Make a POST request to the backend
    const response = await fetch('https://register.rhea.comagno.com/register', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        email: email.value.toLocaleLowerCase(),
        number: parseInt(number.value, 10), 
      }),
    });

    if (response.ok) 
    {
      const result = await response.text();
      console.log('User added successfully:', result);

      // Show the video and add the blur effect
      isVideoVisible.value = true;
      isEffectActive.value = true;
      email.value = ''; // Clear the email input
      number.value = ''; // Clear the number input+
    } else {
      console.error('Failed to add user:', await response.text());
    }
  } catch (error) {
    console.error('Error during the POST request:', error);
  }
};

// Function to close the video and remove the blur
const closeVideo = () => {
  isVideoVisible.value = false; // Hide the video
  isEffectActive.value = false; // Remove the .effect class
};
</script>

<template>
  <div :class="['page', { effect: isEffectActive }]">
    <div class="menu">
      <img :src="exampleImage" alt="Logo" class="image-menu">
    </div> 
    <div class="views">
      <video class="background-video" autoplay muted loop>
        <source :src="backgroundVideo" type="video/mp4" />
        Your browser does not support the video tag.
      </video>
      <div class="split">
        <div class="form-container">
          <div class="top">
            <div class="title">We’ve Got Big Plans!</div>
            <div class="sub-title">Sign Up and Stay Informed</div>
          </div>
          <form @submit.prevent="handleLogin">
            <input 
              type="email" 
              id="email" 
              placeholder="Enter your email" 
              v-model="email" 
              required
            />
            <input 
              type="text" 
              id="number" 
              placeholder="Enter your number" 
              v-model="number" 
              @input="validateInteger"
              required
            />
            <button type="submit">Login</button>
          </form>
        </div>
      </div>
    </div>
    <div :class="['video-container', { 'no-video': !isVideoVisible }]">
      <!-- Close Button -->
      <button class="close-button" @click="closeVideo" aria-label="Close video">✕</button>
      <video controls>
        <source :src="backgroundVideo" type="video/mp4">
        Your browser does not support the video tag.
      </video>
    </div>
  </div>
</template>

<style scoped>
.page {
  display: flex;
  flex-direction: column; /* Stack children vertically */
  height: 100%; /* Ensure .page takes the full height of its parent (#app) */
}
.menu {
    background-color: #d4d4d4;
}
.image-menu {
    width: 80px;
}
.views {
    
  flex: 1; 
  display: flex;
  flex-direction: row-reverse; 
  background: transparent;
}
.split {
    max-width: 800px;
    z-index: 1;
    background-color: #00000030;
    backdrop-filter: blur(10px);
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    display: flex;
    align-items: center;
}

.background-video {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  object-fit: cover; /* Ensure video covers the entire section */
  z-index: -1; /* Place video behind the content */
}

.form-container{
    display: flex;
    flex-direction: column;
    width: 600px;
}
.form-container .top{
    display: flex;
    flex-direction: column;
    align-items: flex-end;
    margin-right: 50px;
    margin-bottom: 50px;
}
.form-container .top .title{
    color: #fff;
    font-size: 34px;
}

.form-container .top .sub-title{
    color: #fff;
}
.form-container form{
    display: flex;
    flex-direction: column;
    padding: 10px 20px;
    gap: 15px;
    align-items: flex-end;
    margin-right: 25px;
}

.form-container form input{
    height: 25px;
    width: 70%;
}

.form-container form button{
    max-width: 50%;
}

.effect {
    position: relative;
    display: flex;
    flex-direction: column;
    height: 100%;
    overflow: hidden; 
}

.effect::before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.4); 
    backdrop-filter: blur(10px);
    z-index: 4; 
    pointer-events: none;
}

.video-container {
  position: absolute;
  z-index: 5;
  width: 80%;
  max-width: 800px;
  border: 2px solid #ccc;
  border-radius: 10px;
  right: 25%;
  top: 25%;
  background: none;
  display: flex;
  flex-direction: column;
  align-items: flex-end; /* Align the close button to the top-right */
}

.video-container.no-video {
  display: none;
}

video {
  width: 100%;
  height: auto;
  display: block;
}

.close-button {
  position: absolute;
  top: 10px;
  right: 10px;
  background: #fff;
  color: #333;
  border: none;
  font-size: 18px;
  font-weight: bold;
  border-radius: 50%;
  width: 30px;
  height: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  z-index: 6;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
  transition: background-color 0.3s ease;
}

.close-button:hover {
  background: #f44336;
  color: #fff;
}


@media (max-width: 1200px) {
  .video-container{
    right: 15%;
  }
}

@media (max-width: 900px) {
  .video-container{
    right: 12%;
  }
}

@media (min-width: 2000px) {
  .video-container{
    right: 30%;
  }
}

@media (min-width: 2500px) {
  .video-container{
    right: 35%;
  }
}
@media (max-width: 450px) {

  .form-container form{
    margin-left: 20%;
  }

  .video-container{
    right: 10%;
  }

}
</style>

<style>
html, body {
  height: 100%; /* Full viewport height */
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  margin: 0;
  padding: 0;
}

#app{
    height: 100%;
}
</style>
