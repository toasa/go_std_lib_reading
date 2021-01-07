#### `func ReadResponse(r *bufio.Reader, req *Request) (*Response, error)`

- 概要
    - `r` から HTTP レスポンスを読み取り `*Response` 型のデータを返す
    - HTTP レスポンスの構造
        - 1 行目
            - ステータス行
                - HTTPのバージョン <空白> ステータスコード
                - HTTPのバージョン
                    - ex. HTTP/1.1
                - ステータスコード
                    - ex. 200 OK
        - 2~n 行目
            - ヘッダ
        - n+1行目
            - 空白
        - n+2行目以降
            - ボディ
- 実装
    - ステータス行のパース
        - `line := ReadLine()`
        - `i := strings.IndexByte(line, ' ')` でHTTPバージョンとステータスコードを分割する空白のインデックスを取得
        - `resp.Proto = line[:i]` と `resp.Staus = line[i+1:]`
    - レスポンスヘッダのパース
        - `tp.ReadMIMEHeader()` の呼び出し


