### `package io`

- 概要
    - Go が提供する様々な I/O プリミティブのラッパーの役割
        - `Reader`, `Writer` など I/O 系のインタフェースが定義されている

#### `func ReadAtLeast(r Reader, buf []byte, min int) (n int, err error)`

- 概要
    - 最低 `min` バイトを `buf` へ読み出す
- 実装
    - ```go
      for n < min && err == nil {
          var nn int
          nn, err = r.Read(buf[n:])
          n += nn
      }
      ```

#### `func ReadFull(r Reader, buf []byte) (n int, err error)`

- 概要
    - `len(buf)` バイトを `buf` へ読み出す
- 実装
    - `ReadAtLeast(r, buf, len(buf))`

#### `func copyBuffer(dst Writer, src Reader, buf []byte) (written int64, err error)`

- 概要
    - `src` から読み出し `buf` へ書き出す
- 実装
    - `buf` が `nil` の場合、32KiB のバッファサイズを指定する
    - ```go
      for {
          nr, er := src.Read(buf)
          if nr > 0 {
              nw, ew := dst.Write(buf[0:nr])
              if nw > 0 {
                  written += nw
              }
              if ew != nil {
                  err = ew
                  break
              }
          }
          if er != nil {
              err = er
              break
          }
      }
      return written, err
      ```

