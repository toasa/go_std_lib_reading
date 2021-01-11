- 概要
    - map の stable なソートを提供する
    - Go ではコンテナ型のデータ構造（配列、スライス、マップなど）をソートするために、ソートインタフェースを満たす必要がある
    - 満たすべきメソッドは３つ
        - `Len() int`
        - `Less(i, j int) bool`
        - `Swap(i, j int)`

#### `func Sort(mapValue reflect.Value) *SortedMap`

- 概要
    - `reflect` で表現されるマップ（Go標準のマップだけでなく動的な型として reflect で生成されたマップ型）からソートされたマップを返す

#### `func (o *SortedMap) Less(i, j int) bool`

- 概要
    - `SortedMap` がソートインタフェースを満たすためのメソッド
    - マップに順序を入れる
- 実装
    - 2つの `reflect.Value` 型のデータを比較する
    - 整数、浮動小数点数、文字列、構造体、配列など、Goのほとんどのデータ構造に対し順序を定義している
    - 特に２つの構造体の順序は自明でないと思う
        - 実装は先頭のフィールドから比較を始め、一致していなければその順序を構造体の順序として返す。一致していれば次のフィールドの比較を行う。以降繰り返し。