## `Package binary`

- 概要
    - 以下の2つの間の変換を提供する
        - 数
        - バイト列
        - ヴァリアントのエンコード
        - ヴァリアントのデコード

### `type littleEndian struct`

- 概要
    - リトルエンディアンを扱う構造体（そのままやな）
    - `Uint16`, `Uint32`, `Uint64` などのメソッドを持つ
        - これにより `ByteOrder` インタフェースを満たす
        - 例えば `Uint16` の動作
            - `func (litteleEndian) Uint16(b []byte) uint16`
            - `uint16(b[0]) | uint16(b[1])<<8` を返す
                - つまり 2 バイトのリトルエンディアンにして返す

#### `func Read(r io.Reader, order ByteOrder, data interface{}) error`

- 概要
    - 構造化されたバイナリデータを `r` から読み出し `data` へ書き込む
    - `r` から読み出されるデータはデコードされ `data` の連続的なフィールドへ書き込まれる
- 実装
    - `data` が整数の型か整数のスライスの型の場合
        - 即席のバイトスライス `bs` を用意し `io.ReadFull(r, bs)` を行う
            - これでバイト列のデータが `bs` に書き込まれた
        - あとは `bs` をデコードし `data` に書き込む
    - 静的に `data` の型がわからない場合
        - `reflect` パッケージを使う

#### `func sizeof(t reflect.Type) int`

- 概要
    - `t` の型からメモリを使うサイズを取得する
- 実装
    - `t` の型が `reflect.Array` の場合
        - 要素の型のサイズ x 要素数
    - `t` の型が `reflect.Struct` の場合
        - 各フィールドのサイズの総和

#### `func (d *decoder) value(v reflect.Value)`

- 概要
    - `v` の種別に応じて、`v` に動的にデコードした値をセットする
