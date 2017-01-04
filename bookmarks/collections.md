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
[How We Release So Frequently][13]  

## Docker
[What Am Container][14]  

### registry
[How To Create an SSL Certificate on Nginx for Ubuntu 14.04][15]  
[How To Set Up a Private Docker Registry on Ubuntu 14.04][16]  
[OpenSSL: Generating an RSA Key From the Command Line][17]  
[https://www.digitalocean.com/community/tutorials/openssl-essentials-working-with-ssl-certificates-private-keys-and-csrs][18]  
[Implementing OAuth for Registry V2][19]  
[Docker Registry v2 Bearer token specification][20]  
[企业级Docker镜像仓库的管理和运维][21]  

## 数据库
[Architecture of a Database System][22]   
[Evolutionary Database Design][23]

### Soft Delete

[Soft Delete vs Hard Delete][24]  
[Don’t Delete – Just Don’t][25]  
[The trouble with soft delete][26]  
[Why soft deletes are evil and what to do instead][27]  

## Golang
[Standard Package Layout][28]  
[SOLID Go Design][29]  
[Canonical import paths in Golang][30]

### Performance
[Handling 1 Million Requests per Minute with Go][31] (需翻墙)  

## Python
[Handling Unicode Strings in Python][32]  
[Pragmatic Unicode][33]  

### Packaging Python Modules

[Sharing Your Labor of Love: PyPI Quick and Dirty][34]

### django
[How to develop/include a Django custom reusable app in a new project? Are there some guidelines? ][35]   
[Enable setup.py test in your Django apps][36]  
[Package Your Python Django Application into a Reusable Component][37]  
[How to develop/include a Django custom reusable app in a new project?][38]

### Test
[An Introduction to Mocking in Python][39]  
[Skipping Test DB Creation (Django)][40]  
[Effective TDD: Tricks to speed up Django tests (up to 10x faster!)][41]  
[Using pytest with Django][42]  
[Test a Reusable Django Application for Support of Multiple Django Releases with Tox and TravisCI][43]  
[DEVELOPING DJANGO APPS WITH ZC.BUILDOUT][44]  
[mbrochh/tdd-with-django-reusable-app][45]  
[Maintaining Reusable Django Apps with Tox][46]  
[QUICK GUIDE TO CREATING REUSABLE DJANGO APPS][47]  

## Javascript

[ECMAScript 6 — New Features: Overview & Comparison][48]  

## 缓存
[利用redis缓存热门数据，分页的一种思路][49]  
[移动端列表缓存/分页问题][50]  
[为什么超长列表数据的翻页技术实现复杂（二）][51]  
[缓存更新的套路][52]  
[Caching Pattern][53]  
[Scaling Memcache at Facebook][54]  

## Restful
[Best Practices for Designing a Pragmatic RESTful API][55]  
[REST best practices][56]  
[Best Practices REST API from Scratch – Introduction][57]  
[10 Best Practices for Better RESTful API][58]  
[RESTful API 设计指南][59]  
[理解RESTful架构][60]  
[撰写合格的REST API][61]  
[Choosing an HTTP Status Code — Stop Making It Hard][62]  
[Rest API 的那些事儿][63]  
[Principles of good RESTful API Design][64]  
[REST Is Not About APIs, Part 1][65]  
[Microsoft REST API Guidelines][66]  
[Thoughts on RESTful API Design][67]  
[REST and long-running jobs][68]  
[Long running REST API with queues][69]  
[restful-api-design-references][70]  
[API设计系列文章][71]  
[理解本真的REST架构风格][72]  
[Restful API 的设计规范][73]  
[Learn REST: A RESTful Tutorial][74]  
[来自于PayPal的RESTful API标准][75]  
[HTTP API Design Guide][76]  
[How to design a REST API][77]  

### 案例
[https://developer.paypal.com/docs/api/][78]  
[https://www.twilio.com/docs/api][79]  
[https://github.com/waylau/RestDemo][80]  


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
  [13]: http://engineering.skybettingandgaming.com/2016/02/02/how-we-release-so-frequently/?utm_source=wanqu.co&utm_campaign=Wanqu%20Daily&utm_medium=website
  [14]: https://glyph.twistedmatrix.com/2016/10/what-am-container.html
  [15]: https://www.digitalocean.com/community/tutorials/how-to-create-an-ssl-certificate-on-nginx-for-ubuntu-14-04
  [16]: https://www.digitalocean.com/community/tutorials/how-to-set-up-a-private-docker-registry-on-ubuntu-14-04
  [17]: https://rietta.com/blog/2012/01/27/openssl-generating-rsa-key-from-command/
  [18]: https://www.digitalocean.com/community/tutorials/openssl-essentials-working-with-ssl-certificates-private-keys-and-csrs
  [19]: https://blog.opendns.com/2016/02/23/implementing-oauth-for-registry-v2/
  [20]: https://docs.docker.com/registry/spec/auth/jwt/
  [21]: http://geek.csdn.net/news/detail/109110
  [22]: https://blog.acolyer.org/2015/01/20/architecture-of-a-database-system/
  [23]: http://www.martinfowler.com/articles/evodb.html
  [24]: http://rohithegde.github.io/soft-vs-hard-delete/
  [25]: http://udidahan.com/2009/09/01/dont-delete-just-dont/
  [26]: http://rdingwall.com/2009/11/20/the-trouble-with-soft-delete/
  [27]: http://jameshalsall.co.uk/posts/why-soft-deletes-are-evil-and-what-to-do-instead
  [28]: https://medium.com/@benbjohnson/standard-package-layout-7cdbc8391fc1#.wqta80kf1
  [29]: https://dave.cheney.net/2016/08/20/solid-go-design?utm_source=golangweekly&utm_medium=email
  [30]: https://texlution.com/post/golang-canonical-import-paths/
  [31]: http://marcio.io/2015/07/handling-1-million-requests-per-minute-with-golang/
  [32]: http://blog.emacsos.com/unicode-in-python.html
  [33]: http://nedbatchelder.com/text/unipain.html
  [34]: https://hynek.me/articles/sharing-your-labor-of-love-pypi-quick-and-dirty/
  [35]: http://blogs.candoerz.com/question/145773/how-to-developinclude-a-django-custom-reusable-app-in-a-new-project-are-there-some-guidelines.aspx
  [36]: http://ericholscher.com/blog/2009/jun/29/enable-setuppy-test-your-django-apps/
  [37]: http://pythoncentral.io/package-python-django-application-reusable-component/
  [38]: http://stackoverflow.com/questions/30743720/how-to-develop-include-a-django-custom-reusable-app-in-a-new-project-are-there
  [39]: https://www.toptal.com/python/an-introduction-to-mocking-in-python
  [40]: https://www.caktusgroup.com/blog/2013/10/02/skipping-test-db-creation/
  [41]: http://www.daveoncode.com/2013/09/23/effective-tdd-tricks-to-speed-up-django-tests-up-to-10x-faster/
  [42]: http://engineroom.trackmaven.com/blog/using-pytest-with-django/
  [43]: http://joebergantine.com/blog/2015/dec/03/test-reusable-django-application-support-multiple-/
  [44]: https://jacobian.org/writing/django-apps-with-buildout/
  [45]: https://github.com/mbrochh/tdd-with-django-reusable-app
  [46]: http://martinbrochhaus.com/tox.html
  [47]: http://racingtadpole.com/blog/reusable-django-apps/
  [48]: http://es6-features.org/#Constants
  [49]: http://blog.csdn.net/hengyunabc/article/details/23401539
  [50]: http://www.jianshu.com/p/af406cb819e5
  [51]: http://timyang.net/data/key-list-pagination-ii/
  [52]: http://coolshell.cn/articles/17416.html
  [53]: http://kircher-schwanninger.de/michael/publications/Caching.pdf
  [54]: https://www.usenix.org/system/files/conference/nsdi13/nsdi13-final170_update.pdf
  [55]: http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#versioning
  [56]: https://bourgeois.me/rest/
  [57]: https://www.sitepoint.com/best-practices-rest-api-scratch-introduction/
  [58]: http://blog.mwaysolutions.com/2014/06/05/10-best-practices-for-better-restful-api/
  [59]: http://www.ruanyifeng.com/blog/2014/05/restful_api.html
  [60]: http://www.ruanyifeng.com/blog/2011/09/restful
  [61]: http://mp.weixin.qq.com/s?__biz=MzA3NDM0ODQwMw==&mid=208060670&idx=1&sn=ce67b8896985e8448137052b338093e0&scene=21#wechat_redirect
  [62]: http://racksburg.com/choosing-an-http-status-code/
  [63]: http://www.ituring.com.cn/article/208878
  [64]: https://codeplanet.io/principles-good-restful-api-design/
  [65]: http://www.nirmata.com/2013/10/rest-apis-part-1/
  [66]: https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md
  [67]: https://restful-api-design.readthedocs.io/en/latest/
  [68]: http://farazdagi.com/blog/2014/rest-long-running-jobs/
  [69]: http://stackoverflow.com/questions/33009721/long-running-rest-api-with-queues
  [70]: https://github.com/aisuhua/restful-api-design-references
  [71]: http://www.jianshu.com/p/261cad07f02b
  [72]: http://blog.sae.sina.com.cn/archives/370
  [73]: http://novoland.github.io/%E8%AE%BE%E8%AE%A1/2015/08/17/Restful%20API%20%E7%9A%84%E8%AE%BE%E8%AE%A1%E8%A7%84%E8%8C%83.html
  [74]: http://www.restapitutorial.com/
  [75]: https://segmentfault.com/a/1190000005924733
  [76]: https://geemus.gitbooks.io/http-api-design/content/en/
  [77]: http://blog.octo.com/en/design-a-rest-api/
  [78]: https://developer.paypal.com/docs/api/
  [79]: https://www.twilio.com/docs/api
  [80]: https://github.com/waylau/RestDemo
