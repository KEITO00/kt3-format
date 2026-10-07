# Changelog / 変更履歴

The `version` byte of the header describes how a KT4 file stores its video and audio.
KT3 files always use version 1.

ヘッダーの `version` は、KT4 の動画と音声の持ち方を表します。KT3 は常に 1 です。

## Version 7 (KTC audio) and scene-adaptive anchors — 2026-10-07

- `mediaFormat` 5 means KTC, an audio compression format designed mainly for use with KT3.
  - Current writers produce KTC for KT3 audio and stems.
  - See [SPEC.md §2.2](SPEC.md#22-mediaformat).
- KT4 version 7 stores the audio as KTC outside the MP4.
  - The MP4 contains only video.
  - The audio comes before the stems, and its size is `meta.audioLen`.
  - This makes the audio of KT3 and KT4 the same format.
  - See [SPEC.md §6.5](SPEC.md#65-kt4-audio).
- Current writers place KT4 anchor frames to fit the scene instead of every 8 frames.
  - Calm scenes get longer intervals (up to 240 frames), which makes the video smaller.
  - Readers already find anchors from `stss`, so they need no change.

- `mediaFormat` の 5 を、主に KT3 の利用を目的とした独自の音声圧縮方式 KTC にした。
  - 今の作成アプリは、KT3 の音声と分離音源を KTC で書く。
  - [SPEC.ja.md 2.2](SPEC.ja.md) を参照。
- KT4 の version 7 では、音声を MP4 の外に KTC で持つようにした。
  - MP4 は映像だけにする。
  - 音声は分離音源の前に入り、大きさは `meta.audioLen` に書く。
  - KT3 と KT4 の音声を、同じ形式にそろえるため。
  - [SPEC.ja.md 6.5](SPEC.ja.md) を参照。
- 今の作成アプリは、KT4 の基準のコマを 8 コマごとではなく、場面に合わせて置くようにした。
  - 動きの少ない場面では間隔が長くなり（最大 240 コマ）、動画が小さくなる。
  - 読む側はすでに `stss` から基準のコマを求めているので、変更は要らない。

## Meta `loops` — 2026-10-05

- A track can have up to 10 loops in `meta.loops`.
  - Each loop has a start, an end, a crossfade and an optional name.
- Loop 1 is also written to the header.
  - This lets older players still repeat loop 1.
- The `version` byte does not change.
- See [SPEC.md §3.1](SPEC.md#31-loops).

- 情報欄の `loops` で、1 つの曲にループを最大 10 個持てるようにした。
  - それぞれのループは、開始・終了・クロスフェード・名前（任意）を持つ。
- ループ 1 は、ヘッダーにも書く。
  - 古いプレイヤーでも、ループ 1 でくり返せるようにするため。
- `version` は変えない。
- [SPEC.ja.md 3.1](SPEC.ja.md) を参照。

## Version 6 — 2026-10-02

- KT4 video is an anchor-reference MP4: anchor key frames at a fixed interval, and every
  other frame predicted only from its anchor. Any frame can be decoded from at most two
  samples. See [SPEC.md §6.3](SPEC.md#63-version-6-anchor-reference-video).
- Smaller than version 5 at the same quality.
- Current writers: VP9 video, Opus audio. KT3 audio and stems are Ogg Opus.
- Loop points are written with full precision (no rounding).

- KT4 の動画を「基準コマ参照」の MP4 にした。一定間隔の基準のコマ（キーフレーム）と、
  基準のコマだけから予測するそれ以外のコマ。どのコマも最大 2 つのサンプルから作れる。
  [SPEC.ja.md 6.3](SPEC.ja.md) を参照。
- 同じ画質で version 5 より小さい。
- 今の作成アプリ：映像は VP9、音声は Opus。KT3 の音声と分離音源は Ogg Opus。
- ループ位置を丸めずに書くようにした。

## Version 5

- KT4 video is an all-intra MP4 (every sample is a sync sample), read lazily from the file.
- KT4 の動画を全コマ独立の MP4 にし、ファイルから必要な分だけ読むようにした。

## Version 4

- KT4: a second, all-intra MP4 of the video is stored in the frame cache section.
- KT4：コマのキャッシュに、同じ動画の全コマ独立の MP4 を別に持つ。

## Version 3

- KT4: the frame cache is a table of still images (WebP) with a separate index.
- KT4：コマのキャッシュを、索引つきの静止画（WebP）の表にした。

## Versions 1, 2

- KT4: a normal video, with an optional list of still images (WebP).
- KT4：普通の動画。静止画（WebP）の並びを持つこともある。
