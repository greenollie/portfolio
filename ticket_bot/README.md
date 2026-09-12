# 📋 Feature List & System Documentation
### Unified Discord Ticket Bot & Minecraft Server Integration

---

## 📑 Table of Contents
1. [System Overview](#1-system-overview)
2. [Support Ticket System](#2-support-ticket-system)
3. [Staff Hierarchy & Vision Dashboard](#3-staff-hierarchy--vision-dashboard)
4. [Minecraft Server Integration & Monitoring](#4-minecraft-server-integration--monitoring)
5. [Server Boost System](#5-server-boost-system)
6. [Moderation & Channel Controls](#6-moderation--channel-controls)
7. [Components v2 Message Builder](#7-components-v2-message-builder)
8. [Command Reference Table](#8-command-reference-table)
9. [Configuration Architecture](#9-configuration-architecture)

---

## 1. System Overview
The bot is a unified, production-ready Discord management solution built with `discord.py` 2.0+ that combines:
- An enterprise-grade **multi-tier ticket system** with dynamic modals and HTML transcript archiving.
- A **staff visibility self-service dashboard** with role-hierarchy access controls.
- Real-time **Minecraft Java server monitoring**, dynamic voice channel counters, and automated GitHub Action server waker.
- Automated **server boost celebrations** using Discord's modern Message Layout Components v2.
- A full **moderation and channel locking suite** with centralized audit logging.
- An administrative **Components v2 JSON layout builder** for publishing custom interactive Discord announcements.

---

## 2. Support Ticket System

### 🎫 Interactive Intake Panel
- **Components v2 Layout**: Deployed with Discord's modern Message Layout Components v2 (clean rounded containers and interactive dropdowns) with automatic fallback to standard embeds.
- **Customizable Intake Categories**: Supports unlimited ticket categories defined in `config.yml` with custom labels, descriptions, and emojis (e.g., Minecraft Reports, Discord Reports, General Help, Bug Reports).
- **Persistent Interactions**: Dropdown menus and buttons persist across bot restarts via `setup_hook` persistent views.

### 📝 Dynamic Pop-Up Modal Forms
- **Field Customization**: Categories can optionally launch Discord Modals requesting specific user input (e.g., Target Username, Evidence, Bug Description).
- **Flexible Field Types**: Supports single-line and multi-line paragraph inputs, placeholders, character limits, and optional vs. required flags.
- **Instant Creation**: Categories without modals (e.g., General Inquiries) open tickets immediately upon selection.

### 📁 Channel Management & Routing
- **Per-Category Discord Routing**: Each ticket category automatically generates channels within its own dedicated Discord Category Channel (folder).
- **Smart Channel Naming**: Follows `<rolegroup>-<username>` (e.g., `helper-notch`).
- **Sequential Ticket Numbering**: Tracks concurrent tickets per user; single tickets have no suffix, while concurrent tickets automatically append `-2`, `-3`, etc. (e.g., `helper-notch-2`).
- **Concurrent Ticket Limit**: Enforces a configurable per-user open ticket limit (`max_open_tickets`) to eliminate spam.
- **Stateless Metadata Tracking**: Encodes ticket metadata (Creator ID, Ticket Type, Category Key, Rolegroup, Sequence Number) into the channel topic for seamless recovery and state persistence.

### 👥 Member Management & Escalation
- **Participant Controls**:
  - `/addmember`: Grants a user read/write access to an active ticket.
  - `/removemember`: Removes an added user's permissions from an active ticket.
- **Role Group Escalation (`/forward`)**:
  - Forwards tickets between staff tiers (e.g., `helper` ➔ `mod` ➔ `dev` ➔ `manager` ➔ `owner`).
  - Automatically updates channel permissions: grants read/write access to the target tier while removing previous tier roles.
  - Automatically renames the channel to reflect the new staff prefix (e.g., `helper-notch` ➔ `mod-notch`).
  - Slash command autocomplete shows allowed forwarding destinations defined in `config.yml`.
  - Embed announcement in the channel logs who forwarded the ticket and why.

### 🔒 Closure & HTML Archiving
- **Interactive Close Request (`/closerequest`)**:
  - Prompts the ticket creator with interactive buttons (**Close Ticket** / **Keep Open**).
  - Only the ticket creator and authorized staff can respond.
- **Direct Closure (`/close`)**:
  - Allowed staff roles or Master Roles can immediately close and delete a ticket.
- **Standalone HTML Transcripts**:
  - Compiles the entire ticket chat history into a styled, standalone HTML document (`transcript-<channel>.html`).
  - Displays user badges (`Creator`, `Staff`, `Bot`), formatted timestamps, and hyperlinked attachment previews.
  - Embeds initial modal form submissions (player name, reasons, evidence) at the top of the transcript.
- **Dual Transcript Dispatch**:
  - Posts transcript file and summary embed to the centralized transcript archive channel (`transcript_channel_id`).
  - Automatically direct-messages (DMs) the HTML transcript file directly to the ticket creator.
- **Graceful Deletion**: 5-second countdown before deleting the Discord text channel.

---

## 3. Staff Hierarchy & Vision Dashboard

### 🛡️ Vision Self-Assignment Panel (`/setup_staff_roles`)
- Deploys a persistent button dashboard allowing staff members to toggle visibility for lower support tiers on and off.
- Keeps staff channel lists clean by allowing them to focus only on their active queues.

### ⚖️ Hierarchical Role Security
- **Rank Weights (`rank_weight`)**: Enforces strict tier ordering (e.g., Helper = 1, Mod = 2, Dev = 3, Manager = 4).
- **Down-Tier Visibility Only**: Staff members can only toggle visibility for tiers ranked **below** their highest verified role.
- **Master Role Override**: Master roles bypass tier restrictions and can toggle any staff role.
- **Discord Role Hierarchy Handling**: Catches permission issues and alerts administrators if the bot's role needs to be moved higher in Discord Server Settings.

---

## 4. Minecraft Server Integration & Monitoring

### 🌐 Live Server Monitoring
- **Asynchronous mcstatus Polling**: Periodically queries the Minecraft Java server for online status, player counts, max capacity, and MOTD.
- **Color Code Stripper**: Automatically parses and strips Minecraft legacy formatting codes (`§a`, `§l`) and hex colors (`§x...`), producing clean text for Discord.

### 📊 Real-Time Server Stats Display
- **Dynamic Bot Presence**: Automatically sets bot status to `Playing <server_ip>` when online.
- **Text Channel Status Embed**: Updates an embed in a dedicated channel with live status, player count, MOTD, and connection IP.
- **Voice Channel Stat Counters**:
  - **Player Counter**: Live voice channel name updates (e.g., `MC Players: 15/100` or `MC Players: offline`).
  - **Discord Member Counter**: Live voice channel displaying server member count (e.g., `DC Members: 1,450`).
- **Discord Rate-Limit Protection**: Automatic throttling ensures voice channel renames respect Discord's 2-renames-per-10-minutes API limits.
- **Voice Channel Lock**: Automatically denies `@everyone` the `Connect` permission on startup, locking stat channels as display-only.

### 🚀 Cloud Server Waker (`/start`)
- Integrates with GitHub Actions via GitHub REST API repository dispatch (`wake-server`).
- Dispatches a wake signal using a GitHub Personal Access Token (PAT) to spin up headless runners that start dormant Minecraft servers on-demand.

### 🔍 On-Demand Status Command (`/mcstatus`)
- Allows members and staff to check live Minecraft server status and current Discord member counts at any time.

---

## 5. Server Boost System

### 💖 Dual-Event Boost Detection
- **Member Update Listener**: Detects when a member begins boosting (`premium_since`).
- **Message Listener**: Catches native Discord system boost messages across Tiers 1, 2, and 3.
- **Debounce Cooldown**: 60-second anti-spam debounce window per member prevents duplicate announcements.

### 🎨 Modern Components v2 Announcements
- Renders rich container layouts using Discord Message Layout Components v2:
  - Header section with booster mention and member avatar accessory.
  - Formatted rewards showcase list loaded from `server_config.yml`.
  - Claim instructions directing boosters to open a support ticket.
  - Footer with total guild boost count and server name.
- Automatic fallback to standard Discord embeds if Components v2 is disabled.

### 🧪 Boost Test Command (`/testboostmessage`)
- Allows administrators to preview and test boost announcements in any channel for any member.

---

## 6. Moderation & Channel Controls

### 🧹 Chat Purge Tools
- `/clear [amount]`: Bulk deletes between 1 and 100 recent messages in the current channel.
- `/purgeuser [member] [amount]`: Scans up to 200 messages and purges only messages sent by a specific user.

### 🔒 Channel Locking Controls
- `/lock [reason]`: Instantly revokes `@everyone` `Send Messages` permission with an optional reason.
- `/unlock`: Restores `@everyone` `Send Messages` permission.

### 📜 Centralized Moderation Logging
- Logs all moderation actions (Clear, Purge User, Lock, Unlock) to a designated audit log channel (`mod_log_channel_id`).
- Logs include action type, target user/channel, moderator, reason, and timestamp.

---

## 7. Components v2 Message Builder

### 🛠️ `/sendmessage` / `!makemessage`
- High-level tool for administrators to post custom messages using Discord Message Layout Components v2 (Flags: `32768`).
- **Multi-Part Message Assembly**: Accepts multi-part JSON split across consecutive messages (`parts` parameter) to bypass Discord's 2,000-character message limit.
- **Flexible Input Sources**:
  - Replying directly to a message containing JSON.
  - Reading JSON within markdown code blocks (````json ... ````).
  - Reading `.json` or `.txt` file attachments.
  - Searching recent channel message history by the author.
- **Automatic Cleansing**: Automatically deletes the source JSON message(s) and command trigger for a seamless, clean channel presentation.
- **Role-Gated Security**: Restricted to users holding the configured `makemessage_role_id` or Master Roles.

---

## 8. Command Reference Table

| Command | Type | Permissions Required | Description |
| :--- | :--- | :--- | :--- |
| `/ticket` | Hybrid (Slash & Prefix) | Everyone | Manually opens a new general support ticket. |
| `/addmember [member]` | Hybrid (Slash & Prefix) | Ticket Staff / Master | Adds a user to the current ticket channel. |
| `/removemember [member]` | Hybrid (Slash & Prefix) | Ticket Staff / Master | Removes a user from the current ticket channel. |
| `/forward [rolegroup] [reason]` | Hybrid (Slash & Prefix) | Ticket Staff / Master | Forwards ticket to another tier, updates roles & renames channel. |
| `/closerequest` | Hybrid (Slash & Prefix) | Ticket Staff / Master | Sends confirmation close buttons to the ticket creator. |
| `/close` | Hybrid (Slash & Prefix) | Close Allowed Roles / Master | Closes ticket, generates HTML transcript, and deletes channel. |
| `/setup_tickets` | Hybrid (Slash & Prefix) | Master Roles Only | Deploys the interactive ticket creation dropdown panel. |
| `/setup_staff_roles` | Hybrid (Slash & Prefix) | Master Roles Only | Deploys the staff self-assignment vision dashboard. |
| `/mcstatus` | Hybrid (Slash & Prefix) | Everyone | Displays live Minecraft server status and player counts. |
| `/start` | Hybrid (Slash & Prefix) | Everyone / Staff | Sends GitHub Action dispatch to wake the Minecraft server. |
| `/testboostmessage [member] [chan]`| Hybrid (Slash & Prefix) | Manage Server (`manage_guild`) | Previews booster celebration announcement. |
| `/clear [amount]` | Hybrid (Slash & Prefix) | Manage Messages (`manage_messages`) | Bulk deletes 1–100 messages with audit log. |
| `/purgeuser [member] [amount]` | Hybrid (Slash & Prefix) | Manage Messages (`manage_messages`) | Purges messages from a specific user (up to 200 scanned). |
| `/lock [reason]` | Hybrid (Slash & Prefix) | Manage Channels (`manage_channels`)| Locks the current channel for `@everyone`. |
| `/unlock` | Hybrid (Slash & Prefix) | Manage Channels (`manage_channels`)| Unlocks the current channel for `@everyone`. |
| `/sendmessage` / `!makemessage` | Text Prefix (`os!`, `sk!`) | MakeMessage Role / Master | Compiles and posts Components v2 JSON messages. |

---

## 9. Configuration Architecture

The bot uses a dual-configuration structure to keep ticket logic and server/moderation operations cleanly separated:

```
├── config.yml            # Ticket categories, role groups, staff ranks, modals, GitHub PAT
├── server_config.yml     # Minecraft IP/ports, polling intervals, voice stat counters, boost rewards, mod logs
└── bot.py                # Core bot engine unifying both configurations
```

- **Fallback Resolution**: Functions query settings across both configuration files with domain-specific priority (`pref="ticket"` or `pref="server"`).
- **Command Prefixes**: Supports multiple prefixes simultaneously (e.g., `os!`, `sk!`, `!os`, `!sk`).
- **Instant Guild Sync**: Slash commands sync instantly to the configured `guild_id` on startup with automatic stale global command cleanup.
