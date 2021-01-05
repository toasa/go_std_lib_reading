#### `func (c *Client) do(req *Request) (*Response, error)`

- 概要
    - HTTP リクエストを送信し HTTP レスポンスを返す関数
- 実装
    - たくさんコードが書かれているが本質的な処理は `resp := c.send(req, deadline)` とし `return resp, nil` とすること
