---
layout: base
title: My Coding Journey
description: Home Page
author: Collin Ge
image: /images/mario_animation.png
hide: true
---

<!--- Concatenation of site URL to frontmatter image  --->
{% assign sprite_file = site.baseurl | append: page.image %}
<!--- Has is a list variable containing mario metadata for sprite --->
{% assign hash = site.data.mario_metadata %}
<!--- Size width/height of Sprit images --->
{% assign pixels = 256 %}

<!--- HTML for page contains <p> tag named "Mario" and class properties for a "sprite"  -->

<p id="mario" class="sprite"></p>
  
<!--- Embedded Cascading Style Sheet (CSS) rules, 
        define how HTML elements look 
--->
<style>

  /*CSS style rules for the id and class of the sprite...
  */
  .sprite {
    height: {{pixels}}px;
    width: {{pixels}}px;
    background-image: url('{{sprite_file}}');
    background-repeat: no-repeat;
  }

  /*background position of sprite element
  */
  #mario {
    background-position: calc({{animations[0].col}} * {{pixels}} * -1px) calc({{animations[0].row}} * {{pixels}}* -1px);
  }
</style>

<!--- Embedded executable code--->
<script>
  ////////// convert YML hash to javascript key:value objects /////////

  var mario_metadata = {}; //key, value object
  {% for key in hash %}  
  
  var key = "{{key | first}}"  //key
  var values = {} //values object
  values["row"] = {{key.row}}
  values["col"] = {{key.col}}
  values["frames"] = {{key.frames}}
  mario_metadata[key] = values; //key with values added

  {% endfor %}

  ////////// game object for player /////////

  class Mario {
    constructor(meta_data) {
      this.tID = null;  //capture setInterval() task ID
      this.positionX = 0;  // current position of sprite in X direction
      this.currentSpeed = 0;
      this.marioElement = document.getElementById("mario"); //HTML element of sprite
      this.pixels = {{pixels}}; //pixel offset of images in the sprite, set by liquid constant
      this.interval = 100; //animation time interval
      this.obj = meta_data;
      this.marioElement.style.position = "absolute";
    }

    animate(obj, speed) {
      let frame = 0;
      const row = obj.row * this.pixels;
      this.currentSpeed = speed;

      this.tID = setInterval(() => {
        const col = (frame + obj.col) * this.pixels;
        this.marioElement.style.backgroundPosition = `-${col}px -${row}px`;
        this.marioElement.style.left = `${this.positionX}px`;

        this.positionX += speed;
        frame = (frame + 1) % obj.frames;

        const viewportWidth = window.innerWidth;
        if (this.positionX > viewportWidth - this.pixels) {
          document.documentElement.scrollLeft = this.positionX - viewportWidth + this.pixels;
        }
      }, this.interval);
    }

    startWalking() {
      this.stopAnimate();
      this.animate(this.obj["Walk"], 7.5);
    }

    startWalkingLeft() {
        this.stopAnimate();
        this.animate(this.obj["WalkL"], -7.5);
    }

    startRunningLeft() {
        this.stopAnimate();
        this.animate(this.obj["Run1L"], -15);
    }

    startRunning() {
      this.stopAnimate();
      this.animate(this.obj["Run1"], 15);
    }

    startPuffing() {
      this.stopAnimate();
      this.animate(this.obj["Puff"], 0);
    }

    startPuffingLeft() {
      this.stopAnimate();
      this.animate(this.obj["PuffL"], 0);
    }

    startCheering() {
      this.stopAnimate();
      this.animate(this.obj["Cheer"], 0);
    }

    startFlipping() {
      this.stopAnimate();
      this.animate(this.obj["Flip"], 0);
    }

    startResting() {
      this.stopAnimate();
      this.animate(this.obj["Rest"], 0);
    }

    startRestingLeft() {
      this.stopAnimate();
      this.animate(this.obj["RestL"], 0);
    }

    stopAnimate() {
      clearInterval(this.tID);
    }
  }

  const mario = new Mario(mario_metadata);

  ////////// event control /////////
  window.addEventListener("keydown", (event) => {
    if (event.key === "ArrowRight") {
      event.preventDefault();
        if (mario.currentSpeed === 0) {
            mario.startWalking();
        } 
        else if (mario.currentSpeed === 7.5) {
            mario.startRunning();
        }
        else if (mario.currentSpeed < 0){
            mario.startResting();
        }
    } 

    else if (event.key === "ArrowDown") {
      event.preventDefault();
      if (event.repeat) {
        mario.stopAnimate();
      } 
      else if (mario.currentSpeed >= 0){
        mario.startPuffing();
      }
      else {
        mario.startPuffingLeft();
      }
    } 

    else if (event.key === "ArrowLeft") {
        event.preventDefault();
        if (mario.currentSpeed === 0) {
            mario.startWalkingLeft();
        } 
        else if (mario.currentSpeed === -7.5) {
            mario.startRunningLeft();
        }
        else if (mario.currentSpeed > 0){
            mario.startRestingLeft();
        }
    }

    else if (event.key === "ArrowUp") {
      event.preventDefault();
      if (mario.currentSpeed === 0) {
        mario.startFlipping();
      }
      else if (mario.currentSpeed < 0) {
        mario.startRestingLeft();
      }
      else if (mario.currentSpeed > 0) {
        mario.startResting();
      }
    }
  });

  //touch events that enable animations
  window.addEventListener("touchstart", (event) => {
    event.preventDefault(); // prevent default browser action
    if (event.touches[0].clientX > window.innerWidth / 2) {
      // move right
      if (currentSpeed === 0) { // if at rest, go to walking
        mario.startWalking();
      } else if (currentSpeed === 7.5) { // if walking, go to running
        mario.startRunning();
      }
    } else {
      // move left
      mario.startPuffing();
    }
  });

  //stop animation on window blur
  window.addEventListener("blur", () => {
    mario.stopAnimate();
  });

  //start animation on page load or page refresh
  document.addEventListener("DOMContentLoaded", () => {
    // adjust sprite size for high pixel density devices
    const scale = window.devicePixelRatio;
    const sprite = document.querySelector(".sprite");
    sprite.style.transform = `scale(${.5 * scale})`;
    mario.startResting();
  });

