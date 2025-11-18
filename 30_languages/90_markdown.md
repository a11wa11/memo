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
