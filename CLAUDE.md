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

**Progression Creator**: Drag-and-drop interface for building chord progressions using Roman numeral chord selection (I, ii, V7, etc.) with real-time voice leading analysis.

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
- `Progressions/` - Sample progression files (JSON format)

## State Management

Global JavaScript objects manage progression state and UI state. The application uses event-driven architecture with DOM event handlers. UI is dynamically generated from voicing data structures.

## Data Persistence

Progressions save as JSON files with timestamp-based naming. Format includes voicing keys, top notes, and scale information. Load functionality parses JSON back into application state.

## Voice Leading Logic

The core algorithm finds closest top notes between chords, maintains voicing quality across progressions, handles secondary dominants and complex harmonies. Movement analysis calculates semitone distances and categorizes as common tones (0 semitones), steps (1-2 semitones), or leaps (3+ semitones).