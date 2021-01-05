- 概要
    - XXXfold 関数が4つほど定義されている
    - fold とあったので最初は畳み込み演算の関数 (Haskell の Fold など) と思った
    - が、そうではなく、2つの文字列が大文字小文字を同一視した場合に一致するかを boolean で判定する関数だった
- 実装
    - ascii 文字の場合
        - アルファベットの大文字と小文字をアスキー値にした場合、その差は 32 である
            - 例えば A: 65, a: 97
        - つまりある文字 `c1` と `c2` が大文字小文字を同一視して同じか確かめたい場合、`==` による一致判定、もしくは `32` だけアスキー値が異なるか確かめれば良い
        - `caseMask := ^(0x20)` とし `c1&caseMask == c2&caseMask` とすればよい