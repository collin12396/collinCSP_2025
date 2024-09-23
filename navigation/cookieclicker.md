---
layout: post
title: Cookie Clicker
permalink: /cookieclicker/
---
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cookie Clicker</title>
    <style>
        body {
            text-align: center;
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
        }
        .cookie {
            width: 150px;
            height: 150px;
            background-image: url('https://banner2.cleanpng.com/20231031/fbt/transparent-chocolate-chip-cookie-cartoon-image-white-plate-br-cartoon-image-of-chocolate-chip-cookie-on-1711037345444.webp');
            background-size: cover;
            border-radius: 50%;
            display: inline-block;
            cursor: pointer;
            margin: 20px;
            transition: transform 0.1s ease; /* Smooth transition */
        }
        .cookie:active {
            animation: zoomOut 0.1s ease;
        }
        @keyframes zoomOut {
            0% {
                transform: scale(1);
            }
            50% {
                transform: scale(0.9);
            }
            100% {
                transform: scale(1);
            }
        }
        .score {
            font-size: 24px;
            margin: 20px;
        }
        .button {
            font-size: 18px;
            padding: 10px 20px;
            cursor: pointer;
            background-color: #28a745;
            color: white;
            border: none;
            border-radius: 5px;
        }
        .button:hover {
            background-color: #218838;
        }
    </style>
</head>
<body>

    <h1>Cookie Clicker</h1>
    <div class="cookie" onclick="bakeCookie()"></div>
    <div class="score" id="cookieCount">Cookies: 0</div>
    <button class="button" onclick="buyUpgrade()">Buy Upgrade (Cost: 50)</button>
    <div class="score" id="cps">Cookies per second: 0</div>

    <script>
        let cookies = 0;
        let cookiesPerClick = 1;
        let cookiesPerSecond = 0;
        let upgradeCost = 50;

        function bakeCookie() {
            cookies += cookiesPerClick;
            document.getElementById('cookieCount').innerText = `Cookies: ${cookies}`;
        }

        function buyUpgrade() {
            if (cookies >= upgradeCost) {
                cookies -= upgradeCost;
                cookiesPerSecond += 1;
                upgradeCost = Math.floor(upgradeCost * 1.5); // Increase upgrade cost after every purchase
                document.getElementById('cookieCount').innerText = `Cookies: ${cookies}`;
                document.getElementById('cps').innerText = `Cookies per second: ${cookiesPerSecond}`;
                document.querySelector('.button').innerText = `Buy Upgrade (Cost: ${upgradeCost})`; // Update button text
            }
        }

        // Automatically add cookies per second
        setInterval(() => {
            cookies += cookiesPerSecond;
            document.getElementById('cookieCount').innerText = `Cookies: ${cookies}`;
        }, 1000);
    </script>

</body>
</html>
