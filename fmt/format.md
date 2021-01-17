### `package fmt`

- 概要
    - C の printf 関数などで使用されるフォーマット指定子のGo版を提供する

#### `func (f *fmt) writePadding(n int)`

- 概要
    - `f` のバッファ `f.buf` に `n` 個の空白 ` ` を追加する

#### `func (f *fmt) fmtUnicode(u int64)`

- 概要
    - Unicode のコードポイント値 `u` を Unicode の形式 `U+XXXX` へフォーマットする
    - `f.sharp` フラグが 1 の場合、`U+XXXX 'X'`のようにコードポイントが指す文字を追加する
- 実装
    - バッファ `buf` を用意し後ろから書き込む
        - `i := len(buf)` とし、`i` をデクリメントしながらバッファへ書き込む
    - `f.sharp` フラグが 1 の場合
        - ```go
          i--
          buf[i] = '\''
          i -= utf8.RuneLen(rune(u))
          utf8.EncodeRune(buf[i:], rune(u))
          i--
          buf[i] = '\''
          i--
          buf[i] = ' '
          ```
    - コードポイントの値 `XXXX` の書き込み
        - ```go
          udigits = "0123456789ABCDEFX"
          for u >= 16 {
              i--
              buf[i] = udigits[u&0xF]
              u >>= 4
          }
          i--
          buf[i] = udigits[u]
          ```

#### `func (f *fmt) fmtInteger(u uint64, base int, isSigned bool, verb rune, digits string)`

- 概要
    - 符号なし整数または符号付き整数をフォーマットする
- 実装
    - 桁が小さい方からバッファに書き込む
        - 10進数の場合
            - ```go
              for u >= 10 {
                  i--
                  next := u / 10
                  buf[i] = byte('0' + u - next*10)
                  u = next
              }
              i--
              buf[i] = digits[u]
              ```

