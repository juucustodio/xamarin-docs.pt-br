---
title: Injeção de dependência
ms.prod: xamarin
ms.assetid: a150f2d1-06f8-4aed-ab4e-7a847d69f103
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 8db8e5b756fe770bdf292ec03c28eb5ed54acf9e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="dependency-injection"></a>Injeção de dependência

Normalmente, um construtor de classe é invocado ao instanciar um objeto e todos os valores que o objeto precisa são passados como argumentos para o construtor. Isso é um exemplo de injeção de dependência e, especificamente, é conhecido como *injeção de construtor*. As dependências que de objeto precisa são injetadas no construtor.

Especificando dependências como tipos de interface, injeção de dependência habilita desacoplamento dos tipos concretos do código que depende desses tipos. Geralmente, ele usa um contêiner que mantém uma lista de registros e os mapeamentos entre as interfaces e tipos abstratos e os tipos concretos que implementam ou estendem esses tipos.

Também há outros tipos de injeção de dependência, como *injeção de setter de propriedade*, e *injeção de chamada de método*, mas eles são menos frequência. Portanto, este capítulo abordará somente executar injeção de construtor com um contêiner de injeção de dependência.

<a name="introduction_to_dependency_injection" />

## <a name="introduction-to-dependency-injection"></a>Introdução a injeção de dependência

Injeção de dependência é uma versão especializada do padrão inversão de controle (IoC), onde a preocupação sendo invertida é o processo de obter a dependência necessária. Com a injeção de dependência, outra classe é responsável por injetar dependências em um objeto em tempo de execução. O seguinte exemplo de código mostra como o `ProfileViewModel` classe estruturada quando usando a injeção de dependência:

```csharp
public class ProfileViewModel : ViewModelBase  
{  
    private IOrderService _orderService;  

    public ProfileViewModel(IOrderService orderService)  
    {  
        _orderService = orderService;  
    }  
    ...  
}
```

O `ProfileViewModel` construtor recebe um `IOrderService` instância como um argumento, inserida por outra classe. A dependência apenas no `ProfileViewModel` classe está no tipo de interface. Portanto, o `ProfileViewModel` classe não tem qualquer conhecimento da classe que é responsável por criar uma instância de `IOrderService` objeto. A classe que é responsável por criar uma instância de `IOrderService` de objeto e inseri-lo no `ProfileViewModel` da classe, que é conhecido como o *contêiner de injeção de dependência*.

Contêineres de injeção de dependência reduzem o acoplamento entre objetos, fornecendo um recurso de instância de classe e gerenciar seu tempo de vida com base na configuração do contêiner. Durante a criação de objetos, o contêiner injeta quaisquer dependências que requer que o objeto nele. Se essas dependências ainda não tem sido criadas, o contêiner cria e suas dependências é resolvido primeiro.

> [!NOTE]
> Injeção de dependência pode ser implementada manualmente usando as fábricas. No entanto, usar um contêiner fornece recursos adicionais, como gerenciamento de vida útil e registro por meio da verificação do assembly.

Há várias vantagens de usar um contêiner de injeção de dependência:

-   Um contêiner elimina a necessidade de uma classe localizar suas dependências e gerenciar suas vidas úteis.
-   Um contêiner permite que o mapeamento de dependências implementados sem afetar a classe.
-   Um contêiner facilita a capacidade de teste, permitindo que dependências fictícios.
-   Um contêiner aumenta a facilidade de manutenção, permitindo que novas classes para ser facilmente adicionadas para o aplicativo.

No contexto de um aplicativo xamarin. Forms que usa o modelo MVVM, um contêiner de injeção de dependência normalmente será usado para registrar e resolver exibir modelos e para registrar os serviços e injetando-los em modelos de exibição.

