---
title: Injeção de dependência
description: Este capítulo explica como o aplicativo móvel eShopOnContainers usa injeção de dependência para desacoplar tipos concretos do código que depende desses tipos.
ms.prod: xamarin
ms.assetid: a150f2d1-06f8-4aed-ab4e-7a847d69f103
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 6cbcd6612323acc8619004d56fff82461e005e9e
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69529144"
---
# <a name="dependency-injection"></a>Injeção de dependência

Normalmente, um construtor de classe é invocado ao instanciar um objeto e quaisquer valores que o objeto precisa são passados como argumentos para o construtor. Este é um exemplo de injeção de dependência e, especificamente, é conhecido como *injeção de Construtor*. As dependências que o objeto precisa são injetadas no construtor.

Ao especificar dependências como tipos de interface, a injeção de dependência permite o desacoplamento dos tipos concretos do código que depende desses tipos. Ele geralmente usa um contêiner que contém uma lista de registros e mapeamentos entre interfaces e tipos abstratos e os tipos concretos que implementam ou estendem esses tipos.

Também há outros tipos de injeção de dependência, como *injeção de setter de propriedade*e *injeção de chamada de método*, mas elas são vistas com menos frequência. Portanto, este capítulo se concentrará exclusivamente na execução de injeção de construtor com um contêiner de injeção de dependência.

<a name="introduction_to_dependency_injection" />

## <a name="introduction-to-dependency-injection"></a>Introdução à injeção de dependência

A injeção de dependência é uma versão especializada do padrão de inversão de controle (IoC), onde a preocupação que está sendo invertida é o processo de obtenção da dependência necessária. Com a injeção de dependência, outra classe é responsável por injetar dependências em um objeto no tempo de execução. O exemplo de código a seguir mostra `ProfileViewModel` como a classe é estruturada ao usar a injeção de dependência:

```csharp
public class ProfileViewModel : ViewModelBase  
{  
    private IOrderService _orderService;  

    public ProfileViewModel(IOrderService orderService)  
    {  
        _orderService = orderService;  
    }  
    ...  
}
```

O `ProfileViewModel` Construtor recebe uma `IOrderService` instância como um argumento, injetado por outra classe. A única dependência na `ProfileViewModel` classe está no tipo de interface. Portanto, a `ProfileViewModel` classe não tem nenhum conhecimento da classe responsável por instanciar o `IOrderService` objeto. A classe responsável por instanciar o `IOrderService` objeto e inseri-lo `ProfileViewModel` na classe, é conhecida como o contêiner de injeção de *dependência*.

Os contêineres de injeção de dependência reduzem o acoplamento entre objetos, fornecendo um recurso para instanciar instâncias de classe e gerenciar sua vida útil com base na configuração do contêiner. Durante a criação de objetos, o contêiner injeta todas as dependências que o objeto exige. Se essas dependências ainda não tiverem sido criadas, o contêiner criará e resolverá suas dependências primeiro.

> [!NOTE]
> A injeção de dependência também pode ser implementada manualmente usando fábricas. No entanto, o uso de um contêiner fornece recursos adicionais, como o gerenciamento de tempo de vida e o registro por meio da verificação de assembly.

Há várias vantagens em usar um contêiner de injeção de dependência:

- Um contêiner remove a necessidade de uma classe para localizar suas dependências e gerenciar seus tempos de vida.
- Um contêiner permite o mapeamento de dependências implementadas sem afetar a classe.
- Um contêiner facilita a capacidade de teste, permitindo que as dependências sejam simuladas.
- Um contêiner aumenta a facilidade de manutenção, permitindo que novas classes sejam facilmente adicionadas ao aplicativo.

No contexto de um aplicativo Xamarin. Forms que usa o MVVM, um contêiner de injeção de dependência normalmente será usado para registrar e resolver modelos de exibição e para registrar serviços e inseri-los em modelos de exibição.

