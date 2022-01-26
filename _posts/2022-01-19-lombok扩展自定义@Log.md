---
layout: post
title: Lombok 扩展自定义 @Log
date: 2022-01-25
categories: code
tags: Lombok
---

> 配合 lombok-intellij-plugin 简化业务代码

## Lombok

1. `git clone https://github.com/projectlombok/lombok.git`

2. 安装 ant 环境

3. 修改版本 `lombok.core.Version#VERSION`

4. 新建类

   ```java
   package lombok.extern.xxx;
   
   import java.lang.annotation.ElementType;
   import java.lang.annotation.Retention;
   import java.lang.annotation.RetentionPolicy;
   import java.lang.annotation.Target;
   
   @Retention(RetentionPolicy.SOURCE)
   @Target(ElementType.TYPE)
   public @interface XXXLog {
       String topic() default "";
   }
   ```

   ```java
   package lombok.javac.handlers;
   
   import com.sun.tools.javac.code.Flags;
   import com.sun.tools.javac.tree.JCTree;
   import com.sun.tools.javac.util.List;
   import com.sun.tools.javac.util.Name;
   
   import lombok.core.AST;
   import lombok.core.AnnotationValues;
   import lombok.extern.xxx.XXXLog;
   import lombok.javac.JavacAnnotationHandler;
   import lombok.javac.JavacNode;
   import lombok.javac.JavacTreeMaker;
   import lombok.spi.Provides;
   
   @Provides
   public class HandleXXXLog extends JavacAnnotationHandler<XXXLog> {
   
       private static final String LOG_FIELD_NAME = "log";
   
       @Override
       public void handle(final AnnotationValues<XXXLog> annotation, final JCTree.JCAnnotation ast, final JavacNode annotationNode) {
           JavacNode typeNode = annotationNode.up();
           if (!checkFieldInject(annotationNode, typeNode)) {
               return;
           }
           JCTree.JCVariableDecl fieldDecl = createField(annotation, annotationNode, typeNode);
           JavacHandlerUtil.injectFieldAndMarkGenerated(typeNode, fieldDecl);
       }
   
       private boolean checkFieldInject(final JavacNode annotationNode, final JavacNode typeNode) {
           if (typeNode.getKind() != AST.Kind.TYPE) {
               annotationNode.addError("@XXXLog is legal only on types.");
               return false;
           }
           if ((((JCTree.JCClassDecl) typeNode.get()).mods.flags & Flags.INTERFACE) != 0) {
               annotationNode.addError("@XXXLog is legal only on classes and enums.");
               return false;
           }
           if (JavacHandlerUtil.fieldExists(LOG_FIELD_NAME, typeNode) != JavacHandlerUtil.MemberExistsResult.NOT_EXISTS) {
               annotationNode.addWarning("Field '" + LOG_FIELD_NAME + "' already exists.");
               return false;
           }s
           return true;
       }
   
       private JCTree.JCVariableDecl createField(final AnnotationValues<XXXLog> annotation, final JavacNode annotationNode, final JavacNode typeNode) {
           JavacTreeMaker maker = typeNode.getTreeMaker();
           Name name = ((JCTree.JCClassDecl) typeNode.get()).name;
           JCTree.JCFieldAccess loggingType = maker.Select(maker.Ident(name), typeNode.toName("class"));
   
           JCTree.JCExpression loggerType = JavacHandlerUtil.chainDotsString(typeNode, "com.xxx.xxx.Logger");
           JCTree.JCExpression factoryMethod = JavacHandlerUtil.chainDotsString(typeNode, "com.xxx.xxx.Logger.get");
   
           JCTree.JCExpression loggerName;
           String topic = annotation.getInstance().topic();
           if (topic == null || topic.trim().length() == 0) {
               loggerName = loggingType;
           } else {
               loggerName = maker.Literal(topic);
           }
           JCTree.JCMethodInvocation factoryMethodCall = maker.Apply(List.<JCTree.JCExpression>nil(), factoryMethod, loggerName != null ? List.of(loggerName) : List.<JCTree.JCExpression>nil());
           JCTree.JCVariableDecl fieldDecl = maker.VarDef(
                   maker.Modifiers(Flags.PRIVATE | Flags.FINAL | Flags.STATIC),
                   typeNode.toName(LOG_FIELD_NAME), loggerType, factoryMethodCall);
           return JavacHandlerUtil.recursiveSetGeneratedBy(fieldDecl , typeNode);
       }
   }
   
   ```

5. 执行`ant maven`，在 dist 目录下生成 lombok.jar

6. 将 jars 上传至私服，项目依赖使用

## IDEA 插件

1. `git clone https://github.com/mplushnikov/lombok-intellij-plugin.git`

2. 设置 JDK 版本为 9+

3. switch 到需要修改的分支，如 dependabot/gradle/org.projectlombok-lombok-1.18.22

4. gradle.properties 中修改IDEA版本 `ideaVersion=2021.3.1`

5. 执行`./gradlew build --info`查看是否报错

6. 若出现`Plugin [id: 'org.jetbrains.intellij', version: '0.7.3'] was not found in any of the following sources:`则将 settings.gradle 中的仓库顺序上下调整

   ```groovy
   pluginManagement {
     repositories {
       maven {
         url "https://plugins.gradle.org/m2/"
       }
       maven {
         url 'https://jetbrains.bintray.com/intellij-plugin-service'
       }
       mavenCentral()
     }
   }
   ```

7. 若出现`Could not resolve org.jetbrains.intellij.deps.jflex:jflex:1.7.0-1.`则将 build.gradle 中的 org.jetbrains.grammarkit 版本修改为 2021.1.3

   ```groovy
   plugins {
     id "org.jetbrains.intellij" version "0.7.3"
     id "org.jetbrains.grammarkit" version "2021.1.3"
     id "com.github.ManifestClasspath" version "0.1.0-RELEASE"
   }
   ```

8. 若 build 中的test 过程出错，需要在 build.gradle 的`sourceSets `上面设置

   ```groovy
   tasks.test {
       systemProperty("idea.force.use.core.classloader", "true")
   }
   ```

9. 新建 XXXLog 类

   ```java
   package de.plushnikov.intellij.plugin.processor.clazz.log;
   
   public class XXXLogProcessor extends AbstractTopicSupportingSimpleLogProcessor {
   
     private static final String LOGGER_ANNO = "lombok.extern.xxx.XXXLog";
     private static final String LOGGER_TYPE = "com.xxx.xxx.Logger";
     private static final String LOGGER_INITIALIZER = "com.xxx.xxx.Logger.get(%s)";
   
     public XXXLogProcessor() {
       super(LOGGER_ANNO, LOGGER_TYPE, LOGGER_INITIALIZER, LoggerInitializerParameter.TYPE);
     }
   }
   ```

10. 将新类加入如下类的配置中，代码中位置参照其他 Logger 类

    * DelombokEverythingAction.java
    * DelombokLoggerAction.java
    * LombokLoggerHandler.java
    * LombokProcessorManager.java
    * plugin.xml

11. 若执行`./gradlew build --info`成功，在 build/distributions 目录下找到编译好的插件

12. 到 IDEA 安装目录下的 plugins 目录移除 lombok 文件夹，让 lombok 变为非内置

13. IDEA 安装生成的插件

