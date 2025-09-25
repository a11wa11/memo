# yaml

yml形式

* タブによるインデントは不可
* キーとコロン(:)に続けて対応する値を記述(コロンの後に半角スペースも必要) ※辞書型
* 変数使用の際は `{{}}` で囲む　例　{{ test_var }}
* 変数と文字列の展開の際は`ダブルクォート(" ")`で囲む　※シングルクォートでは特殊文字は自動でエスケープされる

| 記号 | 呼称 | 効果 |
| - | - | - |
| `-` | ハイフン | リストの一単位となる |
| `\|` | パイブ | 複数行を扱う |
| `>` | 大なり | 複数行を1行として扱う(末尾改行あり) |
| `>-` | 以上 | 複数行を1行として扱う(末尾改行なし) |
| `&` | アンカー | 名前をつける |
| `*` | エイリアス | アンカーでつけられた名前の箇所を参照する |
| `<<` | マージ | 指定箇所と同じ値をもつ |

- 配列の応用

```yaml
# - が配列を表すのでネストする場合以下の表記
key:
  - 
    - value1
    - value2
  -
    - valueA
    - valueB

key: [
    [value1, value2],
    [valueA, valueB]
]

# 配列内オブジェクト
parent_key:
  - child_key1: value1,
    child_key2: value2

parent_key: [{
    child_key1: value1,
    child_key2: value2
}]
```

## yaml内容を表示するモジュール

* `show_yaml_python.sh`

```sh
python3 -c 'import yaml,pprint;pprint.pprint(yaml.load(open("test.yaml").read(), Loader=yaml.FullLoader))'
```
