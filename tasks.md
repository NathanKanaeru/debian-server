# Tasks

## Task 1: Create Auth UI Module Structure and Constants

### Description
Create the new Auth module directory and header file with all enums, constants, color definitions, and variable declarations needed for the textdraw auth UI system.

### Files to Create/Modify
- Create `gamemodes/Modules/Auth/AuthUI_Header.pwn`

### Acceptance Criteria
- [ ] 1.1 Create `Modules/Auth/` directory structure
- [ ] 1.2 Define `E_AUTH_STATE` enum with all states (NONE, LOGIN, VERIFY, CHARSELECT, CHARCREATE_NAME, CHARCREATE_AGE, CHARCREATE_GENDER, BANNED)
- [ ] 1.3 Define color constants (AUTH_COLOR_BG, AUTH_COLOR_PANEL, AUTH_COLOR_BORDER, AUTH_COLOR_ACCENT, AUTH_COLOR_TITLE, AUTH_COLOR_TEXT, AUTH_COLOR_SUBTEXT, AUTH_COLOR_BTN_BG, AUTH_COLOR_BTN_BORDER, AUTH_COLOR_ERROR, AUTH_COLOR_BAN_ACCENT)
- [ ] 1.4 Define MAX_AUTH_PTD constant and player textdraw array `AuthPTD[MAX_PLAYERS][MAX_AUTH_PTD]`
- [ ] 1.5 Define player auth state variable `AuthState[MAX_PLAYERS]` and `AuthUIActive[MAX_PLAYERS]` bool
- [ ] 1.6 Define Global textdraw variables for background, border, inner panel, title, and separator

---

## Task 2: Create Global Textdraw Initialization

### Description
Implement the global textdraw creation function that sets up the static background elements (fullscreen background, border panel, inner panel, "Djava Roleplay" title, separator line). These are created once in OnGameModeInit.

### Files to Create/Modify
- Create `gamemodes/Modules/Auth/AuthUI_Textdraws.pwn`

### Acceptance Criteria
- [ ] 2.1 Create `CreateAuthGlobalTextdraws()` function that creates the fullscreen background textdraw (LD_SPAC:white, 0x000000CC, covering 0-640 x 0-448)
- [ ] 2.2 Create outer border panel textdraw (LD_SPAC:white, AUTH_COLOR_BORDER, positioned at ~20,20 with size ~600x408)
- [ ] 2.3 Create inner content panel textdraw (LD_SPAC:white, AUTH_COLOR_PANEL, positioned at ~30,30 with size ~580x388)
- [ ] 2.4 Create "Djava Roleplay" title textdraw (font 1, AUTH_COLOR_TITLE, centered at X:320, Y:40, large letter size)
- [ ] 2.5 Create green separator line textdraw (LD_SPAC:white, AUTH_COLOR_ACCENT, thin horizontal line at Y:70)
- [ ] 2.6 Create `DestroyAuthGlobalTextdraws()` function for OnGameModeExit cleanup
- [ ] 2.7 Create `ShowAuthBackground(playerid)` function that shows all global TDs for a player
- [ ] 2.8 Create `HideAuthBackground(playerid)` function that hides all global TDs for a player

---

## Task 3: Implement Player Textdraw Helper Functions

### Description
Create utility functions for creating, managing, and destroying player textdraws used in the auth UI. Includes button creation helper, text label helper, and cleanup functions.

### Files to Create/Modify
- Create `gamemodes/Modules/Auth/AuthUI_Utils.pwn`

### Acceptance Criteria
- [ ] 3.1 Create `CreateAuthButton(playerid, &slot, Float:x, Float:y, Float:width, Float:height, text[], color, bgColor, selectable)` helper that creates a 3-layer button (border + bg + label) and stores in AuthPTD array
- [ ] 3.2 Create `CreateAuthLabel(playerid, &slot, Float:x, Float:y, text[], color, font, Float:letterX, Float:letterY)` helper for text labels
- [ ] 3.3 Create `CreateAuthPanel(playerid, &slot, Float:x, Float:y, Float:width, Float:height, color)` helper for box panels using LD_SPAC:white
- [ ] 3.4 Create `DestroyAuthPlayerTextdraws(playerid)` function that loops through AuthPTD array and destroys all valid player textdraws, resetting slots to INVALID_PLAYER_TEXT_DRAW
- [ ] 3.5 Create `GetNextAuthSlot(playerid)` function that returns the next available slot in AuthPTD array
- [ ] 3.6 Create `ResetAuthData(playerid)` function that resets AuthState to AUTH_STATE_NONE and AuthUIActive to false

