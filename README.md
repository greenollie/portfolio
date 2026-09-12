# Developer Portfolio

A showcase of production-ready Discord bots, automation suites, and full-stack tools built for Minecraft networks and gaming communities.

---

## 🚀 Featured Projects

### 1. [MinePeak Staff Bot & Automod Suite](./minepeak_staff_bot/)
An enterprise-grade, multi-process Minecraft automoderation, real-time network chat monitoring, player intelligence, and staff web administration ecosystem.
- **Key Features:** Multi-instance bots across gamemodes (`ops1-3`, `s1-3`, `life1-2`), Cloudflare WARP/SOCKS5 proxy routing, Watchdog process supervisor, FastAPI staff web dashboard with SSE chat streaming, SQLite WAL database, Rapidfuzz player search, and role-based permissions.
- **Tech Stack:** Python 3.10+, `discord.py`, Mineflayer, FastAPI, SQLite (WAL), Rapidfuzz.
- **Explore:** [Browse `minepeak_staff_bot`](./minepeak_staff_bot/)

### 2. [Unified Ticket Bot & Server Integration](./ticket_bot/)
An enterprise support ticket system and community management bot featuring interactive Discord components and Minecraft server telemetry.
- **Key Features:** Interactive intake panels with Discord Components v2, dynamic modal forms, multi-tier staff routing & escalation, standalone HTML transcript archiving with dual dispatch, staff vision self-assignment hierarchy, and real-time Minecraft server status/voice counters.
- **Tech Stack:** Python 3.10+, `discord.py` 2.0+, `mcstatus`, GitHub Actions Rest API.
- **Explore:** [Browse `ticket_bot`](./ticket_bot/)

---

## 📁 Repository Structure

```
.
├── minepeak_staff_bot/     # MinePeak Automod & Staff Management Suite
│   ├── README.md           # In-depth system documentation & architecture diagrams
│   ├── login_screen.png    # Web dashboard login preview
│   └── player_searcher.png # Player directory and dossier search preview
├── ticket_bot/             # Unified Discord Ticket Bot & Server Integration
│   ├── README.md           # Feature breakdown, command table & config specs
│   ├── ticket_creator.png  # Ticket intake panel preview
│   └── custom_modal_example.png # Dynamic pop-up modal preview
└── README.md
```
