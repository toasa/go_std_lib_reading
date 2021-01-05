### `type Var interface`

様々な型のスーパーセット。以下の型はすべて `Var` インターフェイスを満たす。

- `Int`
- `Float`
- `Map`
- `String`

`Var` はWebアプリケーションで、アクセスカウンタやモニタリングに使用できる。というのも `Var` にはアトミックな演算が保証されており、複数クライアントからの同時アクセスのような場合にも、アトミックな足し算ができるからであろう。

### `type Map struct`

```go
type Map struct {
    m     sync.Map
    keyMu sync.RWMutex
    keys  []string
}
```

#### `func (v *Map) Do(f func(KeyValue))`

- Map `v ` の各キーバリューペアに対し、関数 `f` を作用させる高階関数。


