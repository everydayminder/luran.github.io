---
layout: single
title:  "Jekyll로 이전 - Jekyll 설치/ 설정"
categories: 
- life
tags: [jekyll]
---

# 이전 배경

Tistory를 거쳐, 호스팅형 Wordpess 및 설치형 Wordpress를 거쳐 Jekyll로 옮겨본다.

다른 사람들과 마찬가지로 트렌드도 그렇지만, 

1. 워드프레스가 해킹 대상이 많이 되는 점
2. GitHub로 옮기면 호스팅 무료
3. 워드프레스보다 깔끔함
4. 호기심
5. 글쓰기의 편의성

등의 이류로 옮겨 보게 되었다.



# 설치 절차

다음에 혹시 이 작업을 반복할까 싶어, 겪은 내용을 기록으로 남긴다.

## GitHub 

1. GitHub을 방문하여 계정을 생성한다.
2. 홈페이지를 만들기 위해 GitHub IO 설정을 한다.
   - 앞서 만든 계정.github.io 프로젝트를 생성한다.
   - 가이드에 따라, index.html을 생성하여 push하고, Hello World (Hello World 출력 예제)가 뜨는지 확인한다.



## Jekyll 

### 개발 환경 설정

올리기 전에, 제대로 포스팅 되는지 로컬에서 동작 확인을 해야하므로, 로컬 개발 환경 설정도 한다.

```
# 루비 설정 여부 확인
ruby --version 

gem install jekyll
gem install bundler
gem install github-pages

# syntax highlighter
gem install rouge
```

### 테마 설정

이름만큼 고르기 힘든, 테마 고르기. 사람들이 많이 쓰는 것 같은 minimal-mistakes로 일단 정착하기로 한다.

```
gem install minimal-mistakes-jekyll
```

혹은, github에서 clone 또는 다운로드 한다.



### 기본 디렉토리/ 파일 설정

작업 디렉토리에, _posts, _drafts 디렉토리가 없으면 생성한다.

버전 관리 대상이 되지 않는 파일들은 .gitignore에 등록한다.



### 기본 동작 확인

```
jekyll serve
```

를 실행시키면, 기본 설정 포트 4000으로 뜨는 것을 확인할 수 있다. 내 경우는, 4000 포트가 어떤 프로그램이 이미 쓰고 있는 모양이다.

```
jekyll serve --port 8080
```

과 같이 다른 포트를 지정하면, 다른 포트로 띄울 수 있다.  

```
$jekyll --help

jekyll 3.8.3 -- Jekyll is a blog-aware, static site generator in Ruby

Usage:

  jekyll <subcommand> [options]

Options:
        -s, --source [DIR]  Source directory (defaults to ./)
        -d, --destination [DIR]  Destination directory (defaults to ./_site)
            --safe         Safe mode (defaults to false)
        -p, --plugins PLUGINS_DIR1[,PLUGINS_DIR2[,...]]  Plugins directory (defaults to ./_plugins)
            --layouts DIR  Layouts directory (defaults to ./_layouts)
            --profile      Generate a Liquid rendering profile
        -h, --help         Show this message
        -v, --version      Print the name and version
        -t, --trace        Show the full backtrace when an error occurs

Subcommands:
  docs
  import
  build, b              Build your site
  clean                 Clean the site (removes site output and metadata file) without building.
  doctor, hyde          Search site and print specific deprecation warnings
  help                  Show the help message, optionally for a given subcommand.
  new                   Creates a new Jekyll site scaffold in PATH
  new-theme             Creates a new Jekyll theme scaffold
  serve, server, s      Serve your site locally
```

serve에 대한 설명을, 좀더 확인해 보면,

```
$ jekyll serve --help

jekyll serve -- Serve your site locally

Usage:

  jekyll serve [options]

Options:
            --config CONFIG_FILE[,CONFIG_FILE2,...]  Custom configuration file
        -d, --destination DESTINATION  The current folder will be generated into DESTINATION
        -s, --source SOURCE  Custom source directory
            --future       Publishes posts with a future date
            --limit_posts MAX_POSTS  Limits the number of posts to parse and publish
        -w, --[no-]watch   Watch for changes and rebuild
        -b, --baseurl URL  Serve the website from the given base URL
            --force_polling  Force watch to use polling
            --lsi          Use LSI for improved related posts
        -D, --drafts       Render posts in the _drafts folder
            --unpublished  Render posts that were marked as unpublished
        -q, --quiet        Silence output.
        -V, --verbose      Print verbose output.
        -I, --incremental  Enable incremental rebuild.
            --strict_front_matter  Fail if errors are present in front matter
            --ssl-cert [CERT]  X.509 (SSL) certificate.
        -H, --host [HOST]  Host to bind to
        -o, --open-url     Launch your site in a browser
        -B, --detach       Run the server in the background
            --ssl-key [KEY]  X.509 (SSL) Private Key.
        -P, --port [PORT]  Port to listen on
            --show-dir-listing  Show a directory listing instead of loading your index file.
            --skip-initial-build  Skips the initial site build which occurs before the server is started.
        -l, --livereload   Use LiveReload to automatically refresh browsers
            --livereload-ignore ignore GLOB1[,GLOB2[,...]]  Files for LiveReload to ignore. Remember to quote the values so your shell won't expand them
            --livereload-min-delay [SECONDS]  Minimum reload delay
            --livereload-max-delay [SECONDS]  Maximum reload delay
            --livereload-port [PORT]  Port for LiveReload to listen on
        -h, --help         Show this message
        -v, --version      Print the name and version
        -t, --trace        Show the full backtrace when an error occurs
        -s, --source [DIR]  Source directory (defaults to ./)
        -d, --destination [DIR]  Destination directory (defaults to ./_site)
            --safe         Safe mode (defaults to false)
        -p, --plugins PLUGINS_DIR1[,PLUGINS_DIR2[,...]]  Plugins directory (defaults to ./_plugins)
            --layouts DIR  Layouts directory (defaults to ./_layouts)
            --profile      Generate a Liquid rendering profile
        -h, --help         Show this message
        -v, --version      Print the name and version
        -t, --trace        Show the full backtrace when an error occurs
```

port뿐만 아니라 host까지도 지정할 수 있다. 결국, 내 로컬에서도 serve 옵션으로 띄워놓고 운영해도 된다는 뜻이다. (당연히)

그러면, 외부에서도 접속할 수 있도록 공인 IP를 써 주거나, DDNS하거나, 터널링 등을 해주면 될 것이다. 그러나, github에서 서비스 해주는데, 내 로컬 repository가 노출되는게 싫은 이상, 굳이 이렇게까지 하는 사람은 별로 없을 것 같다. 

어쨌든, 나는 

```
jekyll serve --port 8080
```

으로 정착. 

