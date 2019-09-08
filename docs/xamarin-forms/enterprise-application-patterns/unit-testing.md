---
title: Aplicativos empresariais de teste de unidade
description: Este capítulo explica como o teste de unidade é executado no aplicativo móvel eShopOnContainers.
ms.prod: xamarin
ms.assetid: 4af82e52-f99b-4cad-b278-1745f190c240
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 0fb63c650e73bce5a08b204f942f0c19583e4899
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770684"
---
# <a name="unit-testing-enterprise-apps"></a>Aplicativos empresariais de teste de unidade

Os aplicativos móveis têm problemas exclusivos que os aplicativos baseados na área de trabalho e na Web não precisam se preocupar. Os usuários móveis serão diferentes pelos dispositivos que usam, por conectividade de rede, pela disponibilidade de serviços e uma variedade de outros fatores. Portanto, os aplicativos móveis devem ser testados, pois serão usados no mundo real para melhorar sua qualidade, confiabilidade e desempenho. Há muitos tipos de teste que devem ser executados em um aplicativo, incluindo testes de unidade, testes de integração e testes de interface do usuário, com o teste de unidade sendo a forma mais comum de teste.

Um teste de unidade usa uma pequena unidade do aplicativo, normalmente um método, o isola do restante do código e verifica se ele se comporta conforme o esperado. Seu objetivo é verificar se cada unidade de funcionalidade é executada conforme o esperado, para que os erros não se propaguem por todo o aplicativo. Detectar um bug em que ele ocorre é mais eficiente, o que observa o efeito de um bug indiretamente em um ponto secundário de falha.

O teste de unidade tem o maior efeito na qualidade do código quando é parte integrante do fluxo de trabalho de desenvolvimento de software. Assim que um método é escrito, os testes de unidade devem ser escritos para verificar o comportamento do método em resposta a casos padrão, limites e incorretos de dados de entrada e que verificam quaisquer suposições explícitas ou implícitas feitas pelo código. Como alternativa, com o desenvolvimento controlado por teste, os testes de unidade são gravados antes do código. Nesse cenário, os testes de unidade atuam como documentação de design e especificações funcionais.

> [!NOTE]
> Os testes de unidade são muito eficientes em relação à regressão – ou seja, a funcionalidade que costumava funcionar, mas foi incomodada por uma atualização com falha.

Os testes de unidade normalmente usam o padrão Arrange-Act-Assert:

- A seção *Arrange* do método de teste de unidade inicializa objetos e define o valor dos dados que são passados para o método em teste.
- A seção *Act* invoca o método em teste com os argumentos necessários.
- A seção *Assert* verifica se a ação do método em teste se comporta conforme o esperado.

Seguir esse padrão garante que os testes de unidade sejam legíveis e consistentes.

## <a name="dependency-injection-and-unit-testing"></a>Injeção de dependência e teste de unidade

Uma das motivações para adotar uma arquitetura com acoplamento flexível é que ela facilita o teste de unidade. Um dos tipos registrados com Autofac é a `OrderService` classe. O exemplo de código a seguir mostra um contorno desta classe:

```csharp
public class OrderDetailViewModel : ViewModelBase  
{  
    private IOrderService _ordersService;  

    public OrderDetailViewModel(IOrderService ordersService)  
    {  
        _ordersService = ordersService;  
    }  
    ...  
}
```

A `OrderDetailViewModel` classe tem uma dependência `IOrderService` no tipo que o contêiner resolve ao criar uma instância de um `OrderDetailViewModel` objeto. No entanto, em vez `OrderService` de criar um objeto para `OrderDetailViewModel` testar unidade na classe, substitua `OrderService` o objeto por uma simulação para fins dos testes. A Figura 10-1 ilustra essa relação.

![](unit-testing-images/unittesting.png "Classes que implementam a interface IOrderService")

**Figura 10-1:** Classes que implementam a interface IOrderService

Essa abordagem permite que `OrderService` o objeto seja passado para a `OrderDetailViewModel` classe em tempo de execução e, nos interesses da capacidade de teste, ele `OrderMockService` permite que a classe seja passada `OrderDetailViewModel` para a classe no momento do teste. A principal vantagem dessa abordagem é que ela permite que os testes de unidade sejam executados sem a necessidade de recursos difíceis, como serviços Web ou bancos de dados.

## <a name="testing-mvvm-applications"></a>Testando aplicativos MVVM

Os modelos de teste e os modelos de exibição dos aplicativos MVVM são idênticos ao teste de todas as outras classes, e as mesmas ferramentas e técnicas, como o teste de unidade e a simulação, podem ser usadas. No entanto, há alguns padrões típicos para modelar e exibir classes de modelo, que podem se beneficiar de técnicas de teste de unidade específicas.

> [!TIP]
> Teste uma coisa com cada teste de unidade. Não fique tentado a fazer um teste de unidade exercer mais de um aspecto do comportamento da unidade. Isso leva a testes que são difíceis de ler e atualizar. Ele também pode levar à confusão ao interpretar uma falha.

