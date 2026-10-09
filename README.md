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
- Shares live score progress in Telegram groups while a game is running
- Replays game actions on the server to calculate the recorded score
- Detects suspiciously fast or unusually regular tap timing
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

## Backend techniques in the carina-fast source

- Python 3 backend built with the standard library `ThreadingHTTPServer` and JSON API handlers, with no third party Python runtime dependencies
- Go 1.24 game engine compiled for browser WebAssembly and gzip compressed, with matching Python replay simulation for authoritative score calculation
- Telegram Mini App `initData` signature verified with HMAC SHA-256 and a freshness check on its authentication timestamp
- Game sessions use HMAC signed tokens containing the player ID, issue time, random nonce, and random engine seed, and expire after 20 minutes
- Constant time signature comparison and one use session nonces help reject forged and replayed score submissions
- The server replays each game from its seed, validates action order and timing, and calculates the score instead of trusting a browser supplied total
- Statistical tap rate and timing regularity checks flag or block suspicious runs, with optional strike based automatic bans
- Admin API calls require the configured admin secret, while restricted group commands check configured Telegram admin IDs
- Telegram webhook calls require a secret header checked with constant time comparison, with long polling used when no public URL is configured
- JSON request bodies are size limited and checked for valid object structure
- Token bucket limits apply by IP address, player, and API route, with additional cooldowns for group launches and Telegram message edits
- The threaded HTTP server uses bounded worker pools for Telegram updates and live group score updates
- Live game progress is kept briefly in memory by session nonce, protected by a lock, and sent to group messages asynchronously
- Telegram Bot API calls reuse per thread HTTPS keep alive connections and retry once after a stale connection
- Player state is held in memory and persisted to versioned JSON files with locks, coalesced background writes, file sync, and atomic replacement
- A backup copy allows recovery if the active JSON file cannot be read, while admin and gameplay events go to a separate append only JSON Lines audit log
- Static file paths are URL decoded and resolved under the public asset directory, dot paths are rejected, and protected mode blocks the plaintext game files
- Static responses use gzip compression, ETags, conditional cache responses, a content security policy, `nosniff`, and a no referrer policy
- JSON request bodies are capped at 2 MB, malformed or non object payloads are rejected, and error responses avoid returning internal exception details
- The build tool protects game and interface bundles with AES 256 GCM, fresh random nonces, and asset names as authenticated data, then serves the key only to verified Telegram users through the boot API
- Docker runs the Python service as a non root user, keeps data in a persistent volume, exposes a health check, and flushes state on graceful shutdown
- Go engine tests, Python unit tests, replay checks, and WebAssembly synchronization tests are included in the source archive

## Repository contents

This public repository contains this README only

The application source and deployment configuration are maintained separately