---

## Task 4: Implement Login Screen

### Description
Create the Login Screen display function that shows UCP name, login attempt counter, Login button, and Quit button using player textdraws.

### Files to Create/Modify
- Create `gamemodes/Modules/Auth/AuthUI_Screens.pwn`

### Acceptance Criteria
- [ ] 4.1 Create `ShowLoginScreen(playerid)` function that destroys previous player TDs and creates login screen elements
- [ ] 4.2 Display "Welcome back," text label (white, font 2) at Y:100
- [ ] 4.3 Display player UCP name (pData[playerid][pUCP]) in green, font 1, large size at Y:130
- [ ] 4.4 Display "Status: Registered" text in gray at Y:180
- [ ] 4.5 Display login attempt counter "[X/3]" using formatted Player_Textdraw at Y:210
- [ ] 4.6 Create selectable "LOGIN" button (green border, centered at X:320, Y:270, width:180, height:30)
- [ ] 4.7 Create selectable "QUIT" button (dark border, centered at X:320, Y:320, width:180, height:30)
- [ ] 4.8 Call SelectTextDraw with AUTH_COLOR_ACCENT to enable click mode
- [ ] 4.9 Set AuthState[playerid] to AUTH_STATE_LOGIN

---

## Task 5: Implement Verify Code Screen

### Description
Create the Verify Code Screen for unverified accounts showing PIN input instruction and buttons.

### Files to Create/Modify
- Modify `gamemodes/Modules/Auth/AuthUI_Screens.pwn`

### Acceptance Criteria
- [ ] 5.1 Create `ShowVerifyScreen(playerid)` function that destroys previous player TDs and creates verify screen elements
- [ ] 5.2 Display "Account Verification" title text (green, font 1) at Y:100
- [ ] 5.3 Display UCP name in green at Y:140
- [ ] 5.4 Display instruction text "Masukkan PIN yang dikirim oleh BOT Discord" (white, font 2) at Y:190
- [ ] 5.5 Create selectable "INPUT PIN" button (green, centered, Y:270)
- [ ] 5.6 Create selectable "CANCEL" button (dark, centered, Y:320)
- [ ] 5.7 Call SelectTextDraw and set AuthState to AUTH_STATE_VERIFY

---

## Task 6: Implement Character Selection Screen

### Description
Create the Character Selection Screen that displays the player's characters as clickable textdraw entries with character info.

### Files to Create/Modify
- Modify `gamemodes/Modules/Auth/AuthUI_Screens.pwn`

### Acceptance Criteria
- [ ] 6.1 Create `ShowCharSelectScreen(playerid)` function that destroys previous player TDs
- [ ] 6.2 Display "Select Character" title (green, font 1) at Y:100
- [ ] 6.3 Create separator line at Y:130
- [ ] 6.4 Loop through PlayerChar array and create a clickable button for each existing character showing: name, gender text, character story status, last login
- [ ] 6.5 Position character buttons starting at Y:150 with 45px spacing between each
- [ ] 6.6 Create "CREATE CHARACTER" button (green) below the last character entry when count < MAX_CHARS + pExtraChar
- [ ] 6.7 Create "QUIT" button (dark) at the bottom
- [ ] 6.8 Call SelectTextDraw and set AuthState to AUTH_STATE_CHARSELECT
- [ ] 6.9 Store character index mapping so click handler knows which character was selected

---

## Task 7: Implement Character Creation Screens (Multi-Step)

### Description
Create the three character creation sub-screens: Name input, Age input, and Gender selection.

### Files to Create/Modify
- Modify `gamemodes/Modules/Auth/AuthUI_Screens.pwn`

