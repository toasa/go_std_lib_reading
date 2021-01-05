### `type List struct`

```go
type List struct {
    root Element
    len  int
}
```

### `type Element struct`

```go
type Element struct {
    next  *Element
    prev  *Element
    list  *List
    Value interface{}
}
```

- 概要
    - いわゆる Doubly linked list のライブラリ
