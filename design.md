# Design Document

## Overview

Implementasi sistem Auth UI berbasis textdraw fullscreen untuk menggantikan dialog native SA-MP. Arsitektur menggunakan state machine pattern dimana setiap layar (Login, Verify, CharSelect, CharCreate) adalah state terpisah dengan transisi yang jelas. Textdraw dibagi menjadi Global (elemen statis: background, header, decorations) dan Player (elemen dinamis: nama UCP, counter, karakter list). Input teks tetap menggunakan ShowPlayerDialog yang di-trigger oleh klik textdraw button.

## Architecture

### State Machine Design

```
[CONNECT] → OnPlayerDataLoaded
    ├── Unregistered → Kick (no change)
    ├── Unverified (password < 1) → AUTH_STATE_VERIFY
    ├── Registered (password > 10) → AUTH_STATE_LOGIN
    └── Banned → AUTH_STATE_BANNED

AUTH_STATE_LOGIN:
    ├── Click "Login" → Show Input_Dialog (password)
    │   ├── Correct → AUTH_STATE_CHARSELECT
    │   └── Wrong → Update attempt counter (max 3 → Kick)
    └── Click "Quit" → Kick

AUTH_STATE_VERIFY:
    ├── Click "Input PIN" → Show Input_Dialog (PIN)
    └── Click "Cancel" → Kick

AUTH_STATE_CHARSELECT:
    ├── Click Character[i] → Load & Spawn
    ├── Click "Create Character" → AUTH_STATE_CHARCREATE_NAME
    └── Click "Quit" → Kick

AUTH_STATE_CHARCREATE_NAME:
    ├── Click "Input Name" → Show Input_Dialog (name)
    │   ├── Valid → AUTH_STATE_CHARCREATE_AGE
    │   └── Invalid → Show error, re-show dialog
    └── Click "Back" → AUTH_STATE_CHARSELECT

AUTH_STATE_CHARCREATE_AGE:
    ├── Click "Set Birthday" → Show Input_Dialog (date)
    │   ├── Valid → AUTH_STATE_CHARCREATE_GENDER
    │   └── Invalid → Show error, re-show dialog
    └── Click "Back" → AUTH_STATE_CHARSELECT

AUTH_STATE_CHARCREATE_GENDER:
    ├── Click "Male" → Assign gender → Skin selection → Spawn
    ├── Click "Female" → Assign gender → Skin selection → Spawn
    └── Click "Back" → AUTH_STATE_CHARSELECT

AUTH_STATE_BANNED:
    └── Click "Exit" → Kick
```

### File Structure

```
gamemodes/Modules/Auth/
├── AuthUI_Header.pwn      — Enums, constants, variable declarations
├── AuthUI_Textdraws.pwn   — Textdraw creation/destruction functions
├── AuthUI_Screens.pwn     — Show/Hide functions for each screen state
├── AuthUI_Callbacks.pwn   — OnPlayerClickPlayerTextDraw, dialog response hooks
└── AuthUI_Utils.pwn       — Helper functions (color formatting, cleanup)
```

### Textdraw Layout Design (Screen Coordinates)

```
┌─────────────────────────────────────────────────────────────────┐ 0,0
│  FULLSCREEN BACKGROUND (0x000000CC) - Global TD                 │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │  OUTER BORDER PANEL (0x003300AA) - Global TD              │  │ ~20,20
│  │  ┌─────────────────────────────────────────────────────┐  │  │
│  │  │                                                     │  │  │ ~30,30
│  │  │         ╔═══════════════════════════╗               │  │  │
│  │  │         ║   DJAVA ROLEPLAY          ║  (green,f1)   │  │  │ Y:40
│  │  │         ╚═══════════════════════════╝               │  │  │
│  │  │         ─────────────────────────────  (green line) │  │  │ Y:70
│  │  │                                                     │  │  │
│  │  │         ┌─────────────────────────┐                 │  │  │
│  │  │         │   CONTENT AREA          │                 │  │  │ Y:90
│  │  │         │   (per-screen content)  │                 │  │  │
│  │  │         │                         │                 │  │  │
│  │  │         │   [BUTTON 1]            │                 │  │  │ Y:280
│  │  │         │   [BUTTON 2]            │                 │  │  │ Y:320
│  │  │         └─────────────────────────┘                 │  │  │
│  │  │                                                     │  │  │
│  │  └─────────────────────────────────────────────────────┘  │  │
│  └───────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘ 640,448
```

