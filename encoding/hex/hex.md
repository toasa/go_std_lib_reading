#### `func Decode(dst, src []byte) (int, error)`

- 動作
    - src の []byte("0A0BFF") を dst のバッファに []byte{ 10, 11, 255 } のようにデコードし書き込む。
- 実装
    - 例えば "1A" の場合、1 * (2^4) + A * (2^0) = 26 になるように、1 と A をそれぞれ10進数に直し、足せばいい。16進数の char を10進数の char に変換している関数が `fromHexChar` 。

#### `func Encode(dst, src []byte) int`

- 動作
    - 1 を []byte{0, 1}、16 を []byte{1, 0}、255 を []byte{f, f}のようにエンコードする.
    - つまり 0 から 255 までの値を {0, 0} から {f, f} までの２要素のバイトスライスにする.
       - dst の長さは src の長さの倍になる
- 実装
    - 1バイト v の上位 4 bit を格納する処理は `dst[j] = hextable[v>>4]`、下位 4bit は `dst[j+1] = hextable[v&0x0f]` と 0x0f でマスクしている。

#### `func Dump`

- 動作
    - なかなかきれいにフォーマットして hex をダンプしてくれる関数
    - ```
      00000010  2e 2f 30 31 32 33 34 35  36 37 38 39 3a 3b 3c 3d  |./0123456789:;<=|
      ^ offset                          ^ extra space              ^ ASCII of line.
      ```
    
- 実装
    - Dump 用の構造体 `dumper` を作成している。
        - `dumper := Dumper(&buf)`
        - `dumper.Write(data)`
        - `dumper.Close()`

#### `func (h *dumper) Write(data []byte) (n int, err error)`

- 実装
    - 一番外側に data を range ループする。インデックス変数 i を取り出す
        - オフセットを表示
            - まずバッファにオフセットを格納
                - Dumpなので人間に見やすいビッグエンディアン
                - buf[0] = n >> 24
                - buf[1] = n >> 16
                - buf[2] = n >> 8
                - buf[3] = n
            - byte 値を hex にエンコード
                - Encode(h.buf[4:], h.buf[:4]
                - ここまででbufには最初の4バイトと、4バイトをエンコードした8バイト文のデータ、つまり 4 + 8 = 12 バイト文のデータが格納されている。
            - オフセットの最後の2つの空白を挿入
                - h.buf[12] = ' '
                - h.buf[13] = ' '
            - Writer に書き出し
                - h.w.Write(h.buf[4:])
        - byte値を表示
            - hex へエンコードする
                - Encode(h.buf[:], data[i:i+1])
            - 空白を１つ挿入
            - Writerへ書き出し
                - h.w.Write(h.buf[:l])
        - byte値を ascii 文字として表示
            - h.rightChars に格納する
            - 16 バイトたまったら区切り線 `|` と改行を付け加え Write へ書き出す

#### `func (h *dumper) Close()`

- 実装
    - フラグ `h.closed` を立てる
    - オフセットは既にダンプ済のため、byte値を空白埋めして表示する
    - `Dump()` はデータを16バイト単位でダンプするので、例えばデータが12バイトの場合、4バイト分のデータを空白としてダンプする必要がある。以降はその処理。
        - `h.buf{ ' ', ' ', ' ', ' ', '|' }` として `h.used` の値（何バイト目まで処理したか、0 <= h.used < 16）に応じて Writer への書き出しを分岐している。なるほど賢いね。
    - byte値のascii 文字は空白を挿入せず、区切り文字 `|` と改行を書き出し、処理を終える。

#### `func (d *decoder) Read(p []byte) (n int, err error)`

- 概要
    - この関数により `decoder` 構造体は `io.Reader` インターフェースを満たす
