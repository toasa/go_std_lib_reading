- 概要
    - SMTP (Simple Mail Transfer Protocol) を実装したライブラリ

#### `type Client struct`

- 概要
    - SMTP のクライアント側
- 実装
    - フィールド
        - `conn net.Conn`
            - コネクションのリファレンス
            - SMTP ではトランスポート層のプロトコルは通常 TCP が使われる
        - `serverName string`
            - ex. "foo.com"
        - `localName string`
            - ex. "bar.com"
        - `didHello bool`

#### `func SendMail(addr string, a Auth, from string, to []string, msg []byte) error`

- 概要
    - `addr` のコネクションを作成し、`Mail` メソッドで送信元の登録、`Rcpt` メソッドで宛先の登録を行う。`Data` メソッドで本文を書き込むための `Writer` インスタンス `w` を取得する。`w.Write(msg)` で本文を書き込み、`Quit` メソッドで送信終了を知らせる。
