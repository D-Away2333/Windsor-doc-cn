#基础Windsor教程

本教程旨在提供对Windsor最基础的接触和最少的使用步骤，如果要深入了解，请查看站点的其他教程。除了Visual Studio之外，本教程所需要的唯一外部工具就是Castle Windsor组件。本教程使用Castle Windsor 2.5.3和.NET Framework 3.5.版本进行演示。

现在假设你已经熟悉控制反转的概念但是对Windsor并不熟悉，并且已经了解一些有关C#的概念性知识，例如接口。

##开始

下载Windsor组件，关于如何下载Windsor请点击[这里](mvc-tutorial-part-1-getting-windsor.md)，这些都是不同、更详细的教程，并且不需要参考本教程。

本教程基于两个项目 - 一个windows form应用程序项目和一个类库项目，两个项目都在同一个解决方案下。在接下来的教程中，创建与本教程相同的命名相对来说是最简单的方式。我已经创建了一个名为“CastleWindsorExample”的解决方案，其中，windows forms项目叫做“CastleWindsorExample”，类库项目叫做“ClassLibrary1”。

##创建类库项目

在ClassLibrary1项目中，添加两个接口文件 - IDependency1和IDependency2（这些名字是我虚构的 - 它们可以是任意的其他名字）

IDependency1接口内容如下 ...

```csharp
namespace ClassLibrary1
{
    public interface IDependency1
    {
        object SomeObject { get; set; }
    }
}
```

IDendency2接口内容如下 ...

```csharp
namespace ClassLibrary1
{
    public interface IDependency2
    {
        object SomeOtherObject { get; set; }
    }
}
```

现在，创建两个实现类。本教程中创建了Dependency1和Dependency2两个类。

dependency1内容如下 ...

```csharp
namespace ClassLibrary1
{
    public class Dependency1 : IDependency1
    {
        public object SomeObject { get; set; }
    }
}
```

dependency2内容如下 ...

```csharp
namespace ClassLibrary1
{
    public class Dependency2 : IDependency2
    {
        public object SomeOtherObject { get; set; }
    }
}
```

注意，它们继承自前面那些接口。

创建一个新的类Main，这将作为类库程序的入口点。

main内容如下：

```csharp
namespace ClassLibrary1
{
    public class Main
    {
        private IDependency1 object1;
        private IDependency2 object2;

        public Main(IDependency1 dependency1, IDependency2 dependency2)
        {
           object1 = dependency1;
           object2 = dependency2;
        }

        public void DoSomething()
        {
           object1.SomeObject = "Hello World";
           object2.SomeOtherObject = "Hello Mars";
        }
    }
}
```

请注意，该类的构造函数中需要两个参数。在这里，我们不创建实例，而是为其注入依赖。我们不能为main类创建一个对象除非我们为其提供两个依赖项。

## 使用类库

到这里，我们已经有了三个类和两个接口。因为这个例子非常简单，项目中并没有很多的方法，我们的类库ClassLibrary1对外暴露了三种对象类型，而且与Windsor没有任何联系 - 暂时。现在，我们将注意力放在Windows Forms项目“CastleWindsorExample”上。

在CastleWindsorExample项目中，添加三个引用，对ClassLibrary1项目、Castle.Core和Castle.Windsor的引用（下载Castle Windsor组件）。

我的windows forms项目默认从Form1.cs文件开始。我将要添加一个名为button1的按钮到这个窗口。在button1_Click事件中我将把它关联到类库文件中。这是Castle Windsor的核心。到目前为止，所有的东西都还在准备过程中。

Form1.cs文件内容如下 ...

```csharp
using Castle.MicroKernel.Registration;
using Castle.Windsor;

private void button1_Click(object sender, EventArgs e)
{
    // CREATE A WINDSOR CONTAINER OBJECT AND REGISTER THE INTERFACES, AND THEIR CONCRETE IMPLEMENTATIONS.
    var container = new WindsorContainer();
    container.Register(Component.For<Main>());
    container.Register(Component.For<IDependency1>().ImplementedBy<Dependency1>());
    container.Register(Component.For<IDependency2>().ImplementedBy<Dependency2>());

    // CREATE THE MAIN OBJECT AND INVOKE ITS METHOD(S) AS DESIRED.
    var mainThing = container.Resolve<Main>();
    mainThing.DoSomething();
}
```

如果在main函数的构造函数中设置一个断点，你会发现依赖已经被定义。代码会自动实现未创建的dependency1或dependency2实例。Windsor帮助我们完成了这个工作。还可以通过AddComponent方法来连接程序。

## 结语

我们为什么想做这些？main类是完全完全独立的并且可以更方便的进行单元测试。我们已经实现了对每个依赖项的关注分离。每个依赖类可以单独进行单元测试。本教程向我们展示了控制反转的概念和Windsor的基本用法。？用文件获取器（File-Getter）替换dependency1，用文件解析器（File-Parser）替换dependency2。文件获取器和文件解析器将会注入到main类中，但他们都是自动化、可单元测试的。
