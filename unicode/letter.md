#### `const MaxRun`

- 概要
    - valid な Unicode code point の最大値。`0x10FFFF`。

### `type CaseRange struct`

- 概要
    - Unicode の文字で case の変換を表現する構造体
    - 例えばAscii文字の場合 `A` と `a` の差は 32. 
        - この値を足す or 引くことで大文字と小文字が変換できる
- 実装
    - フィールドは３つ
        - `Lo uint32`
        - `Hi uint32`
        - `Delta d`
