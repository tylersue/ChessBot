# ChessBot - Live Chess Assistant

## IMPORTANT: Fair Play Warning

Using chess bots or any form of external assistance that analyzes your live games is **strictly against the Fair Play policy** of most online chess platforms, including Chess.com and Lichess.org. Doing so can result in warnings, account restrictions, or permanent bans.

This tool is provided for educational purposes, to understand how such systems can be built, or for use against computer opponents where permitted. **You assume full responsibility for how you use this software and any consequences thereof.**

## Overview

ChessBot is a Python-based live chess assistant. It captures a portion of your screen, detects the chessboard and pieces, converts the position to Forsyth-Edwards Notation (FEN), and then uses the Stockfish chess engine to analyze the position and suggest moves. The analysis and suggestions are displayed in a simple overlay window.

## Features

*   **Live Board Recognition:** Captures the chessboard from your screen.
*   **Piece Detection:** Uses template matching to identify pieces.
*   **FEN Conversion:** Converts the detected board state into a FEN string.
*   **Stockfish Integration:** Analyzes the position using the powerful Stockfish engine.
*   **Move Suggestion:** Displays suggested moves and top engine lines in an overlay.
*   **Configurable:** Settings managed via `config.json` (e.g., Stockfish path, user color, piece theme).
*   **Board Calibration:** Interactive tool to define the screen region of the chessboard.
*   **Turn Detection:** Attempts to automatically detect and switch turns.
*   **Castling & En Passant:** Includes logic for tracking castling rights and en passant target squares in FEN.
*   **Customizable Piece Themes:** Supports different visual styles for piece templates.

## Requirements

*   Python 3.7+
*   Stockfish chess engine (executable).
*   Python packages:
    *   `opencv-python` (for image processing and template matching)
    *   `pyautogui` (for screen capture)
    *   `python-chess` (for chess logic, FEN validation, and engine communication)
    *   `Pillow` (PIL - Python Imaging Library, often a dependency of pyautogui)
    *   `tk` (Tkinter, for the overlay UI - usually included with standard Python installations on most platforms).

## Setup Instructions

