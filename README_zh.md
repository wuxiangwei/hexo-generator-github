## hexo-generator-github

[![Build Status](https://travis-ci.org/Jamling/hexo-generator-github.svg?branch=master)](https://travis-ci.org/Jamling/hexo-generator-github)
[![node](https://img.shields.io/node/v/hexo-generator-github.svg)](https://www.npmjs.com/package/hexo-generator-github)
[![npm downloads](https://img.shields.io/npm/dt/hexo-generator-github.svg)](https://www.npmjs.com/package/hexo-generator-github)
[![npm version](https://img.shields.io/npm/v/hexo-generator-github.svg)](https://www.npmjs.com/package/hexo-generator-github)
[![GitHub release](https://img.shields.io/github/release/jamling/hexo-generator-github.svg)](https://github.com/Jamling/hexo-generator-github/releases/latest)

Github generator for [Hexo].

## 安装

``` bash
$ npm install hexo-generator-github --save
```

## 使用

hexo-generator-github 可以作为辅助函数/生成器/命令行插件使用。`hexo server`及`hexo generate`将会调用本插件的生成器生成github相关页面的缓存。

``` bash
$ hexo github [-r --replace]
```

如果命令中带`-r`或`--replace`选项，那么此生成器将会忽略已经存在的github缓存，并重新生成。

## 配置

``` yaml
github:
  debug: true
  cache_dir: gh_cache
  user: Jamling
  repos: 
    - hexo-theme-nova
    - hexo-generator-github
    - hexo-generator-i18n
```

- **debug**: 是否在控制台输出debug信息
- **cache_dir**: Github缓存目录
- **user**: Github用户名
- **repos**: Github项目列表，如果未配置此项，那么`gh_repos()`将返回用户所有的项目

## Front-matter
本插件引入了一个名为`gh`的front-matter来指引如何生成github相关的页面。

- gh.user 指定github用户名，默认为<var>_config.yml</var>配置的github.user，参考[gh_opts](#gh_opts)
- gh.repo 指定github项目，默认从url中获取，请参考[gh_opts](#gh_opts)
- gh.type 
    - get_repos 获取github项目，参考[gh_repos](#gh_repos)
    - get_contents 获取github项目中的Markdown文件内容，参考[gh_contents](#gh_contents)
    - get_releases 获取github项目的发布，参考[gh_releases](#gh_releases)

## 辅助函数

### gh_opts
返回完整的<var>page.gh</var>对象
如果gh front-matter中未指定用户名，那么默认用户名为使用配置中的<var>hexo.config.github.user</var>值
如果gh front-matter中未指定项目，那么项目名称将从<var>page.path</var>中获取
示例：

- page.path = p/Android-ORM/ 那么 gh.repo = Android-ORM
- page.path = en/p/Android-ORM/ 那么 gh.repo = Android-ORM (本插件支持国际化，<var>_config.yml</var>中的<var>hexo.config.language</var>必须包含**en**配置项)

### gh_repos

返回github上用户拥有的项目（Array）

``` htmlbars
  {% for p in gh_repos() %}
    <!--<div class="col-sx-6 col-sm-6 col-md-6 col-lg-6">-->
      <div class="panel panel-default" id="project">
        <div class="panel-heading">
        
          <h3>
          <a href="https://github.com/{{p.owner.login}}/">{{p.owner.login}}</a>
          /
          <a href="./{{ p.name }}"> {{ p.name }}</a>
          </h3>
        </div>
        <div class="panel-body">
          <p>{{ p.description }}</p>
        </div>
        <div class="panel-footer">
        </div>
      </div>
    <!--</div>-->
  {% endfor %}

```

选项 | 描述 | 默认值
--- | --- | ---
`user` | Github user | <var>config.github.user</var>

### gh_contents

返回github项目中的(**markdown文件**)内容（String）

示例（设置hexo页面的内容与github项目中某个文件的内容）：

``` js
{% set page.content = gh_contents({path: 'README'}) %}
```

Option | Description | Default
--- | --- | ---
`user` | Github user | <var>config.github.user</var>
`repo` | Github repo | <var>page.gh.repo</var>
`path` | Github content path | README
`ref` | Github reference | master

### gh_edit

返回github项目中的(**markdown文件**)内容的编辑地址（String）

``` js
{{ gh_edit({path: 'README'}) }}
```

Option | Description | Default
--- | --- | ---
`user` | Github user | <var>config.github.user</var>
`repo` | Github repo | <var>page.gh.repo</var>
`path` | Github content path | README
`ref` | Github reference | master

### gh_releases

返回github项目发布（Array）

``` htmlbars
{% for p in gh_releases() %}
<div class="release">
  <div class="header">
    <a href="{{p.html_url}}">{{p.name}}</a>
    <a href="{{p.author.html_url}}">{{p.author.login}}</a> released this on {{gh_time(p.published_at)}}
  </div>
  <div class="markdown-body">
    {{markdown(p.body)}}
  </div>
  <h2 class="release-downloads-header">Downloads</h2>
  <ul class="release-downloads">
    {% for d in p.assets %}
    <li><a href="{{d.browser_download_url}}">
      <strong>{{d.name}}</strong> ({{d.download_count}} downloads)</a>
    </li>
    {%- endfor %}
  </ul>
</div>
{% endfor %}
```

Option | Description | Default
--- | --- | ---
`user` | Github user | <var>config.github.user</var>
`repo` | Github repo | <var>page.gh.repo</var>

## Reference

- [Github Developer](https://developer.github.com/): github开发者文档
- [hexo-theme-nova](https://github.com/Jamling/hexo-theme-nova): Hexo Nova主题

## License

MIT

[Hexo]: http://hexo.io/
