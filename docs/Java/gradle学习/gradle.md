# Gradle入门

## 前言

Gradle是一个灵活和高效自动化构建工具，它的构建脚本采用Groovy或kotlin语言编写，**Groovy或Kotlin都是基于JVM的语言**。

[Gradle官网](https://docs.gradle.org/current/userguide/userguide.html)

[Github地址](https://github.com/gradle/gradle)

## Gradle的特点

1. **Gradle构建脚本采用Groovy或Kotlin语言编写，如果采用Groovy编写，构建脚本后缀为.gradle**，在里面可以使用Groovy语法，如果采用Kotlin编写，构建脚本后缀为.gradle.kts，在里面可以使用Kotlin语法；

2. 因为Groovy或Kotlin都是**面向对象语言**，所以在Gradle中处处皆对象**，Gradle的.gradle或.gradle.kts脚本本质上是一个Project对象**，在脚本中一**些带名字的配置项如buildscript、allprojects等本质上就是对象中的方法**，而配置项后面的闭包{}就是参数，所以我们在使用这个配置项时本质上是在调用对象中的一个方法；

3. 在Groovy或Kotlin中，**函数和类一样都是一等公民，它们都提供了很好的闭包{}支持**，所以它们很容易的编写出具有[DSL](https://zh.wikipedia.org/wiki/领域特定语言)风格的代码，用DSL编写构建脚本的Gradle比其他采用xml编写构建脚本的构建工具如maven、Ant等的可读性更强，动态性更好，整体更简洁；

4. Gradle中**主要有Project和Task对象，Project是Gradle中构建脚本的表示**，一个构建脚本对应一个Project对象，**Task是Gradle中最小的执行单元，它表示一个独立的任务，Project为Task提供了执行的上下文**。




## Groovy基础入门

本文的所有示例都是采用Groovy语言编写，在阅读本文前先简单的入门Groovy：

[Groovy 使用完全解析](https://blog.csdn.net/zhaoyanjun6/article/details/70313790)

下面主要讲Groovy与java的主要区别：

1、Groovy语句后面的分号可以忽略

```groovy
int num1 = 1
int num2 = 2
int result = add(num1, num2)
int add(int num1, int num2) {
  return a+b
}
```

2、Groovy支持动态类型推导，**使用def来定义变量和方法时可以不指定变量的类型和方法的返回值类型**，同时定义方法时参数可以不指定类型

```groovy
def num1 = 1
def num2 = 2
def result = add(num1, num2)
def add(a, b) {
  return a+b
}
```

3、Groovy的方法**调用传参时可以不添加括号，方法不指定return语句时，最后一行默认为返回值**

```groovy
def result = add 1, 2
def add(a, b) {
  a+b
}
```

4、Groovy可以用单、双、三引号来表示字符串，其中单引号表示普通字符串，双引号表示的字符串可以使用取值运算符`${}`，而`$`在单引号只只是表示一个普通的字符，三引号表示的字符串又称为模版字符串，它可以保留文本的换行和缩进格式，三引号同样不支持`$`

```groovy
def world = 'world'
def str1 = 'hello ${world}'
def str2 = "hello ${world}"
def str3 = '''hello
&{world}
'''

//打印输出：
//hello ${world}
//hello world
//hello
//&{world}
```

5、Groovy会为类中每个没有可见性修饰符的字段生成get/set方法，我们访问这个字段其实是调用它的get/set方法，同时如果类中的方法以get/set开头，我们也可以像普通字段一样访问这个方法

```groovy
class Person {
  def name
  private def  country
  
  def setNation(nation) {
    this.country = nation
  }
  def getNation() {
    return nation
  }
}

def person = new Person()
// 访问字段
person.name = 'rain9155'
println person.name
// 像字段一样访问这个以get/set开头的方法
person.nation = "china"
println person.nation
```

6、Groovy中的闭**包是用{参数列表 -> 代码体}表示的代码块**，当参数 <= 1个时，-> 箭头可以省略，同时当只有一个参数时，如果不指定参数名默认以**it**为参数名，在Groovy中闭包对应的类型是[Closure](https://groovy-lang.org/closures.html)，所以闭包可以作为参数传递，同时闭包有一个delegate字段,  通过delegate可以把闭包中的执行代码委托给任意对象来执行

```groovy
class Person {
	def name
	def age
}
def person = new Person()
//定义一个闭包
def closure = {
  name = 'rain9155'
  age = 21
}
// 把闭包委托给person对象执行，类似与指针赋值
closure.delegate = person
//执行闭包，或者调用closure.call()也可以执行闭包
closure()
//以上代码把闭包委托给person对象执行，所以闭包就执行时就处于person对象上下文
//所以闭包就可以访问到person对象中的name和age字段，完成赋值
println person.name // 输出：rain9155
println person.age // 输出：21
```

在Gradle中很多地方都使用**了闭包的委托机制，通过闭包完成一些特定对象的配置，在Gradle中，如果你没有指定闭包的delegate，delegate默认为当前项目的Project对象**。

以上Groovy知识，认为对于有java基础的人来说，用于学习Gradle足够了，当然对于一些集合操作、文件操作等，等以后使用到时可以到[Groovy官网](http://www.groovy-lang.org/single-page-documentation.html)来查漏补缺。



## Gradle的安装配置

官方教程：[Installing Gradle](https://gradle.org/install/)

安装Gradle前需要确保你的电脑已经配置好JDK，JDK的版本要求是8或更高，可以通过包管理器自动安装Gradle或手动下载Gradle安装两种方式，在Window平台下我推荐使用手动安装，在Mac平台下我推荐使用Homebrew包管理器自动安装：

### 2、Mac平台

- 1、安装[Homebrew](https://brew.sh/index_zh-cn)；
- 2、打开终端，输入`brew install gradle`，它默认会下载安装binary-only版本;
- 3、当Homebrew安装Gradle完成后，在终端输入`gradle -v`校验是否安装成功.

## Gradle的项目结构

Gradle项目可以使用Android Studio、IntelliJ IDEA等IDE工具或文本编辑器来编写，这里我以Mac平台为例采用文本编辑器配合命令行来编写，Window平台类似。

新建一个目录，如我这里为：～/GradleDemo，打开命令行，输入`cd ~/GradleDemo`切换到这个目录，然后输入`gradle init`，接着gradle会执行`init`这个task任务，它会让你选择生成的项目模版、编写脚本的语言、项目名称等，我选择了basic模版(即原始的Gradle项目)、Groovy语言、项目名称为GradleDemo，如下：

![](..../docs/images/gradle1.png)

这样会`init`任务就会自动替你生成相应的项目模版，如下：

![](..../docs/images/gradle2.png)

忽略掉以.开头的隐藏文件或目录，gradle init为我们自动生成了以下文件或目录：

### 1、build.gradle

它表**示Gradle的项目构建脚本，在里面我们可以通过Groovy来编写脚本**，在Gradle中，**一个build.gradle就对应一个项目，build.gradle放在Gradle项目的根目录下，表示它对应的是根项目**，build.gradle放在Gradle项目的其他子目录下，表示它对应的是子项目，Gradle构建时会把build.gradle解析成[Project](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project)对象，你在里面编写的DSL，其实就是Project接口中的方法。

### 2、settings.gradle

它表示**Gradle的多项目配置脚本，存放在Gradle项目的根目录下，在里面可以通过include来决定哪些子项目会参与构建**，Gradle构建时会把settings.gradle解析成[Settings](https://docs.gradle.org/current/dsl/org.gradle.api.initialization.Settings.html#org.gradle.api.initialization.Settings)对象，include也只是Settings接口中的一个方法。

### 3、Gradle Wrapper

`gradle init`执行时会同时执行`wrapper`任务，`wrapper`任务会创建gradle/wrapper目录，并创建gradle/wrapper目录下的gradle-wrapper.jar、gradle-wrapper.properties这两个文件，还同时创建gradlew、gradlew.bat这两个脚本，它们统称为Gradle Wrapper，是对Gradle的一层包装。

Gradle Wrapper的作用就是可以让你的电脑在**不安装配置Gradle环境**的前提下运行Gradle项目，例如当你的Gradle项目被用户A clone下来时，而用户A的电脑上没有安装配置Gradle环境，用户A通过Gradle构建项目时，Gradle Wrapper就会从指定下载位置下载Gradle，并解压到电脑的指定位置，然后用户A就可以在不配置Gradle系统变量的前提下在Gradle项目的命令行中运行gradlew或gradlew.bat脚本来使用gradle命令，假设用户A要运行`gradle -v`命令，在linux平台下只需要运行`./gradle -v`，在window平台下只需要运行`gradlew -v`，只是把`gradle`替换成`gradlew`。

Gradle Wrapper的每个文件含义如下：

**1、gradlew**：用于在linux平台下执行gradle命令的脚本；

**2、gradlew.bat**：用于在window平台下执行gradle命令的脚本；

**3、gradle-wrapper.jar**：包含Gradle Wrapper运行时的逻辑代码；

**4、gradle-wrapper.properties**：用于指定Gradle的下载位置和解压位置；

gradle-wrapper.properties中各个字段解释如下：

| 字段名           | 解释                                                         |
| ---------------- | ------------------------------------------------------------ |
| distributionBase | 下载的Gradle的压缩包解压后的主目录，为GRADLE_USER_HOME，在window中它表示**C:/用户/你电脑登录的用户名/.gradle/**，在mac中它表示**～/.gradle/** |
| distributionPath | 相对于distributionBase的解压后的Gradle的路径，为wrapper/dists |
| distributionUrl  | Grade压缩包的下载地址，在这里可以修改下载的Gradle的版本和版本类型(binary或complete)，例如gradle-6.5-all.zip表示Gradle 6.5的complete版本，gradle-6.5-bin.zip表示Gradle 6.5的binary版本 |
| zipStoreBase     | 同distributionBase，不过是表示存放下载的Gradle的压缩包的主目录 |
| zipStorePath     | 同distributionPath，不过是表示存放下载的Gradle的压缩包的路径 |

**使用Gradle Wrapper后，就可以统一项目在不同用户电脑上的Gradle版本，同时不必让运行这个Gradle项目的人安装配置Gradle环境**，提高了开发效率。

## Gradle的多项目配置

现在**我们创建的Gradle项目默认已经有一个根项目了，它的build.gradle文件就处于Gradle项目的根目录下，如果我们想要添加多个子项目，这时就需要通过settings.gradle进行配置**。

首先我们在GradleDemo中创建多个文件夹，这里我创建了4个文件夹，分别为：subproject_1、subproject_2，subproject_3，subproject_4，然后在每个新建的文件夹下创建build.gradle文件，如下：

接着打开settings.gradle，添加如下：

```groovy
include ':subproject_1', ':subproject_2', ':subproject_3', ':subproject_4'
```

这样就完成了子项目的添加，打开命令行，切换到GradleDemo目录处，输入`gradle projects`，执行projects任务展示所有项目之间的依赖关系，如下：

```
# chenjianyu @ FVFCG2HJL414 in ~/GradleDemo 
$ gradle projects     

> Task :projects
Root project 'GradleDemo'
+--- Project ':subproject_1'
+--- Project ':subproject_2'
+--- Project ':subproject_3'
\--- Project ':subproject_4'

BUILD SUCCESSFUL in 540ms
1 actionable task: 1 executed
```

可以看到，4个子项目依赖于根项目，接下来我们来配置项目，**配置项目一般在当前项目的build.gradle中进行，可以通过buildscript方法配置**，但是如果有多个项目时，而每个项目的某些配置又一样，那么在每个项目的build.gradle进行相同的配置是很浪费时间，而**Gradle的Project接口为我们提供了allprojects和subprojects方法，在根项目的build.gradle中使用这两个方法可以全局的为所有子项目进行配置**，allprojects和subprojects的区别是：**allprojects的配置包括根项目而subprojects的配置不包括根项目**，

例如:

```groovy
//根项目的build.gradle

//为所有项目添加maven repo地址
allprojects {
    repositories {
        mavenCentral()
    }
}
//为所有子项目添加groovy插件
subprojects {
    apply plugin: 'groovy'
}
```

## Gradle构建的生命周期

当在命令行输入`gradle build`构建整个项目或`gradle task名称`执行某个任务时就会进行Gradle的构建，它的构建过程分为3个阶段：

**init(初始化阶段) -> configure(配置阶段) -> execute(执行阶段)**

- **init**：初始化阶段主要是解析settings.gradle，**生成Settings对象，确定哪些项目需要参与构建，为需要构建的项目创建Project对象**；
- **configure**：配置阶段主**要是解析build.gradle，配置init阶段生成的Project对象，构建根项目和所有子项目，同时生成和配置在build.gradle中定义的Task对象**，构造Task的关系依赖图，关系依赖图是一个有向无环图；
- **execute**：根据configure阶段的关系依赖图**执行Task**.

Gradle在上面3个阶段中每一个阶段的开始和结束都会hook一些监听，暴露给开发者使用，方便开发者在Gradle的不同生命周期阶段做一些事情。

**settings.gradle和build.gradle分别代表Settings对象和Project对象**，它们都有一个[Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.invocation.Gradle.html#org.gradle.api.invocation.Gradle)对象，我们可**以在Gradle项目根目录的settings.gradle或build.gradle中获取到Gradle对象**，然后进行生命周期监听，如下：

```groovy
//build.gradle或settings.gradle

this.gradle.buildStarted {
    println "Gradle构建开始"
}

//---------------------init开始--------------------------------
this.gradle.settingsEvaluated {
    println "settings.gradle解析完成"
}
this.gradle.projectsLoaded {
    println "所有项目从settings加载完成"
}
//---------------------init结束--------------------------------

//-------------------configure开始-----------------------------
this.gradle.beforeProject {project ->
    //每一个项目构建之前被调用
    println "${project.name}项目开始构建"
}
this.gradle.afterProject {project ->
    //每一个项目构建完成被调用
    println "${project.name}项目构建完成"
}
this.gradle.projectsEvaluated {
    println "所有项目构建完成"
}
this.gradle.taskGraph.whenReady {
    println("task图构建完成")
}
//-------------------configure结束-----------------------------

//-------------------execute开始-----------------------------
this.gradle.taskGraph.beforeTask {task ->
    //每个task开始执行时会调用这个方法
    println("${task.name}task开始执行")
}
this.gradle.taskGraph.afterTask {task ->
    //每个task执行结束时会调用这个方法
    println("${task.name}task执行完成")
}
//-------------------execute结束-----------------------------

this.gradle.buildFinished {
    println "Gradle构建结束"
}
```

上面监听方法的放置顺序就是整个Gradle构建的顺序，但是要注意的是Gradle的buildStarted方法永远不会被回调，因为我们注册监听的时机太晚了，当解析settings.gradle或build.gradle时，Gradle就已经构建开始了，所以这个方法也被Gradle标记为废弃的了，因为我们没有机会监听到Gradle构建开始，同时如果你是在build.gradle中添加上面的所有监听，那么Gradle的settingsEvaluated和projectsLoaded方法也不会被回调，因为settings.gradle的解析是在build.gradle之前，在build.gradle中监听这两个方法的时机也太晚了。

> 





参考：https://juejin.im/post/6844904200120303623#heading-3