# Requirements Document

## Introduction

Fitur ini mengganti sistem autentikasi berbasis dialog native SA-MP (ShowPlayerDialog) dengan antarmuka fullscreen berbasis textdraw. Sistem mencakup layar login, register (verify code), pemilihan karakter, dan pembuatan karakter baru. Semua core logic (query MySQL, hashing password, validasi nama, dll) tetap sama — hanya lapisan presentasi UI yang berubah dari dialog ke textdraw. Desain menggunakan kombinasi warna hijau-hitam dengan nama server "Djava Roleplay", memanfaatkan keterbatasan textdraw SA-MP (kotak dan teks) secara kreatif.

## Glossary

- **Auth_UI**: Sistem antarmuka textdraw fullscreen yang menangani seluruh flow autentikasi pemain (login, verify, character select, character create)
- **Auth_Screen**: Satu layar/state tertentu dalam Auth_UI (misalnya Login_Screen, CharSelect_Screen)
- **Textdraw_Panel**: Elemen visual berupa kotak (box textdraw) yang membentuk panel, header, atau tombol pada Auth_UI
- **Textdraw_Button**: Elemen textdraw yang dapat diklik (selectable) dan berfungsi sebagai tombol aksi
- **Player_Textdraw**: Textdraw yang dibuat per-pemain menggunakan CreatePlayerTextDraw, digunakan untuk konten dinamis
- **Global_Textdraw**: Textdraw yang dibuat sekali menggunakan TextDrawCreate, digunakan untuk elemen statis yang sama untuk semua pemain
- **Login_Screen**: Layar textdraw yang ditampilkan saat pemain harus memasukkan password
- **Verify_Screen**: Layar textdraw yang ditampilkan saat pemain harus memasukkan PIN verifikasi
- **CharSelect_Screen**: Layar textdraw yang menampilkan daftar karakter pemain untuk dipilih
- **CharCreate_Screen**: Layar textdraw untuk flow pembuatan karakter baru (nama, tanggal lahir, gender, skin)
- **Input_Dialog**: Dialog native SA-MP (ShowPlayerDialog) yang digunakan khusus untuk menerima input teks dari pemain, karena textdraw tidak mendukung input teks
- **Screen_Coordinate**: Sistem koordinat layar SA-MP dengan rentang X: 0-640 dan Y: 0-448

## Requirements

### Requirement 1: Fullscreen Background Panel

**User Story:** Sebagai pemain, saya ingin melihat tampilan UI yang menutupi seluruh layar saat proses autentikasi, sehingga saya mendapat pengalaman visual yang immersive dan profesional.

#### Acceptance Criteria

1. WHEN a player connects to the server, THE Auth_UI SHALL display a fullscreen background Textdraw_Panel covering Screen_Coordinate X: 0-640 and Y: 0-448 using a dark black color (0x000000CC)
2. THE Auth_UI SHALL display the server name "Djava Roleplay" as a header text using green color (0x00FF00FF) with font size 2 at the top-center of the screen
3. THE Auth_UI SHALL use a color scheme consisting of black (0x000000FF) for backgrounds, dark green (0x003300FF) for panel borders, bright green (0x00FF00FF) for active/highlight elements, and white (0xFFFFFFFF) for body text
4. WHILE the Auth_UI is displayed, THE Auth_UI SHALL hide the default SA-MP HUD elements from the player

### Requirement 2: Login Screen

**User Story:** Sebagai pemain terdaftar, saya ingin melihat layar login yang informatif dengan textdraw, sehingga saya tahu status akun saya dan dapat melakukan login.

#### Acceptance Criteria

1. WHEN a registered player connects, THE Login_Screen SHALL display the player UCP name in green text within a bordered Textdraw_Panel
2. THE Login_Screen SHALL display login attempt count in format "[X / 3]" using a Player_Textdraw that updates per attempt
3. THE Login_Screen SHALL display a "Login" Textdraw_Button that is selectable and triggers an Input_Dialog for password entry when clicked
4. THE Login_Screen SHALL display a "Quit" Textdraw_Button that is selectable and kicks the player when clicked
5. WHEN the player clicks the "Login" Textdraw_Button, THE Auth_UI SHALL show an Input_Dialog (DIALOG_STYLE_PASSWORD) for password input
6. WHEN the player enters a correct password in the Input_Dialog, THE Auth_UI SHALL hide the Login_Screen and proceed to CharSelect_Screen
7. WHEN the player enters an incorrect password, THE Login_Screen SHALL update the attempt counter Player_Textdraw and display an error message in red text
8. IF the player reaches 3 failed login attempts, THEN THE Auth_UI SHALL kick the player from the server

