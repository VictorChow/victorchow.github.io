---
layout: post
title: 编译期修改 Java AST - JCTree
date: 2022-03-21
categories: code
tags: Java AST
typora-root-url: ../../victorchow.github.io
---

> 编译期修改 AST，实现对类的增强

## JCTree

抽象语法树（Abstract Syntax Tree）语法分析分析出来的树结构是由 **JCTree** 来表示的，语法树的每一个节点都代表着程序代码中的一个语法结构，例如包、类型、修饰符、运算符、接口、返回值、代码注释等都是一个语法结构。

## JCTree 子类

### JCAnnotatedType

>  被注解的泛型：（注解的 Target 为`ElementType.TYPE_USE`时可注解泛型）

```java
public static class A <T extends @Reality String> {
}
```

* annotations (`List<JCTree.JCAnnotation>`) [0] = (`JCAnnotation`) @Realitys()
* underlyingType (`JCExpression`) = (`JCIdent`) String

```java
protected JCAnnotatedType(List<JCAnnotation> annotations, JCExpression underlyingType) {
    Assert.check(annotations != null && annotations.nonEmpty());
    this.annotations = annotations;
    this.underlyingType = underlyingType;
}
```

### JCAnnotation

> 注解：@annotationType(args) 

```java
@Reality(callSuper = false)
```

* annotationType (`JCTree`) = (`JCIdent`) Reality
* args (`List<JCExpression>`) = callSuper = false
* attribute (`Compound`) = @com.example.annotations.Reality(callSuper=false)

```java
protected JCAnnotation(Tag tag, JCTree annotationType, List<JCExpression> args) {
    this.tag = tag;
    this.annotationType = annotationType;
    this.args = args;
}
```

### JCArrayAccess

> 数组访问：`a[0]`

* index (`JCExpression`) = 0 (`JCLiteral`)
* indexed (`JCExpression`) = a (`JCIdent`)

```java
protected JCArrayAccess(JCExpression indexed, JCExpression index) {
    this.indexed = indexed;
    this.index = index;
}
```

### JCArrayTypeTree

>  数组类型

```java
String[]
```

* elemtype (`JCExpression`) = String (`JCIdent`)

```java
protected JCArrayTypeTree(JCExpression elemtype) {
    this.elemtype = elemtype;
}
```

### JCAssert

> assert 断言

```java
protected JCAssert(JCExpression cond, JCExpression detail) {
    this.cond = cond;
    this.detail = detail;
}
```

### JCAssign

> 赋值：`lhs = rhs` 例如 `i = 1`

```java
protected JCAssign(JCExpression lhs, JCExpression rhs) {
    this.lhs = lhs;
    this.rhs = rhs;
}
```

### JCAssignOp

> 赋值：`lhs 操作= rhs` 例如 `i += 1`

```java
protected JCAssignOp(Tag opcode, JCTree lhs, JCTree rhs, Symbol operator) {
    this.opcode = opcode;
    this.lhs = (JCExpression)lhs;
    this.rhs = (JCExpression)rhs;
    this.operator = operator;
}
```

操作符可选值：

```java
 BITOR_ASG(BITOR),                // |=
 BITXOR_ASG(BITXOR),              // ^=
 BITAND_ASG(BITAND),              // &=
 SL_ASG(SL),                      // <<=
 SR_ASG(SR),                      // >>=
 USR_ASG(USR),                    // >>>=
 PLUS_ASG(PLUS),                  // +=
 MINUS_ASG(MINUS),                // -=
 MUL_ASG(MUL),                    // *=
 DIV_ASG(DIV),                    // /=
 MOD_ASG(MOD),                    // %=
```

### JCBinary

> 二叉结构的语句

```java
protected JCBinary(Tag opcode, JCExpression lhs, JCExpression rhs, Symbol operator) {
    this.opcode = opcode;
    this.lhs = lhs;
    this.rhs = rhs;
    this.operator = operator;
}
```

```java
double i = d + i * f
```

第一个 JCBinary 为：

opcode = `PLUS`, lhs = `d`, rhs = `i * f`, operator = `null`

第二个 JCBinary 为：

opcode = `MUL`, lhs = `i`, rhs = `f`, operator = `null`

操作符可选值：