### Acceptance Criteria
- [ ] 7.1 Create `ShowCharCreateNameScreen(playerid)` — displays "Create New Character" title, format instruction, "INPUT NAME" button, "BACK" button
- [ ] 7.2 Create `ShowCharCreateAgeScreen(playerid)` — displays "Set Birthday" title, format "DD/MM/YYYY" instruction, "SET BIRTHDAY" button, "BACK" button
- [ ] 7.3 Create `ShowCharCreateGenderScreen(playerid)` — displays "Select Gender" title, "MALE" button, "FEMALE" button, "BACK" button
- [ ] 7.4 Set appropriate AuthState for each sub-screen (AUTH_STATE_CHARCREATE_NAME, AUTH_STATE_CHARCREATE_AGE, AUTH_STATE_CHARCREATE_GENDER)
- [ ] 7.5 Each screen calls DestroyAuthPlayerTextdraws before creating new elements
- [ ] 7.6 Each screen calls SelectTextDraw to enable click mode
- [ ] 7.7 Display error message textdraw in red when returning from invalid input

---

## Task 8: Implement Banned Player Screen

### Description
Create the ban information screen with red accent showing ban details.

### Files to Create/Modify
- Modify `gamemodes/Modules/Auth/AuthUI_Screens.pwn`

### Acceptance Criteria
- [ ] 8.1 Create `ShowBannedScreen(playerid, bannedName[], adminName[], reason[], banDate, banExpire)` function
- [ ] 8.2 Display "BANNED" title in red (AUTH_COLOR_BAN_ACCENT) at Y:100
- [ ] 8.3 Display ban info labels: UCP Name, IP, Admin, Ban Date, Reason, Duration using Player_Textdraws
- [ ] 8.4 Display Discord appeal message text at bottom of info section
- [ ] 8.5 Create selectable "EXIT" button (red border) at Y:350
- [ ] 8.6 Set AuthState to AUTH_STATE_BANNED

---

## Task 9: Implement Click Handler (OnPlayerClickPlayerTextDraw)

### Description
Create the callback handler that processes textdraw button clicks and routes them to appropriate actions based on current AuthState.

### Files to Create/Modify
- Create `gamemodes/Modules/Auth/AuthUI_Callbacks.pwn`

### Acceptance Criteria
- [ ] 9.1 Implement OnPlayerClickPlayerTextDraw hook that checks if AuthUIActive[playerid] is true
- [ ] 9.2 Handle INVALID_PLAYER_TEXT_DRAW (ESC press) by re-calling SelectTextDraw
- [ ] 9.3 For AUTH_STATE_LOGIN: handle Login button click (show DIALOG_LOGIN password dialog), handle Quit button click (kick)
- [ ] 9.4 For AUTH_STATE_VERIFY: handle Input PIN button click (show DIALOG_VERIFYCODE dialog), handle Cancel button click (kick)
- [ ] 9.5 For AUTH_STATE_CHARSELECT: handle character button clicks (load character), handle Create Character click (transition to name screen), handle Quit click (kick)
- [ ] 9.6 For AUTH_STATE_CHARCREATE_NAME: handle Input Name click (show DIALOG_CREATECHAR dialog), handle Back click (return to charselect)
- [ ] 9.7 For AUTH_STATE_CHARCREATE_AGE: handle Set Birthday click (show DIALOG_AGE dialog), handle Back click (return to charselect)
- [ ] 9.8 For AUTH_STATE_CHARCREATE_GENDER: handle Male click (assign gender 1, proceed to skin/spawn), handle Female click (assign gender 2, proceed to skin/spawn), handle Back click (return to charselect)
- [ ] 9.9 For AUTH_STATE_BANNED: handle Exit button click (kick)
- [ ] 9.10 Play PlayerPlaySound(1083) on each valid button click for audio feedback

---

## Task 10: Modify Existing Dialog Response Handlers

### Description
Update the existing OnDialogResponse handlers in djavarp.pwn to work with the new textdraw UI system. After dialog input is processed, transition to appropriate textdraw screen instead of showing another dialog.

### Files to Create/Modify
- Modify `gamemodes/djavarp.pwn`

### Acceptance Criteria
- [ ] 10.1 Modify DIALOG_LOGIN response: on success, call ShowCharSelectScreen(playerid) after CheckPlayerChar; on failure, update attempt counter textdraw and re-call SelectTextDraw
- [ ] 10.2 Modify DIALOG_LOGINATP response: call SelectTextDraw after showing the login screen again
- [ ] 10.3 Modify DIALOG_VERIFYCODE response: on success proceed normally; on cancel, kick
- [ ] 10.4 Modify DIALOG_CREATECHAR response: on valid name, call ShowCharCreateAgeScreen(playerid); on invalid, show error on textdraw and re-show dialog
- [ ] 10.5 Modify DIALOG_AGE response: on valid date, call ShowCharCreateGenderScreen(playerid); on invalid, show error and re-show dialog
- [ ] 10.6 Remove DIALOG_GENDER dialog usage — gender is now handled by textdraw buttons in click handler
- [ ] 10.7 After each dialog response (success or re-show), call SelectTextDraw(playerid, AUTH_COLOR_ACCENT) to re-enable textdraw click mode
- [ ] 10.8 Remove DIALOG_CHARLIST response handler — character selection is now handled by textdraw clicks

