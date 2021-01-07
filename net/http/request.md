### `type Request struct`
    - 概要
        - HTTP リクエストを表す構造体
    - 実装
        - フィールド
            - `Method string`
                - GET, POST, PUT,...
                - 空文字の場合 GET と解釈される
            - `URL *url.URL`
            - `Proto string`
                - "HTTP/1.0" など
            - `Header Header`
                - 例えば以下のHTTPヘッダ
                  ```
                  Host: example.com
                  accept-encoding: gzip, deflate
                  Accept-Language: en-us
                  fOO: Bar
                  foo: two
                  ```
                  から以下の Header 構造体（中身はキーバリューペア）のインスタンスを作る
                  ```go
                  header := map[string][]string {
                      "Accept-Encoding": {"gzip", "deflate"},
                      "Accept-Language": {"en-us"},
                      "Foo": {"Bar", "two"},
                  }
                  ```
            - `Body io.ReadCloser`
            - `GetBody func() (io.ReadCLoser, error)`
            - etc...

#### `func readRequest(b *bufio.Reader, deleteHostHeader bool) (req *Request, err error)`

- 概要
    - リクエストヘッダの開始行とそれ以降のキーバリューペアをパースする
- 実装
    - リクエストヘッダの開始行のパース
        - 一行からなるので `ReadLine()` を呼び文字列を取得
        - `parseRequestLine` を呼び, HTTP メソッド、リクエストURI、HTTPのバージョン、パース結果を取得する
        - HTTPのバージョンを更にパースし、メジャーバージョン番号とマイナーバージョン番号を取得する
