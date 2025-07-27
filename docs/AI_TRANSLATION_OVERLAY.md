# AI Translation Overlay and Integration Plan

This document outlines a high-level plan for integrating two sets of features into FlorisBoard:

1. **Screen Translation Overlay** – using an Accessibility Service to read on-screen text in chat applications, detect its language, translate it into the user's language via a selectable model, and overlay the translated text.
2. **AI Keyboard Functions** – allowing prediction and translation via external AI providers.

## 1. Screen Translation Overlay

### Overview
- Implement a new `AccessibilityService` that listens to `TYPE_VIEW_TEXT_CHANGED` and related events.
- When chat applications are in the foreground (e.g., WhatsApp, Messenger, Telegram, Outlook, X), capture visible text nodes.
- For text not matching the user's language, send the content to the selected translation model via HTTP API.
- Use floating overlay windows to display translated text above the original content. Update overlays when the user scrolls or the screen changes.

### Key Steps
1. **Accessibility Permission** – Add a service entry in the manifest and request the permission.
2. **Context Detection** – Determine which chat app is active, parse conversation history to send with each translation request.
3. **Language Handling** – Detect source language using the API or built-in detection. Allow the user to set a target language.
4. **Overlay Management** – Use the `WindowManager` overlay type for drawing on top of other apps. Keep track of positions of text nodes to overlay translated text.
5. **Scrolling Support** – Watch for `TYPE_WINDOW_CONTENT_CHANGED` to detect new messages and request translations for any new unseen text.

### Open Questions
- Performance impact and rate limits of translation APIs.
- Handling privacy concerns when reading sensitive data from other apps.

## 2. AI Keyboard Functions

### New Preferences
Add settings screens with the following options:
1. **AI Service Provider** – choose between OpenAI or Grok.
2. **API Key** – store securely using Android's keystore.
3. **Model Selection** – query the provider for available models and allow the user to select one.
4. **Test Button** – verify the selected provider, key, and model.

### Prediction Feature
- When the user pauses typing for ~1.5 s, send the composing text and recent context to the AI provider for next-word or next-phrase prediction.
- Display the predicted word in the suggestion strip. Accepting a suggestion sends it to the editor.

### Translation Input Field
- Similar to Gboard's translation feature. Provide a secondary input field that translates text via the chosen AI model every 1.5 s pause.
- The main input field is updated with the translation output.

### On‑Demand Translation
- Add a “Translate” button in the Smartbar: when pressed, prompt the user for source and target languages (auto‑detect source by default).
- Send the text along with chat context to the AI model, requesting translation while preserving tone and style.

### Implementation Pointers
- Extend `NlpManager` with a new provider type that forwards requests to the AI service.
- Create Kotlin wrappers around the provider APIs (REST clients).
- Reuse existing suggestion and Smartbar infrastructure for displaying predictions and translation results.

## 3. Next Steps
- Prototype the Accessibility Service to evaluate feasibility.
- Design UI mockups for the new settings fields and overlay interface.
- Investigate API costs and ensure proper error handling and privacy safeguards.