### Color Constants

```pawn
#define AUTH_COLOR_BG           0x000000CC  // Fullscreen background (semi-transparent black)
#define AUTH_COLOR_PANEL        0x0A0A0AFF  // Inner panel background (near-black)
#define AUTH_COLOR_BORDER       0x003300AA  // Panel border (dark green)
#define AUTH_COLOR_ACCENT       0x00CC00FF  // Accent/highlight (bright green)
#define AUTH_COLOR_TITLE        0x00FF66FF  // Title text (light green)
#define AUTH_COLOR_TEXT         0xFFFFFFFF  // Body text (white)
#define AUTH_COLOR_SUBTEXT      0xAAAAAAFF  // Secondary text (gray)
#define AUTH_COLOR_BTN_BG       0x1A1A1AFF  // Button background (dark)
#define AUTH_COLOR_BTN_BORDER   0x00AA00FF  // Button border (green)
#define AUTH_COLOR_ERROR        0xFF3333FF  // Error text (red)
#define AUTH_COLOR_BAN_ACCENT   0xFF0000FF  // Ban screen accent (red)
```

### Enum Definitions

```pawn
enum E_AUTH_STATE {
    AUTH_STATE_NONE,
    AUTH_STATE_LOGIN,
    AUTH_STATE_VERIFY,
    AUTH_STATE_CHARSELECT,
    AUTH_STATE_CHARCREATE_NAME,
    AUTH_STATE_CHARCREATE_AGE,
    AUTH_STATE_CHARCREATE_GENDER,
    AUTH_STATE_BANNED
}

// Player textdraw slots for Auth UI
enum E_AUTH_TD {
    // Background & Frame (Global - created once)
    AUTH_GTD_BG,            // Fullscreen black background
    AUTH_GTD_BORDER,        // Green border panel
    AUTH_GTD_INNER,         // Inner dark panel
    AUTH_GTD_TITLE,         // "Djava Roleplay" text
    AUTH_GTD_SEPARATOR,     // Green line separator
    AUTH_GTD_MAX
}

// Player textdraw slots (per-player, dynamic)
#define MAX_AUTH_PTD 20

enum E_AUTH_PLAYER_DATA {
    E_AUTH_STATE:AuthState,
    PlayerText:AuthPTD[MAX_AUTH_PTD],
    bool:AuthUIActive
}
```

### Textdraw Button Design

Each button consists of 3 layered textdraws:
1. **Border box** — Slightly larger green box (LD_SPAC:white, AUTH_COLOR_BTN_BORDER)
2. **Background box** — Dark inner box (LD_SPAC:white, AUTH_COLOR_BTN_BG)  
3. **Label text** — White/green text centered on the button

Button dimensions: ~180x30 pixels, centered at X:320

### Key Implementation Details

1. **Global Textdraws** (created in OnGameModeInit):
   - Background panel, border, inner panel, title "Djava Roleplay", separator line
   - These are shown/hidden per player using TextDrawShowForPlayer/TextDrawHideForPlayer

2. **Player Textdraws** (created per-player per-screen):
   - UCP name, attempt counter, character list entries, buttons, error messages
   - Created with CreatePlayerTextDraw, destroyed on screen transition

3. **Input Handling**:
   - Textdraw buttons trigger ShowPlayerDialog for text input
   - OnDialogResponse processes input, then calls screen update functions
   - After dialog closes, SelectTextDraw is re-called to maintain interactivity

4. **ESC Key Prevention**:
   - OnPlayerClickPlayerTextDraw with INVALID_PLAYER_TEXT_DRAW indicates ESC press
   - Handler re-calls SelectTextDraw to keep UI interactive

5. **Cleanup Strategy**:
   - DestroyAuthPlayerTextdraws(playerid) — destroys all player TDs in AuthPTD array
   - Called on: screen transition, disconnect (OnPlayerDisconnect), spawn completion
   - Global TDs are only hidden, never destroyed until OnGameModeExit

### Dialog Integration

The following dialogs are repurposed (same dialog IDs, same response logic):
- `DIALOG_LOGIN` — Password input (triggered by Login button click)
- `DIALOG_VERIFYCODE` — PIN input (triggered by Input PIN button click)
- `DIALOG_CREATECHAR` — Character name input (triggered by Input Name button click)
- `DIALOG_AGE` — Date of birth input (triggered by Set Birthday button click)
- `DIALOG_GENDER` — Replaced entirely by textdraw buttons (Male/Female)

