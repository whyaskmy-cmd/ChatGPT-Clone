chatgpt-clone/
├── backend/
│   └── server.js
├── frontend/
│   ├── src/
│   │   └── App.js
│   └── public/
const express = require('express');
const cors = require('cors');
const bodyParser = require('body-parser');
const axios = require('axios');

const app = express();
app.use(cors());
app.use(bodyParser.json());

const OPENAI_API_KEY = process.env.OPENAI_API_KEY;

app.post('/chat', async (req, res) => {
  const { message } = req.body;
  try {
    const response = await axios.post(
      'https://api.openai.com/v1/chat/completions',
      {
        model: 'gpt-3.5-turbo',
        messages: [{ role: 'user', content: message }],
      },
      {
        headers: {
          Authorization: `Bearer ${OPENAI_API_KEY}`,
        },
      }
    );
    res.json({ reply: response.data.choices[0].message.content });
  } catch (error) {
    res.status(500).json({ error: 'Something went wrong.' });
  }
});

app.listen(5000, () => {
  console.log('Server running on http://localhost:5000');
});
import React, { useState } from 'react';

function App() {
  const [messages, setMessages] = useState([]);
  const [input, setInput] = useState('');

  const sendMessage = async () => {
    setMessages([...messages, { role: 'user', content: input }]);
    const res = await fetch('http://localhost:5000/chat', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ message: input }),
    });
    const data = await res.json();
    setMessages([...messages, { role: 'user', content: input }, { role: 'assistant', content: data.reply }]);
    setInput('');
  };

  return (
    <div>
      <h1>ChatGPT Clone</h1>
      <div>
        {messages.map((msg, idx) => (
          <div key={idx} style={{ textAlign: msg.role === 'user' ? 'right' : 'left' }}>
            <b>{msg.role}:</b> {msg.content}
          </div>
        ))}
      </div>
      <input value={input} onChange={e => setInput(e.target.value)} />
      <button onClick={sendMessage}>Send</button>
    </div>
  );
}

export default App;
https://github.com/whyaskmy-cmd/ChatGPT-Clone.git
