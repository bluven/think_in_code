# 大杂烩

## CI/CD
[持续集成是什么？][1]  
[如何理解持续集成、持续交付、持续部署？][2]  
[持续部署，并不简单！][3]  
[Continuous Deployment with Containers][4]  
[通过Docker容器运行持续集成/持续部署][5]  
[Docker 持续部署图文详解][6](原文)  
[Jenkins+Git+Maven+Shell+Tomcat 持续集成部署环境大众化解决方案][7]  
[DockOne技术分享（九）：持续集成和“云”][8]  
[Deploying at GitHub][9]  
[HOW TO DEPLOY SOFTWARE][10]  
一个CI/CD博客[flow.ci][11]  
[Continuous Integration架构设计][12]  

## Docker
[What Am Container][13]  

### registry
[How To Create an SSL Certificate on Nginx for Ubuntu 14.04][14]  
[How To Set Up a Private Docker Registry on Ubuntu 14.04][15]  
[OpenSSL: Generating an RSA Key From the Command Line][16]  
[https://www.digitalocean.com/community/tutorials/openssl-essentials-working-with-ssl-certificates-private-keys-and-csrs][17]  
[Implementing OAuth for Registry V2][18]  
[Docker Registry v2 Bearer token specification][19]  
[企业级Docker镜像仓库的管理和运维][20]  

## 数据库
[Architecture of a Database System][21]   
[Evolutionary Database Design][22]

### Soft Delete

[Soft Delete vs Hard Delete][23]  
[Don’t Delete – Just Don’t][24]  
[The trouble with soft delete][25]  
[Why soft deletes are evil and what to do instead][26]  

## Golang
[Standard Package Layout][27]  
[SOLID Go Design][28]  
[Canonical import paths in Golang][29]

### Performance
[Handling 1 Million Requests per Minute with Go][30] (需翻墙)  

## Python
[Handling Unicode Strings in Python][31]  
[Pragmatic Unicode][32]  

### Packaging Python Modules

[Sharing Your Labor of Love: PyPI Quick and Dirty][33]

### django
[How to develop/include a Django custom reusable app in a new project? Are there some guidelines? ][34]   
[Enable setup.py test in your Django apps][35]  
[Package Your Python Django Application into a Reusable Component][36]  
[How to develop/include a Django custom reusable app in a new project?][37]

### Test
[An Introduction to Mocking in Python][38]  
[Skipping Test DB Creation (Django)][39]  
[Effective TDD: Tricks to speed up Django tests (up to 10x faster!)][40]  
[Using pytest with Django][41]  
[Test a Reusable Django Application for Support of Multiple Django Releases with Tox and TravisCI][42]  
[DEVELOPING DJANGO APPS WITH ZC.BUILDOUT][43]  
[mbrochh/tdd-with-django-reusable-app][44]  
[Maintaining Reusable Django Apps with Tox][45]  
[QUICK GUIDE TO CREATING REUSABLE DJANGO APPS][46]  

## Javascript

[ECMAScript 6 — New Features: Overview & Comparison][47]  

## 缓存
[利用redis缓存热门数据，分页的一种思路][48]  
[移动端列表缓存/分页问题][49]  
[为什么超长列表数据的翻页技术实现复杂（二）][50]  
[缓存更新的套路][51]  
[Caching Pattern][52]  
[Scaling Memcache at Facebook][53]  


  [1]: http://www.ruanyifeng.com/blog/2015/09/continuous-integration.html
  [2]: https://www.zhihu.com/question/23444990
  [3]: http://coolshell.cn/articles/7657.html/comment-page-1#comment-1893811
  [4]: https://www.infoq.com/articles/continuous-deployment-containers
  [5]: http://www.uml.org.cn/yunjisuan/201507072.asp
  [6]: http://mp.weixin.qq.com/s?__biz=MzA4Nzg5Nzc5OA==&mid=402143684&idx=2&sn=9e93de8811e342cbd01f634a5e8976c3
  [7]: http://www.thinksaas.cn/topics/0/378/378471.html
  [8]: http://dockone.io/article/470
  [9]: https://github.com/blog/1241-deploying-at-github
  [10]: https://zachholman.com/posts/deploying-software
  [11]: http://blog.flow.ci/
  [12]: http://www.aosabook.org/en/integration.html
  [13]: https://glyph.twistedmatrix.com/2016/10/what-am-container.html
  [14]: https://www.digitalocean.com/community/tutorials/how-to-create-an-ssl-certificate-on-nginx-for-ubuntu-14-04
  [15]: https://www.digitalocean.com/community/tutorials/how-to-set-up-a-private-docker-registry-on-ubuntu-14-04
  [16]: https://rietta.com/blog/2012/01/27/openssl-generating-rsa-key-from-command/
  [17]: https://www.digitalocean.com/community/tutorials/openssl-essentials-working-with-ssl-certificates-private-keys-and-csrs
  [18]: https://blog.opendns.com/2016/02/23/implementing-oauth-for-registry-v2/
  [19]: https://docs.docker.com/registry/spec/auth/jwt/
  [20]: http://geek.csdn.net/news/detail/109110
  [21]: https://blog.acolyer.org/2015/01/20/architecture-of-a-database-system/
  [22]: http://www.martinfowler.com/articles/evodb.html
  [23]: http://rohithegde.github.io/soft-vs-hard-delete/
  [24]: http://udidahan.com/2009/09/01/dont-delete-just-dont/
  [25]: http://rdingwall.com/2009/11/20/the-trouble-with-soft-delete/
  [26]: http://jameshalsall.co.uk/posts/why-soft-deletes-are-evil-and-what-to-do-instead
  [27]: https://medium.com/@benbjohnson/standard-package-layout-7cdbc8391fc1#.wqta80kf1
  [28]: https://dave.cheney.net/2016/08/20/solid-go-design?utm_source=golangweekly&utm_medium=email
  [29]: https://texlution.com/post/golang-canonical-import-paths/
  [30]: http://marcio.io/2015/07/handling-1-million-requests-per-minute-with-golang/
  [31]: http://blog.emacsos.com/unicode-in-python.html
  [32]: http://nedbatchelder.com/text/unipain.html
  [33]: https://hynek.me/articles/sharing-your-labor-of-love-pypi-quick-and-dirty/
  [34]: http://blogs.candoerz.com/question/145773/how-to-developinclude-a-django-custom-reusable-app-in-a-new-project-are-there-some-guidelines.aspx
  [35]: http://ericholscher.com/blog/2009/jun/29/enable-setuppy-test-your-django-apps/
  [36]: http://pythoncentral.io/package-python-django-application-reusable-component/
  [37]: http://stackoverflow.com/questions/30743720/how-to-develop-include-a-django-custom-reusable-app-in-a-new-project-are-there
  [38]: https://www.toptal.com/python/an-introduction-to-mocking-in-python
  [39]: https://www.caktusgroup.com/blog/2013/10/02/skipping-test-db-creation/
  [40]: http://www.daveoncode.com/2013/09/23/effective-tdd-tricks-to-speed-up-django-tests-up-to-10x-faster/
  [41]: http://engineroom.trackmaven.com/blog/using-pytest-with-django/
  [42]: http://joebergantine.com/blog/2015/dec/03/test-reusable-django-application-support-multiple-/
  [43]: https://jacobian.org/writing/django-apps-with-buildout/
  [44]: https://github.com/mbrochh/tdd-with-django-reusable-app
  [45]: http://martinbrochhaus.com/tox.html
  [46]: http://racingtadpole.com/blog/reusable-django-apps/
  [47]: http://es6-features.org/#Constants
  [48]: http://blog.csdn.net/hengyunabc/article/details/23401539
  [49]: http://www.jianshu.com/p/af406cb819e5
  [50]: http://timyang.net/data/key-list-pagination-ii/
  [51]: http://coolshell.cn/articles/17416.html
  [52]: http://kircher-schwanninger.de/michael/publications/Caching.pdf
  [53]: https://www.usenix.org/system/files/conference/nsdi13/nsdi13-final170_update.pdf
