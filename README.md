e · MDCopyMy Workflow — Telegram AI Media Bot

Automated Telegram bot that receives messages, processes images with AI, generates media, and responds with photos, documents, and text — all orchestrated through a multi-branch n8n workflow.


Overview
This workflow is triggered by incoming Telegram messages and routes them through three parallel processing branches depending on the content type and intent. It leverages OpenAI (image analysis & conversation), Fal.ai (AI image/media generation), and Google Sheets for logging.

Workflow Diagram
Telegram Trigger
      │
      ▼
     [If]
    /    \
  true   false
   │         └──► [Branch 2: AI Image Generation + Document Send]
   ▼
[Branch 1: Image Edit Pipeline]       [Branch 3: Sheet Logging + Multi-model Chat]

Branches
🔵 Branch 1 — Image Edit Pipeline (Top Row)
Handles messages where an image needs to be fetched, analyzed, and edited via AI.
StepNodeDescription1Telegram TriggerListens for incoming Telegram messages (Updates: message)2IfRoutes based on a condition (e.g. message type or content)3Get a fileDownloads the file from Telegram4HTTP Request 1Uploads file to ImgBB (POST https://api.imgbb.com/...)5Analyze Image 1Sends image to OpenAI for analysis6Code in JavaScriptProcesses/transforms the analysis result7Generate media using AI modelCalls Fal.ai Flux edit endpoint (fal-ai/flux-2/edit)8Send a photo message 1Sends generated photo back via Telegram (sendPhoto)9Send a text message 5Sends accompanying text message (sendMessage)10Edit FieldsManually sets/transforms fields for downstream use

🟠 Branch 2 — AI Image Generation + Document Send (Middle Row)
Handles a different path (false branch of the If node), generating new AI media and sending it as a document.
StepNodeDescription1Generate media using AI model 1Calls Fal.ai ByteDance/… to generate media2Message a model 2Sends prompt to an AI model, gets Response Text3Code in JavaScript 4Processes model response4Code in JavaScript 5Further transforms data5HTTP Request 2Makes an external HTTP call6Send a documentSends result as Telegram document (sendDocument)7Create a conversationCreates a new conversation context via OpenAI8HTTP RequestAdditional HTTP call9Send a text message 2Sends final reply text to Telegram (sendMessage)

🟢 Branch 3 — Sheet Logging + Multi-model Chat (Bottom Row)
Handles structured logging to Google Sheets and multi-turn AI conversations.
StepNodeDescription1Edit Fields 1Prepares/maps fields manually2Append row in sheetLogs data to Google Sheets (append: sheet)3Code in JavaScript 1Transforms data for AI input4Message a modelSends to AI model, receives Response Text5Message a model 1Second AI model call, receives Response Text6Code in JavaScript 2Processes combined model outputs7Code in JavaScript 3Final data transformation8Send a text message 3Sends final message to Telegram (outputs 4 items)

Integrations
ServicePurposeTelegram Bot APITrigger & send messages, photos, documentsOpenAIImage analysis, conversation creationFal.aiAI image/media generation (Flux, ByteDance models)ImgBBImage hosting for URLs passed to AI APIsGoogle SheetsLogging conversation or request data

Requirements

n8n instance (self-hosted or cloud)
Telegram Bot Token — create via @BotFather
OpenAI API Key — platform.openai.com
Fal.ai API Key — fal.ai
ImgBB API Key — api.imgbb.com
Google Sheets credentials (OAuth2)


Setup

Import the workflow JSON into your n8n instance.
Configure credentials for each service in n8n Settings → Credentials.
Update the If node condition to match your routing logic.
Set the correct Google Sheet ID in the Append row in sheet node.
Activate the workflow — the Telegram Trigger will start listening immediately.


Notes

The workflow currently has 0 / 3 executions completed and is in Published state.
JavaScript code nodes handle data transformation between API calls — review these if you need to customize the prompt structure or output format.
The bottom branch outputs 4 items to the final send node, meaning it can handle batch responses.


License
MIT — feel free to adapt and share.