### Requirement 3: Verify Code Screen

**User Story:** Sebagai pemain baru yang belum verifikasi, saya ingin melihat layar verifikasi PIN dengan textdraw, sehingga saya dapat memasukkan kode verifikasi dari Discord bot.

#### Acceptance Criteria

1. WHEN a player with unverified account connects, THE Verify_Screen SHALL display a Textdraw_Panel with instruction text explaining PIN verification
2. THE Verify_Screen SHALL display the player UCP name in a Player_Textdraw
3. THE Verify_Screen SHALL display an "Input PIN" Textdraw_Button that triggers an Input_Dialog for PIN entry when clicked
4. THE Verify_Screen SHALL display a "Cancel" Textdraw_Button that kicks the player when clicked
5. WHEN the player clicks the "Input PIN" Textdraw_Button, THE Auth_UI SHALL show an Input_Dialog (DIALOG_STYLE_INPUT) for PIN code entry

### Requirement 4: Character Selection Screen

**User Story:** Sebagai pemain yang sudah login, saya ingin melihat daftar karakter saya dalam format textdraw yang rapi, sehingga saya dapat memilih karakter untuk bermain.

#### Acceptance Criteria

1. WHEN a player successfully logs in, THE CharSelect_Screen SHALL display a Textdraw_Panel listing all characters owned by the player
2. THE CharSelect_Screen SHALL display each character as a separate clickable Textdraw_Button showing character name, gender, character story status, and last login date
3. THE CharSelect_Screen SHALL support displaying up to MAX_CHARS (3) characters plus extra characters from pExtraChar
4. THE CharSelect_Screen SHALL display a "Create Character" Textdraw_Button in green text when the player has fewer characters than the maximum allowed
5. WHEN the player clicks a character Textdraw_Button, THE Auth_UI SHALL hide the CharSelect_Screen and load the selected character data
6. WHEN the player clicks the "Create Character" Textdraw_Button, THE Auth_UI SHALL transition to the CharCreate_Screen
7. THE CharSelect_Screen SHALL display a "Quit" Textdraw_Button that kicks the player when clicked
8. THE CharSelect_Screen SHALL use a Player_Textdraw for each character entry to display per-player dynamic data

### Requirement 5: Character Creation Screen

**User Story:** Sebagai pemain yang ingin membuat karakter baru, saya ingin melihat flow pembuatan karakter dalam textdraw, sehingga saya dapat membuat karakter dengan langkah-langkah yang jelas.

#### Acceptance Criteria

1. WHEN the player initiates character creation, THE CharCreate_Screen SHALL display a Textdraw_Panel with a "Create Character" title and instruction text
2. THE CharCreate_Screen SHALL display an "Input Name" Textdraw_Button that triggers an Input_Dialog for character name entry
3. WHEN a valid character name is confirmed, THE CharCreate_Screen SHALL transition to the age input step displaying a "Set Birthday" Textdraw_Button
4. WHEN the player clicks "Set Birthday" Textdraw_Button, THE Auth_UI SHALL show an Input_Dialog for date of birth entry in format DD/MM/YYYY
5. WHEN a valid date of birth is confirmed, THE CharCreate_Screen SHALL transition to the gender selection step displaying "Male" and "Female" Textdraw_Buttons
6. WHEN the player clicks a gender Textdraw_Button, THE Auth_UI SHALL assign the selected gender and proceed to skin selection
7. THE CharCreate_Screen SHALL display a "Back" Textdraw_Button on each step that returns to the CharSelect_Screen
8. IF the player enters an invalid character name in the Input_Dialog, THEN THE Auth_UI SHALL display an error message in red text on the CharCreate_Screen and re-show the Input_Dialog

