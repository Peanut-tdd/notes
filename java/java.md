有没有 main 方法？
├─ 有 → 就是入口 ✅
└─ 没有 → 是不是 Spring Boot？
    ├─ 是 → 找 @SpringBootApplication ✅
    └─ 不是 → 看 web.xml / 容器启动









 **常用命令：**

 \- 查看版本：~/tomcat/bin/catalina.sh version

 \- 启动：~/tomcat/bin/catalina.sh run （前台） 或 ~/tomcat/bin/startup.sh （后台）

 \- 停止：~/tomcat/bin/shutdown.sh









lsof -ti:8080 | xargs kill -9