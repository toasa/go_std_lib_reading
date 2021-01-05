### `type Writer struct`

- 概要
    - `Writer` のフィールドに `*bufio.Writer` 型の `w` があるので、CSV のレコードを書くときは `w` の `WriteXXX` 系のメソッドを呼び出せば良いことがわかる

#### `func (w *Writer) Write(record []string) error`

- 概要
    - `Read` よりはるかに簡単
        - やることも、実装も
    - `record` 内の各 CSV フィールドを `w.w` の `WriteXXX` 系のメソッドを使って書き込めば良い
- 実装
    - エラー処理、コーナーケースなどを省略すればわずか5行
        - ```go
          for n, field := range record {
              w.w.WriteRune(w.Comma)
              w.w.WriteString(field)
          }
          w.w.WriteByte('\n')
          ```
