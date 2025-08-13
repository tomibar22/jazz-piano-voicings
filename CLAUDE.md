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

### **AUTOMATIC DIRECTION FILTERING RULE** 🎯
**ALWAYS APPLY**: When adding new top-note-line progressions to the library, automatically determine and show ONLY the closest/shortest voice leading direction. For any interval movement, calculate both upward and downward possibilities and keep only the shorter path.

**Implementation Pattern**: For each new top-note-line X-Y, add direction filtering logic:
```javascript
// Skip [longer direction] movements for X-Y progressions (only show [shorter direction])
if (topNoteLine === 'X-Y' && movement.direction === '[longer_arrow]') {
    return;
}
```

### **Current Direction Filters** (Reference Examples)
- **♭2-1**: Show only **downward** `↘` (♯11 → 1 = 1 semitone down vs 11 semitones up)
- **♭2-2**: Show only **upward** `↗` (display as ♯1-2 in UI; 1 semitone up vs 11 semitones down)  
- **♯2-3**: Show only **upward** `↗` (♭13 → 3 = 2 semitones up vs 10 semitones down)
- **♯1-2**: Filtered out entirely (same interval as ♭2-2)

### **Automatic Decision Logic** 
For ANY new interval X-Y:
1. **Calculate semitone distances** for both directions (considering octave equivalence)
2. **Choose shorter path**: If upward < downward, filter out `↘`. If downward < upward, filter out `↗`
3. **Add filtering code** to all three functions: `generateV7IProgressions()`, `generateV7iProgressions()`, `generateV7IProgressionsFromFiles()`
4. **Test immediately** to ensure single entry in navigation

### **Quick Reference: Common Intervals** 
| Interval | Upward | Downward | **Keep** | Filter Out |
|----------|--------|----------|----------|------------|
| 1 semitone | 1 | 11 | Upward `↗` | Downward `↘` |
| 2 semitones | 2 | 10 | Upward `↗` | Downward `↘` |
| 3 semitones | 3 | 9 | Upward `↗` | Downward `↘` |
| 4 semitones | 4 | 8 | Upward `↗` | Downward `↘` |
| 5 semitones | 5 | 7 | Upward `↗` | Downward `↘` |
| 6 semitones | 6 | 6 | Either (same) | Choose consistent |
| 7 semitones | 7 | 5 | Downward `↘` | Upward `↗` |

**Display Rule**: ♭2-2 progressions are displayed as ♯1-2 in the user interface for better readability, while maintaining the internal ♭2-2 logic for progression matching and file organization.

**Interval Consolidation**: ♯1-2 and ♭2-2 represent the same musical interval (1 semitone up). Only ♭2-2 progressions are shown (displayed as ♯1-2) to avoid duplicates.

### **Implementation Template** 📋
When adding any new top-note-line progressions, copy and adapt this code template to all three filtering locations:

```javascript
// Skip [direction] movements for [X-Y] progressions (only show [opposite_direction])
if (topNoteLine === '[X-Y]' && movement.direction === '[arrow_to_filter]') {
    return;
}
```

**Example**: For ♯2-3 (2 semitones up is shorter than 10 down):
```javascript
// Skip downward movements for ♯2-3 progressions (only show upward)
if (topNoteLine === '♯2-3' && movement.direction === '↘') {
    return;
}
```

**Locations to update** (search for these function names):
1. `generateV7IProgressions()` - Dynamic major progressions
2. `generateV7iProgressions()` - Dynamic minor progressions  
3. `generateV7IProgressionsFromFiles()` - Embedded file-based progressions

- When adding progressions to the library, remember that "b", "#" and "n" in the filenames stand for "♭", "♯" and "♮"

## Critical Development Notes

### Direction Symbol Bug Warning ⚠️
**IMPORTANT**: When implementing direction filtering for top-note-line movements, use the correct Unicode arrow symbols:
- **Upward**: `↗` (U+2197 NORTH EAST ARROW)
- **Downward**: `↘` (U+2198 SOUTH EAST ARROW)

**DO NOT USE**: `↙` (U+2199 SOUTH WEST ARROW) - this was a critical bug that caused duplicate navigation entries to persist despite filtering logic.

### Duplicate Entry Resolution ⚠️ CRITICAL DEBUGGING GUIDE
When troubleshooting duplicate top-note-line entries in navigation:

**STEP 1: IDENTIFY THE CORRECT PAGE** 🎯
The application has TWO separate library pages with different functions:
- **"Progression Library"** (main page) → uses `generateV7IProgressionsFromFiles()` + `updateTopNoteLinesNavFiles()`
- **"Progression Library (Raw)"** → uses `generateV7IProgressions()` + `generateTopNoteLineNavigation()`

**⚠️ MAKE SURE YOU'RE DEBUGGING THE RIGHT PAGE THAT THE USER IS REPORTING ISSUES ON!**

**STEP 2: Apply filtering to ALL relevant functions**
1. **Main Library page**: Check `generateV7IProgressionsFromFiles()` (around line 5400+)
2. **Raw page**: Check `generateV7IProgressions()` (around line 3920+) 
3. **Minor progressions**: Check `generateV7iProgressions()` (around line 4050+)

**STEP 3: Debug systematically**
1. **Add debug logging**: Use `console.log` to trace progression processing and navigation creation
2. **Verify direction symbols**: Ensure filtering logic uses correct arrow symbols (`↘` not `↙`)  
3. **Test with browser console**: Open developer tools to verify filtering is working correctly

**STEP 4: Template for adding missing direction filters**
```javascript
// Skip [upward/downward] movements for X-Y progressions (only show [opposite])
if (topNoteLine === 'X-Y' && movement.direction === '[↗/↘]') {
    console.log(`[FUNCTION_NAME] FILTERED OUT: ${topNoteLine} ${movement.direction}`);
    return;
}
```

**Common Root Causes**:
- Missing direction filtering in one of the generation functions (especially `generateV7IProgressionsFromFiles()`)
- Wrong Unicode arrow symbols in direction filters (`↙` instead of `↘`)
- Debugging the wrong page (Raw vs Main Library)

**Remember**: Apply the SAME filtering logic to ALL generation functions to prevent duplicates.
