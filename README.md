# Carina

Carina is a karate themed Telegram mini app for friendly play, score tracking, and group games

![Carina idle screen](https://ahura.site/carina/assets/images/karate-idle.png)

![Carina action screen](https://ahura.site/carina/assets/images/karate-attack.png)

## Bot identity

- Telegram bot: [@CarinaPls_bot](https://t.me/CarinaPls_bot)
- Telegram bot ID: `8994152935`
- Mini app: [ahura.site/carina](https://ahura.site/carina)

## What it does

- Opens the game inside Telegram from the bot
- Runs a compact karate game in the browser using WebAssembly
- Tracks player profiles, scores, attempts, and daily progress
- Shows player rankings and rival information
- Provides sponsor offers and lets players claim available sponsor rewards
- Supports referral reward claims
- Presents game rules and records acceptance
- Starts friendly group challenges
- Offers game shortcuts through inline queries
- Posts a game launcher when the bot is added to a group
- Responds to the Carina name and the Persian name کارینا in groups

Practice opponents, when enabled, are simulated records for gameplay and do not represent live Telegram users

## Telegram commands

- `/start` opens the welcome message with the game button and group add option
- `/admin` privately opens the admin panel for authorized admins
- `/startgame` lets an authorized admin start a group challenge
- `/ttt` and `/dooz` start a Tic Tac Toe group game
- `/connect4` and `/c4` start a Connect Four group game

## Admin panel

Authorized admins can manage player records, update names and scores, remove or restore users, and change sponsor channels

## Backend implementation

- Python backend using the standard library `ThreadingHTTPServer` and JSON API handlers
- Telegram Mini App identity checked by validating signed `initData` with HMAC SHA-256 and its authentication timestamp
- Admin endpoints and restricted bot commands authorized against configured Telegram user IDs
- Telegram webhook requests checked with a configured secret token using constant time comparison
- Webhook delivery used when a public URL is configured, with long polling as the fallback
- Per-user and per-endpoint token bucket rate limits, plus throttling for group keyword launches
- Game sessions use server-issued nonces and score submissions are checked server side to limit forged or replayed results
- Player state kept in memory and saved to versioned JSON files, with locks protecting concurrent reads and mutations
- A background writer coalesces updates and persists them through temporary files, file sync, and atomic replacement
- Timestamped backup files provide recovery when the active state file cannot be read
- Admin and gameplay events are recorded in a separate append only JSON Lines audit log
- Telegram Bot API requests reuse pooled HTTP connections
- Protected deployments can serve encrypted game and interface bundles with their key supplied through the bootstrap API
- A health endpoint reports service and game window status

## Repository contents

This public repository contains this README only

The application source and deployment configuration are maintained separately
