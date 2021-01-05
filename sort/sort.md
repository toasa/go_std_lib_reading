### `type Interface interface`

- 概要
    - `Interface` インターフェイスを満たす構造体であれば、`sort.go` に定義されているソート関数によってソートすることができる
    - 満たすべきメソッドは３つ
        - `Len() int`
        - `Less(i, j int) bool`
        - `Swap(i, j int)`

#### `func insertionSort(data Interface, a, b int)`

- 概要
    - インデックス `a` から `b` までを Insertion sort する
    - stable なソート


#### `func siftDown(data Interface, lo, hi, first int)`

```go
func siftDown(data Interface, lo, hi, first int) {
    root := lo
    for {
        child := 2*root + 1
        // 例外処理っぽい
        if child >= hi {
            break
        }
        // 左のノードを値が大きい状態にする
        if child+1 < hi && data.Less(first+child, first+child+1) {
            child++
        }
        // 既に親ノードの値が子ノードより大きかったらループを抜ける
        if !data.Less(first+root, first+child) {
            return
        }
        // 親ノードと子ノードをスワップ
        data.Swap(first+root, first+child)
        root = child
    }
}
```

#### `func heapSort(data Interface, a, b int)`

```go
func heapSort(data Interface, a, b int) {
    first := a
    lo := 0
    hi := b - a

    // 最大の要素をトップに持っていく
    for i := (hi - 1) / 2; i >= 0; i-- {
        siftDown(data, i, hi, first)
    }

    for i := hi - 1; i >= 0; i-- {
        data.Swap(first, first+i)
        siftDown(data, lo, i, first)
    }
}
```

#### `func Stable(data Interface)`

- 概要
    - log(n) しか追加のメモリ領域を使わない、stable なソート
    - 他の in-place で stable なソートアルゴリズムと比べても優れている
