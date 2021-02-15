---
title: Injeção de dependência
description: Este capítulo explica como o aplicativo móvel eShopOnContainers usa injeção de dependência para desacoplar tipos concretos do código que depende desses tipos.
ms.prod: xamarin
ms.assetid: a150f2d1-06f8-4aed-ab4e-7a847d69f103
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/04/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3facf6e1e5796d8e17488f3c018cba23e5f99b7f
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375350"
---
# <a name="dependency-injection"></a>Injeção de dependência

> [!NOTE]
> Este livro eletrônico foi publicado na Primavera de 2017 e não foi atualizado desde então. Há muito no livro que permanece valioso, mas parte do material está desatualizada.

Normalmente, um construtor de classe é invocado ao instanciar um objeto e quaisquer valores que o objeto precisa são passados como argumentos para o construtor. Este é um exemplo de injeção de dependência e, especificamente, é conhecido como *injeção de Construtor*. As dependências que o objeto precisa são injetadas no construtor.

Ao especificar dependências como tipos de interface, a injeção de dependência permite o desacoplamento dos tipos concretos do código que depende desses tipos. Ele geralmente usa um contêiner que contém uma lista de registros e mapeamentos entre interfaces e tipos abstratos e os tipos concretos que implementam ou estendem esses tipos.

Também há outros tipos de injeção de dependência, como *injeção de setter de propriedade* e *injeção de chamada de método* , mas elas são vistas com menos frequência. Portanto, este capítulo se concentrará exclusivamente na execução de injeção de construtor com um contêiner de injeção de dependência.

## <a name="introduction-to-dependency-injection"></a>Introdução à injeção de dependência

A injeção de dependência é uma versão especializada do padrão de inversão de controle (IoC), onde a preocupação que está sendo invertida é o processo de obtenção da dependência necessária. Com a injeção de dependência, outra classe é responsável por injetar dependências em um objeto no tempo de execução. O exemplo de código a seguir mostra como a `ProfileViewModel` classe é estruturada ao usar a injeção de dependência:

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

O `ProfileViewModel` Construtor recebe uma `IOrderService` instância como um argumento, injetado por outra classe. A única dependência na `ProfileViewModel` classe está no tipo de interface. Portanto, a `ProfileViewModel` classe não tem nenhum conhecimento da classe responsável por instanciar o `IOrderService` objeto. A classe responsável por instanciar o `IOrderService` objeto e inseri-lo na `ProfileViewModel` classe, é conhecida como o contêiner de injeção de *dependência*.

Os contêineres de injeção de dependência reduzem o acoplamento entre objetos, fornecendo um recurso para instanciar instâncias de classe e gerenciar sua vida útil com base na configuração do contêiner. Durante a criação de objetos, o contêiner injeta todas as dependências que o objeto exige. Se essas dependências ainda não tiverem sido criadas, o contêiner criará e resolverá suas dependências primeiro.

> [!NOTE]
> A injeção de dependência também pode ser implementada manualmente usando fábricas. No entanto, o uso de um contêiner fornece recursos adicionais, como o gerenciamento de tempo de vida e o registro por meio da verificação de assembly.

Há várias vantagens em usar um contêiner de injeção de dependência:

- Um contêiner remove a necessidade de uma classe para localizar suas dependências e gerenciar seus tempos de vida.
- Um contêiner permite o mapeamento de dependências implementadas sem afetar a classe.
- Um contêiner facilita a capacidade de teste, permitindo que as dependências sejam simuladas.
- Um contêiner aumenta a facilidade de manutenção, permitindo que novas classes sejam facilmente adicionadas ao aplicativo.

No contexto de um Xamarin.Forms aplicativo que usa MVVM, um contêiner de injeção de dependência normalmente será usado para registrar e resolver modelos de exibição e para registrar serviços e injeta-los em modelos de exibição.

