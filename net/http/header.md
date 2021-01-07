### `type Header map[string][]string`

- 概要
    - HTTP ヘッダをキーバリューペアで表現している
    - 実際の HTTP ヘッダは大文字小文字を区別しないヘッダ名、コロン `:`、値からなる

#### `func CanonicalHeaderKey(s string) string`

- 概要
    - HTTP ヘッダのキーはある種の正規化がなされる、それ正規化を施す関数
    - それは単語の先頭を大文字それ以外を小文字で表す。ハイフン以下の単語もこれに従う。
    - 例
        - `accept-encoding` -> `Accept-Encoding`
