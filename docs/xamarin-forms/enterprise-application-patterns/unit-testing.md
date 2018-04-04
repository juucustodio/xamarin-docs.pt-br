---
title: Teste de unidade
ms.prod: xamarin
ms.assetid: 4af82e52-f99b-4cad-b278-1745f190c240
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 57201a32f5ffc0ae962f6db851a25a737e1cb17d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="unit-testing"></a>Teste de unidade

Aplicativos móveis têm problemas específicos que a área de trabalho e aplicativos baseados na web não precisam se preocupar sobre. Usuários móveis variam por dispositivos que eles usam, por conectividade de rede, a disponibilidade de serviços e uma variedade de outros fatores. Portanto, os aplicativos móveis devem ser testados como eles serão usados no mundo real para melhorar sua qualidade, confiabilidade e desempenho. Há muitos tipos de testes que devem ser executados em um aplicativo, incluindo testes de unidade, testes de integração e teste com testes sendo a forma mais comum de teste de unidade de interface do usuário.

Um teste de unidade entra em uma unidade pequena do aplicativo, normalmente um método, isola do restante do código e verifica que se comporta como esperado. Sua meta é verificar se cada unidade de funcionalidade executa conforme o esperado, para que os erros não se propague através do aplicativo. Detectar um bug onde ele ocorre é mais eficiente do que observar o efeito de um bug indiretamente em um secundário ponto de falha.

Teste de unidade tem o maior efeito sobre a qualidade do código quando ele é parte integral do fluxo de trabalho de desenvolvimento de software. Assim que um método foi escrito, testes de unidade devem ser escritos que verificar o comportamento do método em resposta a standard, limites e casos incorretos de dados de entrada e essa seleção suposições explícitas ou implícitas feitas pelo código. Como alternativa, com teste desenvolvimento controlado por testes de unidade são gravados antes do código. Nesse cenário, testes de unidade atuam como documentação de design e especificações funcionais.

> [!NOTE]
> Testes de unidade são muito eficientes em relação a regressão – ou seja, a funcionalidade que costumava funcionar, mas tiver sido afetada por uma atualização com falha.

Testes de unidade geralmente usam o ato de organizar-declaração padrão:

-   O *organizar* seção do método de teste de unidade inicializa objetos e define o valor dos dados que são passados para o método de teste.
-   O *agir* seção invoca o método em teste com os argumentos necessários.
-   O *assert* seção verifica a ação do método em teste se comporta como esperado.

Seguindo esse padrão garante que os testes de unidade são legíveis e consistente.

## <a name="dependency-injection-and-unit-testing"></a>Injeção de dependência e o teste de unidade

Uma das motivações para adotar a uma arquitetura flexível é que ele facilita o teste de unidade. Um dos tipos registrados com Autofac é o `OrderService` classe. O exemplo de código a seguir mostra uma estrutura de tópicos desta classe:

```csharp
public class OrderDetailViewModel : ViewModelBase  
{  
    private IOrderService _ordersService;  

    public OrderDetailViewModel(IOrderService ordersService)  
    {  
        _ordersService = ordersService;  
    }  
    ...  
}
```

O `OrderDetailViewModel` classe tem uma dependência de `IOrderService` que o contêiner resolve quando ele cria uma instância de tipo um `OrderDetailViewModel` objeto. No entanto, em vez de criar um `OrderService` objeto de teste de unidade de `OrderDetailViewModel` classe, em vez disso, substitua o `OrderService` objeto com uma simulação para fins de testes. Figura 10-1 ilustra essa relação.

![](unit-testing-images/unittesting.png "Classes que implementam a interface IOrderService")

**Figura 10-1:** Classes que implementam a interface IOrderService

Essa abordagem permite que o `OrderService` objeto a serem passados para o `OrderDetailViewModel` de classe em tempo de execução e questões de capacidade de teste, ele permite que o `OrderMockService` classe a serem passados para o `OrderDetailViewModel` classe em tempo de teste. A principal vantagem dessa abordagem é que ela permite que os testes de unidade a ser executado sem a necessidade de recursos complicados, como serviços web, ou bancos de dados.

## <a name="testing-mvvm-applications"></a>Testando aplicativos MVVM

Testando modelos e exibir modelos de aplicativos MVVM é idêntico de outras classes de teste e as mesmas ferramentas e técnicas – como teste de unidade e fictícias, podem ser usadas. No entanto, há alguns padrões comuns para o modelo e classes de modelo de exibição, que podem se beneficiar das técnicas de teste de unidade específica.

> [!TIP]
> Teste uma coisa com cada teste de unidade. Não se tentado a fazer com que uma unidade de teste exercício mais de um aspecto do comportamento da unidade. Isso leva para testes que são difíceis de ler e atualizar. Ele também pode causar confusão ao interpretar uma falha.

