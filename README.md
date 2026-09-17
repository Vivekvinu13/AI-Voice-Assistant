# Telegram AI Voice & Text Assistant

## Overview

This n8n workflow creates a Telegram-based AI assistant that accepts
**text and voice messages**, processes them through an AI Agent, and
returns responses as text and audio.

The AI Agent is connected to: - An OpenAI Chat Model - Simple Memory -
Amazon search through SerpApi - Gmail - Calculator

## Workflow Diagram

``` text
Telegram Trigger
       |
       v
     Switch
    /      \
 Voice     Text
   |         |
Get a File  Input_var
   |
STT Recording
   |
audio_var
    \       /
     \     /
     AI Agent
      /   \
     /     \------------------> Telegram (Text Response)
    /
Basic LLM Chain
    |
   TTS
    |
Telegram1 (Audio Response)
```

## Workflow Components

### 1. Telegram Trigger

Receives incoming Telegram updates and starts the workflow. The trigger
supports the two main input types used in this workflow:

-   Text messages
-   Voice messages

### 2. Switch

Routes the incoming message based on its type.

  Input   Route
  ------- ---------------------------------------------------
  Voice   Get a File → STT Recording → audio_var → AI Agent
  Text    Input_var → AI Agent

### 3. Voice Processing

#### Get a File

Downloads or retrieves the audio file associated with the Telegram voice
message.

#### STT Recording

Converts the voice recording into text using speech-to-text processing.

#### audio_var

Stores or prepares the transcribed text before sending it to the AI
Agent.

### 4. Text Processing

#### Input_var

Stores or prepares the text received directly from Telegram so that it
can enter the same AI processing path as transcribed voice input.

### 5. AI Agent

The AI Agent is the central orchestration component. It interprets the
user request, generates a response, and can use connected tools when
required.

Connected resources:

  -----------------------------------------------------------------------
  Resource                            Purpose
  ----------------------------------- -----------------------------------
  OpenAI Chat Model                   Natural-language understanding and
                                      response generation

  Simple Memory                       Maintains conversational context

  Amazon search in SerpApi            Product and Amazon-related searches

  Gmail                               Email-related operations

  Calculator                          Mathematical calculations
  -----------------------------------------------------------------------

### 6. OpenAI Chat Model

Provides the language model used by the AI Agent. The model
configuration, prompts, and credentials should be maintained in n8n.

### 7. Simple Memory

Maintains relevant conversation context to support follow-up questions
and more consistent responses.

> Memory persistence and retention depend on the configuration of the
> n8n memory node.

### 8. Amazon Search in SerpApi

Provides Amazon search capabilities. Possible use cases include:

-   Searching for products
-   Retrieving product information
-   Supporting shopping-related questions
-   Comparing available product results

SerpApi credentials must be configured in n8n.

### 9. Gmail

Connects the AI Agent to Gmail-related functionality. Depending on its
configuration, it may support operations such as drafting or sending
email messages.

Use appropriate OAuth permissions and consider adding confirmation
before sending emails.

### 10. Calculator

Allows the AI Agent to perform calculations and numerical operations.

### 11. Telegram Text Response

The `Telegram` node sends the AI Agent's response back to the user as a
text message.

### 12. Basic LLM Chain

The `Basic LLM Chain` processes or formats the AI Agent's response
before it is passed to text-to-speech.

Possible responsibilities include:

-   Formatting the response
-   Preparing text for audio generation
-   Applying a speech-specific prompt
-   Removing content that should not be spoken aloud

### 13. Text-to-Speech

The `TTS` node converts the prepared response text into audio.

Typical configuration options include:

-   Voice
-   Language
-   Speech speed
-   Audio format
-   Text input mapping

### 14. Telegram Audio Response

The `Telegram1` node sends the generated audio back to the user through
Telegram.

## End-to-End Flow

### Text Message

1.  The user sends a text message to the Telegram bot.
2.  Telegram Trigger receives the update.
3.  Switch routes the message to `Input_var`.
4.  The AI Agent processes the message.
5.  The AI Agent uses the language model and tools when necessary.
6.  A text response is sent through the Telegram node.
7.  The response can also be processed by the Basic LLM Chain and TTS
    nodes to create an audio response.

### Voice Message

1.  The user sends a voice message to the Telegram bot.
2.  Telegram Trigger receives the update.
3.  Switch routes the message to `Get a File`.
4.  The audio file is retrieved.
5.  STT Recording converts the audio to text.
6.  `audio_var` prepares the transcription.
7.  The AI Agent processes the transcribed request.
8.  A text response is sent through Telegram.
9.  The response is passed to the Basic LLM Chain.
10. TTS converts the response into audio.
11. `Telegram1` sends the audio response to the user.

## Required Integrations

  Integration        Purpose
  ------------------ -------------------------------------------------------
  Telegram Bot API   Receive and send messages
  OpenAI             Chat, speech-to-text, and text-to-speech capabilities
  SerpApi            Amazon product search
  Gmail OAuth        Gmail operations
  n8n                Workflow orchestration

## Configuration Checklist

-   [ ] Configure Telegram bot credentials.
-   [ ] Confirm that Telegram Trigger receives messages.
-   [ ] Configure Switch rules for text and voice messages.
-   [ ] Configure Telegram file retrieval.
-   [ ] Configure speech-to-text credentials and audio mapping.
-   [ ] Connect the OpenAI Chat Model to the AI Agent.
-   [ ] Configure Simple Memory.
-   [ ] Configure SerpApi credentials if product search is required.
-   [ ] Configure Gmail OAuth permissions if email operations are
    enabled.
-   [ ] Configure the Calculator tool.
-   [ ] Configure the Basic LLM Chain prompt.
-   [ ] Configure TTS settings and audio mapping.
-   [ ] Test text and audio responses in Telegram.
-   [ ] Add error handling and monitoring.

## Security Considerations

-   Store API keys and OAuth credentials in n8n credentials.
-   Do not hardcode secrets in nodes, scripts, or exported workflow
    files.
-   Use least-privilege permissions for Gmail.
-   Add confirmation before sensitive actions, especially sending
    emails.
-   Validate user input before calling external tools.
-   Monitor API usage, failures, and unexpected tool calls.
-   Review AI Agent tool permissions before production deployment.

## Testing Recommendations

  Test                      Expected result
  ------------------------- -----------------------------------------------------
  Text message              The AI Agent returns a response
  Voice message             Audio is retrieved and transcribed
  Invalid audio             A controlled error is returned
  Product search            SerpApi is called when appropriate
  Calculation               The Calculator returns the correct result
  Gmail action              Permissions and confirmation work correctly
  Follow-up question        Memory preserves relevant context
  TTS generation            Audio is generated successfully
  Telegram audio delivery   The user receives the audio
  API failure               The workflow logs the issue and provides a fallback

## Suggested Enhancements

-   Add centralized error handling and retry logic.
-   Add structured logging and execution monitoring.
-   Use persistent conversation storage when required.
-   Add user authentication or access controls.
-   Add confirmation for external or sensitive actions.
-   Add rate limiting and usage monitoring.
-   Support multiple languages.
-   Track latency, error rate, tool usage, and user adoption.
-   Separate development, test, and production credentials.
-   Add regression tests for the text and voice paths.

## Summary

This workflow combines **Telegram, speech processing, AI Agent
orchestration, external tools, memory, and text-to-speech** to create a
multimodal personal assistant.

Users can send text or voice messages, receive AI-generated answers, and
use additional capabilities such as product search, Gmail operations,
and calculations.