---

## Task 11: Modify Auth Flow Entry Points

### Description
Update OnPlayerDataLoaded and related functions to use the new textdraw UI instead of ShowPlayerDialog for initial auth screens.

### Files to Create/Modify
- Modify `gamemodes/Modules/Players/Playerucp.pwn`
- Modify `gamemodes/Modules/Players/UCP.pwn`

### Acceptance Criteria
- [ ] 11.1 In OnPlayerDataLoaded: replace ShowPlayerDialog(DIALOG_VERIFYCODE) with ShowAuthBackground(playerid) + ShowVerifyScreen(playerid)
- [ ] 11.2 In OnPlayerDataLoaded: replace ShowPlayerDialog(DIALOG_LOGIN) with ShowAuthBackground(playerid) + ShowLoginScreen(playerid)
- [ ] 11.3 In ShowCharacterList (Playerucp.pwn): replace ShowPlayerDialog(DIALOG_CHARLIST) with ShowCharSelectScreen(playerid)
- [ ] 11.4 In ShowCharacterList (UCP.pwn): replace ShowPlayerDialog(DIALOG_CHARLIST) with ShowCharSelectScreen(playerid) or remove duplicate
- [ ] 11.5 In CheckBanUCP: replace ShowPlayerDialog ban info with ShowAuthBackground(playerid) + ShowBannedScreen(playerid, ...)
- [ ] 11.6 Set AuthUIActive[playerid] = true when showing first auth screen
- [ ] 11.7 Ensure SetLoginCamera(playerid) is still called before showing auth UI

---

## Task 12: Implement Cleanup and Spawn Integration

### Description
Ensure all textdraws are properly cleaned up on spawn, disconnect, and auth completion. Integrate with existing spawn flow.

### Files to Create/Modify
- Modify `gamemodes/Modules/Auth/AuthUI_Callbacks.pwn`
- Modify `gamemodes/djavarp.pwn` (OnPlayerDisconnect, spawn logic)

### Acceptance Criteria
- [ ] 12.1 Create `CleanupAuthUI(playerid)` function that calls DestroyAuthPlayerTextdraws, HideAuthBackground, CancelSelectTextDraw, resets AuthState and AuthUIActive
- [ ] 12.2 Call CleanupAuthUI in OnPlayerDisconnect hook
- [ ] 12.3 Call CleanupAuthUI after successful character load (before spawn/AssignPlayerData)
- [ ] 12.4 Call CleanupAuthUI after successful character creation (before skin selection spawn)
- [ ] 12.5 Ensure SetCameraBehindPlayer is called after CleanupAuthUI on spawn
- [ ] 12.6 Initialize auth player data (ResetAuthData) in OnPlayerConnect

---

## Task 13: Include New Module Files in Main Gamemode

### Description
Add #include directives for the new Auth module files in the main gamemode file and ensure proper compilation order.

### Files to Create/Modify
- Modify `gamemodes/djavarp.pwn`

### Acceptance Criteria
- [ ] 13.1 Add `#include "Modules/Auth/AuthUI_Header.pwn"` before other auth-related includes
- [ ] 13.2 Add `#include "Modules/Auth/AuthUI_Utils.pwn"` after header
- [ ] 13.3 Add `#include "Modules/Auth/AuthUI_Textdraws.pwn"` after utils
- [ ] 13.4 Add `#include "Modules/Auth/AuthUI_Screens.pwn"` after textdraws
- [ ] 13.5 Add `#include "Modules/Auth/AuthUI_Callbacks.pwn"` after screens
- [ ] 13.6 Call `CreateAuthGlobalTextdraws()` in OnGameModeInit
- [ ] 13.7 Call `DestroyAuthGlobalTextdraws()` in OnGameModeExit
- [ ] 13.8 Verify compilation succeeds without errors
