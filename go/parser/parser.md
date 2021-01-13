#### `func (p *parser) tryResolve(x ast.Expr, collectUnresolved bool)`

- 概要
    - パーサ `p` の現在のスコープから識別子 `x` を探す。見つからなかった場合１つ上のスコープへ移動し `x` を探索する。１つ上のスコープがなくなるまで再帰的に探索する。

#### `func (p *parser) parseMapType() *ast.MapType`

- 概要
    - Goのマップ型をパースし、ASTノードを返す
- 実装
    - Go のマップ型は `map[key_type]value_type` のように定義される
    - つまり、`map`, `[`, `key_type`, `]`, `value_type` の順でパースすればよい
    - コード
        - ```go
          pos := p.expect(token.Map)
          p.expect(token.LBRACK)
          key := p.parseType()
          p.expect(token.RBRACK)
          value := p.parseType()
          ```

#### `func (p *parser) parseArrayType() ast.Expr`

- 概要
    - 配列の型をパースする関数
- 実装
    - Go の配列型は `[expr]type` または `[...]type` で定義される
    - コード
        - ```go
          lbrack := p.expect(token.LBRACK)
          p.exprLev++
          if p.toke == token.ELLIPSIS {
              len = &ast.Ellipsis{Ellipsis: p.pos} // おそらくこれで配列の要素数を求めていない。要素数を決定する別処理があると思われる
              p.next()
          } else if p.toke != token.RBARCK {
              len = parseRhs()
          }
          p.exprLev--
          p.expect(token.RBRACK) // ']'
          ```

#### `func (p *parser) parseType() ast.Expr`

- 概要
    - 型をパースする
        - Go コンパイラでは、式(expr)と型(type) を `Expr` インタフェースとして扱っているため、戻り値は `ast.Expr` 型になる
- 実装
    - 内部で `tryType()` を呼び、戻り値が `nil` でなければそれを返す

#### `func (p *parser) tryType() ast.Expr`

- 概要
    - 型のパースを試みる、失敗時は `nil` を返す
- 実装
    - ```go
      typ := p.tryIdentOrType()
      if typ != nil {
          p.resolve(typ)
      }
      return typ
      ```

#### `func tryIdentOrType() ast.Expr`

- 概要
    - 現在検査中のトークンに応じて型をパースする

#### `func parseTypeName() ast.Expr`

- 概要
    - 型名をパースする
