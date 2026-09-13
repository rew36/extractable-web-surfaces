# Extractable Web Surfaces

**A proposal for user-driven extraction of web components into persistent operating-system objects.**

**Concept by REW**  
**Initial public proposal: 13 September 2026**  
**Version: 0.1**

---

## Abstract

Modern web applications can be highly capable, but their interfaces are still usually confined to browser tabs or windows.

A user may open Synology DSM, Proxmox, Telegram, YouTube, Google Drive, a smart-home dashboard, or another complex web application, yet useful parts of those systems — a file manager, chat, monitoring graph, event log, media player, or control panel — remain trapped inside the browser.

Progressive Web Apps partially address this by allowing an entire website to behave more like a desktop application.

This proposal explores a different model:

> **Web applications may expose individual interface components as Extractable Web Surfaces — components that users can explicitly detach from the browser and promote into persistent operating-system objects.**

An extracted surface would continue to execute inside the browser's security environment, while visually and functionally becoming part of the operating system.

The goal is not to remove the security boundary between Web and OS.

The goal is to remove the **interface boundary**, while preserving the **security boundary**.

---

## The Problem

Today, one service may require multiple separate applications:

```text
Web application
Windows application
macOS application
Linux application
Android application
iOS application
```

Each version must be developed, tested, maintained, updated, and supported separately.

This is especially visible on Linux.

A vendor may support Ubuntu but not Fedora, Debian, Arch Linux, or another distribution, even though the service already has a fully functional browser-based interface.

For many applications, the Web version already contains most of the required functionality.

This raises a simple question:

> **Why should a web-based service need a separate native client for every operating system if its functionality already exists on the Web?**

---

## Core Idea

A website should be able to declare selected interface components as **extractable surfaces**.

For example:

```text
Synology DSM
│
├── File Station
├── Drive
├── Download Station
├── Storage Monitor
└── System Widgets
```

The developer may mark some of these components as extractable.

The user could then drag **Synology Drive** out of the browser.

The operating system might ask:

```text
Create "Synology Drive" as an application?

[ Cancel ] [ Create ]
```

After confirmation, the surface becomes a persistent OS object:

- it receives an application icon;
- it can be launched from the desktop environment;
- it can have its own window;
- it may receive notifications;
- it may request limited filesystem access;
- it may perform permitted background work.

However, the application still runs through the browser runtime and remains inside the browser's security model.

The browser stops being a mandatory visual container.

Instead, it becomes the runtime.

---

## Example: Synology Drive

Today, a desktop synchronization client may need separate support for different operating systems and Linux distributions.

With Extractable Web Surfaces, Synology could expose its Drive interface as an extractable component.

The user could:

```text
Open Synology DSM
        ↓
Select Synology Drive
        ↓
Drag it outside the browser
        ↓
Create application
        ↓
Grant access to a local directory
        ↓
Use Synology Drive as an OS application
```

The application could request access only to:

```text
/home/user/Documents
```

instead of receiving unrestricted access to the entire system.

---

## Capability-Based Permissions

Extracting a web surface should **not** automatically grant additional system privileges.

Permissions should be granted separately and explicitly.

For example:

```text
Synology Drive requests:

Filesystem
/home/user/Documents

[✓] Read
[✓] Write
[✓] Watch for changes

[✓] Notifications
[✓] Background execution
[ ] Camera
[ ] Microphone
```

The extracted surface sees only the capabilities granted by the user.

This creates a simple principle:

> **Integration with the operating system should be based on explicit capabilities, not unrestricted application access.**

A web application may become more integrated with the desktop without becoming less isolated.

---

## Not Only Whole Applications

The most important part of this proposal is that an extractable surface does not need to represent an entire application.

It may represent only one useful part of a larger web system.

For example, Proxmox could expose:

```text
CPU / RAM graph   → desktop widget

VM list           → standalone window

Event log         → desktop panel

VM console        → separate application window
```

A user may not want the entire Proxmox interface permanently open.

They may only want the CPU graph on one desktop and the event log on another.

The operating system becomes something the user can compose from useful pieces of different web applications.

---

## More Examples

### Messaging

A web messaging service could expose:

```text
Conversation       → standalone window
Contact list       → desktop panel
Notifications      → system notifications
```

A user could extract a specific conversation without installing a separate desktop client.

### Media

A media service could expose:

```text
Player             → floating window
Playback controls  → system panel
Queue              → standalone surface
```

### Smart Home

A smart-home interface could expose:

```text
Climate            → desktop widget
Security status    → system panel
Lighting controls  → compact surface
Event log          → standalone window
```

### Monitoring

A server-management system could expose:

```text
CPU graph          → widget
System health      → panel indicator
Alerts             → notification source
Logs               → separate window
```

---

## Difference from PWA

A Progressive Web App generally follows this model:

