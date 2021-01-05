- 概要
    - UTF8 エンコードされた文字列のスキャナ
    - スキャンする文字はフラグにより指定できる
        - `ScanIndent`
        - `ScanInts`
        - `SkipComments`
        - etc...

### `func (s *Scanner) Scan() rune`

- 実装
    - フラグに応じて適切なスキャナを呼び出す
        - 例えば `ScanIdents` フラグの場合 `s.scanIdentifier` を呼び出す

