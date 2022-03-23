---
layout: post
title: 编译期修改 Java AST - TreeMaker
date: 2022-03-23
categories: code
tags: Java AST
typora-root-url: ../../victorchow.github.io
---

> 编译期修改 AST，实现对类的增强

## TreeMaker

TreeMaker 用于创建一系列的语法树节点，创建 JCTree 不能直接使用 new 来创建，Java 为我们提供了一个工具类 TreeMaker，它会在创建时为我们创建的 JCTree 对象设置 pos 字段，所以必须使用上下文相关的 TreeMaker 对象来创建语法树节点。

## TreeMaker 常用方法

### Modifiers

> 用于创建`访问标志`语法树节点（JCModifiers）

* flags：访问标志
* annotations：注解列表

```java
public JCModifiers Modifiers(long flags, List<JCAnnotation> annotations) {
    JCModifiers tree = new JCModifiers(flags, annotations);
    boolean noFlags = (flags & (Flags.ModifierFlags | Flags.ANNOTATION)) == 0;
    tree.pos = (noFlags && annotations.isEmpty()) ? Position.NOPOS : pos;
    return tree;
}

public JCModifiers Modifiers(long flags) {
    return Modifiers(flags, List.<JCAnnotation>nil());
}
```

入参的`flags`使用`com.sun.tools.javac.code.Flags`中的常量值，例如：

```java
private static final
Flags.PRIVATE | Flags.STATIC | Flags.FINALs
```

### ClassDef

> 用于创建`类定义`语法树节点（JCClassDecl）

* mods：访问标志
* name：类名
* typarams：泛型参数列表
* extending：父类
* implementing：接口列表
* defs：类定义的语句，包括字段，方法定义等

```java
public JCClassDecl ClassDef(JCModifiers mods,
                            Name name,
                            List<JCTypeParameter> typarams,
                            JCExpression extending,
                            List<JCExpression> implementing,
                            List<JCTree> defs) {
    JCClassDecl tree = new JCClassDecl(mods,
                                 name,
                                 typarams,
                                 extending,
                                 implementing,
                                 defs,
                                 null);
    tree.pos = pos;
    return tree;
}
```

### MethodDef

>用于创建`方法定义`语法树节点（JCMethodDecl）

* mods：访问标志

* name：方法名
* restype：返回类型
* typarams：泛型参数列表
* params：参数列表
* thrown：异常声明列表
* body：方法体
* defaultValue：默认方法（可能是interface中的那个default）
* m：方法符号
* mtype：方法类型。包含多种类型，泛型参数类型、方法参数类型，异常参数类型、返回参数类型

```java
public JCMethodDecl MethodDef(JCModifiers mods,
                           Name name,
                           JCExpression restype,
                           List<JCTypeParameter> typarams,
                           List<JCVariableDecl> params,
                           List<JCExpression> thrown,
                           JCBlock body,
                           JCExpression defaultValue) {
    return MethodDef(
            mods, name, restype, typarams, null, params,
            thrown, body, defaultValue);
}

public JCMethodDecl MethodDef(JCModifiers mods,
                           Name name,
                           JCExpression restype,
                           List<JCTypeParameter> typarams,
                           JCVariableDecl recvparam,
                           List<JCVariableDecl> params,
                           List<JCExpression> thrown,
                           JCBlock body,
                           JCExpression defaultValue) {
    JCMethodDecl tree = new JCMethodDecl(mods,
                                   name,
                                   restype,
                                   typarams,
                                   recvparam,
                                   params,
                                   thrown,
                                   body,
                                   defaultValue,
                                   null);
    tree.pos = pos;
    return tree;
}

public JCMethodDecl MethodDef(MethodSymbol m, JCBlock body) {
    return MethodDef(m, m.type, body);
}

public JCMethodDecl MethodDef(MethodSymbol m, Type mtype, JCBlock body) {
    return (JCMethodDecl)
        new JCMethodDecl(
            Modifiers(m.flags(), Annotations(m.getRawAttributes())),
            m.name,
            Type(mtype.getReturnType()),
            TypeParams(mtype.getTypeArguments()),
            null, // receiver type
            Params(mtype.getParameterTypes(), m),
            Types(mtype.getThrownTypes()),
            body,
            null,
            m).setPos(pos).setType(mtype);
}
```

### VarDef

> 用于创建`字段/变量定义`语法树节点（JCVariableDecl）

* mods：访问标志
* vartype：类型
* init：初始化语句
* v：变量符号

```java
public JCVariableDecl VarDef(JCModifiers mods, Name name, JCExpression vartype, JCExpression init) {
    JCVariableDecl tree = new JCVariableDecl(mods, name, vartype, init, null);
    tree.pos = pos;
    return tree;
}

public JCVariableDecl VarDef(VarSymbol v, JCExpression init) {
    return (JCVariableDecl)
        new JCVariableDecl(
            Modifiers(v.flags(), Annotations(v.getRawAttributes())),
            v.name,
            Type(v.type),
            init,
            v).setPos(pos).setType(v.type);
}
```

### Ident

> 用于创建`标识符`语法树节点（JCIdent）

```java
public JCExpression Ident(JCVariableDecl param) {
    return Ident(param.sym);
}

public JCIdent Ident(Name name) {
    JCIdent tree = new JCIdent(name, null);
    tree.pos = pos;
    return tree;
}

public JCIdent Ident(Symbol sym) {
    return (JCIdent)new JCIdent((sym.name != names.empty)
                            ? sym.name
                            : sym.flatName(), sym)
        .setPos(pos)
        .setType(sym.type);
}
```

