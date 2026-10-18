# KT3 / KT4 File Format

[English](#english) | [日本語](#日本語)

## English

Specification of the `.kt3` (audio) and `.kt4` (video) file formats used by
[KT3 Player](https://github.com/KEITO00/kt3-player) and
[KT3 Creator](https://github.com/KEITO00/kt3-creator).

A file contains the audio or video together with loop points, lyrics, BPM, and other
data used during playback.

### Documents

- [SPEC.md](SPEC.md): the specification ([日本語](SPEC.ja.md))
- [CHANGELOG.md](CHANGELOG.md): format versions

### Stored data

- Audio (`.kt3`): [KTC](https://github.com/KEITO00/ktc) (an audio compression format designed mainly for use with KT3). Older files may contain Ogg Opus, MP3, Ogg Vorbis, WAV or AAC
- Video (`.kt4`): MP4 with VP9 video, and KTC audio stored outside the MP4. Older files have Opus audio inside the MP4
- Loops, up to 10: start and end (64-bit float seconds), crossfade and an optional name
- Lyrics, up to two lines
- BPM, beat grid and meter changes
- Chorus sections and spark cue times
- Vocal and instrumental tracks (optional)
- Thumbnail (WebP, optional)

### Video in `.kt4` (version 6 and later)

Key frames are placed where the picture has changed enough, and every other frame
references only the preceding key frame.

- Any frame can be decoded from at most two samples.
  - This keeps the cost of seeking and scratching low.
- See [SPEC.md §6.3](SPEC.md#63-version-6-anchor-reference-video) for details.

### License

The specification is licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
You can implement the format in your own software. See [LICENSE](LICENSE).

## 日本語

[KT3 Player](https://github.com/KEITO00/kt3-player) と
[KT3 Creator](https://github.com/KEITO00/kt3-creator) で使用する
`.kt3`（音声）と `.kt4`（動画）のファイル形式の仕様です。

音声や動画とともに、ループ位置・歌詞・BPM など、再生に使用する情報を 1 つのファイルに格納します。

### 文書

- [SPEC.ja.md](SPEC.ja.md)：仕様（[English](SPEC.md)）
- [CHANGELOG.md](CHANGELOG.md)：形式の版ごとの変更点

### 格納するデータ

- 音声（`.kt3`）：[KTC](https://github.com/KEITO00/ktc)（主に KT3 での利用を目的とした独自の音声圧縮方式）。古いファイルでは、Ogg Opus・MP3・Ogg Vorbis・WAV・AAC の場合もあります
- 動画（`.kt4`）：MP4（映像は VP9）と、MP4 の外に格納する KTC の音声。古いファイルでは、音声は MP4 内の Opus です
- ループ（最大 10 個）：開始位置と終了位置（秒・64 ビット浮動小数点数）、クロスフェード、名前（任意）
- 歌詞（2 行まで）
- BPM、拍の位置、拍子の変化
- サビの区間、火花のタイミング
- ボーカルとインストの音声（任意）
- サムネイル（WebP・任意）

### `.kt4` の動画（version 6 以降）

映像が十分に変化した位置にキーフレームを配置し、それ以外のフレームは直前のキーフレームだけを参照します。

- 任意のフレームを、最大 2 つのサンプルからデコードできます。
  - そのため、シークやスクラッチの処理負荷を低く抑えられます。
- 詳しくは [SPEC.ja.md の 6.3](SPEC.ja.md) を参照してください。

### ライセンス

仕様は [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/deed.ja) で公開しています。
許可を得ることなく、ソフトウェアに実装できます。詳しくは [LICENSE](LICENSE) を参照してください。
