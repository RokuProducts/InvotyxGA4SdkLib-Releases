# Google Analytics 4 (GA4) SDK (BrightScript / SceneGraph Component Library)
The **Invotyx GA4 SDK** provides a lightweight, efficient, and extensible interface for integrating **Google Analytics 4 (GA4)** into **Roku SceneGraph** applications. It enables Roku developers to collect analytics events, manage session data, and send structured telemetry directly to the **GA4 Measurement Protocol** endpoint, all while following Roku platform best practices.

## 🧩 Overview
This SDK is designed to act as a modular analytics layer for Roku apps. It abstracts the complexity of manually creating GA4 event payloads and handling network communication, while maintaining flexibility for developers who require full control over analytics data flow.
The SDK can be distributed as a **Roku ComponentLibrary**, allowing seamless integration into any SceneGraph project. Developers can extend the SDK to track screen views, user sessions, engagement duration, ad interactions, or custom business-specific events.


## 🧠 Core Features

### 1. **GA4 Event Management**
- Handles the construction and dispatching of valid GA4-compliant events.
- Automatically formats parameters such as timestamps, client IDs, app version, and device context.
- Supports custom event types and additional parameters as defined in GA4 Measurement Protocol documentation.

### 2. **Session Tracking**
- Maintains a lightweight session model to represent user activity periods.
- Tracks first-time visits (`first_visit`) and app openings (`first_open`).
- Periodically updates session-based events (`user_engagement`, `app_time`).

### 3. **Offline Event Queue**
- Implements an internal buffering system to safely queue events when a network connection is unavailable.
- Ensures that events are sent once connectivity is restored, minimizing data loss.

### 4. **Event Batching**
- Supports batching multiple analytics events into a single network request, improving performance and minimizing bandwidth usage.

### 5. **Custom Parameters**
- Allows extension of default payloads with user-defined parameters such as device info, playback details, or in-app navigation context.

### 6. **Asynchronous Networking**
- Uses Roku’s asynchronous task model (`roSGNode: Task`) to send events without blocking UI or user interactions.
- Provides internal retry logic for transient network failures.

## Requirements
- Roku device (SceneGraph, OS 9.4+ recommended)
- GA4 property and a Data Stream (web/app). You must create a Measurement Protocol API secret in GA UI for the data stream and Token from Invotyx owner.

## Usage Guide
This component encapsulates the entire logic for interacting with the GA4 Measurement Protocol using Roku’s asynchronous networking and task execution model.

### Step 1: Initialization Flow
We can initialize the GA4 SDK in both ways i.e. in brightscript & in scenegraph XML
- brightscript initialization
```brs
m.global.AddField("ga4", "node", false)
m.componentLib = CreateObject("roSGNode", "ComponentLibrary")
m.componentLib.id="componentLib"
m.componentLib.uri="https://github.com/RokuProducts/InvotyxGA4SdkLib-Releases/releases/download/v1.0.1/InvotyxGA4SdkLib_11_03.pkg"
```
- scenegraph XML initialization
```xml
<ComponentLibrary
  id="componentLib"
  uri="https://github.com/hmDanish/ComponentLibrary/releases/latest/download/roku-deploy.zip"
/>
```

### Step 2: Observe loadStatus
After initialization, observe "componentLib" in brs:
```brs
m.componentLib.observeField("loadStatus", "onLoadStatus")
```
If loadStatus is ready then call **initialize()** function form SDK and then **start()**.
```brs
sub onLoadStatus()
    if m.componentLib.loadStatus = "ready"
        m.global.ga4 = CreateObject("roSGNode", "InvotyxGA4SdkLib:GoogleAnalytics")
        m.global.ga4.callFunc("initialize", {
            Token: "YOUR_TOKEN_KEY",
            measurementId: "G-XXXXXXXXXXX",
            appName: "YOUR_APP_NAME",
            customArgs: { debug_mode: false }
        })
        m.global.ga4.callFunc("start")
    else
      ' write your alternative flow
    end if
end sub
```
### Step 3: Log GA4 Events
Now you can simply log GA4 custom event by using the following snipped:
- m.global.ga4.callFunc("logEvent", eventName, params)
```brs
m.global.ga4.callFunc("logEvent", "select_content", { 
    content_type: "video", 
    item_id: "ad_12345" 
})
```
- output
```
[ANALYTICS] POST https://analytics.google.com/g/collect?...&en=select_content...
```



## 🧾 Licensing
This SDK is developed and maintained by **Invotyx Software Company**.  
All rights reserved. Redistribution or modification is subject to the terms defined in the associated license file.