O aplicativo móvel eShopOnContainers usa o [xUnit](https://xunit.github.io/) para executar testes de unidade, que oferece suporte a dois tipos diferentes de testes de unidade:

- Os fatos são testes que são sempre verdadeiros, que testam condições invariáveis.
- Teorias são testes que só são verdadeiros para um determinado conjunto de dados.

Os testes de unidade incluídos no aplicativo móvel eShopOnContainers são testes de fato e, portanto, cada método de teste de unidade `[Fact]` é decorado com o atributo.

> [!NOTE]
> os testes de xUnit são executados por um executor de teste. Para executar o Test Runner, execute o projeto eShopOnContainers. TestRunner para a plataforma necessária.

### <a name="testing-asynchronous-functionality"></a>Testando a funcionalidade assíncrona

Ao implementar o padrão MVVM, os modelos de exibição normalmente invocam operações em serviços, geralmente de forma assíncrona. Testes para o código que invoca essas operações normalmente usam simulações como substituições para os serviços reais. O exemplo de código a seguir demonstra o teste de funcionalidade assíncrona passando um serviço fictício para um modelo de exibição:

```csharp
[Fact]  
public async Task OrderPropertyIsNotNullAfterViewModelInitializationTest()  
{  
    var orderService = new OrderMockService();  
    var orderViewModel = new OrderDetailViewModel(orderService);  

    var order = await orderService.GetOrderAsync(1, GlobalSetting.Instance.AuthToken);  
    await orderViewModel.InitializeAsync(order);  

    Assert.NotNull(orderViewModel.Order);  
}
```

Esse teste `Order` `InitializeAsync` de unidade verifica se a propriedade da instânciateráumvalordepoisqueométodoforinvocado.`OrderDetailViewModel` O `InitializeAsync` método é invocado quando a exibição correspondente do modelo de exibição é navegada para. Para obter mais informações sobre navegação, consulte [Navigation](~/xamarin-forms/enterprise-application-patterns/navigation.md).

Quando a `OrderDetailViewModel` instância é criada, espera-se `OrderService` que uma instância seja especificada como um argumento. No entanto `OrderService` , o recupera dados de um serviço Web. Portanto, uma `OrderMockService` instância, que é uma versão fictícia `OrderService` da classe, é especificada como o argumento para o `OrderDetailViewModel` Construtor. Em seguida, quando o método do `InitializeAsync` modelo de exibição é invocado, `IOrderService` que invoca operações, os dados fictícios são recuperados em vez de se comunicar com um serviço Web.

### <a name="testing-inotifypropertychanged-implementations"></a>Testando implementações INotifyPropertyChanged

A implementação `INotifyPropertyChanged` da interface permite que as exibições reajam a alterações originadas em modelos de exibição e modelos. Essas alterações não são limitadas aos dados mostrados em controles – elas também são usadas para controlar a exibição, como exibir Estados de modelo que fazem com que as animações sejam iniciadas ou controles a serem desabilitados.

As propriedades que podem ser atualizadas diretamente pelo teste de unidade podem ser testadas anexando um manipulador `PropertyChanged` de eventos ao evento e verificando se o evento é gerado após a definição de um novo valor para a propriedade. O exemplo de código a seguir mostra um teste desse tipo:

```csharp
[Fact]  
public async Task SettingOrderPropertyShouldRaisePropertyChanged()  
{  
    bool invoked = false;  
    var orderService = new OrderMockService();  
    var orderViewModel = new OrderDetailViewModel(orderService);  

    orderViewModel.PropertyChanged += (sender, e) =>  
    {  
        if (e.PropertyName.Equals("Order"))  
            invoked = true;  
    };  
    var order = await orderService.GetOrderAsync(1, GlobalSetting.Instance.AuthToken);  
    await orderViewModel.InitializeAsync(order);  

    Assert.True(invoked);  
}
```

Esse teste `InitializeAsync` `Order` de unidade invoca o método da classe,oquefazcomquesuapropriedadesejaatualizada.`OrderViewModel` O teste de unidade será aprovado, desde que `PropertyChanged` o evento seja gerado para `Order` a propriedade.

### <a name="testing-message-based-communication"></a>Testando a comunicação baseada em mensagens

Os modelos de exibição que [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) usam a classe para se comunicar entre classes livremente acopladas podem ser testados por unidade assinando a mensagem que está sendo enviada pelo código sob teste, conforme demonstrado no exemplo de código a seguir:

```csharp
[Fact]  
public void AddCatalogItemCommandSendsAddProductMessageTest()  
{  
    bool messageReceived = false;  
    var catalogService = new CatalogMockService();  
    var catalogViewModel = new CatalogViewModel(catalogService);  

    Xamarin.Forms.MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
        this, MessageKeys.AddProduct, (sender, arg) =>  
    {  
        messageReceived = true;  
    });  
    catalogViewModel.AddCatalogItemCommand.Execute(null);  

    Assert.True(messageReceived);  
}
```

Esse teste de unidade verifica se `CatalogViewModel` o publica a `AddProduct` mensagem em resposta à sua `AddCatalogItemCommand` execução. Como a [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe dá suporte a assinaturas de mensagens de multicast, o teste de unidade pode `AddProduct` assinar a mensagem e executar um delegado de retorno de chamada em resposta para o recebimento. Esse delegado de retorno de chamada, especificado como uma expressão lambda `boolean` , define um campo que é `Assert` usado pela instrução para verificar o comportamento do teste.

### <a name="testing-exception-handling"></a>Testando manipulação de exceção

Os testes de unidade também podem ser escritos para verificar se as exceções específicas são geradas para ações ou entradas inválidas, conforme demonstrado no exemplo de código a seguir:

```csharp
[Fact]  
public void InvalidEventNameShouldThrowArgumentExceptionText()  
{  
    var behavior = new MockEventToCommandBehavior  
    {  
        EventName = "OnItemTapped"  
    };  
    var listView = new ListView();  

    Assert.Throws<ArgumentException>(() => listView.Behaviors.Add(behavior));  
}
```

Este teste de unidade gerará uma exceção, pois [`ListView`](xref:Xamarin.Forms.ListView) o controle não tem um evento chamado `OnItemTapped`. O `Assert.Throws<T>` método é um método genérico em `T` que é o tipo da exceção esperada. O argumento passado para o `Assert.Throws<T>` método é uma expressão lambda que gerará a exceção. Portanto, o teste de unidade será aprovado desde que a expressão lambda gere `ArgumentException`um.

> [!TIP]
> Evite escrever testes de unidade que examinem cadeias de caracteres de mensagem de exceção. As cadeias de caracteres de mensagem de exceção podem mudar com o tempo e, portanto, os testes de unidade que dependem de sua presença são considerados frágeis.

### <a name="testing-validation"></a>Testando a validação

Há dois aspectos para testar a implementação da validação: testar se as regras de validação foram implementadas corretamente e testar se `ValidatableObject<T>` a classe é executada conforme o esperado.

A lógica de validação é geralmente simples de testar, porque normalmente é um processo independente em que a saída depende da entrada. Deve haver testes nos resultados da invocação do `Validate` método em cada propriedade que tenha pelo menos uma regra de validação associada, conforme demonstrado no exemplo de código a seguir:

```csharp
[Fact]  
public void CheckValidationPassesWhenBothPropertiesHaveDataTest()  
{  
    var mockViewModel = new MockViewModel();  
    mockViewModel.Forename.Value = "John";  
    mockViewModel.Surname.Value = "Smith";  

    bool isValid = mockViewModel.Validate();  

    Assert.True(isValid);  
}
```

Esse teste de unidade verifica se a validação é realizada com `ValidatableObject<T>` sucesso quando as `MockViewModel` duas propriedades na instância têm dados.

Além disso, verificando se a validação foi realizada com sucesso, os testes de unidade de validação `Value`também `IsValid`devem verificar os valores da `ValidatableObject<T>` Propriedade, e `Errors` de cada instância, para verificar se a classe é executada conforme o esperado. O exemplo de código a seguir demonstra um teste de unidade que faz isso:

```csharp
[Fact]  
public void CheckValidationFailsWhenOnlyForenameHasDataTest()  
{  
    var mockViewModel = new MockViewModel();  
    mockViewModel.Forename.Value = "John";  

    bool isValid = mockViewModel.Validate();  

    Assert.False(isValid);  
    Assert.NotNull(mockViewModel.Forename.Value);  
    Assert.Null(mockViewModel.Surname.Value);  
    Assert.True(mockViewModel.Forename.IsValid);  
    Assert.False(mockViewModel.Surname.IsValid);  
    Assert.Empty(mockViewModel.Forename.Errors);  
    Assert.NotEmpty(mockViewModel.Surname.Errors);  
}
```

Esse `Surname` teste `Errors` `Value` `IsValid`de unidade verifica se a validação falha quando a propriedade de `ValidatableObject<T>` nãotemnenhumdado,eapropriedade,edecadainstânciaestádefinidacorretamente.`MockViewModel`

## <a name="summary"></a>Resumo

Um teste de unidade usa uma pequena unidade do aplicativo, normalmente um método, o isola do restante do código e verifica se ele se comporta conforme o esperado. Seu objetivo é verificar se cada unidade de funcionalidade é executada conforme o esperado, para que os erros não se propaguem por todo o aplicativo.

O comportamento de um objeto em teste pode ser isolado por meio da substituição de objetos dependentes por objetos fictícios que simulam o comportamento dos objetos dependentes. Isso permite que os testes de unidade sejam executados sem a necessidade de recursos difíceis, como serviços Web ou bancos de dados.

Os modelos de teste e os modelos de exibição dos aplicativos MVVM são idênticos ao teste de todas as outras classes, e as mesmas ferramentas e técnicas podem ser usadas.

## <a name="related-links"></a>Links relacionados

- [Download do eBook (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
