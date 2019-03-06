---
layout: post
title: "SpringBoot使用ssl证书配置https"
categories: misc
author: "yaabert" 

---


## 由来  
由于Http服务传输都是明文传输的，容易被拦截信息。所以采用https服务来传输，对信息进行加密。
```
安全是因为有证书对报文继续加密了，如果大规模的使用就需要一个通用版本的证书，本地的证书会不被浏览器认可，识别为不安全。这里采用的是阿里云的免费证书。
```

下载证书Tomcat版本的，下载到本地后文件夹内容如下：    

<img src="http://ww1.sinaimg.cn/large/005XOqo6ly1g0f1dlgmtdj30bo03o74h.jpg"/>

其中.txt为证书密码，pfx为证书内容。

此处记录为Springboot直接配置证书，war包Tomcat亦可配置，但于本文不同。
将证书直接放入springboot的```resources```文件夹下，配置application.properties文件 配置内容如下：
<img src="http://ww1.sinaimg.cn/large/005XOqo6ly1g0f94qsaldj30qi07qmyf.jpg"/>

然后修改启动类中代码如下：
```
@SpringBootApplication
public class HttpsTestApplication implements CommandLineRunner {
    public static void main(String[] args) {
        SpringApplication.run(HttpsTestApplication.class, args);
    }
    @Override
    public void run(String... args) throws Exception {

    }
    @Bean
    public TomcatServletWebServerFactory servletContainer() {
        TomcatServletWebServerFactory tomcat = new TomcatServletWebServerFactory() {
            @Override
            protected void postProcessContext(Context context) {
                SecurityConstraint constraint = new SecurityConstraint();
                constraint.setUserConstraint("CONFIDENTIAL");
                SecurityCollection collection = new SecurityCollection();
                collection.addPattern("/*");
                constraint.addCollection(collection);
                context.addConstraint(constraint);
            }
        };
        tomcat.addAdditionalTomcatConnectors(httpConnector());
        return tomcat;
    }
    @Bean
    public Connector httpConnector() {
        Connector connector = new Connector("org.apache.coyote.http11.Http11NioProtocol");
        connector.setScheme("http");
        connector.setPort(80);
        connector.setSecure(false);
        connector.setRedirectPort(8080);
        return connector;
    }
}

```
8080为服务端口可以在properties中修改，此处应该和properties中具体端口相同，80端口为修改为https服务后 会同时起一个80端口的http服务，将那个服务导入到此处来。
