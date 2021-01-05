### `type Reader struct`

```go
type Reader struct {
    s        []byte // 内部に持つバッファ。ここから読み出す
    i        int64  // 現在 read するインデックス
    prevRune int    // １つ前の rune のインデックス
}
```

#### `func (r *Reader) Read(b []byte) (n int, err error)`

- 概要
    - `Reader` 構造体が `io.Reader` インターフェイスを満たすためのメソッド
    - 与えられたバッファ `b` に書き込む
    - 書き込んだバイト数 `n` を `r.i` に加算し、戻り値として返す

#### `func (r *Reader) UnreadByte() error`

- 概要
    - 現在 read するインデックス `r.i` を１つデクリメントする

#### `func (r *Reader) Seek(offset int64, whence int) (int64, error)`

- 概要
    - 現在 read するインデックス `r.i` を `offset` 分移動させる
    - 移動の計測点は `whence` で指定する
        - `whence` の値が
            - `io.SeekStart` の場合
                - バッファ `r.s` の先頭からのオフセット
            - `io.SeekCurrent` の場合
                - `r.i` からのオフセット
            - `io.SeekEnd` の場合
                - バッファの最後のインデックス `len(r.s)` からのオフセット



