**Reproduce steps**
- Build wildfly from https://github.com/jimma/wildfly/tree/undertow-other-domain-ws-0323
- Run following things to remove security subsystem and enable elytron 
   1. `cd $JBOSS_HOME`
   2. `./bin/jboss-cli.sh --file=./docs/examples/enable-elytron.cli`
   3. `./bin/standalone.sh`
   4. `./bin/jboss-cli.sh -c`
   5. `[standalone@localhost:9990 /] /subsystem=security:remove()`
- Deploy the war jaxws-samples-wsse-policy-username-jaas.war under deployment folder in this repo
  The source code for this war deployment file can be found from :https://github.com/jimma/jbossws-cxf/blob/elytron-null-securitydomain/modules/testsuite/cxf-tests/src/test/java/org/jboss/test/ws/jaxws/samples/wsse/policy/jaas/UsernameAuthorizationTestCase.java

- Run curl to post the quest to server:
  ```
  curl -d "<soap:Envelope xmlns:soap=\"http://schemas.xmlsoap.org/soap/envelope/\"><soap:Header><wsse:Security soap:mustUnderstand=\"1\" xmlns:wsse=\"http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd\"><wsse:UsernameToken xmlns:wsu=\"http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd\" wsu:Id=\"UsernameToken-0e16ab28-14e2-469c-8ed6-ff8a5de3b553\"><wsse:Username>kermit</wsse:Username><wsse:Password Type=\"http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-username-token-profile-1.0#PasswordText\">thefrog</wsse:Password></wsse:UsernameToken></wsse:Security></soap:Header><soap:Body><ns2:sayHello xmlns:ns2=\"http://www.jboss.org/jbossws/ws-extensions/wssecuritypolicy\"/></soap:Body></soap:Envelope>" http://localhost:8080/jaxws-samples-wsse-policy-username-jaas
  ```
