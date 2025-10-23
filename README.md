const express = require('express');
const { Client, middleware } = require('@line/bot-sdk');
const app = express();
require('dotenv').config();

const config = {
  channelAccessToken:M3vlwbrwKhblV7D8mR/t2yw6pkNkJGHwirpmvVyKv7NvXFFTbUzt8A4xPljbyTayaOjDZWLvYQAipzZ3Kk37ybEP3LoWKlmexIPspLWw/J9PcZnAmjWVovi0lmvymgdh4t417VAAtf5QEw/lPSp77gdB04t89/1O/w1cDnyilFU= process.env.LINE_CHANNEL_ACCESS_TOKEN,
  channelSecret: process.env.LINE_CHANNEL_SECRET,6267215bc5bb5436a7f5869421982fc3
};

const client = new Client(config);

app.post('/webhook', middleware(config), (req, res) => {
  Promise
    .all(req.body.events.map(handleEvent))
    .then((result) => res.json(result))
    .catch((err) => {
      console.error(err);
      res.status(500).end();
    });
});

function handleEvent(event) {
  if (event.type === 'unsend') {
    const time = new Date().toLocaleString('th-TH');
    const message = {
      type: 'text',
      text: `มีคนยกเลิกข้อความเมื่อ ${time} 😼`,
    };
    return client.pushMessage(event.source.groupId || event.source.userId, message);
  }
  return Promise.resolve(null);
}

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server running on http://localhost:${PORT}`));
