### `package fnv`

- 概要
    - FNV (Fowler–Noll–Vo) ハッシュ関数を実装したパッケージ
    - FNV ハッシュ関数
        - 実装がとてもかんたん
            - 使用する演算は掛け算とXORのみ
            - 掛け算の演算子の１つに FNV prime と呼ばれる決まった素数を使用する
        - 暗号学的ハッシュ関数では**ない**

#### `func (s *sum32) Write(data []byte) (int, error)`

- 実装
    - ```go
      hash := *s
      for _, c := range data {
          hash *= prime32
          hash ^= sum32(c)
      }
      *s = hash
      ```
