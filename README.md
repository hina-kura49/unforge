# unforge

Rust でビルドされたバイナリを、Rust として読むための道具です（作成中、v0.1）。

今あるのは、その土台となる ELF の読み取りです。

- **unforge-elf**: 壊れた ELF や、わざと崩した ELF でも、読めるところまで読む ELF リーダーです。
  - パニックしません。`unsafe` も使いません（`#![forbid(unsafe_code)]`）。
  - データをコピーせず、`zerocopy` で入力のバイト列を直接読みます。
  - 読み取れないのは、ELF のヘッダーすら読めない場合だけです。それ以外の異常は、すべて診断（`audit`）として報告します。Linux カーネルが無視する欄の異常で、読み取りが止まることはありません。
  - 解釈を選ばなければならない場面では、選んだ解釈と、その手がかりと、ほかの候補を記録します。
  - ファイルの中のオフセットと大きさは、すべてオーバーフローを確かめてから使います。ふつうの読み取りでは、メモリを確保しません。
- **unforge**（CLI）: ELF のヘッダー、セグメント、セクション、シンボルを表示します。

```sh
cargo run -p unforge -- elf --header --segments --sections --symbols path/to/binary
cargo run -p unforge -- elf --strict --loader-checks path/to/binary
```

## 試験

```sh
cargo test
```

- `crates/unforge-elf/tests/`: 境界の試験、ファズで見つかった入力の再実行、実際のバイナリとの突き合わせ（`object` と `goblin` の結果と比べる）。
- `testdata/`: 試験に使う実際のバイナリを作るための Rust のプログラムと、ビルド用のスクリプト（`testdata/build.sh`）。作ったバイナリは commit していません。詳しくは [testdata/README.md](testdata/README.md) を見てください。
- `fuzz/`: cargo-fuzz のファズ対象。既知の違いは [fuzz/KNOWN_DIVERGENCES.md](fuzz/KNOWN_DIVERGENCES.md) にあります。

## ライセンス

MIT または Apache-2.0 のどちらかを選んで利用できます（[LICENSE-MIT](LICENSE-MIT)、[LICENSE-APACHE](LICENSE-APACHE)）。

RustSecJP は非公式のコミュニティです。Rust Project、Rust Foundation とは提携していません。
