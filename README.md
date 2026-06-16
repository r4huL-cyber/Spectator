# Spectator

> Advanced in-browser API & endpoint intelligence tool. Passive traffic monitoring, security scanning, request replaying, and OpenAPI generation in a sleek, high-contrast interface.

Spectator is a lightweight, Chrome Side Panel extension designed for web developers, QA engineers, and security researchers. By intercepting network traffic in real-time directly within the browser, it removes the complexity of setting up heavy Man-in-the-Middle (MITM) proxies (like Burp Suite or Charles Proxy) for API exploration.

---

## Key Features

*   **Real-Time Traffic Interception**: Seamlessly intercept and log outgoing HTTP/HTTPS traffic (XHR and Fetch) without proxy configurations.
*   **Automated Endpoint Discovery**: Aggregates raw traffic logs into unique backend API routes, displaying hit frequencies and differentiating standard REST paths from GraphQL operations.
*   **Passive Security Scanner**: Scans responses in the background for common misconfigurations and exposures (e.g., exposed JWT tokens, AWS/Google/Firebase API keys, and insecure CORS headers).
*   **Live WebSocket Monitor**: Hooks into native WebSockets, logs active connection lifecycles, and streams incoming/outgoing message frames in real-time.
*   **Request Replayer**: Edit URLs, headers, methods, or request bodies, and replay HTTP queries directly from your sidebar context—no need to switch to Postman.
*   **OpenAPI 3.0 Export**: Automatically reverse-engineers captured traffic to draft a fully compliant OpenAPI 3.0 specification. View a live preview and download the output JSON.
*   **Session Data Export**: Export the entire captured dataset (requests, endpoints, findings, and socket frames) to a single portable JSON file.

---

## Architecture

Spectator uses a secure, decoupled three-layer system to operate inside Google Chrome:

```
[ Web Page DOM (Main World) ]
      │ (Intercepts fetch/XHR & WebSocket constructors)
      ▼
[ Content Script Bridge (Isolated World) ]
      │ (Receives event payload safely)
      ▼
[ Background Service Worker ] ──(Saves to local storage)──► [ chrome.storage.local ]
                                                                   │
                                                                   ▼
                                                         [ React UI Side Panel ]
```

1.  **Main World Interceptor (interceptor.js)**: Injected directly into the DOM context. Overrides native `fetch` and `XMLHttpRequest` objects, as well as the `WebSocket` constructor, to intercept request/response payloads at the root level.
2.  **Content Script Bridge (content.js)**: Listens to custom events from the main world interceptor and securely routes them to the extension's background process.
3.  **Background Service Worker (background.js)**: Executes passive security scans and commits structured logs to `chrome.storage.local`.
4.  **React Frontend Side Panel (UI)**: Listens for storage updates in real-time, displaying traffic data in a sleek, custom-designed dark interface.

---

## Tech Stack

*   **Core**: React 19, Vite, TypeScript
*   **Styling**: Tailwind CSS & Vanilla CSS (high-contrast black, white, and cyan theme)
*   **Animations**: Framer Motion (for transitions and micro-interactions)
*   **Database**: IndexedDB (via `idb`) / `chrome.storage` for persistent logs
*   **Icons**: Lucide React

---

## Getting Started

### Prerequisites

*   Node.js (v18.0 or higher)
*   npm (v9.0 or higher)

### Setup & Local Development

1. Clone the repository:
   ```bash
   git clone https://github.com/r4huL-cyber/spectator.git
   cd spectator
   ```

2. Install dependencies:
   ```bash
   npm install
   ```

3. Start the Vite development server (with hot module replacement for UI prototyping):
   ```bash
   npm run dev
   ```

4. Build the extension:
   ```bash
   npm run build
   ```
   This generates the production-ready assets inside the **dist** directory.

---

## How to Load the Extension in Chrome

To run Spectator in your browser using the local build:

1. Open Google Chrome and go to **chrome://extensions/**.
2. Turn on **Developer mode** using the toggle switch in the top-right corner.
3. Click on **Load unpacked** in the top-left corner.
4. Select the **dist** folder inside your project directory:
   `path/to/spectator/dist`
5. The extension is now active! Click the **Extensions** puzzle icon in your toolbar, click **Spectator**, and the side panel will open on the right.

---


## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