```java
OR,                              // ||
AND,                             // &&
BITOR,                           // |
BITXOR,                          // ^
BITAND,                          // &
EQ,                              // ==
NE,                              // !=
LT,                              // <
GT,                              // >
LE,                              // <=
GE,                              // >=
SL,                              // <<
SR,                              // >>
USR,                             // >>>
PLUS,                            // +
MINUS,                           // -
MUL,                             // *
DIV,                             // /
MOD,                             // %s
```

### JCBlock

> 语句块

```java
{
    ...
}
//或
static {
    ...
}
```

```java
protected JCBlock(long flags, List<JCStatement> stats) {
    this.stats = stats;
    this.flags = flags;
}
```

### JCCase

> case语句：`case pat : stats`

```java
protected JCCase(JCExpression pat, List<JCStatement> stats) {
    this.pat = pat;
    this.stats = stats;
}
```

### JCCatch

> catch捕捉异常：`catch(param) body`s

* param 定义异常变量 `Exception e`
* body 代码块

```java
protected JCCatch(JCVariableDecl param, JCBlock body) {
    this.param = param;
    this.body = body;
}
```

### JCClassDecl

> 类的定义

```java
@Reality
public class MainActivity extends AppCompatActivity {

    private static int kk = 1;

    static {
        kk = 2;
    }

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }
}
```

* mods (`JCModifiers`) = public

* name (`Name`) = (`NameImpl`) MainActivity

* extending (`JCExpression`) = (`JCIdent`) AppCompatActivity

* implementing (`JCExpression`) 实现的接口

* defs (`List<JCTree>`) ：

  - defs(1) : `JCMethodDecl`

    ```java
    public <init>() {
        super();
    }
    ```

  - defs(2) : `JCBlock`

    ```java
    static {
        kk = 2;
    }
    ```

  - defs(3) : `JCVariableDecl`

    ```java
    private static int kk = 1
    ```

  - defs(4) : `JCMethodDecl`

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }
    ```
  
* sym (`ClassSymbol`) = com.example.MainActivity


```java
protected JCClassDecl(JCModifiers mods,
                   Name name,
                   List<JCTypeParameter> typarams,
                   JCExpression extending,
                   List<JCExpression> implementing,
                   List<JCTree> defs,
                   ClassSymbol sym)
{
    this.mods = mods;
    this.name = name;
    this.typarams = typarams;
    this.extending = extending;
    this.implementing = implementing;
    this.defs = defs;
    this.sym = sym;
}
```

### JCCompilationUnit

```java
protected JCCompilationUnit(List<JCAnnotation> packageAnnotations,
                JCExpression pid,
                List<JCTree> defs,
                JavaFileObject sourcefile,
                PackageSymbol packge,
                ImportScope namedImportScope,
                StarImportScope starImportScope) {
    this.packageAnnotations = packageAnnotations;
    this.pid = pid;
    this.defs = defs;
    this.sourcefile = sourcefile;
    this.packge = packge;
    this.namedImportScope = namedImportScope;
    this.starImportScope = starImportScope;
}
```

### JCConditional

> 三目运算符：`cond ? truepart : falsepart` 例如 `a ? 1 : 3`

```java
 protected JCConditional(JCExpression cond, JCExpression truepart, JCExpression falsepart)
 {
     this.cond = cond;
     this.truepart = truepart;
     this.falsepart = falsepart;
 }
