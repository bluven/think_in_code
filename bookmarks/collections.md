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
[Online migrations at scale][24]  

### Soft Delete

[Soft Delete vs Hard Delete][25]  
[Don’t Delete – Just Don’t][26]  
[The trouble with soft delete][27]  
[Why soft deletes are evil and what to do instead][28]  

## Golang
[Standard Package Layout][29]  
[SOLID Go Design][30]  
[Canonical import paths in Golang][31]

### Performance
[Handling 1 Million Requests per Minute with Go][32] (需翻墙)  

## Python
[Handling Unicode Strings in Python][33]  
[Pragmatic Unicode][34]  

### Packaging Python Modules

[Sharing Your Labor of Love: PyPI Quick and Dirty][35]

### django
[How to develop/include a Django custom reusable app in a new project? Are there some guidelines? ][36]   
[Enable setup.py test in your Django apps][37]  
[Package Your Python Django Application into a Reusable Component][38]  
[How to develop/include a Django custom reusable app in a new project?][39]

### Test
[An Introduction to Mocking in Python][40]  
[Skipping Test DB Creation (Django)][41]  
[Effective TDD: Tricks to speed up Django tests (up to 10x faster!)][42]  
[Using pytest with Django][43]  
[Test a Reusable Django Application for Support of Multiple Django Releases with Tox and TravisCI][44]  
[DEVELOPING DJANGO APPS WITH ZC.BUILDOUT][45]  
[mbrochh/tdd-with-django-reusable-app][46]  
[Maintaining Reusable Django Apps with Tox][47]  
[QUICK GUIDE TO CREATING REUSABLE DJANGO APPS][48]  

## Javascript

[ECMAScript 6 — New Features: Overview & Comparison][49]  

## 缓存
[利用redis缓存热门数据，分页的一种思路][50]  
[移动端列表缓存/分页问题][51]  
[为什么超长列表数据的翻页技术实现复杂（二）][52]  
[缓存更新的套路][53]  
[Caching Pattern][54]  
[Scaling Memcache at Facebook][55]  

## Restful
[Best Practices for Designing a Pragmatic RESTful API][56]  
[REST best practices][57]  
[Best Practices REST API from Scratch – Introduction][58]  
[10 Best Practices for Better RESTful API][59]  
[RESTful API 设计指南][60]  
[理解RESTful架构][61]  
[撰写合格的REST API][62]  
[Choosing an HTTP Status Code — Stop Making It Hard][63]  
[Rest API 的那些事儿][64]  
[Principles of good RESTful API Design][65]  
[REST Is Not About APIs, Part 1][66]  
[Microsoft REST API Guidelines][67]  
[Thoughts on RESTful API Design][68]  
[REST and long-running jobs][69]  
[Long running REST API with queues][70]  
[restful-api-design-references][71]  
[API设计系列文章][72]  
[理解本真的REST架构风格][73]  
[Restful API 的设计规范][74]  
[Learn REST: A RESTful Tutorial][75]  
[来自于PayPal的RESTful API标准][76]  
[HTTP API Design Guide][77]  
[How to design a REST API][78]  
[Choosing the Best Approach for Partial Updates in REST API][79]  

