# n8n-voice-transcription-bot
# N8N AI Voice Transcription Chatbot for Telegram

![Demo Screenshot](https://i.imgur.com/gK9Q2aB.png)
*(Replace the link above with a URL to your own screenshot or GIF of the bot in action)*

This project is a powerful, real-time voice chatbot built on the n8n workflow automation platform. It listens for voice messages on Telegram, transcribes them to text using the high-speed Groq API, cleans and processes the text with an AI agent, and generates an intelligent, conversational response.

---

## ✨ Features

*   **🎙️ Real-Time Voice Transcription:** Transcribes user voice messages from Telegram instantly.
*   **🧠 AI-Powered Text Cleaning:** An initial AI agent processes the raw transcription output, removing artifacts and correcting grammar to ensure high-quality input for the main chatbot.
*   **🤖 Conversational AI Response:** Uses a second, more sophisticated AI agent to understand the user's intent and generate a helpful, context-aware response.
*   **⚡ High-Speed Performance:** Leverages the Groq API for both transcription (`Whisper-large-v3`) and chat response, providing incredibly fast and seamless interaction.
*   **🧩 Modular Workflow:** Built entirely within n8n, allowing for easy modification, debugging, and future expansion.

---

## 🛠️ Tech Stack

*   **Orchestration:** [N8N](https://n8n.io/) - The core platform used to build, manage, and run the entire workflow.
*   **Containerization:** [Docker](https://www.docker.com/) - Used to run the n8n instance in a self-contained, portable environment.
*   **AI Services:** [Groq API](https://groq.com/) - Provides access to blazing-fast Large Language Models (LLMs) for both transcription and chat.
*   **Interface:** [Telegram API](https://core.telegram.org/bots/api) - Serves as the user-facing platform for sending and receiving messages.
*   **Tunneling:** [ngrok](https://ngrok.com/) - Used during development to expose the local Docker container to the public internet, allowing Telegram's webhooks to connect.

---

## ⚙️ How It Works: The Workflow Architecture

The entire process is a linear flow of data managed by the n8n workflow:

1.  **Telegram Trigger:** The workflow starts when a new message is received by the Telegram bot.
2.  **Voice Check:** An IF node checks if the message contains a voice file. If not, it sends a help message back to the user.
3.  **Download & Convert:** The `.oga` voice file is downloaded from Telegram. A code node then correctly re-labels it as a `.ogg` file to ensure compatibility with the transcription API.
4.  **Transcription:** An HTTP Request node sends the audio file to the Groq API's Whisper endpoint, which returns the transcribed text.
5.  **Text Cleaning:** A "Cleaning Agent" (the first LLM call) takes the raw text and refines it into a clean, readable prompt.
6.  **Response Generation:** A "Response Gen" agent (the second LLM call) takes the clean prompt and generates a thoughtful, conversational reply.
7.  **Send Response:** The final response text is sent back to the user on Telegram.

---

## 🚀 Setup & Installation

To run this project yourself, you will need the following prerequisites and to follow these steps.

### Prerequisites

*   [Docker](https://www.docker.com/products/docker-desktop/) and Docker Desktop installed.
*   [ngrok](https://ngrok.com/download) installed and an account with an authentication token.
*   A **Telegram Bot Token** from the `BotFather`.
*   A **GroqCloud API Key**.

### Installation Steps

1.  **Clone the Repository**
    ```bash
    git clone https://github.com/<Your-Username>/<Your-Repo-Name>.git
    cd <Your-Repo-Name>
    ```

2.  **Start the ngrok Tunnel**
    Open a terminal and expose your local port `5678` to the internet.
    ```bash
    ngrok http 5678
    ```
    Copy the public `https` URL ngrok provides (e.g., `https://random-string.ngrok-free.app`).

3.  **Run the N8N Docker Container**
    Open a second terminal. This command starts the n8n container, connects your local data volume, and, most importantly, sets the `WEBHOOK_URL` to your public ngrok address.

    ```bash
    docker run -it --rm --name n8n_voice_bot -p 5678:5678 \
    -e WEBHOOK_URL=<YOUR_NGROK_HTTPS_URL> \
    -v ~/.n8n:/home/node/.n8n \
    n8nio/n8n
    ```
    *Replace `<YOUR_NGROK_HTTPS_URL>` with the URL you copied from ngrok.*

4.  **Import and Configure the Workflow**
    *   Open your browser and navigate to `http://localhost:5678`.
    *   Import the `Voice_Bot.json` workflow file from this repository.
    *   Configure the **Telegram** and **Groq** credentials using your API tokens.
    *   Activate the workflow using the toggle in the top-right corner.

Your bot is now live and ready to use!

---

## 🔧 Troubleshooting: The Webhook Challenge

A key challenge in this project was correctly configuring the webhook for the local Docker environment. Telegram requires a public `HTTPS` URL to send updates to, but a local n8n instance runs on `http://localhost`.

The solution was to use **ngrok** to create a secure tunnel and set the `WEBHOOK_URL` environment variable when starting the Docker container. This forces n8n to register the public ngrok URL with Telegram, solving the issue and allowing the bot to function correctly during development.

---

## 💡 Future Improvements

*   **Implement Conversational Memory:** The next major step is to add a Memory node (e.g., Simple Memory, Redis Memory) to the workflow. This will allow the bot to remember previous turns of the conversation, enabling more natural and context-aware follow-up questions.
*   **Add Voice Responses (Text-to-Speech):** Complete the voice-in, voice-out loop by adding a TTS service (like OpenAI TTS or ElevenLabs) to convert the bot's text response back into an audio file.
*   **Grant Web Search Capabilities:** Integrate a tool like SerpAPI to allow the bot to search the web and answer questions about current events or topics outside of its training data.

---

## 📄 License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
