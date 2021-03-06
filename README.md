# README

vuepress + nodeppt搭建博客

# 使用

- 结构: article -> topic -> chapter -> nav

```
1. navPrefixArr 是数组类型 ['nav', 'ch'] 
2. README.md必须大写 --> @lazy
3. navbar上可点击的目录(含navPrefixArr前缀)都要有README.md, 否则不计入navbar,
普通目录可以没有README.md
4. 除README.md以外的文章, 要包含includeArticleSuffix(eg.@nice)后缀
才会加入文章列表 --> 把一些low的文章藏起来
5. 想渲染成PPT的在文件名里加 @ppt
```

- 发布

```
如果安装过git bash
windows上直接点击 deploy.vuepress.sh
```

![效果大概是这样的](/static/images/preview.jpg)


# TODO

- [x] 按照自己的笔记命名习惯调整vuepress-bar的递归逻辑!!!
- [x] 引入一些常用vuepress插件
- [x] 保证static绝对路径正常引用 --> 在github上直接阅读markdown也能正常加载图片路径

```
目前, vuepress提供的这种引入方法实在不能接受...
![Image from alias](~@alias/image.png)

暂时用gulp4简单搬运那些静态文件 --> 文件一多就jj了

部署后还需要全局替换路径 
gulp.task('path:replace', function() {
    return gulp
        .src([
            '_docs/.vuepress/dist/**/*.{html,js}',
        ])
        // 理解一波 ??
        .pipe(gulpReplace(/(?<!note\/)static\/images/g, 'note/static/images'))
        .pipe(gulp.dest('_docs/.vuepress/dist'));
});
```

- [x] 文件可以处理 "[" 等特殊符号, 但目录路径却无法处理这些符号!! --> ??

```
习惯给目录这样命名
eg. [8-2]xxxx
希望可以支持...

暂时也通过gulp搬运的时候顺便重命名了
```

- [x] vuepress build时不要打包文件夹下的所有markdown!!!
    - Ignore some markdown files in source directory 
    - https://github.com/vuejs/vuepress/issues/1558

```
改为 在运行前将所有符合条件的markdown搬运到 _docs

gulp.task('copy:markdown', function () {
    return gulp.src([
        `**/*{README,@nice}.md`,
    ])
    .pipe(rename(function (path) {
        if (path.dirname.includes('[') && path.dirname.includes(']')) {
            path.dirname = path.dirname.replace(/\[/, 'nav.').replace(/\]/, ".")
        }
    }))
    .pipe(gulp.dest(`_docs`))
})

还是gulp大法好啊!!!
```

- [ ] 用nodeppt指定部分markdown, 渲染成ppt
    - 如何创建多个md 文件 https://github.com/ksky521/nodeppt/issues/262

```
暂时将nodeppt降到1.4.5 --> 对写法会有很多要求, 和平时写的markdown有很大差别...

希望nodeppt2可以早点支持多页面!!
```

- [ ] url的正确渲染..., 现在无法点击

```
// 替换链接 空格开头
.pipe(gulpReplace(/ ((((ht|f)tps?:(?:\/\/)?)(?:[\-;:&=\+\$,\w]+@)?[A-Za-z0-9\.\-]+|(?:www\.|[\-;:&=\+\$,\w]+@)#?[A-Za-z0-9\.\-]+)((?:\/[\+~%\/\.\w\-_]*)?\??(?:[\-\+=&;%@\.\w_]*)#?(?:[\.\!\/\\\w]*))?)/ig, function(match, prefix, content) {
    // console.log(match, prefix, content)
    return '['+ match +']('+ match +')';
}))

还有一些细节, 会读取失败...
eg.
https://juejin.im/entry/599bdfb8f265da24722fb77d#%E9%9F%B3%E9%A2%91

碰到正则，我就是个弟弟...
```

- [ ] 对涉及正则表达式/路径匹配的代码进行优化 --> 构建前clean文件失败??
- [ ] 处理文件名里的tag + keyword

# 次要改进

- [ ] 加入部分 vuepress-theme-meteorlxy主题包的内容 
- [ ] 发布时间 timeline
- [ ] 首页装饰
- [ ] 评论功能 vssue
- [ ] 放宽对README.md的格式要求
- [ ] 英文对应的翻译文章

# FAQ

- last updated插件没生效??
- Field 'browser' doesn't contain a valid alias configuration --> 找不到图片等

- 使用github-page需要配置的base路径 @ignore --> vuepress已处理

```
module.exports = {
  base: process.env.NODE_ENV === 'production' ? '/note/' : '',
}

并且.env.production里配置环境变量

vuepress build好像还是拿不到NODE_NEV?? --> cross-env
```


# 参考

- 搭建过程
    - https://github.com/realpdai/tech-arch-doc-comments @nice
- 如何自动读取目录结构
    - https://github.com/ozum/vuepress-bar
    - https://github.com/boboidream/note

