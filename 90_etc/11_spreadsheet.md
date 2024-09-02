# spreadsheet

## 関数

```spreadsheet
# 行と列を入れ替える関数
=TRANSPOSE()

# 条件に一致する内容を絞り込んで表示する
# 例 =FILTER(第1引数:抽出対象, 第2引数:条件, [第3引数:条件])
=FILTER('種目'!D:D, '種目'!A:A = C9)

# 対象の箇所から正規表現で一致する内容かどうか
# REGEXMATCH(第1引数:テキスト, 第2引数:正規表現)
REGEXMATCH('種目'!E:E, B9)
```
