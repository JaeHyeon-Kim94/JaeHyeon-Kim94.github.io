---
categories: [etc]
tags: [chirpy] # TAG names should always be lowercase
---

# **서문**

> Jekyll, chirpy를 이용한 깃헙 블로그를 만들기 위해 수행한 작업을 기록하고  
> chirpy, Jekyll 문서 일부를 번역한 글입니다. 잘못된 정보가 있을 수도 있습니다

#### **Jekyll Predefined Variables**

[Jekyll doc](https://jekyllrb.com/docs/front-matter/)

> | 변수                       | 설명                                                                                                                                        |
> | -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
> | `layout`                   | 사용할 layout 파일을 특정하는 속성. 확장자는 제외하고 파일명만. <br>layout 파일은 반드시 \_layouts directory에 위치해야 함.                 |
> | `permalink`                | 다른 blog의 post 사용하고 싶을때. final url로 사용됨.                                                                                       |
> | `published`                | generated 됐을 때 보여줄지 말지                                                                                                             |
> | `date`                     | post의 날짜를 덮어쓸 수 있으며 정확성 있는 정렬에 활용될 수 있음. <br> 형식은 `YYYY-MM-DD HH:MM:SS +/- TTTT`인데, 뒤의 timezone은 optional. |
> | `category`<br>`categories` | 특정 폴더에 post를 위치시키는 대신,<br> post가 속할 하나 이상의 카테고리를 지정할 수 있음. <br>                                             |
> | `tags`                     | categories와 비슷함. 하나 이상의 tags들이 post에 추가될 수 있음.                                                                            |

---

#### Custom Variables

front matter block에서 변수를 지정하여 다음의 방식으로 본문에서 사용할 수 있음.

```md
---
food: Pizza
---

{% raw %}<h1>{{ page.food }}</h1>{% endraw %}
```

### Chirpy Guide

#### Front Matter

```yaml
title: TITLE
date: YYYY-MM-DD HH:MM:SS +/-TTTT
categories: [TOP_CATEGORIE, SUB_CATEGORIE]
tags: [TAG] # TAG names should always be lowercase
```

layout 속성은 default 설정되어있으므로 Front Matter block에 추가할 필요 없음.
