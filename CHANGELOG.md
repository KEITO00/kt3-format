# Changelog / 変更履歴

The `version` byte of the header describes how the video of a KT4 file is stored.
KT3 files always use version 1.

ヘッダーの `version` は、KT4 の動画の格納方式を表します。KT3 は常に 1 です。

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
