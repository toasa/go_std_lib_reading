### `type Replacer struct`

```go
type Replacer struct {
    once   sync.Once
    r      replacer
    oldnew []string
}
```

```go
type replacer interface {
    Replace(s string) string
    WriteString(w io.Writer, s string) (n int, err error)
}
```

#### `func NewReplacer(oldnew ...string) *Replacer`

- 動作
    - `oldnew` は置換前と後の文字列を可変長で指定する
        - 例えば `NewReplacer("a", "A", "b", "B")` , "A", "b", "B")`.
    - `oldnew` は偶数個の文字列を想定。奇数の場合 `panic` を出す。

#### `func (r *Replacer) buildOnce()`

- 動作
    - ２つのみ
    - `replacer` インターフェイス `r.r` のビルド
        - `r.r = r.build()`
    - `r.oldnew` の初期化
        - `r.oldnew = nil`
- 感想
    - `Replacer` 構造体は `replacer` 型のフィールド `r` を内部にもつ。`replacer.go` の中にはいろいろな `replacer` が定義されている。`oldnew` に応じて `replacer` を使い分けるため `r.build()` 後は `r.oldnew` を `nil` 初期化しているのだろう。実際 `build` のコードでは `oldnew` の性質に応じてビルドする replacer を分岐している。

#### `func (b *Replacer) build() replacer`

- 動作
    - `replacer` インターフェイスを満たす構造体のビルド
- 実装
    - 置換対象文字列 `b.oldnew` に応じてビルドする replacer を分岐している
        - 置換する対象が一つの場合
            - `singleStringReplacer`
        - 置換前の文字の長さが 1 以上の場合
            - `genericReplacer`
        - 置換後の文字の長さが 1 の場合
            - `byteReplacer`
        - それ以外
            - `byteStringReplacer`

#### `func (r *Replacer) Replace(s string) string`

- 動作
    - `r` が持つ replacer `r.r` に対し `Replace` メソッドを呼ぶ

### `type singleStringReplacer struct`

```go
type singleStringReplacer struct {
	finder *stringFinder
	value string // 置換後の文字列
}
```

- 感想
    - フィールド `finder` の構造体 `stringFinder` は `strings/search.go` に定義されている。
        - ```go
          type stringFinder struct {
              pattern string
              badCharSkip [256]int
              goodSuffixSkip []int
          }
          ```
    - コメントによるとテキスト中からパターンと呼ばれる文字列を効率よく探索できるようだ。
    - アルゴリズムには Boyer-Moore string search algorithm が使われている。
        - [wiki](https://en.wikipedia.org/wiki/Boyer-Moore_string_search_algorithm)によると、パターンに事前処理を行うため、見つけたいパターンが対象のテキストにくらべ遥かに短い場合に適しているようだ。
        - [記事1](https://algoful.com/Archive/Algorithm/BMSearch)や[記事2](http://capm-network.com/?tag=C%E8%A8%80%E8%AA%9E%E3%82%A2%E3%83%AB%E3%82%B4%E3%83%AA%E3%82%BA%E3%83%A0-BM%E6%B3%95)で「ずらし表」と言われているのが `badCharSkip` というテーブルだろう。
