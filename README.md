const express = require("express");
const bodyParser = require("body-parser");
const { Client, middleware } = require("@line/bot-sdk");

const config = {
  channelAccessToken: "YOUR_CHANNEL_ACCESS_TOKEN",
  channelSecret: "YOUR_CHANNEL_SECRET"
};

const app = express();
app.use(middleware(config));
app.use(bodyParser.json());

const client = new Client(config); 

const messageCache = {};

app.post("/webhook", (req, res) => {
  const events = req.body.events;
  events.forEach(async (event) => {
    if (event.type === "message" && event.message.type === "text") {
      messageCache[event.message.id] = {
        text: event.message.text,
        userId: event.source.userId,
        timestamp: event.timestamp
      };
    }

    if (event.type === "unsend") {
      const unsent = messageCache[event.unsend.messageId];
      if (unsent) {
        const text = `⚠️ มีการยกเลิกข้อความ: "${unsent.text}"`;
        await client.pushMessage(event.source.userId, { type: "text", text });
      }
    }
  });
  res.sendStatus(200);
});

app.listen(3000, () => console.log("Bot is running on port 3000"));

