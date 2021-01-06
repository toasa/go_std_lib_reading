- 概要
    - HTTP Cookie を扱うライブラリ
    - Cookie とは
        - サーバとクライアントとの間でユーザの状態などを管理するための仕組み
        - HTTP にステート（状態）をもたせる事ができる
            - これにより同じURLでもアクセスするページを動的に変化することができる
        - 通信順序
            1. (cli) <- (serv): HTTP レスポンスの際 Cookie をHTTPヘッダに含めて送信する
            2. (cli) -> (serv): 与えられた Cookie をHTTPヘッダに含めて HTTP リクエストする
            3. (cli) <- (serv): サーバは Cookie をもとにコンテンツの内容をユーザに合わせてカスタマイズし、レスポンスする
            4. 2 と 3 の繰り返し
    - Cookie の主な属性値
        - <名前>=<値>
        - Expires=<日時>
        - Max-Age=<値>
        - Domain=<ドメイン指定>
            - first party cookie と third party cookie
            - first party cookie
                - アクセスしたドメインから発行された cookie
                - つまり Domain の属性値がアクセスしたドメインと同じ場合 first party

#### `func readSetCookies(h Header) []*Cookie`

- 概要
    - "Set-Cookie" はサーバからクライアントへクッキーを送信するためにしようされる
    - HTTP のレスポンスヘッダに書かれる
        - 例
            - Set-Cookie: <属性名>=<属性値>
            - Set-Cookie: <属性名>=<属性値>; Expires=<date>
            - Set-Cookie: <属性名>=<属性値>; Domain=<domain-value>
        - 各クッキーの中で <属性名>=<属性値> は必須。
    - `readSetCookies` は HTTP ヘッダ内フィールド "Set-Cookie" をパースし、書かれているクッキー情報をスライスにまとめ返す
- 実装
    - `h` は `map[string][]string` 型なのでキー "Set-Cookie" を用いてクッキー文字列のスライスを取得する
        - `for _, line := range h["Set-Cookie"]` でループを回す
    - `line` を `;` で分割する
        - 上の例の 1 番目と 2,3 番目のように `;` の後にキーバリューペアが続かない場合と続く場合がある
        - `parts := strings.Split(strings.TrimSpace(line), ";")`
    - `parts[0]`
        - <属性名> と <属性値> があるはずなのでパースする
            - `=` のインデックス取得
                - `j := strings.Index(parts[0], "=")`
            - インデックスをもとに `name`, `value` の生成
                - `name, value := parts[0][:j], parts[0][j+1:]`
            - `Cookie` 構造体のインスタンスを生成
                - `c := &Cookie{ Name: name, Value: value, Raw: line }`
    - `parts[1:]`
        - `;` 以降の属性をパースする
        - 手法は `parts[0]` と一緒
            - `=` のインデックスを取得し `j` に代入
            - `attr, val := attr[:j], attr[j+1:]`
        - 属性名 `attr` を小文字にする
            - `switch` で分岐するため
                - 例えば `expires` の場合、属性値は日時のフォーマットされた文字列が来るはずなので `exptime, err := time.Parse(time.RFC1123, val)` でペースする
                - パースに成功すればフィールド `c.Expires` を `exptime.UTC()` 埋める
