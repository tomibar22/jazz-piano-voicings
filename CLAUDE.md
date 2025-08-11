# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **Jazz Piano Voicing Application** - a sophisticated music theory tool built with vanilla HTML, CSS, and JavaScript. The application helps musicians explore jazz piano voicings and create chord progressions with intelligent voice leading analysis.

## Architecture

### Technology Stack
- **Pure Vanilla Web**: HTML, CSS, JavaScript only - no frameworks or build tools
- **Self-contained**: Single `index.html` file (2,195 lines) contains the entire application
- **Data-driven**: Voicing data loaded from `complete-voicing-data.js` module
- **JSON persistence**: Save/load progressions as JSON files

### Core Components

**Voicing Data Structure**: 169+ jazz piano voicings organized by chord types (major, minor, dominant, sus, half-diminished, diminished). Each voicing is a 6-note array representing piano positions, parsed from CSV source files in `/csv/` directory.

**Browse Mode**: Grid-based interface displaying voicings organized by chord type and top note. Cells are interactive and top notes are highlighted in gold for visual emphasis.

**Progression Creator**: Drag-and-drop interface for building chord progressions using Roman numeral chord selection (I, ii, V7, etc.) with real-time voice leading analysis and movement summary indicators (3S, 2L format).

**Progression Library**: File-based progression browser with embedded JSON data from 65+ curated progressions. Features load buttons for seamless integration with Creator mode, state preservation for returning users, and organized by top-note-lines (2-2, 3-3, 7-7, etc.).

**Progression Library (Raw)**: Algorithmic progression generator that creates all possible V7-I voicing combinations, organized by movement types and semitone analysis.

**Voice Leading Engine**: Sophisticated algorithm that analyzes semitone intervals between chords, categorizes movements (common tones, steps, leaps), and automatically selects optimal voicings for smooth voice leading.

### Key Algorithms

The voice leading engine converts chord degrees to actual pitches considering chord root and type, calculates optimal voice movement within octave equivalence, and prioritizes common tones and stepwise motion. Visual indicators show movement types: green for common tones, blue for steps, orange for leaps.

## Development Commands

**No Build System**: This is a pure HTML/CSS/JS application with no package.json or build tools.

**Run Application**: Open `index.html` directly in a web browser - no server required.

**Development**: Edit `index.html` directly. The application is entirely self-contained.

**Data Updates**: Modify voicing data in `complete-voicing-data.js` or update CSV files in `/csv/` directory.

## File Structure

- `index.html` - Complete application (all HTML, CSS, JavaScript)
- `complete-voicing-data.js` - Exported voicing data module
- `csv/` - Source CSV files for each chord type's voicings
- `Progressions/V7-I (major)/` - 65+ curated V7-I progression files (JSON format)

## Recent Features (Latest Version)

### Progression Library Enhancements
- **Load in Creator Buttons**: Each progression card includes a "Load in Creator" button that seamlessly transfers the progression to the Create Progression page with full voicing data
- **Movement Summary Indicators**: Added compact movement analysis indicators (e.g., "3S" for 3 semitones, "2L" for 2 leaps) displayed in both Library and Creator modes
- **State Preservation**: Library page remembers selected top-note-line and scroll position when navigating between pages
- **Improved Visual Design**: Enhanced spacing, brighter movement colors, and less prominent load buttons for better user experience

### Navigation Improvements  
- **Reordered Navigation**: Create Progression moved before Browse Voicings for better workflow
- **Progression Library** set as default startup page
- **Consistent Formatting**: Standardized interval display format ("2-2" instead of "2 - 2")

### Technical Improvements
- **Self-contained Architecture**: All progression data embedded directly in HTML, no external file dependencies
- **Enhanced Error Handling**: Fixed missing functions and improved load button functionality  
- **Code Cleanup**: Removed redundant console.log statements and improved code organization

## State Management

Global JavaScript objects manage progression state and UI state. The application uses event-driven architecture with DOM event handlers. UI is dynamically generated from voicing data structures.

**Library State Preservation**: The `libraryPageState` object tracks selected top-note-line and scroll position, automatically restored when returning to the Progression Library page. Functions `saveLibraryPageState()` and `restoreLibraryPageState()` handle seamless user experience across page transitions.

## Data Persistence

Progressions save as JSON files with timestamp-based naming. Format includes voicing keys, top notes, and scale information. Load functionality parses JSON back into application state.

## Voice Leading Logic

The core algorithm finds closest top notes between chords, maintains voicing quality across progressions, handles secondary dominants and complex harmonies. Movement analysis calculates semitone distances and categorizes as common tones (0 semitones), steps (1-2 semitones), or leaps (3+ semitones).

## Progression Library Rules

**Direction Display Rule**: When adding new progressions to the library, only display the closest/shortest voice leading direction for each top-note-line movement. The system automatically calculates both upward and downward possibilities, but only the most efficient (shortest interval) direction should be shown to avoid duplicate entries. 

Example: For ♭2-1 progressions, only show the downward movement (♯11 → 1 going down a semitone), not the upward octave equivalent. This is implemented with filters in `generateV7IProgressionsFromFiles()` function - add similar filters for any new progression types that create unwanted directional duplicates.
- when adding progressions to the library, remember that "b", "#" and "n" in the filenames stand for "♭", "♯" and "♮"