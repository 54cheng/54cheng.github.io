按照一下网址的guide先安装好相应的软件
https://docs.github.com/zh/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll
 
## 先决条件
  必须安装 Jekyll 和 Git 后才可使用 Jekyll 创建 GitHub Pages 站点。 有关详细信息，请参阅 Jekyll 文档中的安装和“设置 Git”。

  建议使用 Bundler 安装和运行 Jekyll。 Bundler 可管理 Ruby gem 依赖项，减少 Jekyll 构建错误和阻止环境相关的漏洞。 要安装 Bundler：

  安装 Ruby。 有关详细信息，请参阅 Ruby 文档中的“安装 Ruby”。
  安装 Bundler。 有关详细信息，请参阅“Bundler”。

## 按照guide安装好软件并使用jekyll创建一个简单的站点
  如图：
  <img src="/my_pic/2023-03-03 112549.png">

  其中index.markdown文件是站点的入口文件，内容如下：  

    ---
      #Feel free to add content and custom Front Matter to this file.
      #To modify the layout, see 
      #https://jekyllrb.com/docs/themes/#overriding-theme-defaults

     layout: home
    ---  

  这个文件可以替换成index.html,可以在其中自定自己的内容，注意layout: home。

## 替换theme
  这里做替换大概可以分为2中方法：
 （1）直接在theme中进行开发（写html,md等），然后上传到username.github.io仓库中
      1.git  clone  https://xxxxx.git (这个是theme的.git地址)
      2.在xxxxxx中开发即可，文件夹中没有_posts文件夹可以自己新建，然后在其中添加你的*.md等文件
      3.这里可能需要你修改index.html/index.md/index.markdown等文件，可以参照theme minima中_layouts文件夹中的home.html文件

   以dinky theme示例(修改index.html)：  
   <img src="/my_pic/2023-03-03 123850.png">

    新建_posts文件夹，添加文件 
    bundle exec jekyll serve 运行（git bash中）

  <img src="/my_pic/2023-03-03 115712.png">
  (2)按照文档中的进行修改，参照文首的链接

  https://docs.github.com/zh/pages/setting-up-a-github-pages-site-with-jekyll/adding-a-theme-to-your-github-pages-site-using-jekyll

## 添加分页
  参照http://jekyllcn.com/docs/pagination/  也可以参照其他的theme或是个人的网站进行修改或补充
  我自己的这个简单网站是修改的index.html文件（先删除了index.markdown文件，后新建的）
  具体的可以参看https://github.com/54cheng/54cheng.github.io/tree/master/docs 下的index.html

## 总结
  一些东西还是要自己动手做，算是锻炼脑袋吧。


    
