- 概要
    - パースする関数群
    - `net` パッケージで使用する

#### `func splitAtBytes(s string, t string) []string`

- 概要
    - `t` 内のすべてのバイトを区切り文字として `s` を分割する
    - 例
        - `t := "abc"`, `s := "lcblbcjkl"` の場合、戻り値は `{ "l", "l", "jkl" }` となる
- 実装
    - `t` の区切り文字が `s` に何箇所存在するか数を求める
    - その数 + 1 の長さを持つ文字列型のスライスを作る
        - 最後にこのスライスをリターンする
    - `s` の内部を `for` 文でイテレートする: `s[i]`
        - `s[i]` が `t` の任意のバイトであればそれを区切り文字とし `s` を分割する

#### `func dtoi(s string) (n int, i int, ok bool)`

- 概要
    - decimal to integer の略
    - `s` の先頭の数字をパースし整数値として返す
    - 戻り値 `i` は読み進めたイテレータのインデックスを返す
    - 例
        `dtoi("404 Not Found")` -> `404`, `3`, `true`

#### `func trimSpace(x []byte) []byte`

- 概要
    - leading or trailing の whitespace を除去する
- 実装
    - シンプル
        - 先頭からイテレートし空白であれば切り詰める
        - trailing も同様

#### `func foreachLine(x []byte, fn func(line []byte) error) error`

- 概要
    - バイト列 `x` を `\n` で区切られた line ごとに切り出し、その line に `fn` をほどこす
- 実装
    - ```go
      for len(x) > 0 {
          nl := IndexByte(x, '\n')
          line := x[:nl]
          x = x[nl+1:]
          fn(line)
      }
      ```

#### `func stringsHasSuffix(s, suffix string) bool`

- 概要
    - `s` がサフィックスとして `suffix` を持つか判定する
- 実装
    - 一行
    - `return len(s) >= len(suffix) && s[len(s)-len(suffix):] == suffix`

#### `func stringsEqualFold(s, t string) bool`

- 概要
    - 大文字小文字を無視し文字列比較を行い `s` と `t` が一致するか判定する