The existing OnDialogResponse handlers remain mostly unchanged. The key modification is:
- After successful dialog response → call the appropriate Auth screen transition function instead of showing another dialog
- Gender selection is fully handled by textdraw clicks (no dialog needed)

### Screen-Specific Layouts

#### Login Screen Content (Y: 90-380)
```
Y:100  "Welcome back,"                    (white, font 2)
Y:130  "{UCP_NAME}"                        (green, font 1, large)
Y:180  "Status: Registered"               (gray, font 2)
Y:210  "Login Attempts: [X/3]"            (white/red, font 2)
Y:270  [====== LOGIN ======]              (green button, selectable)
Y:320  [====== QUIT  ======]              (dark button, selectable)
```

#### Character Select Screen Content (Y: 90-400)
```
Y:100  "Select Character"                 (green, font 1)
Y:130  ─────────────────────              (separator)
Y:150  [Char1_Name | Gender | Story | LastLogin]  (button, selectable)
Y:195  [Char2_Name | Gender | Story | LastLogin]  (button, selectable)
Y:240  [Char3_Name | Gender | Story | LastLogin]  (button, selectable)
Y:300  [==== CREATE CHARACTER ====]       (green button, selectable)
Y:350  [========= QUIT =========]         (dark button, selectable)
```

#### Character Create Screen Content (Y: 90-380)
```
Step 1 (Name):
Y:100  "Create New Character"             (green, font 1)
Y:150  "Enter your character name"        (white, font 2)
Y:180  "Format: Firstname_Lastname"       (gray, font 2)
Y:250  [==== INPUT NAME ====]             (green button, selectable)
Y:300  [======= BACK =======]             (dark button, selectable)

Step 2 (Age):
Y:100  "Set Birthday"                     (green, font 1)
Y:150  "Format: DD/MM/YYYY"               (white, font 2)
Y:180  "Example: 15/04/1998"              (gray, font 2)
Y:250  [=== SET BIRTHDAY ===]             (green button, selectable)
Y:300  [======= BACK =======]             (dark button, selectable)

Step 3 (Gender):
Y:100  "Select Gender"                    (green, font 1)
Y:200  [====== MALE ======]               (blue-green button, selectable)
Y:250  [===== FEMALE =====]               (pink-green button, selectable)
Y:320  [======= BACK =======]             (dark button, selectable)
```

## Modifications to Existing Code

### Playerucp.pwn Changes
- `OnPlayerDataLoaded`: Replace `ShowPlayerDialog(DIALOG_LOGIN/DIALOG_VERIFYCODE)` calls with `ShowAuthScreen(playerid, AUTH_STATE_LOGIN/AUTH_STATE_VERIFY)`
- `ShowCharacterList`: Replace `ShowPlayerDialog(DIALOG_CHARLIST)` with `ShowAuthScreen(playerid, AUTH_STATE_CHARSELECT)`

### djavarp.pwn Changes  
- `OnDialogResponse` for `DIALOG_LOGIN`: After successful login, call `ShowAuthScreen(playerid, AUTH_STATE_CHARSELECT)` instead of relying on dialog flow
- `OnDialogResponse` for `DIALOG_CHARLIST`: Replace with textdraw click handler (this dialog is no longer shown)
- `OnDialogResponse` for `DIALOG_CREATECHAR`: After valid name, call `ShowAuthScreen(playerid, AUTH_STATE_CHARCREATE_AGE)`
- `OnDialogResponse` for `DIALOG_AGE`: After valid date, call `ShowAuthScreen(playerid, AUTH_STATE_CHARCREATE_GENDER)`
- `OnDialogResponse` for `DIALOG_GENDER`: Remove — handled by textdraw buttons
- Add `OnPlayerClickPlayerTextDraw` handler for auth button clicks
- Add `#include` for new Auth module files

### UCP.pwn Changes
- `ShowCharacterList`: Redirect to new textdraw-based character list (or remove duplicate)

## Testing Strategy

- Manual testing on SA-MP client for visual verification
- Test all state transitions: connect → login → charselect → spawn
- Test error paths: wrong password, invalid name, ESC key handling
- Test cleanup: disconnect during auth, multiple reconnects
- Test edge cases: max characters, extra char slots, banned players