```

### JCContinue

> continue跳过一次循环，`continue label`

```java
protected JCContinue(Name label, JCTree target) {
    this.label = label;
    this.target = target;
}
```

### JCDoWhileLoop

> do-while循环

```java
do {
    body
} while (cond)
```

```java
protected JCDoWhileLoop(JCStatement body, JCExpression cond) {
    this.body = body;
    this.cond = cond;
}
```

### JCEnhancedForLoop

>增强循环

````java
for (String s : list) {
    System.out.println(s);
}
````

* `JCVariableDecl` String s 定义字符串变量s
* `JCIdent` list 遍历的容器
* `JCBlock` { print(s); } 遍历块

```java
protected JCEnhancedForLoop(JCVariableDecl var, JCExpression expr, JCStatement body) {
    this.var = var;
    this.expr = expr;
    this.body = body;
}
```

### JCExpression

>  表达式，赋值、调用方法等都算一个表达式，凡是继承 **JCExpression** 都算一个表达式

### JCExpressionStatement

> 内部封装了 **JCExpression**，实际上还是个表达式

### JCFieldAccess

访问父类、其他类的变量时

* selected (`JCIdent`) = this
* name (`NameImpl`) = age

```java
this.age
```

### JCForloop

> for 循环

* `for (init; cond; step) {body}`（body 为 **JCBlock**）
* `for (init; cond; step) body`（body 为 **JCExpressionStatement**）

```java
protected JCForLoop(List<JCStatement> init,
                  JCExpression cond,
                  List<JCExpressionStatement> update,
                  JCStatement body)
{
    this.init = init;
    this.cond = cond;
    this.step = update;
    this.body = body;
}
```

### JCFunctionalExpression

>函数式表达式：lambda(JCLambda) 和 method reference(`JCMemberReference`) 继承于`JCFunctionalExpression`

### JCIdent

> 出现在表达式中的变量名、调用的方法名以及类名等

* `Reality` 类（注解）
* `AppCompatActivity `类
* `String` 类
* `Override` 类
* `Bundle` 类
* `super` 和 `this` 都是一个 `JcIdent`
* `savedInstanceState` 调用变量的名字
* `setContentView `方法
* `R` 类
* `textView` 调用变量的名字
* `findViewById` 方法

```java
@Reality //JCIdent Reality ClassSymbol com.juno.annotations.Reality
//JCIdent AppCompatActivity ClassSymbol android.support.v7.app.AppCompatActivity
public class MainActivity extends AppCompatActivity  {

    //JCIdent String ClassSymbol java.lang.String
    private static final String TAG = "MainActivity";
    
    private int kk = 1;

