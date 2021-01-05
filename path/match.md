### `func Match(pattern, name string) (matched bool, err error)`

- 概要
    - `name` の中に `pattern` がマッチするか判定する

### `func scanChunk(pattern string) (star bool, chunk, rest string)`

- 概要
    - `*` のオペランドを抜き出して返す
    - ex. `[abc]*foo` => `return false, "[abc]", "foo"

### `func matchChunk(chunk, s string) (rest string, ok bool, err error)`

- 概要
    - `chunk` が `s` の先頭文字列とマッチするか判定する