Os usos de aplicativo móvel eShopOnContainers [xUnit](https://xunit.github.io/) para executar testes de unidade, que dá suporte a dois tipos diferentes de testes de unidade:

-   Fatos são testes que são sempre true, que invariável condições de teste.
-   Teorias são testes que só são verdadeiros para um determinado conjunto de dados.

Os testes de unidade incluídos com o aplicativo móvel eShopOnContainers são testes de fato e, para cada método de teste de unidade é decorado com o `[Fact]` atributo.

> [!NOTE]
> xUnit testes são executados por um executor de teste. Para executar o executor de teste, execute o projeto eShopOnContainers.TestRunner para plataforma necessária.

### <a name="testing-asynchronous-functionality"></a>Teste da funcionalidade assíncrona

Ao implementar o padrão MVVM, exibir modelos normalmente invocar operações em serviços, geralmente de forma assíncrona. Testes do código que chama essas operações normalmente usam as simulações como substituições para os serviços reais. O exemplo de código a seguir demonstra o teste da funcionalidade assíncrona, passando um serviço simulações em um modelo de exibição:

```csharp
[Fact]  
public async Task OrderPropertyIsNotNullAfterViewModelInitializationTest()  
{  
    var orderService = new OrderMockService();  
    var orderViewModel = new OrderDetailViewModel(orderService);  

    var order = await orderService.GetOrderAsync(1, GlobalSetting.Instance.AuthToken);  
    await orderViewModel.InitializeAsync(order);  

    Assert.NotNull(orderViewModel.Order);  
}
```

Esse teste de unidade verifica se o `Order` propriedade o `OrderDetailViewModel` instância terá um valor após o `InitializeAsync` método foi chamado. O `InitializeAsync` método é invocado quando navega para a exibição correspondente do modelo de exibição. Para obter mais informações sobre a navegação, consulte [navegação](~/xamarin-forms/enterprise-application-patterns/navigation.md).

Quando o `OrderDetailViewModel` instância é criada, ela espera um `OrderService` instância a ser especificado como um argumento. No entanto, o `OrderService` recupera dados de um serviço web. Portanto, um `OrderMockService` instância, que é uma versão fictícia do `OrderService` de classe, que é especificado como o argumento para o `OrderDetailViewModel` construtor. Então, quando o modelo de exibição `InitializeAsync` método é chamado, que invoca `IOrderService` dados fictícios de operações, são recuperado em vez de se comunicando com um serviço web.

### <a name="testing-inotifypropertychanged-implementations"></a>Implementações de teste INotifyPropertyChanged

Implementando o `INotifyPropertyChanged` interface permite que os modos de exibição reagir a alterações que se originam da exibição de modelos e modelos. Essas alterações não são limitadas aos dados mostrados em controles – eles também são usados para controlar o modo de exibição, como estados de modelo de exibição que causam animações a serem iniciados ou controles a ser desabilitado.

Propriedades que podem ser atualizadas diretamente, o teste de unidade podem ser testadas com a anexação de um manipulador de eventos para o `PropertyChanged` evento e verificando se o evento é gerado depois de definir um novo valor para a propriedade. O exemplo de código a seguir mostra um teste tal:

```csharp
[Fact]  
public async Task SettingOrderPropertyShouldRaisePropertyChanged()  
{  
    bool invoked = false;  
    var orderService = new OrderMockService();  
    var orderViewModel = new OrderDetailViewModel(orderService);  

    orderViewModel.PropertyChanged += (sender, e) =>  
    {  
        if (e.PropertyName.Equals("Order"))  
            invoked = true;  
    };  
    var order = await orderService.GetOrderAsync(1, GlobalSetting.Instance.AuthToken);  
    await orderViewModel.InitializeAsync(order);  

    Assert.True(invoked);  
}
```

Esse teste de unidade chama o `InitializeAsync` método o `OrderViewModel` classe, que faz com que seu `Order` propriedade a ser atualizada. O teste de unidade passará, contanto que o `PropertyChanged` é gerado para o `Order` propriedade.

### <a name="testing-message-based-communication"></a>Testando a comunicação baseada em mensagens

Exibição de modelos que usam o [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) classe para se comunicar entre classes flexível unidade testar assinando a mensagem enviada pelo código em teste, conforme demonstrado no exemplo de código a seguir:

```csharp
[Fact]  
public void AddCatalogItemCommandSendsAddProductMessageTest()  
{  
    bool messageReceived = false;  
    var catalogService = new CatalogMockService();  
    var catalogViewModel = new CatalogViewModel(catalogService);  

    Xamarin.Forms.MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
        this, MessageKeys.AddProduct, (sender, arg) =>  
    {  
        messageReceived = true;  
    });  
    catalogViewModel.AddCatalogItemCommand.Execute(null);  

    Assert.True(messageReceived);  
}
```

Esse teste de unidade verifica se o `CatalogViewModel` publica o `AddProduct` mensagem em resposta a seu `AddCatalogItemCommand` que está sendo executada. Porque o [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) classe oferece suporte a assinaturas de mensagem multicast, o teste de unidade pode assinar o `AddProduct` da mensagem e executar um representante de retorno de chamada em resposta a recebê-lo. Este representante de retorno de chamada, especificado como uma expressão lambda, define um `boolean` campo que é usado pelo `Assert` instrução para verificar o comportamento do teste.

### <a name="testing-exception-handling"></a>Teste o tratamento de exceção

Testes de unidade também podem ser gravados nessa verificação geradas exceções específicas para ações inválidas ou entradas, como demonstrado no exemplo de código a seguir:

```csharp
[Fact]  
public void InvalidEventNameShouldThrowArgumentExceptionText()  
{  
    var behavior = new MockEventToCommandBehavior  
    {  
        EventName = "OnItemTapped"  
    };  
    var listView = new ListView();  

    Assert.Throws<ArgumentException>(() => listView.Behaviors.Add(behavior));  
}
```

Esse teste de unidade gerará uma exceção, porque o [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) controle não tem um evento chamado `OnItemTapped`. O `Assert.Throws<T>` é um método genérico onde `T` é o tipo da exceção esperada. O argumento passado para o `Assert.Throws<T>` método é uma expressão lambda que lançará a exceção. Portanto, o teste de unidade passará desde que a expressão lambda lança um `ArgumentException`.

>💡 **Dica**: evite escrever testes de unidade que examinar cadeias de caracteres de mensagem de exceção. Cadeias de caracteres de mensagem de exceção podem ser alterado ao longo do tempo e, portanto os testes de unidade que dependem de sua presença são considerados frágil.

### <a name="testing-validation"></a>Testes de validação

Há dois aspectos para testar a implementação de validação: teste que todas as regras de validação são implementadas corretamente e o teste que o `ValidatableObject<T>` classe executa conforme o esperado.

Lógica de validação é geralmente simple testar, porque normalmente ele é um processo independente de onde a saída depende da entrada. Deve haver testes nos resultados de chamar o `Validate` método em cada propriedade que tenha pelo menos uma regra de validação associados, como demonstrado no exemplo de código a seguir:

```csharp
[Fact]  
public void CheckValidationPassesWhenBothPropertiesHaveDataTest()  
{  
    var mockViewModel = new MockViewModel();  
    mockViewModel.Forename.Value = "John";  
    mockViewModel.Surname.Value = "Smith";  

    bool isValid = mockViewModel.Validate();  

    Assert.True(isValid);  
}
```

Esse teste de unidade verifica se a validação é bem-sucedida quando as duas `ValidatableObject<T>` propriedades na `MockViewModel` instância ambos têm dados.

Bem como a verificação de validação for bem-sucedida, os testes de unidade de validação também devem verificar os valores da `Value`, `IsValid`, e `Errors` propriedade de cada `ValidatableObject<T>` instância, para verificar se a classe executa conforme o esperado. O exemplo de código a seguir demonstra um teste de unidade que faz isso:

```csharp
[Fact]  
public void CheckValidationFailsWhenOnlyForenameHasDataTest()  
{  
    var mockViewModel = new MockViewModel();  
    mockViewModel.Forename.Value = "John";  

    bool isValid = mockViewModel.Validate();  

    Assert.False(isValid);  
    Assert.NotNull(mockViewModel.Forename.Value);  
    Assert.Null(mockViewModel.Surname.Value);  
    Assert.True(mockViewModel.Forename.IsValid);  
    Assert.False(mockViewModel.Surname.IsValid);  
    Assert.Empty(mockViewModel.Forename.Errors);  
    Assert.NotEmpty(mockViewModel.Surname.Errors);  
}
```

Esse teste de unidade verifica se a validação falhar quando o `Surname` propriedade o `MockViewModel` não tem nenhum dado e o `Value`, `IsValid`, e `Errors` propriedade de cada `ValidatableObject<T>` instância estão definidas corretamente.

## <a name="summary"></a>Resumo

Um teste de unidade entra em uma unidade pequena do aplicativo, normalmente um método, isola do restante do código e verifica que se comporta como esperado. Sua meta é verificar se cada unidade de funcionalidade executa conforme o esperado, para que os erros não se propague através do aplicativo.

O comportamento de um objeto em teste pode ser isolado, substituindo os objetos dependentes com objetos fictícios que simulam o comportamento dos objetos dependentes. Isso permite que os testes de unidade a ser executado sem a necessidade de recursos complicados, como serviços web, ou bancos de dados.

Testando modelos e exibir modelos de aplicativos MVVM é idêntico de outras classes de teste e as mesmas ferramentas e técnicas podem ser usadas.


## <a name="related-links"></a>Links relacionados

- [Baixar eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