</script>


<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Journey</title>
    <style>
        .section {
            border: 3px solid white; 
            padding: 10px;
            margin-bottom: 10px;
            display: flex;          /* Enables flexbox */
            justify-content: center; /* Centers content horizontally */
            align-items: center;     /* Centers content vertically */
            flex-direction: column;
        }
      img{
        display: block;
        margin: auto;
      }
    </style>
    <style>
    button {
        background-color: #4CAF50;   
        color: white;                
        padding: 12px 24px;          
        border: none;                
        border-radius: 12px;         
        cursor: pointer;             
        font-size: 16px;             
        transition: background-color 0.3s ease, transform 0.3s ease, opacity 0.3s ease; 
    }

    button:hover {
        background-color: #45a049;    /* Darker green when hovered */
        opacity: 0.7;                 /* Fade effect */
        transform: scale(1.05);       /* Slight zoom effect */
    }

    .nklink button {
        margin-top: 10px;            /* Adds space between buttons */
    }
</style>

</head>
<body>
    <h3>My Journey</h3>
    <div>
     <p>
     </p>
    </div>
    <img src="images/dragon.jpg" alt="Image" width="60%">
    <div>
      <p>
      </p>
    </div>
    <div class="section">
        <p>
            When setting up the tools, the biggest problem I encounter is the problem of root. Which is most possibly caused by me forgetting the first username and password. First, I tried to check my username on terminal, but only my username show, which is my first account "collin". After that, I find out that the password does not show and I don't know how to edit it. I choose to create a new account name "Qixiang" and set up the password and username. But it still shows the root in front of my account. At last, I tried deleting everything and follow from step one and see if it fix, then I download everything I need and download the vs code, then it finally work and it quit root.
        </p>
        <p>
        - Good luck! 😊
        </p>
    </div>