Vários contêineres de injeção de dependência estão disponíveis com o aplicativo móvel eShopOnContainers usando Autofac para gerenciar a instanciação do modelo de exibição e classes no aplicativo de serviço. Autofac facilita a criação de aplicativos acoplados de forma flexível e fornece todos os recursos geralmente encontrados em contêineres de injeção de dependência, incluindo métodos para registrar os mapeamentos de tipos e instâncias de objeto, resolva os objetos, gerenciar a vida útil do objeto e injetar objetos dependentes em construtores de objetos que ele resolve. Para obter mais informações sobre Autofac, consulte [Autofac](http://autofac.readthedocs.io/en/latest/index.html) em readthedocs.io.

Em Autofac, o `IContainer` interface fornece o contêiner de injeção de dependência. Figura 3-1 mostra as dependências, ao usar esse contêiner, que instancia um `IOrderService` de objeto e insere-o na `ProfileViewModel` classe.

![](dependency-injection-images/dependencyinjection.png "Exemplo de dependências, ao usar injeção de dependência")

**Figura 3-1:** dependências quando usando a injeção de dependência

Em tempo de execução, o contêiner deve saber qual implementação do `IOrderService` interface deve instanciar, antes que ele pode instanciar uma `ProfileViewModel` objeto. Isso envolve:

-   O contêiner decidir como instanciar um objeto que implementa o `IOrderService` interface. Isso é conhecido como *registro*.
-   O contêiner instanciar o objeto que implementa o `IOrderService` interface e o `ProfileViewModel` objeto. Isso é conhecido como *resolução*.

Por fim, o aplicativo será concluído usando o `ProfileViewModel` objeto e ficarão disponíveis para coleta de lixo. Neste ponto, o coletor de lixo deve descartar o `IOrderService` instância se outras classes não compartilham a mesma instância.

> [!TIP]
> Escreva código independente do contêiner. Sempre tente escrever o código do contêiner independente para desacoplar o aplicativo do contêiner de dependência específico que está sendo usado.

## <a name="registration"></a>Registro

Antes de dependências podem ser inseridas em um objeto, os tipos de dependências devem primeiro ser registrados com o contêiner. Registrar um tipo geralmente envolve passar o contêiner de uma interface e um tipo concreto que implemente a interface.

Há duas maneiras de registrar tipos e objetos no contêiner por meio de código:

-   Registre um tipo ou o mapeamento com o contêiner. Quando necessário, o contêiner criará uma instância do tipo especificado.
-   Registre um objeto existente no contêiner como um singleton. Quando necessário, o contêiner retornará uma referência para o objeto existente.

> [!TIP]
> Contêineres de injeção de dependência nem sempre são adequados. Injeção de dependência apresenta complexidade adicional e requisitos que não podem ser apropriado ou útil para aplicativos pequenos. Se uma classe não tem nenhuma dependência ou não é uma dependência para outros tipos, ele não pode fazer sentido para colocá-lo no contêiner. Além disso, se uma classe tiver um único conjunto de dependências que são essenciais para o tipo e nunca será alterado, não pode fazer sentido para colocá-lo no contêiner.

O registro de tipos que exigem a injeção de dependência deve ser executado em um único método em um aplicativo, e esse método deve ser chamado no início do ciclo de vida do aplicativo para garantir que o aplicativo esteja ciente das dependências entre suas classes. No aplicativo móvel eShopOnContainers isso é feito com o `ViewModelLocator` classe quais compilações o `IContainer` do objeto e é a única classe no aplicativo que contém uma referência a esse objeto. O exemplo de código a seguir mostra como o aplicativo móvel eShopOnContainers declara o `IContainer` objeto o `ViewModelLocator` classe:

```csharp
private static IContainer _container;
```

Tipos e instâncias são registradas no `RegisterDependencies` método o `ViewModelLocator` classe. Isso é feito criando primeiro uma `ContainerBuilder` instância, que é demonstrada no exemplo de código a seguir:

```csharp
var builder = new ContainerBuilder();
```

Tipos e as instâncias são registradas com o `ContainerBuilder` objeto e o exemplo de código a seguir demonstra a forma mais comum de registro do tipo:

```csharp
builder.RegisterType<RequestProvider>().As<IRequestProvider>();
```

O `RegisterType` método mostrado aqui mapeia um tipo de interface para um tipo concreto. Indica o contêiner para instanciar um `RequestProvider` objeto quando ele cria um objeto que requer uma inclusão de um `IRequestProvider` por meio de um construtor.

Tipos concretos também podem ser registrados diretamente sem um mapeamento de um tipo de interface, conforme mostrado no exemplo de código a seguir:

```csharp
builder.RegisterType<ProfileViewModel>();
```

Quando o `ProfileViewModel` tipo for resolvido, o contêiner será injetar suas dependências necessárias.

Autofac também permite o registro de instância, em que o contêiner é responsável por manter uma referência a uma instância singleton de um tipo. Por exemplo, o exemplo de código a seguir mostra como o aplicativo móvel eShopOnContainers registra o tipo concreto para usar quando um `ProfileViewModel` instância requer um `IOrderService` instância:

```csharp
builder.RegisterType<OrderService>().As<IOrderService>().SingleInstance();
```

O `RegisterType` método mostrado aqui mapeia um tipo de interface para um tipo concreto. O `SingleInstance` método configura o registro para que todos os objetos dependentes recebe a mesma instância compartilhada. Portanto, um único `OrderService` instância existirá no contêiner, que é compartilhado por objetos que exigem uma inclusão de um `IOrderService` por meio de um construtor.

Registro da instância também pode ser executado com o `RegisterInstance` método, que é demonstrado no exemplo de código a seguir:

```csharp
builder.RegisterInstance(new OrderMockService()).As<IOrderService>();
```

O `RegisterInstance` método mostrado aqui cria um novo `OrderMockService` de instância e a registra com o contêiner. Portanto, um único `OrderMockService` instância existe no contêiner, que é compartilhado por objetos que exigem uma inclusão de um `IOrderService` por meio de um construtor.

Após o registro de tipo e instância, o `IContainer` objeto deve ser criado, que é demonstrado no exemplo de código a seguir:

```csharp
_container = builder.Build();
```

Invocar o `Build` método o `ContainerBuilder` instância cria um novo contêiner de injeção de dependência que contém os registros que foram feitos.

>💡 **Dica**: considere uma `IContainer` como imutável. Enquanto Autofac fornece um `Update` para atualizar registros em um contêiner existente, chamar este método deve ser evitada sempre que possível. Há riscos à modificação de um contêiner depois que ela for criada, especialmente se o contêiner tiver sido usado. Para obter mais informações, consulte [considerar um contêiner como imutável](http://docs.autofac.org/en/latest/best-practices/#consider-a-container-as-immutable) em readthedocs.io.

<a name="resolution" />

## <a name="resolution"></a>Resolução

Depois que um tipo é registrado, pode ser resolvido ou inserida como uma dependência. Quando um tipo que está sendo resolvido e o contêiner precisa criar uma nova instância, ele injeta quaisquer dependências na instância.

Em geral, quando um tipo for resolvido, um dos três coisas acontece:

1.  Se o tipo não tiver sido registrado, o contêiner gera uma exceção.
1.  Se o tipo tiver sido registrado como um singleton, o contêiner retorna a instância singleton. Se esta for a primeira vez em que o tipo é chamado para, o contêiner cria se necessário e mantém uma referência a ele.
1.  Se o tipo não foi registrado como um singleton, o contêiner retorna uma nova instância e não manter uma referência a ele.

O seguinte exemplo de código mostra como o `RequestProvider` tipo que foi registrado anteriormente com Autofac pode ser resolvido:

```csharp
var requestProvider = _container.Resolve<IRequestProvider>();
```

Neste exemplo, Autofac é chamado para resolver o tipo concreto para o `IRequestProvider` tipo, juntamente com quaisquer dependências. Normalmente, o `Resolve` método é chamado quando uma instância de um tipo específico é necessária. Para obter informações sobre como controlar o tempo de vida de objetos resolvidos, consulte [gerenciamento de tempo de vida de objetos resolvido](#managing_the_lifetime_of_resolved_objects).

O exemplo de código a seguir mostra como o aplicativo móvel eShopOnContainers cria tipos de modelo de exibição e suas dependências:

```csharp
var viewModel = _container.Resolve(viewModelType);
```

Neste exemplo, Autofac é chamado para resolver o tipo de modelo de exibição para um modelo de exibição solicitado e o contêiner também resolverá as dependências. Ao resolver o `ProfileViewModel` é do tipo, a dependência para resolver uma `IOrderService` objeto. Portanto, Autofac primeiro constrói um `OrderService` de objeto e, em seguida, passa para o construtor do `ProfileViewModel` classe. Para obter mais informações sobre como o aplicativo móvel eShopOnContainers constrói o modo de exibição de modelos e as associa a modos de exibição, consulte [criar automaticamente um modelo de exibição com um localizador de modelo de exibição](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

> [!NOTE]
> Registrar e resolver os tipos com um contêiner tem um custo por causa do uso do contêiner de reflexão para a criação de cada tipo, especialmente se as dependências estão sendo reconstruídas para cada navegação de página no aplicativo de desempenho. Se houver muitos ou profundas dependências, o custo de criação pode aumentar significativamente.

<a name="managing_the_lifetime_of_resolved_objects" />

## <a name="managing-the-lifetime-of-resolved-objects"></a>Gerenciar a vida útil de objetos resolvidos

Depois de registrar um tipo, o comportamento padrão para Autofac é criar uma nova instância do tipo registrado cada vez que o tipo é resolvido ou quando o mecanismo de dependência injeta instâncias em outras classes. Nesse cenário, o contêiner não mantenha uma referência para o objeto resolvido. No entanto, ao registrar uma instância, o comportamento padrão para Autofac é gerenciar a vida útil do objeto como um singleton. Portanto, a instância permanece em escopo enquanto o contêiner está no escopo e é descartado quando o contêiner sai do escopo e é coletado como lixo, ou quando o código dispõe explicitamente o contêiner.

Um escopo de instância Autofac pode ser usado para especificar o comportamento de singleton de um objeto que Autofac cria a partir de um tipo registrado. Escopos de instância Autofac gerenciam os tempos de vida do objeto instanciados pelo contêiner. O escopo da instância padrão para o `RegisterType` método é o `InstancePerDependency` escopo. No entanto, o `SingleInstance` escopo pode ser usado com o `RegisterType` método, para que o contêiner cria ou retorna uma instância de um tipo de singleton ao chamar o `Resolve` método. O exemplo de código a seguir mostra como Autofac é instruído para criar uma instância singleton do `NavigationService` classe:

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

Na primeira vez que o `INavigationService` interface for resolvida, o contêiner cria um novo `NavigationService` de objeto e mantém uma referência a ele. Em qualquer resoluções subsequentes do `INavigationService` interface, o contêiner retorna uma referência para o `NavigationService` objeto que foi criado anteriormente.

> [!NOTE]
> O escopo de SingleInstance descarta objetos criados quando o contêiner é descartado.

Autofac inclui escopos de instância adicional. Para obter mais informações, consulte [escopo da instância](http://autofac.readthedocs.io/en/latest/lifetime/instance-scope.html) em readthedocs.io.

## <a name="summary"></a>Resumo

Injeção de dependência habilita desacoplar tipos concretos do código que depende desses tipos. Normalmente, ele usa um contêiner que mantém uma lista de registros e os mapeamentos entre as interfaces e tipos abstratos e os tipos concretos que implementam ou estendem esses tipos.

Autofac facilita a criação de aplicativos acoplados de forma flexível e fornece todos os recursos geralmente encontrados em contêineres de injeção de dependência, incluindo métodos para registrar os mapeamentos de tipos e instâncias de objeto, resolva os objetos, gerenciar a vida útil do objeto e injetar objetos dependentes em construtores de objetos que é resolvida.


## <a name="related-links"></a>Links relacionados

- [Baixar eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
