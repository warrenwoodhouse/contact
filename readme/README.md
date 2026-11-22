# install
## Basic Contact Form with Email Address
add template link here later

## Discord Server Private Channel Webhook
CLICK HERE to see the code for the form.

1. You’ll need to create a private channel on your Discord server and then in the channel, enter Settings > Integrations > Webhooks and create a new webhook
2. After that, copy the Webhook URL
3. Place the Webhook URL in the form from this template, you can see that mine is used in the template.

You’ll now need to generate either a Cloudflare Worker (CLICK HERE for the file) or GitHub Actions Agent (CLICK HERE for the file) to prevent spam.

## Alternative - Discord Bot (Node.js) sends DMs to you privately via Discord
If you strictly want the message to land in your Direct Messages tab rather than a private server channel, you must keep your Bot Token hidden on a backend server.

1. Go to the Discord Developer Portal, create a New Application, go to the Bot tab and copy the Bot Token
2. Invite the bot to any server you are also in (Discord requires bots and users to share at least one mutual server to send DMs)
3. Enable Developer Mode in your Discord client (User Settings -> Advanced), right-click your profile and click Copy User ID
4. Run a backend endpoint using discord.js or standard fetch to Discord’s REST API:

```
// Node.js (Express) backend example
const express = require('express');
const app = express();
app.use(express.json());

const BOT_TOKEN = 'YOUR_BOT_TOKEN';
const YOUR_USER_ID = 'YOUR_DISCORD_USER_ID';

app.post('/api/contact', async (req, res) => {
  const { name, email, message } = req.body;

  try {
    // 1. Open a DM channel with your User ID
    const dmRes = await fetch('https://discord.com/api/v10/users/@me/channels', {
      method: 'POST',
      headers: {
        'Authorization': `Bot ${BOT_TOKEN}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({ recipient_id: YOUR_USER_ID })
    });
    const dmChannel = await dmRes.json();

    // 2. Send the message to that DM channel
    await fetch(`https://discord.com/api/v10/channels/${dmChannel.id}/messages`, {
      method: 'POST',
      headers: {
        'Authorization': `Bot ${BOT_TOKEN}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        content: `**New Contact Message**\n**From:** ${name} (${email})\n**Message:** ${message}`
      })
    });

    res.status(200).json({ success: true });
  } catch (error) {
    res.status(500).json({ error: 'Failed to send DM' });
  }
});

app.listen(3000, () => console.log('Server running on port 3000'));
```
