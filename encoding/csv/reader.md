- 概要
    - RFC 4180 で決められた CSV ファイルの reader
    - 仕様
        - 各レコードは newline で区切られている
        - 最後のレコードは newline があってもなくても良い
        - 空白はフィールドの一部とみなされる
            - つまり空白は区切り文字にならない
        - newline の前の Carriage return は除去される
        - 空白行は無視される
        - " で囲まれたフィールドは " の中身がフィールドと認識される
            - quoted-field と呼ばれる
            - abc, "dec" の場合,
            - {`abc`, `dec`} と解釈される
        - quoted-field の中にクオート文字がある場合、単にクオート文字１つとみなされる
            - "the ""word"" is true", "a ""quoted-field""" は,
            - {`the "word" is true`, `a "quoted-field"`}
        - newline と カンマ は quoted-field の中に含まれる

### `type Reader struct`

- 概要
    - `Reader` は `\r\n` を `\n` に変換する

#### `func (r *Reader) readRecord(dst []string) ([]string, error)`

- 概要
    - CSV の 1 レコード分を読み出し、結果を `dst` に格納する
    - このファイルのメイン処理
- 実装
    - この関数と関係ないが `Go` にもラベルと `goto` のような機能があることを初めて知った
    - 以下のように書く
        - ```go
          x := 1
          LABEL:
          for {
              if x > 10 {
                  fmt.Println(x)
                  continue LABEL // LABEL へジャンプ
              } else {
                  break LABEL    // LABEL のスコープから脱出
              }
          }
          ```
    - すごく技工的というわけではないが必要な処理が簡潔にかかれている
    - 大まかな流れ
        - `r.readLine()` で一行を読み出す。`line` という変数に格納する
        - ループ：各フィールドを読み込む
            - quoted-field か non-quoted かで場合分け
            - 読み込んだフィールドを `r` が持つバッファ `r.recordBuffer` に書き込む
            - `r.recordBuffer` に追加したフィールドの最終インデックス(exclusive) を `r.fieldIndexes` に追加
                - `r.recordBuffer` に溜め込んだフィールドのバイト列はパスの最後に `r.fieldIndexes` を使ってスライシングしながら `dst` に書き込む
        - `r.FieldsPerRecord` と読み込んだフィールド総数に過不足ないか確かめる
            - `r.FieldsPerRecord` が `0` の場合は未初期化なため `len(dst)` を代入する
