# 编程猫文档主题

编程猫文档主题fork自官方0.x版本的最后一个稳定版本的[默认主题](https://v0.vuepress.vuejs.org/zh/default-theme-config/)，并对其进行拓展，以下只会阐述拓展的部分，未来等vuepress1.x版本完全成熟再同步官方1.x版本

## 使用侧边栏跟随滚动导航

你可以使用 `themeConfig.useScrollNav` 来开启全局的侧边栏跟随滚动条，不过目前只会提取h2级标题，适用于单页内容很多h2标题的文章，暂不支持h3或更多级别的跟随：

``` js
// .vuepress/config.js
module.exports = {
  themeConfig: {
    useScrollNav: true
  }
}
```

你也可以通过 `YAML front matter` 来开启某个指定页面的侧边栏跟随滚动条：

``` yaml
---
useScrollNav: true
---
```

效果如红框所示：

![效果](./images/useScrollNav.jpg)