### Requirement 6: Textdraw Click Handling

**User Story:** Sebagai pemain, saya ingin dapat mengklik tombol-tombol pada textdraw UI, sehingga saya dapat berinteraksi dengan sistem autentikasi tanpa mengetik command.

#### Acceptance Criteria

1. WHILE the Auth_UI is displayed, THE Auth_UI SHALL enable textdraw selection mode using SelectTextDraw for the player
2. WHEN the player clicks a Textdraw_Button, THE Auth_UI SHALL process the click via OnPlayerClickPlayerTextDraw callback and execute the corresponding action
3. IF the player presses ESC while textdraw selection is active, THEN THE Auth_UI SHALL re-enable textdraw selection mode to prevent the player from escaping the Auth_UI
4. THE Auth_UI SHALL use unique identifiers for each Textdraw_Button to distinguish click targets in the callback handler
5. WHEN an Input_Dialog is closed (response received), THE Auth_UI SHALL re-enable textdraw selection mode using SelectTextDraw

### Requirement 7: Screen Transition and Cleanup

**User Story:** Sebagai pemain, saya ingin transisi antar layar yang smooth tanpa artefak visual, sehingga pengalaman UI terasa polished.

#### Acceptance Criteria

1. WHEN transitioning between Auth_Screens, THE Auth_UI SHALL destroy all Player_Textdraws from the previous screen before creating new ones
2. WHEN the player completes authentication and spawns, THE Auth_UI SHALL destroy all Auth_UI textdraws (both Global_Textdraw and Player_Textdraw) and disable textdraw selection mode
3. WHEN the player disconnects during authentication, THE Auth_UI SHALL destroy all Player_Textdraws associated with that player
4. THE Auth_UI SHALL use PlayerPlaySound to provide audio feedback when transitioning between screens

### Requirement 8: Visual Design Elements

**User Story:** Sebagai pemain, saya ingin melihat desain UI yang kreatif dan menarik menggunakan elemen-elemen yang tersedia di SA-MP textdraw, sehingga server terlihat profesional.

#### Acceptance Criteria

1. THE Auth_UI SHALL use layered Textdraw_Panels to create depth effect: outer panel (dark black), inner panel (dark green border), content area (slightly transparent black)
2. THE Auth_UI SHALL display decorative line separators using thin box textdraws with green color between header and content sections
3. THE Auth_UI SHALL use font ID 1 (bold) for headers and font ID 2 (regular) for body text to create visual hierarchy
4. THE Auth_UI SHALL display Textdraw_Buttons with a green border box and dark background, changing to brighter green highlight color to indicate interactivity
5. THE Auth_UI SHALL position all elements using centered alignment relative to Screen_Coordinate X: 320 (horizontal center) for the main content panel
6. THE Auth_UI SHALL use "LD_SPAC:white" sprite (font 4) for creating clean rectangular panel shapes with precise color control

### Requirement 9: Login Camera Integration

**User Story:** Sebagai pemain, saya ingin kamera tetap menampilkan view cinematic saat berada di layar autentikasi, sehingga background di balik textdraw tetap menarik.

#### Acceptance Criteria

1. WHEN the Auth_UI is displayed, THE Auth_UI SHALL call the existing SetLoginCamera function to set the cinematic camera view
2. THE Auth_UI SHALL maintain the camera interpolation behavior from the existing login system
3. WHEN the player completes authentication and spawns, THE Auth_UI SHALL reset the camera to normal player view using SetCameraBehindPlayer

### Requirement 10: Banned Player Screen

**User Story:** Sebagai sistem, saya ingin menampilkan informasi ban dalam format textdraw yang jelas, sehingga pemain yang di-ban mendapat informasi lengkap.

#### Acceptance Criteria

1. WHEN a banned player connects, THE Auth_UI SHALL display a dedicated ban information Textdraw_Panel with red accent color
2. THE Auth_UI SHALL display ban details (UCP name, admin name, ban reason, ban date, ban duration) using Player_Textdraws
3. THE Auth_UI SHALL display an "Exit" Textdraw_Button that kicks the player when clicked
4. THE Auth_UI SHALL display a message directing the player to Discord for ban appeals