-  Wildfly server prints some error stack trace like : 

   ```
   10:38:02,467 WARNING [org.apache.cxf.phase.PhaseInterceptorChain] (default task-1) Interceptor for {http://www.jboss.org/jbossws/ws-extensions/wssecuritypolicy}SecurityService has thrown exception, unwinding now: java.lang.SecurityException: JBWS024057: Failed Authentication : Subject has not been created
   	at org.jboss.wsf.stack.cxf.security.authentication.SubjectCreatingPolicyInterceptor.createSubject(SubjectCreatingPolicyInterceptor.java:119)
   	at org.jboss.wsf.stack.cxf.security.authentication.SubjectCreatingPolicyInterceptor.handleMessage(SubjectCreatingPolicyInterceptor.java:92)
   	at org.apache.cxf.phase.PhaseInterceptorChain.doIntercept(PhaseInterceptorChain.java:308)
   	at org.apache.cxf.transport.ChainInitiationObserver.onMessage(ChainInitiationObserver.java:121)
   	at org.apache.cxf.transport.http.AbstractHTTPDestination.invoke(AbstractHTTPDestination.java:267)
   	at org.jboss.wsf.stack.cxf.RequestHandlerImpl.handleHttpRequest(RequestHandlerImpl.java:110)
   	at org.jboss.wsf.stack.cxf.transport.ServletHelper.callRequestHandler(ServletHelper.java:134)
   	at org.jboss.wsf.stack.cxf.CXFServletExt.invoke(CXFServletExt.java:88)
   	at org.apache.cxf.transport.servlet.AbstractHTTPServlet.handleRequest(AbstractHTTPServlet.java:301)
   	at org.apache.cxf.transport.servlet.AbstractHTTPServlet.doPost(AbstractHTTPServlet.java:220)
   	at javax.servlet.http.HttpServlet.service(HttpServlet.java:523)
   	at org.jboss.wsf.stack.cxf.CXFServletExt.service(CXFServletExt.java:136)
   	at org.jboss.wsf.spi.deployment.WSFServlet.service(WSFServlet.java:140)
   	at javax.servlet.http.HttpServlet.service(HttpServlet.java:590)
   	at io.undertow.servlet.handlers.ServletHandler.handleRequest(ServletHandler.java:74)
   	at io.undertow.servlet.handlers.security.ServletSecurityRoleHandler.handleRequest(ServletSecurityRoleHandler.java:62)
   	at io.undertow.servlet.handlers.ServletChain$1.handleRequest(ServletChain.java:68)
   	at io.undertow.servlet.handlers.ServletDispatchingHandler.handleRequest(ServletDispatchingHandler.java:36)
   	at org.wildfly.elytron.web.undertow.server.ElytronRunAsHandler.lambda$handleRequest$1(ElytronRunAsHandler.java:68)
   	at org.wildfly.security.auth.server.FlexibleIdentityAssociation.runAsFunctionEx(FlexibleIdentityAssociation.java:103)
   	at org.wildfly.security.auth.server.Scoped.runAsFunctionEx(Scoped.java:161)
   	at org.wildfly.security.auth.server.Scoped.runAs(Scoped.java:73)
   	at org.wildfly.elytron.web.undertow.server.ElytronRunAsHandler.handleRequest(ElytronRunAsHandler.java:67)
   	at io.undertow.servlet.handlers.RedirectDirHandler.handleRequest(RedirectDirHandler.java:68)
   	at io.undertow.servlet.handlers.security.SSLInformationAssociationHandler.handleRequest(SSLInformationAssociationHandler.java:132)
   	at io.undertow.servlet.handlers.security.ServletAuthenticationCallHandler.handleRequest(ServletAuthenticationCallHandler.java:57)
   	at io.undertow.server.handlers.PredicateHandler.handleRequest(PredicateHandler.java:43)
   	at io.undertow.security.handlers.AbstractConfidentialityHandler.handleRequest(AbstractConfidentialityHandler.java:46)
   	at io.undertow.servlet.handlers.security.ServletConfidentialityConstraintHandler.handleRequest(ServletConfidentialityConstraintHandler.java:64)
   	at io.undertow.security.handlers.AbstractSecurityContextAssociationHandler.handleRequest(AbstractSecurityContextAssociationHandler.java:43)
   	at org.wildfly.elytron.web.undertow.server.servlet.CleanUpHandler.handleRequest(CleanUpHandler.java:38)
   	at io.undertow.server.handlers.PredicateHandler.handleRequest(PredicateHandler.java:43)
   	at org.wildfly.extension.undertow.security.jacc.JACCContextIdHandler.handleRequest(JACCContextIdHandler.java:61)
   	at io.undertow.server.handlers.PredicateHandler.handleRequest(PredicateHandler.java:43)
   	at org.wildfly.extension.undertow.deployment.GlobalRequestControllerHandler.handleRequest(GlobalRequestControllerHandler.java:68)
   	at io.undertow.server.handlers.PredicateHandler.handleRequest(PredicateHandler.java:43)
   	at io.undertow.servlet.handlers.ServletInitialHandler.handleFirstRequest(ServletInitialHandler.java:269)
   	at io.undertow.servlet.handlers.ServletInitialHandler.access$100(ServletInitialHandler.java:78)
   	at io.undertow.servlet.handlers.ServletInitialHandler$2.call(ServletInitialHandler.java:133)
   	at io.undertow.servlet.handlers.ServletInitialHandler$2.call(ServletInitialHandler.java:130)
   	at io.undertow.servlet.core.ServletRequestContextThreadSetupAction$1.call(ServletRequestContextThreadSetupAction.java:48)
   	at io.undertow.servlet.core.ContextClassLoaderSetupAction$1.call(ContextClassLoaderSetupAction.java:43)
   	at org.wildfly.extension.undertow.deployment.UndertowDeploymentInfoService$UndertowThreadSetupAction.lambda$create$0(UndertowDeploymentInfoService.java:1541)
   	at org.wildfly.extension.undertow.deployment.UndertowDeploymentInfoService$UndertowThreadSetupAction.lambda$create$0(UndertowDeploymentInfoService.java:1541)
   	at org.wildfly.extension.undertow.deployment.UndertowDeploymentInfoService$UndertowThreadSetupAction.lambda$create$0(UndertowDeploymentInfoService.java:1541)
   	at org.wildfly.extension.undertow.deployment.UndertowDeploymentInfoService$UndertowThreadSetupAction.lambda$create$0(UndertowDeploymentInfoService.java:1541)
   	at io.undertow.servlet.handlers.ServletInitialHandler.dispatchRequest(ServletInitialHandler.java:249)
   	at io.undertow.servlet.handlers.ServletInitialHandler.access$000(ServletInitialHandler.java:78)
   	at io.undertow.servlet.handlers.ServletInitialHandler$1.handleRequest(ServletInitialHandler.java:99)
   	at io.undertow.server.Connectors.executeRootHandler(Connectors.java:376)
   	at io.undertow.server.HttpServerExchange$1.run(HttpServerExchange.java:830)
   	at org.jboss.threads.ContextClassLoaderSavingRunnable.run(ContextClassLoaderSavingRunnable.java:35)
   	at org.jboss.threads.EnhancedQueueExecutor.safeRun(EnhancedQueueExecutor.java:1982)
   	at org.jboss.threads.EnhancedQueueExecutor$ThreadBody.doRunTask(EnhancedQueueExecutor.java:1486)
   	at org.jboss.threads.EnhancedQueueExecutor$ThreadBody.run(EnhancedQueueExecutor.java:1377)
   	at java.lang.Thread.run(Thread.java:748)
   Caused by: java.lang.NullPointerException
   	at org.jboss.as.webservices.security.ElytronSecurityDomainContextImpl.authenticate(ElytronSecurityDomainContextImpl.java:119)
   	at org.jboss.as.webservices.security.ElytronSecurityDomainContextImpl.isValid(ElytronSecurityDomainContextImpl.java:80)
   	at org.jboss.wsf.stack.cxf.security.authentication.SubjectCreator.createSubject(SubjectCreator.java:105)
   	at org.jboss.wsf.stack.cxf.security.authentication.SubjectCreatingPolicyInterceptor.createSubject(SubjectCreatingPolicyInterceptor.java:115)
   	... 55 more
   


 