1.  **Install Python:**
    *   Download and install Python from [python.org](https://www.python.org/downloads/) if you don't have it. Ensure it's added to your system's PATH.

2.  **Download/Clone ChessBot:**
    *   If you have Git: `git clone <repository_url>`
    *   Otherwise, download the source code as a ZIP file and extract it.

3.  **Install Stockfish:**
    *   Download the Stockfish engine from the official site: [stockfishchess.org/download/](https://stockfishchess.org/download/).
    *   Extract the executable (e.g., `stockfish.exe` on Windows, `stockfish` on macOS/Linux) to a known location on your computer.

4.  **Install Python Dependencies:**
    *   Open a terminal or command prompt.
    *   Navigate to the directory where you cloned or extracted ChessBot.
    *   Install the required packages:
        ```bash
        pip install opencv-python pyautogui python-chess Pillow
        ```
        (Tkinter is usually included, but if not, you might need to install it separately, e.g., `sudo apt-get install python3-tk` on Debian/Ubuntu).

5.  **Configure `ChessBot/config.json`:**
    *   The configuration file `ChessBot/config.json` is created automatically with default values when you run the bot for the first time.
    *   **Crucial Step:** You **must** edit this file to set the correct `STOCKFISH_PATH`.
        *   `STOCKFISH_PATH`: Full path to the Stockfish executable you downloaded.
            *   Example (Windows): `"C:/path/to/stockfish/stockfish.exe"` (use forward slashes)
            *   Example (macOS): `"/Applications/Stockfish.app/Contents/MacOS/Stockfish"` or `"/path/to/stockfish/stockfish"`
            *   Example (Linux): `"/usr/games/stockfish"` or `"/path/to/stockfish/stockfish"`
    *   Other important settings:
        *   `USER_COLOR`: Set to `"white"` or `"black"` depending on which color you are playing. This affects board orientation for FEN.
        *   `PIECE_THEME`: The name of the theme directory under `ChessBot/templates/` for piece images (default: `"default_theme"`).
        *   `MATCH_THRESHOLD`: Confidence level (0.0 to 1.0) for piece detection. Lower if pieces aren't detected, higher if incorrect detections occur. Default is `0.8`.
        *   `BOARD_REGION`: Screen coordinates `[x, y, width, height]` of the board. This will be set by the calibration tool on first run.
        *   `calibrate_on_startup`: If `true`, prompts for board calibration on first run or if the config is reset. Set to `false` to skip automatic calibration.

## Running the Bot

1.  Open a terminal or command prompt.
2.  Navigate to the parent directory of `ChessBot` (if `liveBot1.py` is inside `ChessBot`).
3.  Run the script:
    ```bash
    python ChessBot/liveBot1.py
    ```
4.  **First Run:**
    *   You'll see a welcome message about the `config.json` being created.
    *   You'll be guided through an on-screen board calibration: click the top-left corner of your chessboard on screen, then the bottom-right corner.
    *   You'll be prompted in the console: "Whose turn? (w/b) New game? (y/n) [e.g., w y]:"
        *   Enter 'w' or 'b' for whose turn it is to move.
        *   Enter 'y' if it's a new game (resets castling rights etc.) or 'n' if continuing.
5.  **Subsequent Runs:**
    *   If `calibrate_on_startup` is `false` in `config.json` (set automatically after first successful calibration), you'll be asked if you want to recalibrate.
    *   You'll be prompted for the current turn and new game status.
    *   The overlay window will appear, displaying game analysis.

## How it Works (Brief Technical Overview)

1.  **Configuration:** Loads settings from `ChessBot/config.json`.
2.  **Screen Capture:** Uses `pyautogui` to capture the screen region defined by `BOARD_REGION` in the config.
3.  **Board Processing:** The captured image is split into 8x8 squares.
4.  **Template Matching:** `opencv-python` is used to compare each square against pre-defined template images of chess pieces (e.g., white pawn, black knight) stored in the theme directory.
5.  **State Detection:** Based on the best matches, an 8x8 internal representation of the board is created.
6.  **FEN Conversion:** The internal board state, along with current turn, castling rights (updated based on king/rook positions), and en passant target square (detected from pawn moves), is converted into a FEN string.
7.  **Stockfish Analysis:** The FEN string is sent to the Stockfish engine via `python-chess`. Stockfish analyzes the position and returns its top moves and evaluations.
8.  **Overlay Display:** A Tkinter window (always on top) displays the FEN, suggested moves, and other analysis information.

## Customizing Piece Templates (Themes)

You can customize the appearance of the pieces the bot recognizes by creating your own image templates.

1.  **Directory Structure:**
    *   Templates are organized in theme directories under `ChessBot/templates/`.
    *   The default theme is `ChessBot/templates/default_theme/`.
    *   To create a new theme, make a new folder, e.g., `ChessBot/templates/my_theme/`.

2.  **Creating Template Images:**
    *   Each piece type needs template images. The bot uses a naming convention that includes the piece, its color, and the square color it's on (though square color matching isn't strictly enforced by the current detection logic, it helps differentiate templates).
    *   **Filenames must match those in `TEMPLATE_MAPPING` in `liveBot1.py`.**
        *   Examples: `wP_white.png` (White Pawn on a white square), `wP_green.png` (White Pawn on a dark/green square), `bN_white.png` (Black Knight on a white square).
    *   **Image Guidelines:**
        *   Create small PNG images of each piece (e.g., from screenshots of your chessboard).
        *   The images should be tightly cropped around the piece.
        *   Ensure consistency in size and style for best results. The size should roughly match the size of pieces on your board when captured at your screen resolution.
        *   Save as PNG with transparency if desired, though the bot converts to grayscale for matching.

3.  **Setting the Theme:**
    *   Open `ChessBot/config.json`.
    *   Change the value of `PIECE_THEME` to your theme's folder name (e.g., `"PIECE_THEME": "my_theme"`).

## Troubleshooting / Tips

*   **Poor Piece Recognition:**
    *   **Recalibrate Board:** Ensure `BOARD_REGION` is accurately set using the calibration tool.
    *   **Adjust `MATCH_THRESHOLD`:** In `config.json`, try lowering this value (e.g., to `0.7` or `0.65`) if pieces are not being detected, or increasing it if there are many false positives.
    *   **Custom Templates:** Create high-quality templates specific to your chessboard's piece style and colors. Pay attention to the lighting.
    *   **Board Colors:** The default templates include `_white.png` and `_green.png` variants. While the current logic might not strongly differentiate based on square color, providing templates that match your board's light and dark squares can be beneficial.
*   **Stockfish Errors:**
    *   "Failed to start Stockfish engine..." usually means the `STOCKFISH_PATH` in `config.json` is incorrect or the Stockfish executable is missing/not runnable. Double-check the full path.
*   **Incorrect FEN / Analysis:**
    *   Observe the information in the overlay (FEN, turn, castling, EP). This shows what the bot *thinks* the state of the game is.
    *   If pieces are misidentified, it will lead to an incorrect FEN. Improve template matching as above.
    *   If turn, castling, or EP status seems wrong, there might be an issue in the state tracking logic for those specific edge cases.

## Limitations

*   **Template Matching Sensitivity:** Piece recognition is based on template matching, which can be sensitive to changes in piece appearance, lighting, screen resolution, and board themes. Custom templates are often necessary for good performance.
*   **UI Changes on Chess Platforms:** If the website (e.g., Chess.com, Lichess) changes its board design or how pieces are rendered, template matching may fail or become less accurate.
*   **Game State Tracking:**
    *   **Turn Detection:** Relies on detecting a piece movement of the expected color. Rapid pre-moves or unusual piece drops might confuse it.
    *   **Castling Rights:** Updated by checking if kings/rooks have moved from their initial squares. Doesn't currently parse game moves to know *if* a castling move occurred.
    *   **En Passant:** Detected based on a pawn's two-square advance. Complex sequences might be missed.
*   **No Halfmove/Fullmove Clock:** The FEN generated currently uses `0 1` for the halfmove clock and fullmove number, which is not dynamically tracked. This generally doesn't affect Stockfish's short-term tactical analysis.
*   **Promotion:** Piece promotion detection is not explicitly handled beyond normal piece recognition. If a pawn promotes, it should be detected as the new piece type on the next scan.

---
*This README provides guidance for ChessBot. Remember to play fair and respect the terms of service of any platform you use it on.*