Há muitos contêineres de injeção de dependência disponíveis, com o aplicativo móvel eShopOnContainers usando Autofac para gerenciar a instanciação do modelo de exibição e classes de serviço no aplicativo. O Autofac facilita a criação de aplicativos menos rígidos e fornece todos os recursos normalmente encontrados em contêineres de injeção de dependência, incluindo métodos para registrar mapeamentos de tipo e instâncias de objeto, resolver objetos, gerenciar tempos de vida de objetos e injetar objetos dependentes em construtores de objetos que ele resolve. Para obter mais informações sobre Autofac, consulte [Autofac](http://autofac.readthedocs.io/en/latest/index.html) em readthedocs.IO.

No Autofac, a `IContainer` interface fornece o contêiner de injeção de dependência. A Figura 3-1 mostra as dependências ao usar esse contêiner, que instancia `IOrderService` um objeto e o `ProfileViewModel` injeta na classe.

![](dependency-injection-images/dependencyinjection.png "Exemplo de dependências ao usar injeção de dependência")

**Figura 3-1:** Dependências ao usar injeção de dependência

Em tempo de execução, o contêiner deve saber qual implementação `IOrderService` da interface ela deve instanciar, antes de poder `ProfileViewModel` criar uma instância de um objeto. Isso envolve:

- O contêiner que decide como criar uma instância de um objeto `IOrderService` que implementa a interface. Isso é conhecido como *registro*.
- O contêiner que instancia o objeto que implementa a `IOrderService` interface e o `ProfileViewModel` objeto. Isso é conhecido como *resolução*.

Eventualmente, o aplicativo terminará de usar `ProfileViewModel` o objeto e ele ficará disponível para coleta de lixo. Neste ponto, o coletor de lixo deve descartar `IOrderService` a instância se outras classes não compartilharem a mesma instância.

> [!TIP]
> Gravar código independente de contêiner. Sempre tente escrever código independente de contêiner para desacoplar o aplicativo do contêiner de dependência específico que está sendo usado.

## <a name="registration"></a>Registro

Antes que as dependências possam ser injetadas em um objeto, os tipos das dependências devem primeiro ser registrados com o contêiner. O registro de um tipo normalmente envolve a passagem de um contêiner de uma interface e um tipo concreto que implementa a interface.

Há duas maneiras de registrar tipos e objetos no contêiner por meio de código:

- Registre um tipo ou mapeamento com o contêiner. Quando necessário, o contêiner criará uma instância do tipo especificado.
- Registre um objeto existente no contêiner como um singleton. Quando necessário, o contêiner retornará uma referência ao objeto existente.

> [!TIP]
> Contêineres de injeção de dependência nem sempre são adequados. A injeção de dependência apresenta mais complexidade e requisitos que podem não ser apropriados ou úteis para aplicativos pequenos. Se uma classe não tiver nenhuma dependência ou não for uma dependência para outros tipos, talvez não faça sentido colocá-la no contêiner. Além disso, se uma classe tiver um único conjunto de dependências que são integrais para o tipo e nunca serão alteradas, talvez não faça sentido colocá-la no contêiner.

O registro de tipos que exigem injeção de dependência deve ser executado em um único método em um aplicativo, e esse método deve ser invocado no início do ciclo de vida do aplicativo para garantir que o aplicativo esteja ciente das dependências entre suas classes. No aplicativo móvel eShopOnContainers, isso é executado pela `ViewModelLocator` classe, que cria o `IContainer` objeto e é a única classe no aplicativo que contém uma referência a esse objeto. O exemplo de código a seguir mostra como o aplicativo móvel eShopOnContainers declara `IContainer` o objeto `ViewModelLocator` na classe:

```csharp
private static IContainer _container;
```

Tipos e instâncias são registrados no `RegisterDependencies` método `ViewModelLocator` na classe. Isso é obtido primeiro com a criação `ContainerBuilder` de uma instância, que é demonstrada no exemplo de código a seguir:

```csharp
var builder = new ContainerBuilder();
```

Os tipos e as instâncias são então registrados `ContainerBuilder` com o objeto, e o exemplo de código a seguir demonstra a forma mais comum do registro de tipo:

```csharp
builder.RegisterType<RequestProvider>().As<IRequestProvider>();
```

O `RegisterType` método mostrado aqui mapeia um tipo de interface para um tipo concreto. Ele informa ao contêiner para criar uma `RequestProvider` instância de um objeto ao criar uma instância de um objeto que requer `IRequestProvider` uma injeção de um por meio de um construtor.

Os tipos concretos também podem ser registrados diretamente sem um mapeamento de um tipo de interface, conforme mostrado no exemplo de código a seguir:

```csharp
builder.RegisterType<ProfileViewModel>();
```

Quando o `ProfileViewModel` tipo for resolvido, o contêiner injetará suas dependências necessárias.

O Autofac também permite o registro de instância, em que o contêiner é responsável por manter uma referência a uma instância singleton de um tipo. Por exemplo, o exemplo de código a seguir mostra como o aplicativo móvel eShopOnContainers registra o tipo concreto a ser `ProfileViewModel` usado quando uma `IOrderService` instância requer uma instância:

```csharp
builder.RegisterType<OrderService>().As<IOrderService>().SingleInstance();
```

O `RegisterType` método mostrado aqui mapeia um tipo de interface para um tipo concreto. O `SingleInstance` método configura o registro para que cada objeto dependente receba a mesma instância compartilhada. Portanto, apenas uma única `OrderService` instância existirá no contêiner, que é compartilhada por objetos que exigem uma injeção de um `IOrderService` por meio de um construtor.

O registro da instância também pode ser executado `RegisterInstance` com o método, que é demonstrado no exemplo de código a seguir:

```csharp
builder.RegisterInstance(new OrderMockService()).As<IOrderService>();
```

O `RegisterInstance` método mostrado aqui cria uma nova `OrderMockService` instância e a registra com o contêiner. Portanto, há apenas uma `OrderMockService` única instância no contêiner, que é compartilhada por objetos que exigem uma injeção de um `IOrderService` por meio de um construtor.

Após o registro de tipo e instância `IContainer` , o objeto deve ser criado, o que é demonstrado no exemplo de código a seguir:

```csharp
_container = builder.Build();
```

Invocar o `Build` método `ContainerBuilder` na instância cria um novo contêiner de injeção de dependência que contém os registros que foram feitos.

> [!TIP]
> Considere um `IContainer` como imutável. Embora o Autofac forneça `Update` um método para atualizar os registros em um contêiner existente, chamar esse método deve ser evitado sempre que possível. Há riscos para modificar um contêiner após sua criação, especialmente se o contêiner tiver sido usado. Para obter mais informações, consulte [considerar um contêiner como imutável](http://docs.autofac.org/en/latest/best-practices/#consider-a-container-as-immutable) em readthedocs.IO.

<a name="resolution" />

## <a name="resolution"></a>Resolução

Depois que um tipo é registrado, ele pode ser resolvido ou injetado como uma dependência. Quando um tipo está sendo resolvido e o contêiner precisa criar uma nova instância, ele injeta quaisquer dependências na instância.

Geralmente, quando um tipo é resolvido, uma das três coisas acontece:

1. Se o tipo não foi registrado, o contêiner gera uma exceção.
1. Se o tipo tiver sido registrado como um singleton, o contêiner retornará a instância singleton. Se esta for a primeira vez que o tipo é chamado para, o contêiner a cria, se necessário, e mantém uma referência a ele.
1. Se o tipo não tiver sido registrado como um singleton, o contêiner retornará uma nova instância e não manterá uma referência a ele.

O exemplo de código a seguir mostra `RequestProvider` como o tipo que foi registrado anteriormente com Autofac pode ser resolvido:

```csharp
var requestProvider = _container.Resolve<IRequestProvider>();
```

Neste exemplo, Autofac é solicitado a resolver o tipo concreto para o `IRequestProvider` tipo, juntamente com quaisquer dependências. Normalmente, o `Resolve` método é chamado quando uma instância de um tipo específico é necessária. Para obter informações sobre como controlar o tempo de vida de objetos resolvidos, consulte [Gerenciando o tempo de vida de objetos resolvidos](#managing_the_lifetime_of_resolved_objects).

O exemplo de código a seguir mostra como o aplicativo móvel eShopOnContainers instancia tipos de modelo de exibição e suas dependências:

```csharp
var viewModel = _container.Resolve(viewModelType);
```

Neste exemplo, Autofac é solicitado a resolver o tipo de modelo de exibição para um modelo de exibição solicitado, e o contêiner também resolverá quaisquer dependências. Ao resolver o `ProfileViewModel` tipo, a dependência a ser resolvida é `IOrderService` um objeto. Portanto, o Autofac primeiro constrói um `OrderService` objeto e, em seguida, o passa para o `ProfileViewModel` Construtor da classe. Para obter mais informações sobre como o aplicativo móvel eShopOnContainers constrói modelos de exibição e os associa a exibições, consulte [criando automaticamente um modelo de exibição com um localizador de modelo de exibição](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

> [!NOTE]
> Efetuar o registro e a resolução de tipos usando um contêiner tem um custo de desempenho devido ao uso da reflexão pelo contêiner para criar cada tipo, especialmente se as dependências estiverem sendo reconstruídas para cada navegação de página no aplicativo. Se houver muitas dependências ou se elas forem profundas, o custo da criação poderá aumentar significativamente.

<a name="managing_the_lifetime_of_resolved_objects" />

## <a name="managing-the-lifetime-of-resolved-objects"></a>Gerenciando o tempo de vida de objetos resolvidos

Depois de registrar um tipo, o comportamento padrão para Autofac é criar uma nova instância do tipo registrado cada vez que o tipo é resolvido ou quando o mecanismo de dependência injeta instâncias em outras classes. Nesse cenário, o contêiner não mantém uma referência ao objeto resolvido. No entanto, ao registrar uma instância, o comportamento padrão do Autofac é gerenciar o tempo de vida do objeto como um singleton. Portanto, a instância permanece no escopo enquanto o contêiner está no escopo e é descartada quando o contêiner sai do escopo e é coletado pelo lixo, ou quando o código descarta explicitamente o contêiner.

Um escopo de instância Autofac pode ser usado para especificar o comportamento singleton de um objeto que o Autofac cria a partir de um tipo registrado. Os escopos da instância Autofac gerenciam os tempos de vida do objeto instanciados pelo contêiner. O escopo da instância padrão para `RegisterType` o método é `InstancePerDependency` o escopo. No entanto `SingleInstance` , o escopo pode ser usado `RegisterType` com o método, para que o contêiner crie ou retorne uma instância singleton de um tipo `Resolve` ao chamar o método. O exemplo de código a seguir mostra como Autofac é instruído a criar uma instância singleton `NavigationService` da classe:

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

Na primeira vez que a `INavigationService` interface é resolvida, o contêiner cria um `NavigationService` novo objeto e mantém uma referência a ele. Em qualquer resoluções subsequentes `INavigationService` da interface, o contêiner retorna uma referência `NavigationService` ao objeto que foi criado anteriormente.

> [!NOTE]
> O escopo SingleInstance descarta objetos criados quando o contêiner é Descartado.

Autofac inclui escopos de instância adicionais. Para obter mais informações, consulte [escopo da instância](http://autofac.readthedocs.io/en/latest/lifetime/instance-scope.html) em readthedocs.IO.

## <a name="summary"></a>Resumo

A injeção de dependência permite o desacoplamento de tipos concretos do código que depende desses tipos. Normalmente, ele usa um contêiner que contém uma lista de registros e mapeamentos entre interfaces e tipos abstratos e os tipos concretos que implementam ou estendem esses tipos.

O Autofac facilita a criação de aplicativos menos rígidos e fornece todos os recursos normalmente encontrados em contêineres de injeção de dependência, incluindo métodos para registrar mapeamentos de tipo e instâncias de objeto, resolver objetos, gerenciar tempos de vida de objetos e injetar objetos dependentes em construtores de objetos que ele resolve.


## <a name="related-links"></a>Links relacionados

- [Download do eBook (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
