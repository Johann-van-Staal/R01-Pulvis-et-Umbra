# Johann van Staal - Pulvis et Umbra

Music composed and performed as code, using [Sonic Pi](https://sonic-pi.net).
These tracks explore what happens when funeral liturgy meets the dancefloor —
**Crypt House**: church bells, Latin prayers, rain and crows over house and
witch-house grooves.

Each track is a single, self-contained `.rb` file. It plays the entire
arrangement from bar 0 to the final bell and stops by itself.

## Requirements

- **Sonic Pi v4 or later** — free, available for macOS, Windows and Linux
  at [sonic-pi.net](https://sonic-pi.net). No other software is needed.
- The **sample files** (`*.wav`) from this repository, stored on your
  local machine.

## Setup

1. **Clone or download this repository** to your computer.

2. **Keep all `.wav` files together in one local folder.** The tracks load
   them from disk at startup.

   > ⚠️ If you store the folder in a cloud-synced location (iCloud Drive,
   > OneDrive, Dropbox), make sure the files are *actually downloaded* and
   > not just cloud placeholders. On macOS: right-click the folder →
   > *"Keep Downloaded"* / *"Immer auf diesem Mac behalten"*. A track that
   > hangs silently at startup is almost always a sample file that the
   > cloud has offloaded.

3. **Set your local path.** Near the top of each track file you will find:

```
   define :fx_pfad do |name|
     "... insert local path name here ..." + name + ".wav"
   end
```

   Replace the placeholder with the absolute path to your sample folder.
   **The path must end with a trailing `/`**, because the file name is
   appended directly to it:

```
   define :fx_pfad do |name|
     "/Users/yourname/Music/crypt-house/samples/" + name + ".wav"
   end
```

4. **Run the track as a file — do not paste it into a buffer.**
   Sonic Pi's editor silently truncates large pasted text, which results
   in a half-loaded track (or total silence). Instead, put a single line
   into an empty buffer and press *Run*:

```
   run_file "/Users/yourname/[your folder].rb"
```

   Press *Stop* to end playback at any time. Every track resets its own
   state (master volume, bar counter) at the top, so you can simply press
   *Run* again for a fresh playthrough.

## Samples

The tracks expect the following files, referenced by name:

```
effect_gregorian_chant.wav   effect_rain.wav      effect_craw.wav
requiem_aeternam.wav         pulvis_et_umbra.wav  lux_perpetua.wav
resurgemus.wav               amen.wav             the_mourning_is_over.wav
welcome_home.wav
```

Voice recordings by Johann van Staal. Ambience samples (rain, crows,
Gregorian chant) from free sample libraries; see their respective sources
for license details.

## The Latin texts

The two long spoken samples are transcribed in this repository, with
translations and sources:

- **`requiem_aeternam.txt`** — the Introit of the Requiem Mass
  (*"Requiem aeternam dona eis, Domine, et lux perpetua luceat eis…"*),
  traditional Latin liturgy, public domain. The second line is also the
  source of the recurring hook sample `lux_perpetua.wav`.
- **`pulvis_et_umbra.txt`** — an abridged excerpt from Horace,
  *Odes* IV.7 (*"Diffugere nives"*), ending in *"pulvis et umbra sumus"* —
  "we are dust and shadow". Classical Latin, public domain.

## How the tracks work

All tracks share the same architecture: a master clock (`live_loop :puls`)
counts bars into a shared counter, and every other loop reads that counter
to decide what to play — sections, breakdowns, fades and one-shot events
are all functions of the bar number. Fixed vocal moments live in a single
`case takt` block; ambience levels (e.g. rain) follow explicit level
curves. Because everything derives from the clock, individual loops can be
edited and re-evaluated live without losing their place in the arrangement.

## Troubleshooting

- **Total silence, clock ticking in the log** — a sample failed to load or
  is being fetched from the cloud. Check the log for errors and see the
  iCloud note above.
- **Sound cuts out mid-track with a "Timing Exception"** — your machine is
  under load. The tracks already use shared FX instances and an increased
  `set_sched_ahead_time!`; closing other applications usually resolves it.
- **You re-exported a sample but hear the old version** — Sonic Pi caches
  samples in memory. Run `sample_free_all` once in an empty buffer, then
  restart the track.
