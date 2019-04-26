---
title: Aplicativos empresariais testes de unidade
description: Este capítulo explica como os testes de unidade é executada no aplicativo móvel do eShopOnContainers.
ms.prod: xamarin
ms.assetid: 4af82e52-f99b-4cad-b278-1745f190c240
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 02aeedd5498c47950e2fbc0d218de05bc0bb3204
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61298957"
---
# <a name="unit-testing-enterprise-apps"></a>Aplicativos empresariais testes de unidade

Aplicativos móveis têm problemas exclusivos e área de trabalho e aplicativos baseados na web não precisam se preocupar sobre. Os usuários móveis diferirão de acordo com os dispositivos que eles usam, pela conectividade de rede, a disponibilidade de serviços e uma variedade de outros fatores. Portanto, os aplicativos móveis devem ser testados como eles serão usados no mundo real para melhorar sua qualidade, confiabilidade e desempenho. Há muitos tipos de testes que devem ser executados em um aplicativo, incluindo testes de unidade, testes de integração e testes, com testes que está sendo a forma mais comum de teste de unidade de interface do usuário.

Um teste de unidade usa uma unidade pequena do aplicativo, normalmente um método, seja isolado do restante do código e verifica se ele se comporta conforme o esperado. Sua meta é verificar o que cada unidade de funcionalidade executa conforme o esperado, para que os erros não propagam em todo o aplicativo. Detectar um bug em que ele ocorre é mais eficiente do que observar o efeito de um bug indiretamente em um ponto secundário da falha.

Teste de unidade tem o maior efeito sobre a qualidade do código quando ele é parte integrante do fluxo de trabalho de desenvolvimento de software. Assim que um método foi escrito, testes de unidade devem ser escritos que verificam o comportamento do método em resposta a standard, limites e casos incorretos de dados de entrada e essa seleção suposições explícitas ou implícitas feitas pelo código. Como alternativa, com o desenvolvimento orientado por testes, testes de unidade são gravados antes do código. Nesse cenário, os testes de unidade atuam como documentação de design e as especificações funcionais.

> [!NOTE]
> Testes de unidade são muito eficientes contra regressão – ou seja, a funcionalidade que costumava funcionar, mas tem sido afetada por uma atualização com falha.

Testes de unidade geralmente usam o padrão de organizar-act assert:

-   O *organizar* inicializa os objetos de seção do método de teste de unidade e define o valor dos dados que são passados para o método em teste.
-   O *atuar* seção invoca o método em teste com os argumentos necessários.
-   O *assert* seção verifica se a ação do método em teste se comporta conforme o esperado.

Seguindo esse padrão garante que os testes de unidade são legíveis e consistente.

## <a name="dependency-injection-and-unit-testing"></a>Injeção de dependência e testes de unidade

Uma das motivações para adotar uma arquitetura menos rígida é que ele facilita o teste de unidade. Um dos tipos registrados com o Autofac é o `OrderService` classe. O exemplo de código a seguir mostra uma estrutura de tópicos desta classe:

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

O `OrderDetailViewModel` classe tem uma dependência a `IOrderService` que o contêiner resolve quando ele cria uma instância de tipo um `OrderDetailViewModel` objeto. No entanto, em vez de criar uma `OrderService` objeto de teste de unidade de `OrderDetailViewModel` classe, em vez disso, substitua o `OrderService` objeto com uma simulação para fins de testes. Figura 10-1 ilustra essa relação.

![](unit-testing-images/unittesting.png "Classes que implementam a interface IOrderService")

**Figura 10-1:** Classes que implementam a interface IOrderService

Essa abordagem permite que o `OrderService` objeto a serem passados para o `OrderDetailViewModel` de classe no tempo de execução e em interesses da capacidade de teste, ele permite que o `OrderMockService` classe a serem passados para o `OrderDetailViewModel` classe no tempo de teste. A principal vantagem dessa abordagem é que ele permite que os testes de unidade a ser executado sem a necessidade de recursos complicados como serviços web ou bancos de dados.

