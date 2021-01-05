### `type lazybuf struct`

- 概要
    - コメントを読むと一応用途は書いてある
        - lazily に構築される path buffer
        - サポートするメソッド
            - append
            - 以前 append したバイトの読み出し
            - 最終バイトの取り出し
    - この構造体は `Clean` メソッドに使用される

### `func Clean(path string) string`

- 概要
    - 与えられたパスに以下のルールを施しきれいなパスへと整形する
        - 1. 複数スラッシュをシングルスラッシュへ
        - 2. `.` の削除
        - 3. 内側の `..` の削除
        - 4. root path に続く `..` の削除

### `func Split(path string) (dir, file string)`

- 概要
    - `path` をディレクトリ名とファイル名に分割する
- 実装
    - `strings.LastIndex(path, "/")` で最後のスラッシュのインデックスを取得し、`path` をスライシングしているだけ。かんたん！

### `func Ext(path string) string`

- 概要
    - `path` の拡張子を返す
- 実装
    - `for` ループで後ろからイテレートし `.` がみつかったら

### `func Base(path string) string`

- 概要
    - パスの最後の要素を返す
        - ex. `/foo/bar/baz` ==> `baz`

### `IsAbs(path string) bool`

- 概要
    - 絶対パスか否か判定する

