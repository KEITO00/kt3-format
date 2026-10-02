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

### Contents of a file

- Audio (`.kt3`): Ogg Opus. Older files may contain MP3, Ogg Vorbis, WAV or AAC
- Video (`.kt4`): MP4 with VP9 video and Opus audio
- Loop start and end (64-bit float seconds)
- Lyrics, up to two lines
- BPM, beat grid and meter changes
- Chorus sections and spark cue times
- Vocal and instrumental tracks (optional)
- Thumbnail (WebP, optional)

### Video in `.kt4` (version 6)

Key frames are placed at a fixed interval, and every other frame references only the
preceding key frame. Any frame can be decoded from at most two samples, so seeking and
scratching stay cheap. See [SPEC.md §6.3](SPEC.md#63-version-6-anchor-reference-video).

### License

The specification is licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
You can implement the format in your own software. See [LICENSE](LICENSE).

## 日本語

[KT3 Player](https://github.com/KEITO00/kt3-player) と
[KT3 Creator](https://github.com/KEITO00/kt3-creator) で使う
`.kt3`（音声）と `.kt4`（動画）のファイル形式の仕様です。

音声や動画と一緒に、ループ位置・歌詞・BPM など、再生に使う情報を 1 つのファイルに入れています。

### 文書

- [SPEC.ja.md](SPEC.ja.md)：仕様（[English](SPEC.md)）
- [CHANGELOG.md](CHANGELOG.md)：形式の版ごとの変更

### ファイルに入るもの

- 音声（`.kt3`）：Ogg Opus。古いファイルでは MP3・Ogg Vorbis・WAV・AAC のこともあります
- 動画（`.kt4`）：MP4（映像は VP9、音声は Opus）
- ループの開始と終了（秒・64 ビット浮動小数点数）
- 歌詞（2 行まで）
- BPM、拍の位置、拍子の変化
- サビの区間、火花のタイミング
- ボーカルとインストの音声（任意）
- サムネイル（WebP・任意）

### `.kt4` の動画（version 6）

一定の間隔でキーフレームを置き、それ以外のコマは直前のキーフレームだけを参照します。
どのコマも最大 2 つのサンプルから作れるので、移動やスクラッチが軽く済みます。
詳しくは [SPEC.ja.md の 6.3](SPEC.ja.md) を見てください。

### ライセンス

仕様は [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/deed.ja) で公開しています。
自分のソフトに自由に実装できます。詳しくは [LICENSE](LICENSE) を見てください。