    @Override //JCIdent Override ClassSymbol java.lang.Override
    //JCIdent Bundle ClassSymbol android.os.Bundle
    protected void onCreate(Bundle savedInstanceState) {
    
        //JCIdent super
        //JCIdent savedInstanceState
        super.onCreate(savedInstanceState);
        
        //JCIdent setContentView 
        //JCIdent R
        setContentView(R.layout.activity_main);
        
        TextView textView;
        //JCIdent textView
        //JCIdent findViewById
        //JCIdent R
        textView = findViewById(R.id.text);

        int i = 0;
        int j = 1;
        
        //JCIdent i
        //JCIdent j
        i = j * 3 + 2;
        
        //JCIdent this
        this.kk = 0;
    }
}
```

### JCIf

> if 判断语句：`if(condition) {thenpart} else {elsepart}`

```java
protected JCIf(JCExpression cond, JCStatement thenpart, JCStatement elsepart)
{
    this.cond = cond;
    this.thenpart = thenpart;
    this.elsepart = elsepart;
}
```

### JCImport

> import 语句

* import qulid
* static (if staticImport) import qulid

```java
protected JCImport(JCTree qualid, boolean importStatic) {
    this.qualid = qualid;
    this.staticImport = importStatic;
}
```

### JCInstanceOf

> instanceof语句：`expr instanceof clazz`

```java
textView instanceof View
```

* clazz (`JCTree`) = (`JCIdent`) View
* expr (`JCExpression`) = (`JCIdent`) textView

```java
protected JCInstanceOf(JCExpression expr, JCTree clazz) {
    this.expr = expr;
    this.clazz = clazz;
}
```

### JCLabeledStatement

> 带有标签的语句：`label : body`

```java
protected JCLabeledStatement(Name label, JCStatement body) {
    this.label = label;
    this.body = body;
}
```

### JCLambda

> lambda 表达式：表达式主体只有一句表达式

```java
v -> v.setVisibility(View.INVISIBLE)
```

* params (`List<JCTree.JCVariableDecl>`) = (`List`) /*missing*/ v （参数定义列表）
* body (`JCTree`) = (`JCMethodInvocation`) v.setVisibility(View.INVISIBLE) （主体）
* canCompleteNormally (`Boolean`) = true （未知）
* paramKind (`ParameterKind`) = IMPLICIT （参数类型隐形/明确）
* getBodyKind() (`BodyKind`) = EXPRESSION （主体是表达式还是块）

当主体变为语句块时：

```java
v -> { v.setVisibility(View.VISIBLE); }
```

* body (`JCTree`) = (`JCBlock`) { v.setVisibility(View.VISIBLE); }
* getBodyKind() (`BodyKind`) = STATEMENT

当参数 v 指明类型为 **View** 时：

```java
(View v) -> v.setVisibility(View.INVISIBLE)
```

* paramKind (`ParameterKind`s) = IMPLICIT （参数类型明确）

### JCLiteral

> 表示常量

```java
int a = 1
```

* typetag (`TypeTag`) = INT
* value (`Object`) = (`Integer`) 1

```java
protected JCLiteral(TypeTag typetag, Object value) {
    this.typetag = typetag;
    this.value = value;
}
```

Tag 可选值有

```java
INT,                      //int
LONG,                     //long
FLOAT,                    //float
DOUBLE,                   //double
CHAR,                     //char
CLASS,                    //String
BOT,                      //null
```

### JCMemberReference

> lambda 表达式中的方法引用：`expr::name`

```java
ObjectUtil::new
```

* mode (`ReferenceMode`) = NEW
* name (`Name`) = (`NameImpl`) <init>
* expr (`JCExpression`) = (`JCIdent`) ObjectUtil

```java
this::util
```

* mode (`ReferenceMode`) = INVOKE
* name (`Name`) = (NameImpl) util
* expr (`JCExpression`) = (`JCIdent`) this

```java
protected JCMemberReference(ReferenceMode mode, 
                            Name name, 
                            JCExpression expr, 
                            List<JCExpression> typeargs) {
    this.mode = mode;
    this.name = name;
    this.expr = expr;
    this.typeargs = typeargs;
}
```

### JCMethodDecl

> 方法的定义

```java
@Reality
private static <T extends String> int print(@NonNull final String s) throws RuntimeException {
    Log.e(TAG, s);
    return 1;
}
```

* mods (`JCModifiers`) = @Reality() private static
* name (`Name`) = (`NameImpl`) print
* restype (`JCExpression`) = (`JCPrimitiveTypeTree`) int
* typarams (`JCTypeParameter`) = T extends String
* recvparam = null
* params (`List<JCVariableDecl>`) [0] = @NonNull() final String s
* thrown (`JCExpression`) = (JCIdent) RuntimeException
* body (`JCBlock`) = { Log.e(TAG, s); return 1; }
* defaultVaule (`JCExpression`) = null
* sym (`MethodSymbol`) = <T> print(java.lang.String)

```java
@Reality
@interface B {
    int value() default 1;
}
```

* name (`Name`) = (`NameImpl`) value
* restype (`JCExpression`) = (`JCPrimitiveTypeTree`) int
* typarams (`JCTypeParameter`) 泛型列表为空
* recvparam = null
* params (`List<JCVariableDecl>`) 参数定义为空
* thrown (`JCExpression`) 抛出列表为空
* body (`JCBlock`) 方法体为空
* defaultVaule (`JCExpression`) = (`JCLiteral`) 1
* sym (`MethodSymbol`) = value()

```java
protected JCMethodDecl(JCModifiers mods,
                    Name name,
                    JCExpression restype,
                    List<JCTypeParameter> typarams,
                    JCVariableDecl recvparam,
                    List<JCVariableDecl> params,
                    List<JCExpression> thrown,
                    JCBlock body,
                    JCExpression defaultValue,
                    MethodSymbol sym)
{
    this.mods = mods;
    this.name = name;
    this.restype = restype;
    this.typarams = typarams;
    this.params = params;
    this.recvparam = recvparam;
    // TODO: do something special if the given type is null?
    // receiver != null ? receiver : List.<JCTypeAnnotation>nil());
    this.thrown = thrown;
    this.body = body;
    this.defaultValue = defaultValue;
    this.sym = sym;
}
```

### JCMethodinvocation

>方法执行语句

```java
notNull(before)
```

* meth (`JCExpression`) = (`JCIdent`) notNull
* args (`List<JCExpression>`) = (`JCIdent`) before

```java
proxy.invoke(target, method, args)
```

* meth (`JCExpression`) = (`JCFieldAccess`) proxy.invoke
* args (`List<JCExpression>`) = (`JCIdent`) target, method, args

```java
protected JCMethodInvocation(List<JCExpression> typeargs,
                JCExpression meth,
                List<JCExpression> args) {
    this.typeargs = (typeargs == null) ? List.<JCExpression>nil() : typeargs;
    this.meth = meth;
    this.args = args;
}
```

### JCModifiers

> 类、变量、方法等的修饰符和注解

```java
@Reality private static final String TAG = "MainActivity";
```

* flags (`long`) = 26L （2L | 8L | 16L）
* annotations (`List<JCTree.JCAnnotation>`) = @Reality()

```java
protected JCModifiers(long flags, List<JCAnnotation> annotations) {
    this.flags = flags;
    this.annotations = annotations;
}
```

flag 的可选值，位于 `Flags` 类中

```java
int PUBLIC       = 1;
int PRIVATE      = 1<<1;
int PROTECTED    = 1<<2;
int STATIC       = 1<<3;
int FINAL        = 1<<4;
int SYNCHRONIZED = 1<<5;
int VOLATILE     = 1<<6;
int TRANSIENT    = 1<<7;
int NATIVE       = 1<<8;
int INTERFACE    = 1<<9;
int ABSTRACT     = 1<<10;
int STRICTFP     = 1<<11;
```

### JCNewArray

>new 数组

```java
new String[]{"123","456"}
```

* elemtype (`JCExpression`) = (`JCIdent`) String
* dims (`List<JCTree.JCExpression>`) 空
* elems (`List<JCTree.JCExpression>`) [0] = (`JCLiteral`) "123"
* elems (`List<JCTree.JCExpression>`) [1] = (`JCLiteral`) "456"

```java
new B[3][4]
```

* elemtype (`JCExpression`) = (`JCIdent`) B
* dims (`List<JCTree.JCExpression>`) [0] = (`JCLiteral`) 3
* dims (`List<JCTree.JCExpression>`) [1] = (JCLiteral) 4
* elems (`List<JCTree.JCExpression>`) 空

```java
protected JCNewArray(JCExpression elemtype, List<JCExpression> dims, List<JCExpression> elems) {
    this.elemtype = elemtype;
    this.dims = dims;
    this.annotations = List.nil();
    this.dimAnnotations = List.nil();
    this.elems = elems;
}
```

### JCNewClass

> new 一个对象

```java
new B<String>("123")
```

* clazz (`JCExpression`) = (`JCTypeApply`) B<String>
* args (`List<JCTree.JCExpression>`) [0] = (`JCLiteral`) "123"

```java
protected JCNewClass(JCExpression encl,
                   List<JCExpression> typeargs,
                   JCExpression clazz,
                   List<JCExpression> args,
                   JCClassDecl def) {
    this.encl = encl;
    this.typeargs = (typeargs == null) ? List.<JCExpression>nil() : typeargs;
    this.clazz = clazz;
    this.args = args;
    this.def = def;
}
```

### JCParens

> 括号：(expr) 存在于if、计算式、synchronized中

```java
protected JCParens(JCExpression expr) {
    this.expr = expr;
}
```

### JCPrimitiveTypeTree

> 基本类型

TypeTag 可选值

```java
INT,
LONG,
FLOAT,
DOUBLE,
DOOLEAN,
CHAR,
BYTE,
SHORT,
VOID,
```

### JCReturn

> return语句：return expr

```java
protected JCReturn(JCExpression expr) {
    this.expr = expr;
}
```

### JCSkip

>空操作，即一个无效的分号 ";"

### JCStatement

> 声明，凡是继承`JCStatement`都是一个声明，在`JCBlock`中拿到的都是`JCStatement`，想在`JCBlock`中拿到`JCExpression`就用`JCExpressionStatement`

### JCSWitch

> switch 语句：switch(selector) { cases }

```java
protected JCSwitch(JCExpression selector, List<JCCase> cases) {
    this.selector = selector;
    this.cases = cases;
}
```

### JCSynchronized

> synchronized同步锁：synchronized( lock ) { block }

```java
protected JCSynchronized(JCExpression lock, JCBlock body) {
    this.lock = lock;
    this.body = body;
}
```

### JCThrow

> 抛出异常：throw expr

```java
protected JCThrow(JCExpression expr) {
    this.expr = expr;
}
```

### JCTry

> try块：try body catchers finally finalizer

```java
protected JCTry(List<JCTree> resources, JCBlock body, List<JCCatch> catchers, JCBlock finalizer) {
    this.body = body;
    this.catchers = catchers;
    this.finalizer = finalizer;
    this.resources = resources;
}
```

### JCTypeApply

> 泛型参数

```java
List<String> list = new ArrayList<>()
```

对于`List<String> list`：

* clazz (`JCExpression`) = (`JCIdent`) List
* arguments (`List<JCTree.JCExpression>`) [0] = (`JCIdent`) String

对于`new ArrayList<>()`：

* clazz (`JCExpression`) = (`JCIdent`) ArrayList
* arguments (`List<JCTree.JCExpression>`) = empty

```java
protected JCTypeApply(JCExpression clazz, List<JCExpression> arguments) {
    this.clazz = clazz;
    this.arguments = arguments;
}
```

### JCTypeCast

> 类型转换：(clazz)expr

```java
((TextView) findViewById(R.id.text))
```

* clazz (`JCTree`) = (`JCIdent`) TextView
* expr (`JCExpression`) = (`JCMethodInvocation`) findViewById(R.id.text)

```java
(TextView) textView
```

* clazz (`JCTree`) = (`JCIdent`) TextView
* expr (`JCExpression`) = (`JCIdent`) textView

```java
protected JCTypeCast(JCTree clazz, JCExpression expr) {
    this.clazz = clazz;
    this.expr = expr;
}
```

### JCTypeIntersection

泛型交叉

```java
public static class A<T extends String & Runnable> {
}
```

```java
protected JCTypeIntersection(List<JCExpression> bounds) {
    this.bounds = bounds;
}
```

### JCTypeParameter

> 类的泛型定义：class<annotations name bounds>

```java
<T extends View>
```

* name (`Name`) = (`NameImpl`) T
* bounds (`List<JCTree.JCExpression>`) [0] = (`JCIdent`) View

```java
protected JCTypeParameter(Name name, List<JCExpression> bounds, List<JCAnnotation> annotations) {
    this.name = name;
    this.bounds = bounds;
    this.annotations = annotations;
}
```

### JCTypeUnion

>  catch 块中异常的或定义：T1 | T2 | ... Tn

```java
try {
    ...
} catch (ClassCastException | ArrayIndexOutOfBoundsException e) {
    ...
}
```

* alternatives (`List<JCTree.JCExpression`>) [0] = (`JCIdent`) ClassCastException
* alternatives (`List<JCTree.JCExpression`>) [1] = (`JCIdent`) ArrayIndexOutOfBoundsException

```java
protected JCTypeUnion(List<JCExpression> components) {
    this.alternatives = components;
}
```

### JCUnary

> 一元运算符，例如 i++

* opcode = POSTINC
* arg = i （JCIdent）

```java
protected JCUnary(Tag opcode, JCExpression arg) {
    this.opcode = opcode;
    this.arg = arg;
}
```

Tag 的可选值

```java
POS,                             // +
NEG,                             // -
NOT,                             // !
COMPL,                           // ~
PREINC,                          // ++ _
PREDEC,                          // -- _
POSTINC,                         // _ ++
POSTDEC,                         // _ --
```

### JCVariableDecl

> 定义变量

```java
final TextView textView = findViewById(R.id.text)
```

* mods (`JCModifiers`) = final
* name (`Name`) = (`NameImpl`) textView
* vartype (`JCExpression`) = (`JCIdent`) TextView
* init (`JCExpression`) = (`JCMethodInvocation`) findViewById(R.id.text)

```java
double i = 1 + 2 * 3
```

* name (`Name`) = (`NameImpl`) i
* vartype (`JCExpression`) = （`JCPrimitiveTypeTree`) double
* init (`JCExpression`) = (`JCBinary`) 1 + 2 * 3

```java
int i = cond ? 0 : 1
```

* name (`Name`) = (`NameImpl`) i
* init (`JCExpression`) = (`JCConditional`) cond ? 0 : 1

```java
protected JCVariableDecl(JCModifiers mods,
                 Name name,
                 JCExpression vartype,
                 JCExpression init,
                 VarSymbol sym) {
    this.mods = mods;
    this.name = name;
    this.vartype = vartype;
    this.init = init;
    this.sym = sym;
}
```

### JCWhileLoop

>while循环：while(cond){body}

```java
protected JCWhileLoop(JCExpression cond, JCStatement body) {
    this.cond = cond;
    this.body = body;
}
```

### JCWildcard

> 泛型中的"?"通配符：`? super String`

* kind (`TypeBoundKind`) = ? super
* inner (`JCTree`) = (`JCIdent`) String

```java
protected JCWildcard(TypeBoundKind kind, JCTree inner) {
    kind.getClass(); // null-check
    this.kind = kind;
    this.inner = inner;
}
```

BoundKind 可选值 

```java
EXTENDS("? extends "),
SUPER("? super "),
UNBOUND("?");
```

## 原文

[Java AST JCTree简要分析](https://blog.51cto.com/u_15127683/3682847)
