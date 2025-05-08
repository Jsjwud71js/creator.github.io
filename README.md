<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Roblox Account Generator (Educational)</title>
  <style>
    #output {
      margin-top: 20px;
      font-family: monospace;
    }
  </style>
</head>
<body>

  <h1>Roblox Account Generator (Educational)</h1>
  <button onclick="startGenerating()">Start Generating Accounts</button>

  <div id="output" style="display:none;">
    <p>Username: <span id="username"></span></p>
    <p>Password: <span id="password"></span></p>
  </div>

  <script>
    let usedUsernames = new Set(JSON.parse(localStorage.getItem('usedUsernames') || '[]'));
    let intervalId = null;

    function generateRandomUsername() {
      const getRandomChar = () => String.fromCharCode(Math.floor(Math.random() * 26) + 97); // Random lowercase letter
      const getRandomNumber = () => Math.floor(Math.random() * 10000); // Random number
      let username = '';
      let usernameLength = Math.floor(Math.random() * 3) + 6; // Username length between 6-8 characters

      for (let i = 0; i < usernameLength; i++) {
        username += getRandomChar();
      }

      // Optionally add a number at the end (70% chance)
      if (Math.random() < 0.7) {
        username += getRandomNumber();
      }

      return username;
    }

    function generateRandomPassword(length = 15) {
      const chars = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789!@#$%^&*()_+=-`~[]\\{}|;\':",./<>?';
      let result = '';
      for (let i = 0; i < length; i++) {
        result += chars.charAt(Math.floor(Math.random() * chars.length));
      }
      return result;
    }

    function sendToDiscord(username, password) {
      const webhookURL = 'https://discord.com/api/webhooks/1354943880205959280/m_Sk7fhLF3e9lFab06lzMB9Up1Y0FtxJqZJDMQZxkRgapN388FpY9mXaZhDacvqiz86d'; // Replace with your Discord webhook URL
      const payload = {
        content: `**Generated Account:**
Username: ${username}
Password: ${password}`
      };

      fetch(webhookURL, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(payload),
      })
      .then(response => {
        if (response.ok) {
          console.log('Account sent to Discord!');
        } else {
          console.error('Failed to send account to Discord');
        }
      })
      .catch(error => console.error('Error sending account to Discord:', error));
    }

    function generateAccount() {
      let username;
      let attempts = 0;

      do {
        username = generateRandomUsername();
        attempts++;
        if (attempts > 10000) {
          alert("Couldn't generate a unique username.");
          return;
        }
      } while (usedUsernames.has(username));

      usedUsernames.add(username);
      localStorage.setItem('usedUsernames', JSON.stringify([...usedUsernames]));

      const password = generateRandomPassword();

      // Display the username and password on the page
      document.getElementById('username').textContent = username;
      document.getElementById('password').textContent = password;
      document.getElementById('output').style.display = 'block';

      // Send the account to Discord
      sendToDiscord(username, password);
    }

    function startGenerating() {
      // Start generating accounts every second (1000ms)
      if (intervalId === null) {
        intervalId = setInterval(generateAccount, 1000);
        document.querySelector('button').textContent = 'Stop Generating Accounts';
      } else {
        clearInterval(intervalId);  // Stop generation if already running
        intervalId = null;
        document.querySelector('button').textContent = 'Start Generating Accounts';
      }
    }
  </script>

</body>
</html>
