# KT3 / KT4 File Format

**English** | [日本語](#日本語)

`.kt3` and `.kt4` are single-file formats for **performing** a track like a DJ:
the audio or music video, loop points, lyrics, beat grid, visual cues and an optional
vocal / instrumental split, all in one file.

The `.kt4` video is stored so that **any frame can be shown with a bounded amount of
work**. This lets you scratch a music video like a record, smoothly, even in a web
browser and on a low-end PC.

- **Specification:** [SPEC.md](SPEC.md)
- **Version history:** [CHANGELOG.md](CHANGELOG.md)
- **Player:** [KT3 Player](https://github.com/KEITO00/kt3-player) (Windows) — also in the browser at <https://kt3.keitodaze.net/>
- **Creator:** [KT3 Creator](https://github.com/KEITO00/kt3-creator) (Windows)

## Highlights

| | |
|---|---|
| Scratchable video | Version 6 stores the video as anchor key frames plus frames that reference only their anchor. Any frame needs at most two decodes, in any direction, at any speed |
| Small files | Much smaller than an all-intra video at the same quality |
| Seamless loops | Loop points are stored with sample precision |
| Everything in one file | Lyrics (two lines), beat grid and meter changes, chorus sections, spark cues, stems, thumbnail |
| Standard codecs | Opus audio, VP9 video in MP4, WebP images. No custom codec is needed to read a file |

## License

The specification is licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
Anyone may read and write KT3 / KT4 files in their own software. See [LICENSE](LICENSE).

---

## 日本語

`.kt3` と `.kt4` は、曲を DJ のように **演奏する** ためのファイル形式です。
音声またはミュージックビデオ、ループ位置、歌詞、拍の情報、演出のタイミング、
ボーカル / インストの分離音源を、1 つのファイルにまとめます。

`.kt4` の動画は、**どのコマでも決まった少ない作業量で表示できる** ように格納されています。
そのため、ブラウザや低い性能の PC でも、ミュージックビデオをレコードのように滑らかにスクラッチできます。

- **仕様：** [SPEC.ja.md](SPEC.ja.md)
- **変更履歴：** [CHANGELOG.md](CHANGELOG.md)
- **プレイヤー：** [KT3 Player](https://github.com/KEITO00/kt3-player)（Windows）。ブラウザ版は <https://kt3.keitodaze.net/>
- **作成アプリ：** [KT3 Creator](https://github.com/KEITO00/kt3-creator)（Windows）

## 特徴

| | |
|---|---|
| スクラッチできる動画 | version 6 は、基準のコマ（キーフレーム）と、基準のコマだけを参照するコマで動画を持つ。どのコマも最大 2 回の処理で出せて、向きや速さに関係ない |
| 小さいファイル | 同じ画質の全コマ独立の動画より、ずっと小さい |
| つなぎ目のないループ | ループ位置をサンプル単位の精度で持つ |
| 1 つのファイルにすべて | 歌詞（2 行）、拍と拍子の変化、サビの区間、火花のタイミング、分離音源、サムネイル |
| 標準の形式だけ | 音声は Opus、映像は MP4 の中の VP9、画像は WebP。独自のコーデックなしで読める |

## ライセンス

仕様は [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/deed.ja) で公開しています。
誰でも自分のソフトで KT3 / KT4 を読み書きできます。[LICENSE](LICENSE) を参照してください。
