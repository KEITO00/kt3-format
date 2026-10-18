# Changelog / 変更履歴

The `version` byte of the header describes how a KT4 file stores its video and audio.
KT3 files always use version 1.

ヘッダーの `version` は、KT4 の動画と音声の格納方式を表します。KT3 では常に 1 です。

## Audio after the end of a loop (2026-10-18)

- Writers and players SHOULD treat the continuation of the loop (the audio from the loop start) as the audio that follows the end of the audio.
  - Writers do this when they compress the audio as KTC, and players do this when they convert the sampling rate.
  - If the part after the end is treated as silence, a click can be heard at the loop point.
  - See [SPEC.md §3.1](SPEC.md#31-loops).
- The `version` byte does not change.

- 書き込み側とプレイヤーは、ループの続きの音（ループの開始位置からの音）を、音声の最後に続く音として扱うべきです（SHOULD）。
  - 書き込み側は KTC で圧縮する際に、プレイヤーはサンプリング周波数を変換する際に扱います。
  - 音声の最後より後ろを無音として扱うと、ループのつなぎ目で雑音が聞こえるためです。
  - [SPEC.ja.md 3.1](SPEC.ja.md) を参照してください。
- `version` は変更しません。

## Version 7 (KTC audio) and scene-adaptive anchors (2026-10-07)

- `mediaFormat` 5 means KTC, an audio compression format designed mainly for use with KT3.
  - Current writers produce KTC for KT3 audio and stems.
  - See [SPEC.md §2.2](SPEC.md#22-mediaformat).
- KT4 version 7 stores the audio as KTC outside the MP4.
  - The MP4 contains only video.
  - The audio comes before the stems, and its size is `meta.audioLen`.
  - KT3 and KT4 now use the same audio format.
  - See [SPEC.md §6.5](SPEC.md#65-kt4-audio).
- Current writers place KT4 anchor frames to fit the scene instead of every 8 frames.
  - Calm scenes get longer intervals (up to 240 frames), which makes the video smaller.
  - Readers already find anchors from `stss`, so they need no change.

- `mediaFormat` の 5 を、主に KT3 での利用を目的とした独自の音声圧縮方式 KTC としました。
  - 現行の実装では、KT3 の音声と分離音源を KTC で書き込みます。
  - [SPEC.ja.md 2.2](SPEC.ja.md) を参照してください。
- KT4 の version 7 では、音声を MP4 の外に KTC で格納するようにしました。
  - MP4 は映像のみとします。
  - 音声は分離音源の前に格納し、サイズを `meta.audioLen` に記録します。
  - KT3 と KT4 の音声を、同じ形式に統一するためです。
  - [SPEC.ja.md 6.5](SPEC.ja.md) を参照してください。
- 現行の実装では、KT4 の基準フレームを 8 フレームごとではなく、場面に応じて配置するようにしました。
  - 動きの少ない場面では間隔が長くなり（最大 240 フレーム）、動画のサイズが小さくなります。
  - 読み込み側は、すでに `stss` から基準フレームを求めているため、変更は不要です。

## Meta `loops` (2026-10-05)

- A track can have up to 10 loops in `meta.loops`.
  - Each loop has a start, an end, a crossfade and an optional name.
- Loop 1 is also written to the header.
  - Older players can still repeat loop 1.
- The `version` byte does not change.
- See [SPEC.md §3.1](SPEC.md#31-loops).

- メタ情報の `loops` により、1 つの曲に最大 10 個のループを設定できるようにしました。
  - 各ループは、開始位置・終了位置・クロスフェード・名前（任意）を持ちます。
- ループ 1 は、ヘッダーにも書き込みます。
  - 古いプレイヤーでも、ループ 1 を繰り返せるようにするためです。
- `version` は変更しません。
- [SPEC.ja.md 3.1](SPEC.ja.md) を参照してください。

## Version 6 (2026-10-02)

- KT4 video is an anchor-reference MP4.
  - Anchor key frames are placed at a fixed interval, and every other frame is predicted only from its anchor.
  - Any frame can be decoded from at most two samples.
  - See [SPEC.md §6.3](SPEC.md#63-version-6-anchor-reference-video).
- Files are smaller than version 5 at the same quality.
- Current writers use VP9 video and Opus audio.
  - KT3 audio and stems are Ogg Opus.
- Loop points are written with full precision (no rounding).

- KT4 の動画を「基準フレーム参照」の MP4 にしました。
  - 基準フレーム（キーフレーム）を一定の間隔で配置し、それ以外のフレームは基準フレームだけから予測します。
  - 任意のフレームを、最大 2 つのサンプルからデコードできます。
  - [SPEC.ja.md 6.3](SPEC.ja.md) を参照してください。
- 同じ画質で、version 5 よりサイズが小さくなりました。
- 現行の実装では、映像を VP9、音声を Opus で書き込みます。
  - KT3 の音声と分離音源は Ogg Opus です。
- ループ位置を丸めずに書き込むようにしました。

## Version 5

- KT4 video is an all-intra MP4 (every sample is a sync sample), read lazily from the file.
- KT4 の動画をオールイントラの MP4 にし、ファイルから必要な部分だけを読み込むようにしました。

## Version 4

- KT4 stores a second, all-intra MP4 of the video in the frame cache section.
- KT4 では、フレームキャッシュに、同じ動画のオールイントラの MP4 を別に格納します。

## Version 3

- The KT4 frame cache is a table of still images (WebP) with a separate index.
- KT4 のフレームキャッシュを、索引付きの静止画（WebP）の表にしました。

## Versions 1, 2

- KT4 video is a normal video, with an optional list of still images (WebP).
- KT4 の動画は通常の動画で、静止画（WebP）の配列を持つ場合もあります。
