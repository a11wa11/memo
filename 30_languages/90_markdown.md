# Markdown

- [Markdown](#markdown)
  - [使い方](#使い方)

## 使い方

- 設定ファイルでルールを設定

```json
{
  "extends": "default",
  "MD033": false
}
```

- コメントで設定反映

```markdown
<!-- markdownlint-disable-next-line MD033 -->
<details>
  ...
</details>
```

- テーブル

```markdown
| 項目名	 | 項目 | 項目 |
|-|-|-|
| レコード1 | 1 | 2 |
| レコード2 | 3 | 4 |
|  |  |  |
```

- $\Large 文字サイズ大$

```markdown
$\Large text$
```

### github markdown

> [!NOTE]
> 補足情報

> [!TIP]
> おすすめの方法

> [!IMPORTANT]
> 特に重要な情報

> [!WARNING]
> 注意が必要な内容

> [!CAUTION]
> 危険性の高い内容

```markdown
> [!NOTE]
> 補足情報

> [!TIP]
> おすすめの方法

> [!IMPORTANT]
> 特に重要な情報

> [!WARNING]
> 注意が必要な内容

> [!CAUTION]
> 危険性の高い内容
```
