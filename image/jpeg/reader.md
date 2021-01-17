### `package jpeg`

- 概要
    - JPEG ファイルのデコーダとエンコーダを実装する

#### `type decoder struct`

- 概要
    - JPEG ファイルのデコーダ
- 実装
    - フィールド
        - `bytes struct`
            - `4096` バイトのバッファを中身に持つ
            - コメントの中に byte stuffing とある
                - これはネットワークでよく聞くはなし
                - 説明
                    - ネットワークの上位レイヤと下位レイヤ（例えば IP の層と Ether の層）において、上位から下位へパケットを送る場面
                    - 下位レイヤでは上位レイヤから受け取ったデータ（以降ペイロードと呼ぶ）の先頭と最後にフラグとヘッダを付け加えるとする（実際はヘッダやトレイラも加わる）
                        - ```
                                      +--------------+
                                      |     Data     |    IP packet
                                      +--------------+
                                             |
                                             |
                                             v
                                 +----+--------------+----+
                                 |flag|     Data     |flag|  Ether frame
                                 +----+--------------+----+


                          ```
                    - ペイロードは可変長であるため、もし**ペイロードの中にflagのバイトパターンが登場した**場合、このフレームの受信者は正しく Ether frame を判断できない（より短く解釈してしまう？）
                    - これを防ぐため、ペイロードの中の flag のバイトパターンの**前に** ESC 文字を挿入してエスケープする処理を行う。これが byte stuffing.
        - `tmp [2 * blockSize]byte`
            - blockSize は 8x8 = 64
            - JPEG では画像を 8 x 8 のブロックに分割し、そのブロック単位で DCT (discrete cosine transform) を行う

#### `func (d *decoder) fill() error`

- 概要
    - `d` の持つ io.Reader `d.r` から読み出し、`d.bytes.buf` へ書き込む

#### `func (d *decoder) readByte() (x byte, err error)`

- 概要
    - バッファから 1 バイト読み出す