Há muitos contêineres de injeção de dependência disponíveis, com o aplicativo móvel eShopOnContainers usando TinyIoC para gerenciar a instanciação do modelo de exibição e classes de serviço no aplicativo. O TinyIoC foi escolhido após a avaliação de vários contêineres diferentes e apresenta um desempenho superior em plataformas móveis em comparação com a maioria dos contêineres conhecidos. Ele facilita a criação de aplicativos menos rígidos e fornece todos os recursos normalmente encontrados em contêineres de injeção de dependência, incluindo métodos para registrar mapeamentos de tipo, resolver objetos, gerenciar tempos de vida de objeto e injetar objetos dependentes em construtores de objetos que ele resolve. Para obter mais informações sobre TinyIoC, consulte [TinyIoC](https://github.com/grumpydev/TinyIoC/wiki) em github.com.

No TinyIoC, o `TinyIoCContainer` tipo fornece o contêiner de injeção de dependência. A Figura 3-1 mostra as dependências ao usar esse contêiner, que instancia um `IOrderService` objeto e o injeta na `ProfileViewModel` classe.

![Exemplo de dependências ao usar injeção de dependência](dependency-injection-images/dependencyinjection.png)

**Figura 3-1:** Dependências ao usar injeção de dependência

Em tempo de execução, o contêiner deve saber qual implementação da `IOrderService` interface ela deve instanciar, antes de poder criar uma instância de um `ProfileViewModel` objeto. Isso envolve:

- O contêiner que decide como criar uma instância de um objeto que implementa a `IOrderService` interface. Isso é conhecido como *registro*.
- O contêiner que instancia o objeto que implementa a `IOrderService` interface e o `ProfileViewModel` objeto. Isso é conhecido como *resolução*.

Eventualmente, o aplicativo terminará de usar o `ProfileViewModel` objeto e ele ficará disponível para coleta de lixo. Neste ponto, o coletor de lixo deve descartar a `IOrderService` instância se outras classes não compartilharem a mesma instância.

> [!TIP]
> Gravar código independente de contêiner. Sempre tente escrever código independente de contêiner para desacoplar o aplicativo do contêiner de dependência específico que está sendo usado.

## <a name="registration"></a>Registro

Antes que as dependências possam ser injetadas em um objeto, os tipos das dependências devem primeiro ser registrados com o contêiner. O registro de um tipo normalmente envolve a passagem de um contêiner de uma interface e um tipo concreto que implementa a interface.

Há duas maneiras de registrar tipos e objetos no contêiner por meio de código:

- Registre um tipo ou mapeamento com o contêiner. Quando necessário, o contêiner criará uma instância do tipo especificado.
- Registre um objeto existente no contêiner como um singleton. Quando necessário, o contêiner retornará uma referência ao objeto existente.

> [!TIP]
> Contêineres de injeção de dependência nem sempre são adequados. A injeção de dependência apresenta mais complexidade e requisitos que podem não ser apropriados ou úteis para aplicativos pequenos. Se uma classe não tiver nenhuma dependência ou não for uma dependência para outros tipos, talvez não faça sentido colocá-la no contêiner. Além disso, se uma classe tiver um único conjunto de dependências que são integrais para o tipo e nunca serão alteradas, talvez não faça sentido colocá-la no contêiner.

O registro de tipos que exigem injeção de dependência deve ser executado em um único método em um aplicativo, e esse método deve ser invocado no início do ciclo de vida do aplicativo para garantir que o aplicativo esteja ciente das dependências entre suas classes. No aplicativo móvel eShopOnContainers, isso é executado pela `ViewModelLocator` classe, que cria o `TinyIoCContainer` objeto e é a única classe no aplicativo que contém uma referência a esse objeto. O exemplo de código a seguir mostra como o aplicativo móvel eShopOnContainers declara o `TinyIoCContainer` objeto na `ViewModelLocator` classe:

```csharp
private static TinyIoCContainer _container;
```

Os tipos são registrados no `ViewModelLocator` Construtor. Isso é obtido primeiro com a criação de uma `TinyIoCContainer` instância, que é demonstrada no exemplo de código a seguir:

```csharp
_container = new TinyIoCContainer();
```

Os tipos são então registrados com o `TinyIoCContainer` objeto, e o exemplo de código a seguir demonstra a forma mais comum do registro de tipo:

```csharp
_container.Register<IRequestProvider, RequestProvider>();
```

O `Register` método mostrado aqui mapeia um tipo de interface para um tipo concreto. Por padrão, cada registro de interface é configurado como um singleton para que cada objeto dependente receba a mesma instância compartilhada. Portanto, apenas uma única `RequestProvider` instância existirá no contêiner, que é compartilhada por objetos que exigem uma injeção de um `IRequestProvider` por meio de um construtor.

Os tipos concretos também podem ser registrados diretamente sem um mapeamento de um tipo de interface, conforme mostrado no exemplo de código a seguir:

```csharp
_container.Register<ProfileViewModel>();
```

Por padrão, cada registro de classe concreto é configurado como uma instância múltipla para que cada objeto dependente receba uma nova instância. Portanto, quando o `ProfileViewModel` for resolvido, uma nova instância será criada e o contêiner injetará suas dependências necessárias.

## <a name="resolution"></a>Resolução

Depois que um tipo é registrado, ele pode ser resolvido ou injetado como uma dependência. Quando um tipo está sendo resolvido e o contêiner precisa criar uma nova instância, ele injeta quaisquer dependências na instância.

Geralmente, quando um tipo é resolvido, uma das três coisas acontece:

1. Se o tipo não foi registrado, o contêiner gera uma exceção.
1. Se o tipo tiver sido registrado como um singleton, o contêiner retornará a instância singleton. Se esta for a primeira vez que o tipo é chamado para, o contêiner a cria, se necessário, e mantém uma referência a ele.
1. Se o tipo não tiver sido registrado como um singleton, o contêiner retornará uma nova instância e não manterá uma referência a ele.

O exemplo de código a seguir mostra como o `RequestProvider` tipo que foi registrado anteriormente com TinyIoC pode ser resolvido:

```csharp
var requestProvider = _container.Resolve<IRequestProvider>();
```

Neste exemplo, TinyIoC é solicitado a resolver o tipo concreto para o `IRequestProvider` tipo, juntamente com quaisquer dependências. Normalmente, o `Resolve` método é chamado quando uma instância de um tipo específico é necessária. Para obter informações sobre como controlar o tempo de vida de objetos resolvidos, consulte [Gerenciando o tempo de vida de objetos resolvidos](#managing-the-lifetime-of-resolved-objects).

O exemplo de código a seguir mostra como o aplicativo móvel eShopOnContainers instancia tipos de modelo de exibição e suas dependências:

```csharp
var viewModel = _container.Resolve(viewModelType);
```

Neste exemplo, TinyIoC é solicitado a resolver o tipo de modelo de exibição para um modelo de exibição solicitado, e o contêiner também resolverá quaisquer dependências. Ao resolver o `ProfileViewModel` tipo, as dependências a serem resolvidas são um `ISettingsService` objeto e um `IOrderService` objeto. Como os registros de interface foram usados ao registrar as `SettingsService` `OrderService` classes e, TinyIoC retorna instâncias singleton para as `SettingsService` `OrderService` classes e e passa-as para o construtor da `ProfileViewModel` classe. Para obter mais informações sobre como o aplicativo móvel eShopOnContainers constrói modelos de exibição e os associa a exibições, consulte [criando automaticamente um modelo de exibição com um localizador de modelo de exibição](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically-creating-a-view-model-with-a-view-model-locator).

> [!NOTE]
> Efetuar o registro e a resolução de tipos usando um contêiner tem um custo de desempenho devido ao uso da reflexão pelo contêiner para criar cada tipo, especialmente se as dependências estiverem sendo reconstruídas para cada navegação de página no aplicativo. Se houver muitas dependências ou se elas forem profundas, o custo da criação poderá aumentar significativamente.

## <a name="managing-the-lifetime-of-resolved-objects"></a>Gerenciando o tempo de vida de objetos resolvidos

Depois de registrar um tipo usando um registro de classe concreto, o comportamento padrão para TinyIoC é criar uma nova instância do tipo registrado cada vez que o tipo é resolvido, ou quando o mecanismo de dependência injeta instâncias em outras classes. Nesse cenário, o contêiner não mantém uma referência ao objeto resolvido. No entanto, ao registrar um tipo usando o registro de interface, o comportamento padrão do TinyIoC é gerenciar o tempo de vida do objeto como um singleton. Portanto, a instância permanece no escopo enquanto o contêiner está no escopo e é descartada quando o contêiner sai do escopo e é coletado pelo lixo, ou quando o código descarta explicitamente o contêiner.

O comportamento de registro TinyIoC padrão pode ser substituído usando os `AsSingleton` métodos Fluent e `AsMultiInstance` API. Por exemplo, o `AsSingleton` método pode ser usado com o `Register` método, para que o contêiner crie ou retorne uma instância singleton de um tipo ao chamar o `Resolve` método. O exemplo de código a seguir mostra como TinyIoC é instruído a criar uma instância singleton da `LoginViewModel` classe:

```csharp
_container.Register<LoginViewModel>().AsSingleton();
```

Na primeira vez que o `LoginViewModel` tipo é resolvido, o contêiner cria um novo `LoginViewModel` objeto e mantém uma referência a ele. Em qualquer resoluções subsequentes do `LoginViewModel` , o contêiner retorna uma referência ao `LoginViewModel` objeto que foi criado anteriormente.

> [!NOTE]
> Os tipos que são registrados como singletons são descartados quando o contêiner é Descartado.

## <a name="summary"></a>Resumo

A injeção de dependência permite o desacoplamento de tipos concretos do código que depende desses tipos. Normalmente, ele usa um contêiner que contém uma lista de registros e mapeamentos entre interfaces e tipos abstratos e os tipos concretos que implementam ou estendem esses tipos.

O TinyIoC é um contêiner leve que apresenta desempenho superior em plataformas móveis em comparação com a maioria dos contêineres bem conhecidos. Ele facilita a criação de aplicativos menos rígidos e fornece todos os recursos normalmente encontrados em contêineres de injeção de dependência, incluindo métodos para registrar mapeamentos de tipo, resolver objetos, gerenciar tempos de vida de objeto e injetar objetos dependentes em construtores de objetos resolvidos.

## <a name="related-links"></a>Links Relacionados

- [Download do eBook (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
