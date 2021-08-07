---
title: "タグ・カテゴリーのページをいい感じにする"
date: 2021-08-07T11:29:16+09:00
draft: false
categories: ["blog"]
tags: ["blog","hugo"]
---

# はじめに
デフォルトのタグやカテゴリーのページは
![old_tag.JPG](../old_tag.JPG)
こんな感じでタグごとに記事の一部が表示される感じになっていて、同時に複数のタグのリンクが見れないのでいい感じにする(語彙力)

# term.html追加
/layouts/_default/term.html を以下のように

```html
{{ define "main"}}
  {{ if eq .Title "Tags"}}
    <div align="left" style="margin-left:15px">
      {{ range .Site.Taxonomies.tags.Alphabetical }}
        <h2> 
          <details>
            <summary><a href="{{ "/tags/" | relURL }}{{ .Name | urlize }}">{{ .Name }}({{ .Count }})</a></summary>
              {{ range .Pages.ByPublishDate }}
              　・<a href ="{{ .Permalink}}"> {{ .Title  }} </a><br>
              {{ end }}
          </details>
        </h2>
      {{ end }}
    </div>
  {{ else if eq .Title "Categories"}}
    <div align="left" style="margin-left:15px">
      {{ range .Site.Taxonomies.categories.Alphabetical }}
       <h2>
          <details>
            <summary><a href="{{ "/categories/" | relURL }}{{ .Name | urlize }}">{{ .Name }}({{ .Count }})</a></summary>
              {{ range .Pages.ByPublishDate }}
              　・<a href ="{{ .Permalink}}"> {{ .Title  }} </a><br>
              {{ end }}
          </details>

       </h2>
      {{ end }}
    </div>
    {{ end }}
{{ end }}


```

Hugoのテンプレートは公式の  
https://gohugo.io/templates/introduction/  
ここらへんに説明がある

### テーマを読み込む
```
{{ define "main"}}
```

### ページタイトルがTagsかCategoriesで分岐
[if文](https://gohugo.io/functions/eq/)みれば分かるけど使い方はここ
```
{{ if eq .Title "Tags"}}
    tage~~~
{{ else if eq .Title "Categories"}}
    categories~~
{{ end }}
```

### Site内のタグをByCount(タグの数順)でループさせる 
ループについては[range](https://gohugo.io/functions/range/)を利用して.Site.Taxonomies.tags.Alphabeticalの各要素についてループさせる
```
{{ range .Site.Taxonomies.tags.Alphabetical }}
```

Alphabeticalの部分は順番の決め方がいくつかあって
https://gohugo.io/templates/taxonomy-templates/#taxonomy-methods  
に書いてある。
| method   |  explanation  |
| ---- | ---- |
|  .Get  |  Returns the WeightedPages for a term.  |
|  .Count  |  The number of pieces of content assigned to this term.  |
|  .Alphabetical  |  Returns an OrderedTaxonomy (slice) ordered by Term.  |
|  .ByCount  |  Returns an OrderedTaxonomy (slice) ordered by number of entries.  |
|  .Reverse  |  Returns an OrderedTaxonomy (slice) in reverse order. Must be used with an OrderedTaxonomy.  |

# いい感じになった
大枠のタグとカテゴリーは常に表示するようにして、具体的な記事はhtmlのdetailsタグで表示を隠せるようにした
![new_tag.JPG](../new_tag.JPG)
いいかんじ