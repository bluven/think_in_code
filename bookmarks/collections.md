# 大杂烩

## 工程

[技术选型最怕的是什么？][1]  

## CI/CD
[持续集成是什么？][2]  
[如何理解持续集成、持续交付、持续部署？][3]  
[持续部署，并不简单！][4]  
[Continuous Deployment with Containers][5]  
[通过Docker容器运行持续集成/持续部署][6]  
[Docker 持续部署图文详解][7](原文)  
[Jenkins+Git+Maven+Shell+Tomcat 持续集成部署环境大众化解决方案][8]  
[DockOne技术分享（九）：持续集成和“云”][9]  
[Deploying at GitHub][10]  
[HOW TO DEPLOY SOFTWARE][11]  
一个CI/CD博客[flow.ci][12]  
[Continuous Integration架构设计][13]  
[How We Release So Frequently][14]  

## Docker
[What Am Container][15]  

### registry
[How To Create an SSL Certificate on Nginx for Ubuntu 14.04][16]  
[How To Set Up a Private Docker Registry on Ubuntu 14.04][17]  
[OpenSSL: Generating an RSA Key From the Command Line][18]  
[https://www.digitalocean.com/community/tutorials/openssl-essentials-working-with-ssl-certificates-private-keys-and-csrs][19]  
[Implementing OAuth for Registry V2][20]  
[Docker Registry v2 Bearer token specification][21]  
[企业级Docker镜像仓库的管理和运维][22]  

## 数据库
[Architecture of a Database System][23]   
[Evolutionary Database Design][24]
[Online migrations at scale][25]  

### Soft Delete

[Soft Delete vs Hard Delete][26]  
[Don’t Delete – Just Don’t][27]  
[The trouble with soft delete][28]  
[Why soft deletes are evil and what to do instead][29]  

## Golang
[Standard Package Layout][30]  
[SOLID Go Design][31]  
[Canonical import paths in Golang][32]

### Performance
[Handling 1 Million Requests per Minute with Go][33] (需翻墙)  

## Python
[Handling Unicode Strings in Python][34]  
[Pragmatic Unicode][35]  
[gevent程序员指南][36]
[gevent For the Working Python Developer][37]

### Packaging Python Modules

[Sharing Your Labor of Love: PyPI Quick and Dirty][38]

### django
[How to develop/include a Django custom reusable app in a new project? Are there some guidelines? ][39]   
[Enable setup.py test in your Django apps][40]  
[Package Your Python Django Application into a Reusable Component][41]  
[How to develop/include a Django custom reusable app in a new project?][42]

### Test
[An Introduction to Mocking in Python][43]  
[Skipping Test DB Creation (Django)][44]  
[Effective TDD: Tricks to speed up Django tests (up to 10x faster!)][45]  
[Using pytest with Django][46]  
[Test a Reusable Django Application for Support of Multiple Django Releases with Tox and TravisCI][47]  
[DEVELOPING DJANGO APPS WITH ZC.BUILDOUT][48]  
[mbrochh/tdd-with-django-reusable-app][49]  
[Maintaining Reusable Django Apps with Tox][50]  
[QUICK GUIDE TO CREATING REUSABLE DJANGO APPS][51]  

## Javascript

[ECMAScript 6 — New Features: Overview & Comparison][52]  

## 缓存
[利用redis缓存热门数据，分页的一种思路][53]  
[移动端列表缓存/分页问题][54]  
[为什么超长列表数据的翻页技术实现复杂（二）][55]  
[缓存更新的套路][56]  
[Caching Pattern][57]  
[Scaling Memcache at Facebook][58]  

## Restful
[Best Practices for Designing a Pragmatic RESTful API][59]  
[REST best practices][60]  
[Best Practices REST API from Scratch – Introduction][61]  
[10 Best Practices for Better RESTful API][62]  
[RESTful API 设计指南][63]  
[理解RESTful架构][64]  
[撰写合格的REST API][65]  
[Choosing an HTTP Status Code — Stop Making It Hard][66]  
[Rest API 的那些事儿][67]  
[Principles of good RESTful API Design][68]  
[REST Is Not About APIs, Part 1][69]  
[Microsoft REST API Guidelines][70]  
[Thoughts on RESTful API Design][71]  
[REST and long-running jobs][72]  
[Long running REST API with queues][73]  
[restful-api-design-references][74]  
[API设计系列文章][75]  
[理解本真的REST架构风格][76]  
[Restful API 的设计规范][77]  
[Learn REST: A RESTful Tutorial][78]  
[来自于PayPal的RESTful API标准][79]  
[HTTP API Design Guide][80]  
[How to design a REST API][81]  
[Choosing the Best Approach for Partial Updates in REST API][82]  

### 案例
[https://developer.paypal.com/docs/api/][83]  
[https://www.twilio.com/docs/api][84]  
[https://github.com/waylau/RestDemo][85]  

## Nginx

[Stateless Authentication implementation using JWT, Nginx+Lua and Memcached][86]  
[Playing HTTP Tricks with Nginx][87]  

## HTTP
[HTTP Cookies Explained][88]  
[HTTP cookies 详解][89]  


  [1]: http://www.imgeek.org/article/825308672
  [2]: http://www.ruanyifeng.com/blog/2015/09/continuous-integration.html
  [3]: https://www.zhihu.com/question/23444990
  [4]: http://coolshell.cn/articles/7657.html/comment-page-1#comment-1893811
  [5]: https://www.infoq.com/articles/continuous-deployment-containers
  [6]: http://www.uml.org.cn/yunjisuan/201507072.asp
  [7]: http://mp.weixin.qq.com/s?__biz=MzA4Nzg5Nzc5OA==&mid=402143684&idx=2&sn=9e93de8811e342cbd01f634a5e8976c3
  [8]: http://www.thinksaas.cn/topics/0/378/378471.html
  [9]: http://dockone.io/article/470
  [10]: https://github.com/blog/1241-deploying-at-github
  [11]: https://zachholman.com/posts/deploying-software
  [12]: http://blog.flow.ci/
  [13]: http://www.aosabook.org/en/integration.html
  [14]: http://engineering.skybettingandgaming.com/2016/02/02/how-we-release-so-frequently/?utm_source=wanqu.co&utm_campaign=Wanqu%20Daily&utm_medium=website
  [15]: https://glyph.twistedmatrix.com/2016/10/what-am-container.html
  [16]: https://www.digitalocean.com/community/tutorials/how-to-create-an-ssl-certificate-on-nginx-for-ubuntu-14-04
  [17]: https://www.digitalocean.com/community/tutorials/how-to-set-up-a-private-docker-registry-on-ubuntu-14-04
  [18]: https://rietta.com/blog/2012/01/27/openssl-generating-rsa-key-from-command/
  [19]: https://www.digitalocean.com/community/tutorials/openssl-essentials-working-with-ssl-certificates-private-keys-and-csrs
  [20]: https://blog.opendns.com/2016/02/23/implementing-oauth-for-registry-v2/
  [21]: https://docs.docker.com/registry/spec/auth/jwt/
  [22]: http://geek.csdn.net/news/detail/109110
  [23]: https://blog.acolyer.org/2015/01/20/architecture-of-a-database-system/
  [24]: http://www.martinfowler.com/articles/evodb.html
  [25]: https://stripe.com/blog/online-migrations
  [26]: http://rohithegde.github.io/soft-vs-hard-delete/
  [27]: http://udidahan.com/2009/09/01/dont-delete-just-dont/
  [28]: http://rdingwall.com/2009/11/20/the-trouble-with-soft-delete/
  [29]: http://jameshalsall.co.uk/posts/why-soft-deletes-are-evil-and-what-to-do-instead
  [30]: https://medium.com/@benbjohnson/standard-package-layout-7cdbc8391fc1#.wqta80kf1
  [31]: https://dave.cheney.net/2016/08/20/solid-go-design?utm_source=golangweekly&utm_medium=email
  [32]: https://texlution.com/post/golang-canonical-import-paths/
  [33]: http://marcio.io/2015/07/handling-1-million-requests-per-minute-with-golang/
  [34]: http://blog.emacsos.com/unicode-in-python.html
  [35]: http://nedbatchelder.com/text/unipain.html
  [36]: http://xlambda.com/gevent-tutorial/#greenlet
  [37]: http://sdiehl.github.io/gevent-tutorial/
  [38]: https://hynek.me/articles/sharing-your-labor-of-love-pypi-quick-and-dirty/
  [39]: http://blogs.candoerz.com/question/145773/how-to-developinclude-a-django-custom-reusable-app-in-a-new-project-are-there-some-guidelines.aspx
  [40]: http://ericholscher.com/blog/2009/jun/29/enable-setuppy-test-your-django-apps/
  [41]: http://pythoncentral.io/package-python-django-application-reusable-component/
  [42]: http://stackoverflow.com/questions/30743720/how-to-develop-include-a-django-custom-reusable-app-in-a-new-project-are-there
  [43]: https://www.toptal.com/python/an-introduction-to-mocking-in-python
  [44]: https://www.caktusgroup.com/blog/2013/10/02/skipping-test-db-creation/
  [45]: http://www.daveoncode.com/2013/09/23/effective-tdd-tricks-to-speed-up-django-tests-up-to-10x-faster/
  [46]: http://engineroom.trackmaven.com/blog/using-pytest-with-django/
  [47]: http://joebergantine.com/blog/2015/dec/03/test-reusable-django-application-support-multiple-/
  [48]: https://jacobian.org/writing/django-apps-with-buildout/
  [49]: https://github.com/mbrochh/tdd-with-django-reusable-app
  [50]: http://martinbrochhaus.com/tox.html
  [51]: http://racingtadpole.com/blog/reusable-django-apps/
  [52]: http://es6-features.org/#Constants
  [53]: http://blog.csdn.net/hengyunabc/article/details/23401539
  [54]: http://www.jianshu.com/p/af406cb819e5
  [55]: http://timyang.net/data/key-list-pagination-ii/
  [56]: http://coolshell.cn/articles/17416.html
  [57]: http://kircher-schwanninger.de/michael/publications/Caching.pdf
  [58]: https://www.usenix.org/system/files/conference/nsdi13/nsdi13-final170_update.pdf
  [59]: http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#versioning
  [60]: https://bourgeois.me/rest/
  [61]: https://www.sitepoint.com/best-practices-rest-api-scratch-introduction/
  [62]: http://blog.mwaysolutions.com/2014/06/05/10-best-practices-for-better-restful-api/
  [63]: http://www.ruanyifeng.com/blog/2014/05/restful_api.html
  [64]: http://www.ruanyifeng.com/blog/2011/09/restful
  [65]: http://mp.weixin.qq.com/s?__biz=MzA3NDM0ODQwMw==&mid=208060670&idx=1&sn=ce67b8896985e8448137052b338093e0&scene=21#wechat_redirect
  [66]: http://racksburg.com/choosing-an-http-status-code/
  [67]: http://www.ituring.com.cn/article/208878
  [68]: https://codeplanet.io/principles-good-restful-api-design/
  [69]: http://www.nirmata.com/2013/10/rest-apis-part-1/
  [70]: https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md
  [71]: https://restful-api-design.readthedocs.io/en/latest/
  [72]: http://farazdagi.com/blog/2014/rest-long-running-jobs/
  [73]: http://stackoverflow.com/questions/33009721/long-running-rest-api-with-queues
  [74]: https://github.com/aisuhua/restful-api-design-references
  [75]: http://www.jianshu.com/p/261cad07f02b
  [76]: http://blog.sae.sina.com.cn/archives/370
  [77]: http://novoland.github.io/%E8%AE%BE%E8%AE%A1/2015/08/17/Restful%20API%20%E7%9A%84%E8%AE%BE%E8%AE%A1%E8%A7%84%E8%8C%83.html
  [78]: http://www.restapitutorial.com/
  [79]: https://segmentfault.com/a/1190000005924733
  [80]: https://geemus.gitbooks.io/http-api-design/content/en/
  [81]: http://blog.octo.com/en/design-a-rest-api/
  [82]: http://www.apuchkov.com/choosing-the-best-approach-for-partial-updates-in-rest-api/
  [83]: https://developer.paypal.com/docs/api/
  [84]: https://www.twilio.com/docs/api
  [85]: https://github.com/waylau/RestDemo
  [86]: https://blog.imaginea.com/stateless-authentication-implementation-using-jwt-nginxlua-and-memcached/
  [87]: https://www.elastic.co/blog/playing-http-tricks-nginx
  [88]: https://www.nczonline.net/blog/2009/05/05/http-cookies-explained/
  [89]: http://bubkoo.com/2014/04/21/http-cookies-explained/
