# S612 transfer

A browser tool for the Akai S612 sampler. It sends a sound to the sampler, reads one back, edits it, records through the sampler's own converters and keeps a library of sounds as disk images. It runs in Chrome or Edge with no install, talks to the sampler over MIDI, and the whole app is one HTML file. Made by OPT.

**Use it now: https://opt-fx.github.io/s612-transfer/**

**Status: release candidate.** It has been tested on one real S612. Nothing is uploaded anywhere: sounds and library folders stay on your computer.

## Quick start

1. Open the app's page, or download `index.html` and open it from disk, in **Chrome or Edge** (Safari and Firefox have no Web MIDI).
2. Press **Demo unit** to try everything against a simulated sampler held in the page, or choose your MIDI ports and press **Connect** for a real one (see Setup).
3. **Open WAV** (or drop a WAV, AIFF or `.QD` disk image on the page). The sound appears in the editor.
4. Tick **Allow writes**, then press **Send**. The sampler's whole sample memory is replaced by the sound.

## Setup

- **MIDI both ways.** A MIDI interface with one cable from the computer to the sampler's MIDI IN, and one from its MIDI OUT back to the computer. Sending and reading both need System Exclusive, which the browser asks about when you press Connect.
- **Unit number.** Set **Unit** to the MIDI channel number shown on the sampler's display (0 is omni).
- **Sending replaces the memory.** The S612 holds one sample at a time, so Send, Record and Calibrate overwrite it. They stay disabled until **Allow writes** is ticked. A power cycle or a disk load brings back a saved sound.
- For **Record**, also connect the computer's audio output to the sampler's input.

## The sound and the Rate key

The S612 stores 12-bit words, 32,256 of them. The **Rate** key (MIDI notes 36 to 72) sets the sampling rate: 72 is 32 kHz (about 1 second), 60 is 16 kHz (2 seconds), 48 is 8 kHz (4 seconds), 36 is 4 kHz (8 seconds). Unlocked, the tool picks the highest key that fits the sound. The **padlock** keeps the key where you put it, so a short sound can stay at a deliberately low rate. **Best key** picks it again.

The sampler plays a little slow compared with its nominal rate. **Clock trim %** (default 2.5, measured on one unit) compensates, so pitch matches. **Calibrate** sends a 1 kHz tone and listens to it so you can set the trim from a tuner reading.

## Editing

- **Waveform.** Drag across it to select; the edit tools act on the selection, or on the whole sound if nothing is selected. Scroll to zoom, Shift-scroll to move along, Space to play.
- **Edit tools.** Trim, Cut, Crop to start/end, Silence, Louder, Quieter, Normalise. The round **fade knobs** at each side of the waveform set a fade in and a fade out, with a curve choice for each.
- **Undo** (Ctrl+Z) takes back the last change: an edit, a fade, Reverse, a marker, the key or the mode. Panel knobs are not included.
- **Mode.** One shot plays once, Looping repeats the loop, Alternating plays forwards then backwards. **Mono / poly** and **Reverse** are on the same panel. Reverse swaps Start and End; you can also drag one marker past the other.
- **Start, End and the loop (splice).** In **Precise** mode (on at start) Start and the splice can sit on any word, and End is counted in pages from Start. Switch it off for **Slider** mode, which works as the sampler's own sliders do. **Best match** searches near the splice for the word where the wave carries on smoothly from the loop's last word.
- **Voice** knobs hold the sampler's panel settings. **Read panel** moves the knobs to match the unit.
- **Play** previews in the browser as the sampler would play it, including the loop.

Edits happen on the computer. The sound itself goes to the sampler only when you press **Send** (or Record). The Start and End sliders, the splice, the mode and the panel knobs are written to the sampler as you change them, when one is connected. A label under the waveform says whether what you see is **ON SAMPLER** or **ON COMPUTER**.

## Sending and recording are not the same

The tool can put a sound on the sampler in two ways. They can sound slightly different, and that is expected.

**Send** writes the sound into the sampler's memory word for word, over MIDI. What is in the editor is what the sampler holds. A loop point set in the editor lands on exactly the same word, so a loop that is clean in the editor is clean on the sampler. **Verify** reads the whole memory back afterwards and compares it.

**Record** plays the sound out of the computer's audio output into the sampler's audio input, and the sampler records it with its own converters. The sound then goes through the analogue path, so the recorded words are not identical to the editor's:

- **The start moves a little.** The sampler starts recording when the signal rises above its threshold, so the first word can land a few samples earlier or later. Everything after it moves too, and a loop point you set in the editor then sits a few words away from the place you tuned.
- **The clock is not exactly the computer's.** The sampler's converter clock differs slightly from the computer's rate, so over a longer sound the length can differ by a few words.
- **Level and noise change.** The signal passes through analogue stages and is quantised again to 12 bits, which adds a little noise and changes the sample values slightly.

A small click at the loop after recording is therefore normal. After a recording the tool reads the sound back into the editor (the whole memory, silence included), so you can fix the loop on what the sampler actually recorded, for example with Best match. If you need the loop to be exact without any fixing, use Send. Record is for when you want the sampler's own converters on the sound.

To record, set the **Count in** time, press Record, and press NEW on the sampler during the count in.

## The end of a sound on the sampler

The S612 fades the last two pages (about 16 ms) before the End marker on its own, and adds a small thump (about 29 dB below a full-level tone) as the sound stops. This happens whichever way the sound plays, and nothing in the data you send changes it. It is hard to hear when the sound has already decayed. Play and the Library audition apply the same quick fade to one-shot sounds, so they sound like the unit.

## Library and disk images

**Save QD** writes the sound and the panel settings as a `.QD` disk image, for a Gotek or HxC floppy emulator: put it on the USB stick and Load it on the sampler. **Open WAV** also accepts a `.QD`, from this tool, from the sampler's own save, or from a sound library.

The **Library** panel works on a folder of `.QD` files on your computer (the browser asks once and remembers the folder; subfolders are included):

- **Audition (hold)** plays a sound without opening it. Open puts it in the editor. **Send to sampler** sends it straight from the list.
- **Save to library**, **Fetch from sampler**, **Import files**, **Export all**, with names, tags and notes you can filter on.

The tool reads S612 sound images in the layouts found in the wild, including ones with the loop start outside Start..End and reversed sounds. A `.QD` from another machine, such as an Akai S700 disk, is not an S612 sound and is refused with a message.

## Other things worth knowing

- **Activity** logs what happens; **Details** adds the wire-level lines (notes sent, each setting written).
- AIFF and WAV files of any rate, mono or stereo, 8 to 32 bit, can be opened.
- Needs a connected sampler: Send, Read, Record, Read panel, Calibrate, Fetch from sampler. Everything else, including editing, saving and the Library, works without one.

## Licence

GPL-3.0, see `LICENSE`.