</body>
<style>
    .paste-button {
        position: relative;
        display: block;
        font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    }

    .button {
        background-color: #4CAF50;
        color: #ffffff;
        padding: 10px 15px;
        font-size: 15px;
        font-weight: bold;
        border: 2px solid transparent;
        border-radius: 15px;
        cursor: pointer;
    }

    .dropdown-content, .submenu-content {
        display: none;
        font-size: 13px;
        position: absolute;
        z-index: 1;
        min-width: 200px;
        background-color: #4CAF50;
        border: 2px solid #ffffff;
        border-radius: 0px 15px 15px 15px;
        box-shadow: 0px 8px 16px 0px rgba(0,0,0,0.2);
    }

    .dropdown-content a, .submenu-content a {
        color: #ffffff;
        padding: 8px 10px;
        text-decoration: none;
        display: block;
        transition: 0.1s;
    }

    .dropdown-content a:hover, .submenu-content a:hover {
        background-color: #4CAF50;
        color: #ffffff;
    }

    .dropdown-content a:focus, .submenu-content a:focus {
        background-color: #4CAF50;
        color: #ffffff;
    }

    .dropdown-content #top:hover {
        border-radius: 0px 13px 0px 0px;
    }

    .dropdown-content #bottom:hover {
        border-radius: 0px 0px 13px 13px;
    }

    .paste-button:hover button {
        border-radius: 15px 15px 0px 0px;
    }

    .paste-button:hover .dropdown-content {
        display: block;
    }

    /* Submenu styles */
    .submenu {
        position: relative;
    }

    .submenu-content {
        top: 0;
        left: 100%;
        border-radius: 0 15px 15px 15px;
    }

    .submenu-content a:first-child:hover {
        border-radius: 0px 13px 0px 0px;
    }

    .submenu-content a:last-child:hover {
        border-radius: 0px 0px 13px 13px;
    }

    .submenu:hover .submenu-content {
        display: block;
    }
</style>

<div>
<p></p>
</div>
<div class="paste-button">
  <button class="button">Menu &nbsp; ▼</button>
  <div class="dropdown-content">
    <a id="top" href="{{site.baseurl}}/cspcalendar/">Calendar</a>
    <div class="submenu">
        <a id="middle" href="https://nighthawkcoders.github.io/portfolio_2025/javascript/project/play">Javascript cell &nbsp; ▶</a>
        <div class="submenu-content">
            <a href="{{site.baseurl}}/cookieclicker/">Cookie clicker</a>
            <a href="{{site.baseurl}}/calculator/">Calculator</a>
            <a href="{{site.baseurl}}/snake/">Snake Game</a>
            <a href="{{site.baseurl}}/jupyternotebook/">Jupyter Notebook</a>
        </div>
    </div>
    <a id="bottom" href="{{site.baseurl}}/about/">About Pages</a>
  </div>
</div>
<div>
  <p></p>
</div>  
  <div class="section" class="nklink">
    <button>CSP Button</button>
    <div>
     <p>
     </p>
    </div>
     <a href="https://nighthawkcoders.github.io/portfolio_2025/navigation/section/csp" class="nklink">
      <button>Nighthawk Instruction</button>
     </a>
    <div>
     <p>
     </p>
     <a href="https://nighthawkcoders.github.io/portfolio_2025/devops/tools/home" class="nklink">
      <button>CSP Tool Setup</button>
     </a>
    </div>
  </div>
  <div>
    <p></p>
    <a href="{{site.baseurl}}/snake/" class="nklink">
      <button>Snake Game</button>
    </a>
  </div>
  <div>
  <p></p>
  </div>
</html>