### 案例
[https://developer.paypal.com/docs/api/][80]  
[https://www.twilio.com/docs/api][81]  
[https://github.com/waylau/RestDemo][82]  


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
  [24]: https://stripe.com/blog/online-migrations
  [25]: http://rohithegde.github.io/soft-vs-hard-delete/
  [26]: http://udidahan.com/2009/09/01/dont-delete-just-dont/
  [27]: http://rdingwall.com/2009/11/20/the-trouble-with-soft-delete/
  [28]: http://jameshalsall.co.uk/posts/why-soft-deletes-are-evil-and-what-to-do-instead
  [29]: https://medium.com/@benbjohnson/standard-package-layout-7cdbc8391fc1#.wqta80kf1
  [30]: https://dave.cheney.net/2016/08/20/solid-go-design?utm_source=golangweekly&utm_medium=email
  [31]: https://texlution.com/post/golang-canonical-import-paths/
  [32]: http://marcio.io/2015/07/handling-1-million-requests-per-minute-with-golang/
  [33]: http://blog.emacsos.com/unicode-in-python.html
  [34]: http://nedbatchelder.com/text/unipain.html
  [35]: https://hynek.me/articles/sharing-your-labor-of-love-pypi-quick-and-dirty/
  [36]: http://blogs.candoerz.com/question/145773/how-to-developinclude-a-django-custom-reusable-app-in-a-new-project-are-there-some-guidelines.aspx
  [37]: http://ericholscher.com/blog/2009/jun/29/enable-setuppy-test-your-django-apps/
  [38]: http://pythoncentral.io/package-python-django-application-reusable-component/
  [39]: http://stackoverflow.com/questions/30743720/how-to-develop-include-a-django-custom-reusable-app-in-a-new-project-are-there
  [40]: https://www.toptal.com/python/an-introduction-to-mocking-in-python
  [41]: https://www.caktusgroup.com/blog/2013/10/02/skipping-test-db-creation/
  [42]: http://www.daveoncode.com/2013/09/23/effective-tdd-tricks-to-speed-up-django-tests-up-to-10x-faster/
  [43]: http://engineroom.trackmaven.com/blog/using-pytest-with-django/
  [44]: http://joebergantine.com/blog/2015/dec/03/test-reusable-django-application-support-multiple-/
  [45]: https://jacobian.org/writing/django-apps-with-buildout/
  [46]: https://github.com/mbrochh/tdd-with-django-reusable-app
  [47]: http://martinbrochhaus.com/tox.html
  [48]: http://racingtadpole.com/blog/reusable-django-apps/
  [49]: http://es6-features.org/#Constants
  [50]: http://blog.csdn.net/hengyunabc/article/details/23401539
  [51]: http://www.jianshu.com/p/af406cb819e5
  [52]: http://timyang.net/data/key-list-pagination-ii/
  [53]: http://coolshell.cn/articles/17416.html
  [54]: http://kircher-schwanninger.de/michael/publications/Caching.pdf
  [55]: https://www.usenix.org/system/files/conference/nsdi13/nsdi13-final170_update.pdf
  [56]: http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#versioning
  [57]: https://bourgeois.me/rest/
  [58]: https://www.sitepoint.com/best-practices-rest-api-scratch-introduction/
  [59]: http://blog.mwaysolutions.com/2014/06/05/10-best-practices-for-better-restful-api/
  [60]: http://www.ruanyifeng.com/blog/2014/05/restful_api.html
  [61]: http://www.ruanyifeng.com/blog/2011/09/restful
  [62]: http://mp.weixin.qq.com/s?__biz=MzA3NDM0ODQwMw==&mid=208060670&idx=1&sn=ce67b8896985e8448137052b338093e0&scene=21#wechat_redirect
  [63]: http://racksburg.com/choosing-an-http-status-code/
  [64]: http://www.ituring.com.cn/article/208878
  [65]: https://codeplanet.io/principles-good-restful-api-design/
  [66]: http://www.nirmata.com/2013/10/rest-apis-part-1/
  [67]: https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md
  [68]: https://restful-api-design.readthedocs.io/en/latest/
  [69]: http://farazdagi.com/blog/2014/rest-long-running-jobs/
  [70]: http://stackoverflow.com/questions/33009721/long-running-rest-api-with-queues
  [71]: https://github.com/aisuhua/restful-api-design-references
  [72]: http://www.jianshu.com/p/261cad07f02b
  [73]: http://blog.sae.sina.com.cn/archives/370
  [74]: http://novoland.github.io/%E8%AE%BE%E8%AE%A1/2015/08/17/Restful%20API%20%E7%9A%84%E8%AE%BE%E8%AE%A1%E8%A7%84%E8%8C%83.html
  [75]: http://www.restapitutorial.com/
  [76]: https://segmentfault.com/a/1190000005924733
  [77]: https://geemus.gitbooks.io/http-api-design/content/en/
  [78]: http://blog.octo.com/en/design-a-rest-api/
  [79]: http://www.apuchkov.com/choosing-the-best-approach-for-partial-updates-in-rest-api/
  [80]: https://developer.paypal.com/docs/api/
  [81]: https://www.twilio.com/docs/api
  [82]: https://github.com/waylau/RestDemo