### Return

>用于创建`return语句`语法树节点（JCReturn）

```java
public JCReturn Return(JCExpression expr) {
    JCReturn tree = new JCReturn(expr);
    tree.pos = pos;
    return tree;
}
```

### Select

> 用于创建`域访问/方法访问`（这里的方法访问只是取到名字，方法的调用需要用`TreeMaker.Apply`）语法树节点（JCFieldAccess）

* selected：`.`运算符左边的表达式
* selector：`.`运算符右边的名字

```java
public JCFieldAccess Select(JCExpression selected, Name selector) {
    JCFieldAccess tree = new JCFieldAccess(selected, selector, null);
    tree.pos = pos;
    return tree;
}

public JCExpression Select(JCExpression base, Symbol sym) {
    return new JCFieldAccess(base, sym.name, sym).setPos(pos).setType(sym.type);
}
```

### NewClass

> 用于创建`new XXX()`语法树节点（JCNewClass）

* encl：不太明白此参数含义，一般都为null
* typeargs：参数类型列表
* clazz：待创建对象的类型
* args：参数列表
* def：类定义

```java
public JCNewClass NewClass(JCExpression encl,
                         List<JCExpression> typeargs,
                         JCExpression clazz,
                         List<JCExpression> args,
                         JCClassDecl def) {
    JCNewClass tree = new JCNewClass(encl, typeargs, clazz, args, def);
    tree.pos = pos;
    return tree;
}
```

### Apply

>用于创建`方法调用`语法树节点（JCMethodInvocation）

* typeargs：参数类型列表
* fn：调用语句
* args：参数列表

```java
public JCMethodInvocation Apply(List<JCExpression> typeargs,
                   JCExpression fn,
                   List<JCExpression> args) {
    JCMethodInvocation tree = new JCMethodInvocation(typeargs, fn, args);
    tree.pos = pos;
    return tree;
}
```

### Assign

> 用于创建`赋值语句`语法树节点（JCAssign）

* lhs：赋值语句左边表达式
* rhs：赋值语句右边表达式

```java
public JCAssign Assign(JCExpression lhs, JCExpression rhs) {
    JCAssign tree = new JCAssign(lhs, rhs);
    tree.pos = pos;
    return tree;
}
```

### Exec

>用于创建`可执行语句`语法树节点（JCExpressionStatement）

```java
public JCExpressionStatement Exec(JCExpression expr) {
    JCExpressionStatement tree = new JCExpressionStatement(expr);
    tree.pos = pos;
    return tree;
}
```

**TreeMaker.Apply、TreeMaker.Assign 就需要外面包一层 TreeMaker.Exec 来获得一个 JCExpressionStatement**

### Block

> 用于创建`组合语句`语法树节点（JCBlock）

* flags：访问标志
* stats：语句列表

```java
public JCBlock Block(long flags, List<JCStatement> stats) {
    JCBlock tree = new JCBlock(flags, stats);
    tree.pos = pos;
    return tree;
}
```

## 其他常用类 

### com.sun.tools.javac.util.List

JSR-269 API 中会涉及到一个 List，这个 List 不是 java.util.List，它是 com.sun.tools.javac.util.List。List 包含两个字段，head 和 tail，其中 head 只是一个节点，而 tail 是一个 List

```java
public class List<A> extends AbstractCollection<A> implements java.util.List<A> {
    public A head;
    public List<A> tail;
    private static final List<?> EMPTY_LIST = new List<Object>((Object)null, (List)null) {
        public List<Object> setTail(List<Object> var1) {
            throw new UnsupportedOperationException();
        }

        public boolean isEmpty() {
            return true;
        }
    };

    List(A head, List<A> tail) {
        this.tail = tail;
        this.head = head;
    }

    public static <A> List<A> nil() {
        return EMPTY_LIST;
    }

    public List<A> prepend(A var1) {
        return new List(var1, this);
    }

    public List<A> append(A var1) {
        return of(var1).prependList(this);
    }

    public static <A> List<A> of(A var0) {
        return new List(var0, nil());
    }

    public static <A> List<A> of(A var0, A var1) {
        return new List(var0, of(var1));
    }

    public static <A> List<A> of(A var0, A var1, A var2) {
        return new List(var0, of(var1, var2));
    }

    public static <A> List<A> of(A var0, A var1, A var2, A... var3) {
        return new List(var0, new List(var1, new List(var2, from(var3))));
    }

    ...
}
```

### com.sun.tools.javac.util.ListBuffer

由于 com.sun.tools.javac.util.List 用起来不是很方便，而 ListBuffer 的行为与 java.util.List 的行为类似，并且提供了转换成 com.sun.tools.javac.util.List 的方法

```java
ListBuffer<JCTree.JCStatement> jcStatements = new ListBuffer<>();

//添加语句 " this.xxx = xxx; "
jcStatements.append(...);

//添加Builder模式中的返回语句 " return this; "
jcStatements.append(...);

List<JCTree.JCStatement> lst = jcStatements.toList();
```

## 参考

[Java-JSR-269-插入式注解处理器](https://liuyehcf.github.io/2018/02/02/Java-JSR-269-%E6%8F%92%E5%85%A5%E5%BC%8F%E6%B3%A8%E8%A7%A3%E5%A4%84%E7%90%86%E5%99%A8/)