```text
Website
   ↓
Install entire website as an application
```

Extractable Web Surfaces propose something different:

```text
Website
│
├── Surface A → remains inside the website
├── Surface B → standalone application
├── Surface C → desktop widget
├── Surface D → system panel
└── Surface E → floating window
```

The key difference is granularity.

The user does not have to install an entire website.

The user can promote only the parts that are useful outside the browser.

This is not primarily about making websites look like native applications.

It is about allowing parts of the Web to become first-class objects of the operating system.

---

## The Browser as a Runtime

In this model, the browser becomes less visible.

Instead of being only a window containing tabs, it also becomes a shared execution environment for web-based OS objects.

```text
                     WEB

   Synology   Telegram   YouTube   Proxmox
       \         |          |         /
        \        |          |        /
         ───── Browser Runtime ─────
                     │
                Permissions
                     │
        ┌────────────┼────────────┐
        │            │            │
      Files     Notifications    Windows
        │            │            │
        └──── Operating System ───┘
```

The browser can continue to provide:

- JavaScript execution;
- networking;
- authentication;
- cookies;
- storage;
- sandboxing;
- permissions;
- updates;
- isolation.

But the user interface no longer has to remain inside a traditional browser window.

---

## User Control

A web application must never be able to extract itself automatically.

The website may only declare:

> "This component supports extraction."

The action must always be initiated or explicitly approved by the user.

```text
Not:

Website
   ↓
"I want to become a desktop application"
   ↓
Operating System
```

Instead:

```text
Web component
      ↓
User extracts or installs it
      ↓
Browser / OS confirmation
      ↓
Capability permissions
      ↓
Persistent OS object
```

This distinction is essential for security and trust.

---

## Possible Surface Types

An Extractable Web Surface could potentially become different kinds of operating-system objects:

```text
application window
desktop widget
floating window
system panel item
notification source
background service
file handler
media control surface
```

The website would declare which surface types it supports.

The operating system would decide how those surface types are represented on that platform.

---

## Platform Independence

A major benefit of this model is that developers would no longer need to build every web-connected application separately for every operating system.

Instead of:

```text
Windows client
macOS client
Ubuntu client
Fedora client
Android client
iOS client
```

a service could provide:

```text
One web implementation
        +
Extractable Surfaces
        +
Capability declarations
```

Each operating system or desktop environment could integrate those surfaces according to its own conventions.

The application remains portable because the browser runtime provides the common execution environment.

---

## This Does Not Replace Native Software

This proposal does not suggest that all software should become web software.

Many applications still benefit from direct native access:

- CAD software;
- games;
- professional video editing;
- hardware drivers;
- low-level system utilities;
- software requiring specialized performance or hardware access.

Extractable Web Surfaces are aimed primarily at software whose core functionality already exists on the Web.

Examples include:

- cloud storage;
- messaging;
- social platforms;
- monitoring dashboards;
- NAS interfaces;
- smart-home systems;
- music services;
- email;
- CRM systems;
- administration panels;
- collaboration tools.

---

## Design Principle

The central principle of this proposal is:

> **Remove the interface boundary between Web and OS while preserving the security boundary.**

The Web should not need to remain visually trapped inside a browser window.

At the same time, web content should not receive uncontrolled access to the operating system.

The connection between the two should be explicit, user-driven, permission-based, and reversible.

---

## Long-Term Possibility

If such a model became widely supported, websites themselves might evolve.

Instead of designing only pages, developers could begin designing reusable surfaces:

```text
Website
│
├── chat surface
├── file surface
├── media surface
├── monitoring surface
├── control surface
└── notification surface
```

Users could then build their own working environment from the components they actually need.

The distinction between "website" and "desktop application" would become less important.

What matters would be the function, the permissions granted to it, and where the user chooses to place it.

---

## Summary

**Extractable Web Surfaces** proposes a user-controlled model where individual components of web applications can become persistent operating-system objects.

The browser remains responsible for execution and isolation.

The operating system remains responsible for integration and permissions.

The user remains responsible for deciding what crosses the interface boundary.

In short:

> **Do not turn websites into applications.  
> Let useful parts of websites become operating-system objects.**

---

## Status

This document is an early concept proposal intended for discussion and exploration.

It does not define a complete technical standard or API.

Questions that still require exploration include:

- surface lifecycle;
- background execution;
- filesystem capabilities;
- authentication persistence;
- cross-platform representation;
- browser / compositor communication;
- permission revocation;
- offline behavior;
- resource limits;
- phishing and impersonation prevention;
- interaction between multiple extracted surfaces;
- synchronization with the original web application.

---

## Author

**Concept: REW**

Initial public proposal: **September 2026**

GitHub: **rew36**

---

## Version History

### 0.1 — September 2026

Initial public concept.
