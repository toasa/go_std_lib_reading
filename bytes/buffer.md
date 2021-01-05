### `type Buffer struct`

```go
type Buffer struct {
    buf      []byte // buf[off : len(buf)] に中身が入っている
    off      int    // &buf[off] から読み、&buf[len(buf)] へ書き込む
    lastRead readOp // 最後の read オペレーション. UnreadXXX メソッドのためのもの.
}
```

```
    +-----------------------------------------+
    |             +---------------+           |
    |             |     unread    |           |
    |             +---------------+           |
    +-----------------------------------------+
                  ^               ^           ^
                  |               |           |
                 off           len(buf)     cap(buf)
           (read at here)   (write at here)

```

- 概要
    - 自分で byte 型のバッファを作るときにも参考になりそう
        - フィールドとして `buf` と `off` をもたせることで、読み書きが楽にできそうだね
        - `lastRead` はどのような効果があるのだろう。まだよくわかっていない。

#### `const maxInt`

- 概要
    - `maxInt` の定義がおもしろい。
    - 処理系最大の符号付き整数の値を取得したい場合に使えそう。
    - `const maxInt = int(^uint(0) >> 1)`


#### `func (b *Buffer) Truncate(n int)`

- 概要
    - コメントを一読したとき unread 部分を `n` バイト押し上げるメソッドかと思ったら違った。
    - unread 部分の **後ろから** `n` バイトを切り捨てるメソッドだった。
    - コメント
        - Truncate discards all but the first n unread bytes from the buffer.
        - 最初の `n` バイトを除いて、すべて切り捨てると書いてあった。

#### `func (b *Buffer) grow(n int) int`

- 概要
    - 引数 `n` バイト文バッファを拡張する
    - 返り値は拡張したバッファに対する **書き込み** をはじめるインデックス

#### `func (b *Buffer) ReadFrom(r io.Reader) (n int64, err error)`

- 概要
    - `io.Reader` インターフェイスを引数にとり、`b.buf[len(buf):]` をインターフェイスにわたすバッファとして使用し、`Read` メソッドを呼び出す。

#### `func (b *Buffer) WriteTo(w io.Writer) (n int64, err error)`

- 概要
    - `io.Writer` インターフェイスを引数にとり、バッファ `b.buf[b.off:]` に対し書き込みを行う `Write` メソッドを呼び出す。

#### `func (b *Buffer) Read(p []byte) (n int, err error)`

- 概要
    - バッファの unread な部分 `b.buf[b.off:]` を `p` へコピーする
        - `n = copy(p, b.buf[b.off:]`

#### `func (b *Buffer) UnreadByte() error`

- 概要
    - `UnreadByte()` を呼ぶ前に `ReadXXX` メソッドを呼んでいることが前提
    - `ReadXXX` メソッドで `b.off` の値を読み出した分だけインクリメントした。`UnreadByte` は `b.off` の値を `1` デクリメントし、unread の部分を `1` バイト増やす。

