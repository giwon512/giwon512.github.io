­---
layout: single
title: previewLink 기능 추가하기
categories: ['Github Pages']
tag: [blog, jekyll]
---



## ❓ 기능 추가 시도 이유

notion에서 페이지를 만들고 나서 마크다운 파일을 깃허브에 넣는 식으로 포스팅을 진행해왔는데, 깃허브 페이지에서는 링크 미리보기를 지원해주지 않았다. 그래서 따로 추가하는 방법을 찾아보게 되었다.

아래 글을 참고해서 공부를 해보았다.

"[https://leeminjoo.github.io/jekyll-setting/2021/06/20/Git-page-link-preview.html](https://leeminjoo.github.io/jekyll-setting/2021/06/20/Git-page-link-preview.html)”

## 📖 linkpreview plugin 추가

```markdown
plugins:
    ......
  - jekyll-linkpreview
```

_config.yml 파일의 plugins에 jekyll-linkpreview 추가

## 📖 Gemfile 코드 변경

```markdown
group :jekyll_plugins do
    gem 'jekyll-sitemap'
    gem 'jekyll-feed'
    gem 'jekyll-seo-tag'
    gem 'jekyll-linkpreview'
  end
```

로컬 서버에서 돌릴 때 다시 bundle install 이후 bundle exec jekyll serve를 하면 제대로 잘 돌아가는 것을 확인할 수 있다.

## 📖 linkpreview.css 파일 추가

```markdown
<link rel="stylesheet" type="text/css" href="/assets/css/linkpreview.css" media="screen">
```

assets/css 폴더 밑에 linkpreview를 보여줄 css 파일을 만들어주고, _includes/head.html에 스타일시트를 연결해준다.

## 📖 포스팅 시 활용 문법

```markdown
{% linkpreview "https://giwon512.github.io" %}
```

## ⚠️trouble shooting

![linkpreviewError.png]({{"../assets/images/linkpreviewError.png" | relative_url}})

linkpreview를 인식하지 못하는 에러가 발생했다. 로컬 서버에서 실행을 할 때도 같은 오류가 발생했지만 bundle install로 해결이 되었는데, 실제 페이지에서는 해결이 되지 않아 일단 gem에 대해서 제대로 공부하고 다시 시도해볼 생각이다.