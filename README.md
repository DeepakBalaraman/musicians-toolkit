# Musician's Toolkit

A [Windhawk](https://windhawk.net/) mod that puts the things you reach for
while you practise into one small panel on top of Windows: a chord library, a
chromatic tuner, a metronome, a strumming pattern builder, an interactive
circle of fifths, a tap tempo and a timed note trainer.

Press **Win+Y** to open it. The panel floats above your other windows, so a
click or a chord chart can sit beside your DAW, tab app or video lesson, and it
keeps running when you click away from it. Everything is drawn and synthesised
by the mod itself - no images, no audio files, no network requests - so it
behaves the same offline on any machine.

![Sample Screenshot](https://raw.githubusercontent.com/DeepakBalaraman/musicians-toolkit/master/assets/screenshot.png)

## What's in it

| Tool | What it does |
| --- | --- |
| Chords | Search a chord, then read it off a fretboard or a keyboard; every fingering the library knows is a click or an arrow away. |
| Tuner | Twelve instruments and their common tunings, with a chromatic fallback for anything else. |
| Metronome | Eight meters, subdivisions, a tempo you can type, and it keeps playing after the panel closes. |
| Strumming | A one-measure grid, 38 patterns in eight meters, three strum sounds, and a practice ramp. |
| Circle of fifths | Both rings at once, so the majors and their relative minors sit on the same spoke. |
| Tap tempo | Tap out a tempo, see how steady you were, then halve, double or reset it. |
| Note trainer | A note name, a countdown and a microphone, scored for speed and streak. |

## Requirements

- Windows 10 or 11.
- Windhawk, set up and running.
- A microphone for the tuner and the note trainer.

## Installing

1. Install [Windhawk](https://windhawk.net/) and let it finish its first
   setup.
2. Open Windhawk, go to the **Mods** tab and click **Create new mod**.
3. Set the id to `musicians-toolkit`, replace the template code with the whole
   of `musicians-toolkit.wh.cpp`, and click **Compile mod**.
4. Turn the mod on and press **Win+Y**.

The mod is a single file and needs nothing beside it: the chord shapes are
packed into the source. It loads into `windhawk.exe` only, so it does not
inject anything into the programs you have running.

## Keyboard shortcuts

| Keys | Does |
| --- | --- |
| `Win+Y` | Open or close the panel. The key and the modifiers are configurable. |
| `Ctrl+Tab` | Switch tools. |
| `Tab` | Switch instrument, and step through the pattern list without opening it. |
| `Up` / `Down` | Move through search results, step the metronome, hop between the two rings of the circle. |
| `Left` / `Right` | Step through fingerings, halve or double a tap tempo, change the metronome's meter. |
| `D` `U` `X` `M` `R` | Lay a downstroke, upstroke, muted downstroke, muted upstroke or a rest on the strumming grid. |
| `A` `G` `T` `C` | Accent, ghost note, tie and clear the strumming bar. |
| `Space` | Tap tempo, and start or stop a note training round. |
| `0` - `9` | Type a tempo in the metronome or the strumming tool; `Enter` sets it, `Esc` cancels. |
| `1` - `7` | Play a diatonic chord of the selected key. |
| `R` | Reset the note trainer's score. |
| `N` | Keep the note trainer to the naturals. |
| `Esc` | Clear the search, cancel a typed tempo, or close the panel. |

## Strumming patterns

The list is grouped by the grid the pattern is written in - quarters, eighths,
triplets, sixteenths - and picking one moves the grid chips to match, so a
pattern always arrives with the cells it was notated for.

| Meter | Patterns |
| --- | --- |
| 4/4 | Downstrokes, Eighths, Down-up, Folk, Pop, Backbeat, Skank, Chug, Shuffle, Sixteenths |
| 3/4 | Downstrokes, Waltz, Eighths, Shuffle, Sixteenths |
| 2/4 | Downstrokes, Eighths, Off-beats, Shuffle, Sixteenths |
| 5/4 | Three-two, Two-three, Eighths, Shuffle, Sixteenths |
| 6/8 | Downstrokes, Ballad, Eighths, Sixteenths |
| 7/8 | Two-two-three, Three-two-two, Sixteenths |
| 9/8 | Downstrokes, Ballad, Sixteenths |
| 12/8 | Downstrokes, Shuffle, Sixteenths |

## Settings

| Group | Holds |
| --- | --- |
| Startup | The tool and instrument the panel opens with. |
| Appearance | Theme, accent colour, panel opacity. |
| Placement | Corner, monitor, offsets, and whether the panel closes when it loses focus. |
| Shortcut | The key and modifiers that open the panel. |
| Metronome | Volume, background playback, default meter and subdivision. |
| Chords | Whether to hide the finger numbers. |
| Tap tempo | How long a pause resets the taps, and whether Space or Enter tap. |
| Tuner | Reference pitch, from 415 to 466 Hz. |
| Strumming | Default grid, count-in, loop, and the practice ramp and target. |
| Circle of fifths | Which ring opens, and whether selecting a key plays its scale. |
| Note trainer | Seconds per note, and chromatic or naturals only. |

## Notes on how it works

- The panel is painted by a small software renderer that ships in the file
  itself: no browser, no UI framework, no resource files.
- The plucked voices are Karplus-Strong strings, and the piano is a struck
  model of the same shape, all rendered at the device's own sample rate.
- The metronome renders in blocks on its own thread and publishes the cue a
  block late, so the playhead on the strumming grid lands where the sound does
  rather than where the clock does.
- Chord shapes come from chords-db, packed into the mod at build time.

## Working on it

The mod is compiled by Windhawk, not by a local toolchain, so the source has to
stay well formed for the editor's compiler. These scripts keep an eye on the
parts that are easy to break by hand:

| Command | Checks |
| --- | --- |
| `node tools/check-balance.js` | Braces, parens and brackets balance across the file, and prints the line count. |
| `node tools/check-settings.js` | Every setting in the YAML is one `LoadSettings` actually asks for, so a typo cannot fall back to a default unnoticed. |
| `node tools/check-presets.js` | Every strumming pattern is `beats * subdivision` cells long, the rows of a meter run from the thinnest grid up, and the busiest list still fits the card. |
| `node tools/build-chord-data.js` | Rebuilds the packed chord data from `data/*.json` and writes it into the mod. |
| `node tools/inspect-data.js` | A structural report of the chords-db files, for when the decoder looks wrong. |

Run the three checks before compiling - one per line, since Windows PowerShell
does not accept `&&`:

```
node tools/check-balance.js
node tools/check-presets.js
node tools/check-settings.js
```

## Credits

Chord data by David Rubert from
[chords-db](https://github.com/tombatossals/chords-db), MIT licensed and
packed into the mod.

Released under the MIT license.
