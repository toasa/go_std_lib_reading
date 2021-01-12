### `type Scope struct`

- 概要
    - スコープを表す構造体
- 実装
    - フィールドは２つ
        - `Outer   *Scope`
        - `Objects map[string]*Object`

### `type Object struct`

- 概要
    - パッケージ、定数、型、変数、関数、ラベルのように名前をもつ Go のオブジェクトを表す
