### `package time`

- 概要
    - グレゴリオ暦に基づく時間、カレンダーのAPIを提供するライブラリ
    - OS には２種類の時計がある
        - wall clock
            - 時間を伝えるために使用
        - monotonic clock
            - 時間を測定するために使用
    - `time.Now` で返される時間は２つの時計のどちらも含む
        - 取得した時間をフォーマット、プリントなどする場合は wall clock を使う
        - 取得した時間を比較、演算などする場合は monotonic clock を使う

### `type Time struct`
- 概要
    - nano秒精度の時間インスタンスを表す
    - フィールドは３つ
        - `wall uint64`
            - 63-64: hasMonotonic flag
            - 30-63: wall seconds
                - hasMonotonic flag が 0 の場合
                    - フィールドは must 0
                - hasMonotonic flag が 1 の場合
                    - wall clock として、1885/1/1 からの経過秒数を符号**なし**整数で表現
            - 0-30: wall nanoseconds
        - `ext int64`
            - hasMonotonic flag が 0 の場合
                - wall clock として、1/1/1 からの経過秒数を符号**付き**整数で表現
            - hasMonotonic flag が 1 の場合
                - monotonic clock として、プロセスの開始からの経過ナノ秒を符号**付き**整数で表現
        - `loc *Location`
    - 時間計算
        - `Sub` メソッドは２つの `Time` インスタンスを引数にとり、`Duration` インスタンスを返す
        - `Add` メソッドは `Time` インスタンスと `Duration` インスタンスを引数にとり、`Time` インスタンスを返す

#### `var daysBefore`

- 概要
    - １月から先月までの日数を格納した集合
- 実装
    - ```go
      var daysBefore = [...]int32{
          0,
          31,
          31 + 28,
          31 + 28 + 31,
          31 + 28 + 31 + 30,
          31 + 28 + 31 + 30 + 31,
          31 + 28 + 31 + 30 + 31 + 30,
          31 + 28 + 31 + 30 + 31 + 30 + 31,
          31 + 28 + 31 + 30 + 31 + 30 + 31 + 31,
          31 + 28 + 31 + 30 + 31 + 30 + 31 + 31 + 30,
          31 + 28 + 31 + 30 + 31 + 30 + 31 + 31 + 30 + 31,
          31 + 28 + 31 + 30 + 31 + 30 + 31 + 31 + 30 + 31 + 30,
          31 + 28 + 31 + 30 + 31 + 30 + 31 + 31 + 30 + 31 + 30 + 31,
      }
      ```

#### `func nextStdChunk(layout string) (prefix string, std int, suffix string)`

- 概要
    - 文字列 `layout` の中から、Time layout 文字列を見つけ、`prefix` と `suffix` に切り分けて返す
