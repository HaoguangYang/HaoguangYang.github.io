---
layout: post
title: ���ʹ�� Jacman ����
tagline: By yitao
tags:
- jekyll
- jacman
categories: Jekyll
description: Jacman ��һ���ƽ����������Ӧʽ��Ƶ� Jekyll ���⡣��վ��ʽʹ���� Jacman ���⡣Jacman ���� Jacman �� Hexo �����޸Ķ����������ǰ����վ�� Demo Ԥ��������ڱ�����ĸ���Ч������������κ�����������ӭ�� GitHub ���� issue��
---
##�������
Jekyll-Jacman ��Ϊ [Jekyll](http://jekyllrb.com) ��Ƶ�һ�������Ҿ�����Ӧʽ�����⣬ӵ�и��ḻ�����Բ�֧���˺ܶ�Ĺ��ڷ���Jacman ʼ�� [Jacman](https://github.com/wuchong/jacman) ��ֲ������

<!--more-->

##����ָ��

Jacman �����ṩ�˷ḻ���������ԣ�����ʵ������������Զ��塣�����ļ�`_config.yml`λ�������Ŀ¼�¡����θ��¶������ļ������˽ϴ����������֮ǰ��ʹ���� Jacman��Ҳ��Ҫ����������ָ�Ͻ�����Ӧ���޸ġ�

```
##### �˵�
menu:
  ��ҳ: /
  �鵵: /archives
  ����: /about

#### �ؼ�
widgets: 
- category
- tag
- links
- rss

#### RSS 
rss: /atom.xml 

#### ͼƬ���
imglogo:
  enable: true               ## �Ƿ���ʾ��վ logo
  src: img/logo.png        
favicon: img/favicon.ico     ## ��վͼ��    
apple_icon: img/jacman.jpg   ## ƻ���豸�ϵ�ͼ�꣬������Ҫ͸��
author_img: img/author.jpg   ## ��վ�ײ��Ĳ���ͷ��
banner_img: img/banner.jpg   ## ���Ͷ�����ͼƬ

close_aside: false      ##�Ƿ�������ҳ���Զ��رղ����

#### ��ҳ���
index:
  expand: true              ## ��ҳ�����Ƿ�չ����Ĭ��Ϊչ��ʽ����ʾ Read More��
  excerpt_link: Read More    

#### ������Ϣ
author:
  name: ## ������
  intro_line1:  "Hello ,I'm Larry Page in Google."    ## ��վ�ײ��ĸ��˽���
  intro_line2:  "This is my blog,believe it or not."  
  weibo_verifier:  ## ΢�������֤��
  tsina:           ## ����΢�����΢������
  weibo:           ## ������ʾ��վ�ײ��罻��ť����ͬ
  douban:         
  zhihu:  
  email:     
  twitter:   
  github:     
  facebook: 
  linkedin:   
  google_plus:   
  stackoverflow:  


#### Ŀ¼
toc:
  article: true   ## �Ƿ�����������ʾĿ¼
  aside: true     ## �Ƿ��ڲ������ʾĿ¼

#### ��������
links:
  ��ũȦ: https://coderq.com,һ���������Ա�����������һ������
  Jark's Blog: http://wuchong.me
  
#### ����
duoshuo_shortname: 
disqus_shortname:  

#### ����ť
jiathis:
  enable: false   ## Ĭ��ʹ�������ڽ�����
  id:    
  tsina: 
  
#### ��վͳ��
google_analytics:
  enable: false
  id:            ## google analytics ID.
  site:          ## ��վ��ַ.
baidu_tongji:
  enable: false
  sitecode:      ## �ٶ�ͳ��վ��������
cnzz_tongji:
  enable: false
  siteid:        ## CNZZͳ��վ��ID

#### ����
ShowCustomFont: true  
fancybox: true        
totop: true           

#### �Զ�������
google_cse: 
  enable: false
  cx:  
baidu_search:    
  enable: false
  id:   
  site: http://zhannei.baidu.com/cse/search 
tinysou_search:     ## http://tinysou.com/
  enable: false
  id: "4ac092ad8d749fdc6293" 
```

###���Թ���
- **�˵� menu**
Ĭ��û������ `/tags` �� `/categories`ҳ�棬�����Ҫ�������ڲ���Ŀ¼�·ֱ���`tags` �� `categories`�ļ���ÿ���ļ����зֱ����һ��`index.md`�ļ�������Ϊ��

```
layout: tags (��categories)
title: tags (��categories)
---
```


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;��Ϊ�������Ѿ�������������ҳ���ģ�壬�������ǻᱻ��ȷ�Ľ���������

- **�ؼ� widgets**
�ṩ��7��С���ߡ�������ǩ�����ࡢRSS���������ӡ�΢���㡣

 **��������**���������ӵ���ַ��ӿ�����`links`��������ӡ�
 
 **΢����**����Ҫע����ǣ����Ҫ����΢���㣬����������`author`������`tsina`��`weibo_verifier`��ֵ��ǰ������΢��ID����������΢�������֤�룬���� http://app.weibo.com/tool/weiboshow ������ͼλ�ã����Ի������ verifier���磺�ҵ���`b3593ceb`��
![](http://ww1.sinaimg.cn/large/81b78497jw1emegd6b0ytj209204pweu.jpg)

 ���Ҫ�رղ��������`close_aside`��Ϊ`true`���ͻ��ڲ���ҳ���Զ��رղ������

- **ͼƬ��� Image**
���������������վ���ͼƬ��������վͼ�꣨`favicon`������վlogo��`imglogo`��������ͷ��`author_img`��������������վlogo����ʽ����Ϊ`.svg`��`.png`��ʽ��ͬʱ�����ṩ���׵� favicon �Լ���ƻ���豸�ϵ�ͼ��`apple_icon`��������Ҫ͸������

- **��ҳ��ʾģʽ Index**
Ŀǰ��ҳ����ʾģʽ֧�����֣�һ����ԭ�ȵĿ�Ƭʽ��ǰ�� [Demo](http://wuchong.me/jacman) Ԥ��������һ�������ƹٷ����������չ��ʽ��[��վ](http://wuchong.me)�����õ����֣������߸������ӣ�ǰ����ҳ�����ٶȸ��죬�����������ݸ����������ߡ�����Ĭ�ϲ��ú�һ��չ��ʽ�����迪����һ�ֿ�Ƭʽ��������`index`�����µ�`expand: false`��

 ��Ƭʽ������ժҪ�ǽ�ȡ�������ݵ�ǰ140���֣�Ҳ�����Լ��ܽ�`description`��������ڿ�ͷ��`front-matter`�С�չ��ʽ������ժҪ����ʹ��`<!-- more -->`��ȡ�ˡ�

- **������Ϣ author**
������Ϣ�����龡����д����������`tsina`���������΢��ID����ͬ���û�����΢����ҳ��ַ������������Ժ������û���΢���Ϸ��������µ�ͬʱ���Զ�@�㡣ͬʱ����`weibo_verifier`һ����������΢���㡣`intro_line1`��`intro_line2`����վ�ײ��ĸ��˽��ܡ�`weibo`��`twitter`��`facebook`����������ʾ��վ���½ǵ��罻��ť�ģ�����ͼ��ʾ��
![](http://ww4.sinaimg.cn/large/81b78497jw1emgscr3575j2078050jrc.jpg)

- **Ŀ¼ toc**
�Ƿ������������л������е�Ŀ¼���ܡ����߿��Զ�Ϊ`true`��Ϊ`false`��ͬʱ�������ϣ�����ض���ĳһƪ�����йر�Ŀ¼����������������ļ���ͷ�е�`front-matter`�м���һ��`toc: false`��

- **���� comments**
��д`duoshuo_shortname`[��˵](http://duoshuo.com/)���û��������ö�˵����ϵͳ���ڴ�½���������õ�����ϵͳ��

 ��д`disqus_shortname`[disqus](http://disqus.com/) ���û��������� disqus ����ϵͳ�������ϸ��㷺ʹ�õ�����ϵͳ�����ò��͸�Ŀ¼�µ�`_config.yml`�ļ��е�`disqus_shortname`ͬ��Ҳ�ܿ����ù��ܡ�

- **�������� jiathis**
[����](http://www.jiathis.com/)����ϵͳ��Ĭ�Ϲرգ���Ϊ�����Ѿ�������ԭ���ķ����ܡ�

- **��վͳ�� Analytics**
`google_analytics`��Google Analytics׷�ٴ��롣��ע�⣺Google Analytics�Ѿ���������Universal Analytics������ǰ����̨�������Google Analytics�汾��������׷�ٴ��룬������Ϣ��[�������](https://developers.google.com/analytics/devguides/collection/upgrade/?hl=zh_CN)�˽⡣

 `baidu_tongji`���ٶ�ͳ�ƹ��ܡ���Ҫ��дվ��������`sitecode`����[����](http://tongji.baidu.com/web/welcome/login)ע�Ტ����վ��󣬻�ȡ�����롣����������ڡ���վ���ġ�-> �������ȡ���в鿴������ͼ��ʾ��`e6d1f421bbc9962127a50488f9ed37d1`��ע��ȥ��ǰ���`3F`��
![](http://ww4.sinaimg.cn/large/81b78497jw1emf4v6qf91j20kf07sq8v.jpg)

 `cnzz_tongji`��վ��ͳ�ƹ��ܡ���Ҫ��дվ��ID`siteid`��ͬ����[վ������](http://www.cnzz.com)ע�Ტ����վ����á�

- **��ѧ��ʽ mathjax**
����֧��д LaTex ��ѧ��ʽ��ֻ��Ҫ�������ļ���ͷ��`front-matter`�У�����һ��`mathjax: true`������������д LaTex ��ʽ��

- **ͼƬ��� fancybox**
Ĭ�Ϲرգ�����㾭������ Gallery ���͵����£���ô������Ϊ`true`��

- **���ض��� totop**
���½�`���ض���`��ť��Ĭ�Ͽ�����

- **�Զ������� Search**
`baidu_search`����������ٶ�վ��������Ҫ��¼ [�ٶ�վ������](http://zn.baidu.com/)�����ú����վ�㣬������վ��������ȡ����ID������`site`���Կ�����Ĭ��ֵ��Ҳ�������Լ�����CNAME�Ķ�������������ϸ�Ŀ����Ķ�[��ƪ����](http://gengbiao.me/hexo/hexo%E6%B7%BB%E5%8A%A0%E7%99%BE%E5%BA%A6%E7%AB%99%E5%86%85%E6%90%9C%E7%B4%A2/)�˽⡣

 `google_cse`����������ȸ��Զ���������Ҫ�ȵ�¼ [Google CSE](https://www.google.com/cse/)�����ú����վ�㣬����ô��Զ���������ID����������Ҫ�ڲ���Ŀ¼�½���`search`�ļ��в�����һ��`index.md`�ļ�������Ϊ��
 ```
 layout: search
 title: search
 ---
 ```

 `tiny_search`: ���Ҫ����[΢����](http://tinysou.com/)����Ҫ��ע��һ���ʺţ�����һ��Engine����Engine��Key���������ļ��е�`id`���ɡ�


##��������
- **Q��ͼƬĬ�϶��Ǿ���ģ�����ô��������ͼƬ�����أ�**
>ʹ�� `<img src="" style="display:block;margin:auto"/>`��HTML��ǩ��

- **Q����ν���һƪͼƬ�����£�Gallery Post����**
> ֱ���½�һ�� Markdown �ļ�������`front-matter`�޸�Ϊ���£����ɿ�������ΪͼƬ�������ṩ����ʽ��
```
---
layout: photo
title: Gallery Post
photos:
- http://i.minus.com/ibobbTlfxZgITW.jpg
- http://i.minus.com/iedpg90Y0exFS.jpg
---
```

- **Q�����������ļ��и�ĳһ��������ֵ����Ϊʲô���ǿ�����Ч������**
>`_config.yml`�ļ��е�ÿ������ֵǰ�������һ���ո񣬽����� Sublime/Notepad++ �п�����ʾ���пո�ģʽ����ÿƪ���µ� `front-matter` ҲҪע��������⡣

- **Q����ô������ͽ��飿**
>���⻹�ڲ��������У���ӭ [open issue](https://github.com/Simpleyyt/jekyll-jacman/issues) ���Ὠ�飬�������ۡ�

- **Q��Ϊʲô���޸��������ļ�/�����˲��ģ�����������ȴ�������أ� **
> �뽫��������ļ�/markdown�ļ������ `UTF-8` ��ʽ��

- **Q��Ϊʲô������΢�������ʾ�ǿհ׵ģ�û������չʾ�� **
> ÿ���޸Ĳ���������������Ҫ��ˢ�¼��λ����ϴ����������Ͼͺ��ˡ