## <a name="testing-mvvm-applications"></a>Testando aplicativos MVVM

Teste de modelos e modelos de exibição de aplicativos MVVM é idêntico ao teste de outras classes e as mesmas ferramentas e técnicas – como o teste de unidade e de simulação, podem ser usadas. No entanto, há alguns padrões que são comuns ao modelo e classes de modelo de exibição, que podem se beneficiar das técnicas de teste de unidade específica.

> [!TIP]
> Uma coisa com cada teste de unidade de teste. Não fique tentado a fazer com que uma unidade de teste exercício mais de um aspecto do comportamento da unidade. Isso leva a testes que são difíceis de ler e atualizar. Ele também pode gerar confusão ao interpretar uma falha.

Os usos de aplicativo móvel do eShopOnContainers [xUnit](https://xunit.github.io/) para executar testes de unidade, que dá suporte a dois tipos diferentes de testes de unidade:

-   Fatos são testes que sempre são verdadeiros, que testam condições invariáveis.
-   Teorias são testes que só são verdadeiros para um determinado conjunto de dados.

Os testes de unidade incluídos com o aplicativo móvel do eShopOnContainers são testes de fato e, portanto, cada método de teste de unidade é decorado com o `[Fact]` atributo.

> [!NOTE]
> testes xUnit são executados por um executor de teste. Para executar o executor de teste, execute o projeto eShopOnContainers.TestRunner para plataforma necessária.

### <a name="testing-asynchronous-functionality"></a>Teste de funcionalidade assíncrona

Ao implementar o padrão MVVM, modelos de exibição normalmente invocação operações em serviços, muitas vezes de forma assíncrona. Testes para código que invoca a essas operações normalmente usam objetos fictícios como substituições para os serviços reais. O exemplo de código a seguir demonstra o teste da funcionalidade assíncrona, passando um serviço fictício para um modelo de exibição:

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

Esse teste de unidade verifica se o `Order` propriedade do `OrderDetailViewModel` instância terá um valor após o `InitializeAsync` método foi invocado. O `InitializeAsync` método é invocado quando o modo de exibição do modelo de exibição correspondente é navegado. Para obter mais informações sobre navegação, consulte [navegação](~/xamarin-forms/enterprise-application-patterns/navigation.md).

Quando o `OrderDetailViewModel` instância é criada, ela espera um `OrderService` instância a ser especificado como um argumento. No entanto, o `OrderService` recupera dados de um serviço web. Portanto, uma `OrderMockService` instância, que é uma versão fictícia do `OrderService` classe, é especificado como o argumento para o `OrderDetailViewModel` construtor. Então, quando o modelo de exibição `InitializeAsync` método é invocado, que invoca `IOrderService` operações, os dados fictícios são recuperado em vez de se comunicando com um serviço web.

### <a name="testing-inotifypropertychanged-implementations"></a>Teste de implementações de INotifyPropertyChanged

Implementando o `INotifyPropertyChanged` interface permite que os modos de exibição reagir a alterações originadas na exibição de modelos e modelos. Essas alterações não são limitadas aos dados mostrados em controles – eles também são usados para controlar o modo de exibição, como estados de exibição de modelo que fazem com que controles a ser desabilitado ou animações a serem iniciados.

Propriedades que podem ser atualizadas diretamente pelo teste de unidade podem ser testadas, anexando um manipulador de eventos para o `PropertyChanged` evento e verificando se o evento é gerado depois de definir um novo valor para a propriedade. O exemplo de código a seguir mostra esse teste:

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

Invoca esse teste de unidade do `InitializeAsync` método da `OrderViewModel` classe, que faz com que seu `Order` propriedade a ser atualizada. O teste de unidade passará, desde que o `PropertyChanged` é gerado para o `Order` propriedade.

### <a name="testing-message-based-communication"></a>Testando a comunicação baseada em mensagens

Modelos de exibição que usam o [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) classe para se comunicar entre classes flexível pode ser unidade testada ao assinar a mensagem sendo enviada pelo código em teste, conforme demonstrado no exemplo de código a seguir:

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

Esse teste de unidade verifica se o `CatalogViewModel` publica o `AddProduct` mensagem em resposta a sua `AddCatalogItemCommand` que está sendo executado. Porque o [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) classe dá suporte a assinaturas de mensagem multicast, o teste de unidade pode assinar o `AddProduct` da mensagem e executar um delegado de retorno de chamada em resposta a recebê-lo. Esse delegado de retorno de chamada, especificado como uma expressão lambda, define uma `boolean` campo é usado pelo `Assert` instrução para verificar o comportamento do teste.

### <a name="testing-exception-handling"></a>Teste de manipulação de exceção

Testes de unidade também podem ser escritos nessa verificação geradas exceções específicas para ações inválidas ou entradas, como demonstrado no exemplo de código a seguir:

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

Esse teste de unidade gerará uma exceção, porque o [ `ListView` ](xref:Xamarin.Forms.ListView) controle não tem um evento chamado `OnItemTapped`. O `Assert.Throws<T>` é um método genérico onde `T` é o tipo da exceção esperada. O argumento passado para o `Assert.Throws<T>` método é uma expressão lambda que lançará a exceção. Portanto, o teste de unidade passará desde que a expressão lambda lançar um `ArgumentException`.

>💡 **Dica**: Evite escrever testes de unidade que examinar cadeias de caracteres de mensagem de exceção. Cadeias de caracteres de mensagem de exceção pode ser alterado ao longo do tempo e, portanto, os testes de unidade que dependem de sua presença são consideradas como frágil.

### <a name="testing-validation"></a>Teste de validação

Há dois aspectos para testar a implementação de validação: teste de qualquer regra de validação sejam corretamente implementada e teste que o `ValidatableObject<T>` classe executa conforme o esperado.

Lógica de validação é geralmente simple testar, porque geralmente é um processo independente de onde a saída depende de entrada. Deve haver testes nos resultados da invocação de `Validate` método em cada propriedade que tem pelo menos uma regra de validação associados, conforme demonstrado no exemplo de código a seguir:

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

Esse teste de unidade verificações de validação é bem-sucedida quando as duas `ValidatableObject<T>` propriedades no `MockViewModel` instância as duas têm dados.

Bem como a verificação de validação for bem-sucedida, os testes de unidade de validação também devem verificar os valores da `Value`, `IsValid`, e `Errors` propriedade de cada `ValidatableObject<T>` instância, para verificar que a classe executa conforme o esperado. O exemplo de código a seguir demonstra um teste de unidade que faz isso:

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

Esse teste de unidade que verifica se a validação falhar quando o `Surname` propriedade do `MockViewModel` não tem quaisquer dados e o `Value`, `IsValid`, e `Errors` propriedade de cada `ValidatableObject<T>` instância estão definidas corretamente.

## <a name="summary"></a>Resumo

Um teste de unidade usa uma unidade pequena do aplicativo, normalmente um método, seja isolado do restante do código e verifica se ele se comporta conforme o esperado. Sua meta é verificar o que cada unidade de funcionalidade executa conforme o esperado, para que os erros não propagam em todo o aplicativo.

O comportamento de um objeto sob teste pode ser isolado, substituindo os objetos dependentes com objetos fictícios que simulam o comportamento dos objetos dependentes. Isso permite que os testes de unidade a ser executado sem a necessidade de recursos complicados como serviços web ou bancos de dados.

Teste de modelos e modelos de exibição de aplicativos MVVM é idêntico ao teste de outras classes e as mesmas ferramentas e técnicas podem ser usadas.


## <a name="related-links"></a>Links relacionados

- [Baixe o livro eletrônico (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (amostra)](https://github.com/dotnet-architecture/eShopOnContainers)
