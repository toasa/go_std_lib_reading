### `func Abs(x float64) float64`

- 実装
    - `float64` の値の絶対値を返す関数
    - IEEE 754 で標準化された仕様では msb (most significant bit) が符号ビットなのでこれを 0 にすればよい
    - 簡略化したコード
        - `Float64bits(x) & ^(1 << 63)` 
