# KT3 / KT4 File Format Specification

日本語版: [SPEC.ja.md](SPEC.ja.md)

This document describes the `.kt3` (audio) and `.kt4` (video) file formats used by
[KT3 Player](https://github.com/KEITO00/kt3-player) and
[KT3 Creator](https://github.com/KEITO00/kt3-creator).

A KT file contains the audio (or a music video) together with loop points, lyrics, BPM,
chorus sections, spark cue times, an optional vocal and instrumental track, and a
thumbnail. In `.kt4` version 6, any frame of the video can be decoded from at most two
samples, so a player can seek and scratch without decoding long runs of frames.

The key words "MUST", "SHOULD" and "MAY" are used as described in RFC 2119.

## 1. Overview

```
+--------------------------+  offset 0
| Header (48 bytes)        |
+--------------------------+  48
| Meta (JSON, UTF-8)       |  metaLen bytes
+--------------------------+
| Lyrics (line 1)          |  lyricsLen bytes
+--------------------------+
| Media                    |  mediaLen bytes   audio file (KT3) / MP4 video (KT4)
+--------------------------+
| Sparks                   |  sparkLen bytes
+--------------------------+
| Frame cache (legacy)     |  frameCacheLen bytes, 0 in current files
+--------------------------+
| Vocal stem               |  meta.stems.vocalLen bytes (optional)
+--------------------------+
| Instrumental stem        |  meta.stems.instLen bytes (optional)
+--------------------------+
| Thumbnail                |  meta.thumbnailLen bytes (optional)
+--------------------------+  end of file
```

All integers are little-endian. All times are in seconds from the start of the track.

## 2. Header

| Offset | Size | Type | Field | Description |
|---|---|---|---|---|
| 0 | 3 | ASCII | magic | `KT3` for audio files, `KT4` for video files |
| 3 | 1 | u8 | reserved | 0 |
| 4 | 1 | u8 | version | Storage version (see 2.1) |
| 5 | 1 | u8 | mediaFormat | Hint for the media type (see 2.2) |
| 6 | 2 | — | reserved | 0 |
| 8 | 8 | f64 | loopStart | Loop start time (loop 1, see 3.1) |
| 16 | 8 | f64 | loopEnd | Loop end time. 0 means the end of the track (loop 1, see 3.1) |
| 24 | 4 | u32 | metaLen | Size of the Meta section |
| 28 | 4 | u32 | lyricsLen | Size of the Lyrics section |
| 32 | 4 | u32 | mediaLen | Size of the Media section |
| 36 | 2 | u16 | crossfadeMs | Crossfade length used at the loop point, in milliseconds. 0 = none (loop 1, see 3.1) |
| 38 | 4 | u32 | sparkLen | Size of the Sparks section |
| 42 | 4 | u32 | frameCacheLen | Size of the legacy Frame cache section. 0 in current files |
| 46 | 2 | — | reserved | 0 |

Loop points are stored as 64-bit floats so that they can be sample-accurate.
A player SHOULD loop from `loopEnd` back to `loopStart` without a gap.

### 2.1 version

`version` describes **how the video is stored**. It does not describe the audio codec.

- KT3 files always use version `1`.
- KT4 files use versions `1` to `6` (see section 6). Writers SHOULD use `6`.

### 2.2 mediaFormat

`mediaFormat` is only a hint. Readers SHOULD detect the real format from the data itself
(for example the `OggS` / `OpusHead` signature, or the MP4 sample entries).

| Value | KT3 | KT4 |
|---|---|---|
| 0 | MP3 | MP4 |
| 1 | Ogg | WebM |
| 2 | WAV | — |
| 3 | M4A (AAC) | — |
| 4 | Ogg Opus | — |

## 3. Meta

A UTF-8 JSON object. Unknown keys MUST be ignored by readers. All keys are optional
except `title` and `artist`.

| Key | Type | Description |
|---|---|---|
| `title` | string | Track title |
| `artist` | string | Artist name |
| `lyrics2` | array of `{ time, text }` | Second lyrics line (for example a translation or a call-and-response part) |
| `bpm` | number | Tempo in beats per minute |
| `bpmOffset` | number | Time of a beat, in seconds. Beats are at `bpmOffset + n * 60 / bpm` |
| `bpmBeats` | integer | Beats per bar. Default 4 |
| `bpmDownbeat` | integer | Which beat (0-based, counted from `bpmOffset`) is the first beat of a bar. Default 0 |
| `beatMap` | array of `{ t, beats, bpm? }` | Changes of meter, sorted by `t`. At time `t` a new bar starts (`t` is its first beat), the bar has `beats` beats from then on, and the tempo becomes `bpm` if given |
| `chorusSections` | array of `{ start, end, bpm, offset }` | Chorus sections. Players MAY flash the screen on the beat (`offset + n * 60 / bpm`) inside a section |
| `gain` | number | Playback gain applied to all audio. Default 1 |
| `loops` | array of `{ start, end, crossfadeMs?, name? }` | List of loops (see 3.1) |
| `stems` | object | Present when stems are stored (see section 7) |
| `thumbnailLen` | integer | Size of the Thumbnail section (see section 8) |

### 3.1 Loops

A track can have up to 10 loops. This is for tracks such as game music, where the part that repeats depends on the scene.

| Key | Type | Description |
|---|---|---|
| `start` | number | Loop start time (seconds) |
| `end` | number | Loop end time (seconds). 0 means the end of the track |
| `crossfadeMs` | integer | Crossfade length at the loop point (milliseconds). Default 0 |
| `name` | string | Display name (up to 24 characters). Without it, players show a number such as "Loop 1" |

- The first entry of `loops` is called loop 1.
- Writers MUST also write loop 1 into `loopStart`, `loopEnd` and `crossfadeMs` of the header.
  - This lets older players that do not know `loops` still repeat loop 1.
- Without `loops`, the track has only the loop in the header (loop 1).
- Readers ignore an entry whose `end` is greater than 0 and not after `start`.
- A player SHOULD repeat only the one loop the user selected. Loop 1 is selected at first.
- When another loop is selected during playback, a player SHOULD keep playing from the current position and go back to the start of the new loop when it reaches the end of the new loop.
  - If the current position is after the end of the new loop, the player SHOULD move to the start of the new loop right away.

## 4. Lyrics (line 1)

```
u32 count
repeat count times:
  f32 time        seconds
  u16 textLen     bytes
  u8[textLen]     UTF-8 text
```

Entries are sorted by time. Each line is shown from its time until the next entry.
An empty text clears the line. The second lyrics line is stored in `meta.lyrics2`.

## 5. Sparks

A list of f32 times (`sparkLen / 4` entries). Players MAY play a visual effect
(sparks) at each time.

## 6. Media

### 6.1 KT3: audio

The Media section is a complete audio file. Current writers produce **Ogg Opus**.
Older files may contain MP3, Ogg Vorbis, WAV or AAC (M4A). `mediaLen` MAY be 0 when
both stems are stored (section 7).

### 6.2 KT4: video

The Media section is a video file. The audio track, if any, is inside the video file.
How the video is stored depends on `version`:

| version | Video | Scratching |
|---|---|---|
| 1, 2 | A normal video (MP4 or WebM). May have an old frame cache (6.4) | Without a frame cache, a player can only seek |
| 3 | A normal video, plus a frame cache of still images (6.4) | Uses the still images |
| 4 | A normal video, plus a frame cache that is a second, all-intra MP4 (6.4) | Uses the all-intra MP4 |
| 5 | An all-intra MP4: every video sample is a sync sample | Any frame can be decoded alone |
| 6 | **Anchor-reference MP4** (6.3) | Any frame can be decoded from at most two samples |

Writers SHOULD use version 6. Current writers use VP9 video and Opus audio.

The MP4 SHOULD be "fast start" (the `moov` box before `mdat`) so that a reader can find
every sample by reading only the beginning of the media.

### 6.3 Version 6: anchor-reference video

Version 6 keeps the random access of an all-intra video while storing most frames as
small differences.

- **Anchor frames** are key frames (sync samples). They appear at a fixed interval
  (current writers: every 8 frames) and at the start of the video.
- **Every other frame** is predicted **only from the most recent anchor frame**. It
  does not reference any other frame, and it does not change any decoder state that a
  later frame could depend on (no reference buffer updates, no probability context
  updates).

As a result, any frame `n` can be shown by decoding exactly:

1. the anchor frame `a` (the last sync sample at or before `n`), then
2. frame `n` itself (if `n` is not the anchor).

The result MUST be identical no matter which frames were decoded before, and in which
order. A player can therefore keep the current anchor decoded and jump to any frame of
its group with a single decode, in either direction and at any speed.

Readers MUST find anchors from the MP4 sync sample table (`stss`) and MUST NOT assume a
fixed interval.

Writer notes for VP9 (libvpx): anchors are forced key frames; the other frames use the
GOLDEN reference only, with no reference refresh, no entropy (probability) update and
error-resilient mode, so that no frame depends on the previous frame.

### 6.4 Legacy frame cache (versions 1 to 4)

Readers MAY ignore the frame cache and only seek in the video. Writers MUST NOT write
it (`frameCacheLen` = 0).

- **Versions 1, 2:** `u32 count`, then `count` entries of `{ f32 time, u32 length }`,
  then the images (WebP) back to back.
- **Version 3:** `u32 count`, `f32 time[count]`, `u32 offset[count]` (from the start of
  the section), `u32 length[count]`, then the images (WebP).
- **Version 4:** an all-intra MP4 of the same video.

## 7. Stems

When `meta.stems` is present, the file contains a vocal and/or an instrumental part
right after the Frame cache section:

```json
"stems": { "vocalLen": 123456, "instLen": 234567, "vocalGain": 1, "instGain": 1 }
```

- `vocalLen`, `instLen`: sizes in bytes. 0 means the part is absent.
- `vocalGain`, `instGain`: gain for each part (default 1), applied on top of `meta.gain`.
- Each part is a complete audio file. Current writers produce Ogg Opus.
- Each part is located from the end of the file: the instrumental part ends where the
  Thumbnail begins, and the vocal part ends where the instrumental part begins.

When both parts exist, a player plays their sum and MAY let the user control them
separately. In that case the main audio (KT3 Media, or the KT4 audio track) MAY be
absent.

## 8. Thumbnail

When `meta.thumbnailLen` is greater than 0, the last `thumbnailLen` bytes of the file
are a square WebP image (current writers: 320 × 320). It is used as the record label.

## 9. Reading a file

1. Read the 48-byte header and check `magic`.
2. Read Meta and Lyrics (they follow the header).
3. The Media section starts at `48 + metaLen + lyricsLen`.
4. Sparks and the Frame cache follow the Media section.
5. The Thumbnail and stems are located from the end of the file using the sizes in Meta.

A reader does not need to load the whole file. For KT4 version 5 and 6, a reader can
read only the MP4 `moov` box and then fetch individual samples by their offsets.

## 10. Compatibility rules

- Readers MUST ignore unknown Meta keys.
- Readers SHOULD accept every version from 1 to 6.
- New fields will be added to Meta, not to the header, whenever possible.

## 11. License

This specification is licensed under
[Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/).
Anyone may implement it. See [LICENSE](LICENSE) for the use of the KT3 name.
