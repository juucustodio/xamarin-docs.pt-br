---
title: O padrão Model-View-ViewModel
description: Este capítulo explica como o aplicativo móvel do eShopOnContainers usa o padrão MVVM para separar a lógica de negócios e apresentação do aplicativo da sua interface do usuário de forma precisa.
ms.prod: xamarin
ms.assetid: dd8c1813-df44-4947-bcee-1a1ff2334b87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 87448c556c66ea086db70699848227e1f671792b
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59019380"
---
# <a name="the-model-view-viewmodel-pattern"></a>O padrão Model-View-ViewModel

A experiência do desenvolvedor de xamarin. Forms normalmente envolve a criação de uma interface do usuário em XAML e, em seguida, adicionando o code-behind que opera na interface do usuário. Conforme os aplicativos são modificados e crescem em tamanho e escopo, podem surgir problemas complexos de manutenção. Esses problemas incluem o acoplamento rígido entre os controles de interface do usuário e a lógica de negócios, o que aumenta o custo de fazer modificações de interface do usuário e a dificuldade de tal código de teste de unidade.

O padrão Model-View-ViewModel (MVVM) ajuda a separar a lógica de negócios e apresentação de um aplicativo de sua interface do usuário (UI) de forma precisa. Mantendo uma separação clara entre a lógica do aplicativo e a interface do usuário ajuda a resolver vários problemas de desenvolvimento e pode tornar mais fácil de testar um aplicativo, manter e evoluem. Ele pode também melhoram muito a oportunidades de reutilização de código e permite que os desenvolvedores e designers de interface do usuário mais facilmente colaboram durante o desenvolvimento de suas respectivas partes de um aplicativo.

## <a name="the-mvvm-pattern"></a>O padrão MVVM

Há três componentes principais do padrão MVVM: o modelo, o modo de exibição e o modelo de exibição. Cada uma tem uma finalidade distinta. Figura 2-1 mostra as relações entre os três componentes.

![](mvvm-images/mvvm.png "O padrão MVVM")

**Figura 2-1**: O padrão MVVM

Além de entender as responsabilidades de cada componente, também é importante entender como eles interagem entre si. Em um alto nível, o modo de exibição "conhece" modelo de exibição, o modelo de exibição "conhece" o modelo, mas o modelo não tem conhecimento do modelo de exibição e o modelo de exibição não tem conhecimento do modo de exibição. Portanto, o modelo de exibição isola o modo de exibição do modelo e permite que o modelo evoluir independentemente do modo de exibição.

Os benefícios de usar o padrão MVVM serão o seguinte:

-   Se houver uma implementação existente do modelo que encapsula a lógica de negócios existentes, pode ser difícil ou arriscados para alterá-lo. Nesse cenário, o modelo de exibição atua como um adaptador para as classes de modelo e permite que você evite fazer nenhuma alteração principal no código de modelo.
-   Os desenvolvedores podem criar testes de unidade para o modelo de exibição e o modelo, sem usar o modo de exibição. Os testes de unidade para o modelo de exibição podem exercer exatamente a mesma funcionalidade que foi usado por modo de exibição.
-   O interface do usuário do aplicativo pode ser reprojetado sem tocar no código, desde que a exibição é implementada inteiramente em XAML. Portanto, uma nova versão do modo de exibição deve funcionar com o modelo de exibição existente.
-   Designers e desenvolvedores podem trabalhar independentemente e ao mesmo tempo em seus componentes durante o processo de desenvolvimento. Designers podem focar na exibição, enquanto os desenvolvedores podem trabalhar no modelo de exibição e componentes do modelo.

A chave para uso eficiente de MVVM reside na compreensão de como incluir código de aplicativo para as classes corretas e compreender como as classes interagem. As seções a seguir discutem as responsabilidades de cada uma das classes no padrão MVVM.

### <a name="view"></a>Exibir

O modo de exibição é responsável por definir a estrutura, layout e aparência do que o usuário vê na tela. O ideal é que cada modo de exibição é definido em XAML, com um code-behind limitado que não contém lógica de negócios. No entanto, em alguns casos, o code-behind pode conter lógica de interface do usuário que implementa o comportamento visual que é difícil de expressar no XAML, como animações.

Em um aplicativo xamarin. Forms, uma exibição é normalmente um [ `Page` ](xref:Xamarin.Forms.Page)-derivado ou [ `ContentView` ](xref:Xamarin.Forms.ContentView)-classe derivada. No entanto, os modos de exibição também podem ser representados por um modelo de dados, que especifica os elementos de interface do usuário a ser usado para representar visualmente um objeto quando ele for exibido. Um modelo de dados como uma exibição não tem qualquer code-behind e é projetado para associar a um tipo de modelo de exibição específica.

> [!TIP]
> Evite habilitando e desabilitando elementos de interface do usuário no code-behind. Certifique-se de que os modelos de exibição são responsáveis por definir as alterações de estado lógico que afetam alguns aspectos da exibição do modo de exibição, como se um comando está disponível, ou uma indicação de que uma operação está pendente. Portanto, ativar e desativar elementos da interface do usuário por associação para exibir as propriedades do modelo, em vez de habilitando e desabilitando-os no code-behind.

Há várias opções para executar o código no modelo de exibição em resposta às interações do modo de exibição, como um clique de botão ou a seleção de item. Se um controle dá suporte a comandos, o controle `Command` propriedade pode ser associado a dados para um `ICommand` propriedade no modelo de exibição. Quando comando do controle é invocado, o código no modelo de exibição será executado. Além de comandos, os comportamentos podem ser anexados a um objeto no modo de exibição e podem escutar um evento a ser gerado ou o comando a ser invocado. Em resposta, o comportamento pode invocar um `ICommand` no modelo de exibição ou um método no modelo de exibição.

### <a name="viewmodel"></a>ViewModel

O modelo de exibição implementa propriedades e os comandos aos quais o modo de exibição pode associar dados e notifica a exibição de todas as alterações de estado por meio de eventos de notificação de alteração. As propriedades e os comandos que fornece o modelo de exibição definem a funcionalidade a ser oferecida pela interface do usuário, mas o modo de exibição determina como essa funcionalidade deve ser exibido.

> [!TIP]
> Manter a interface do usuário responsiva com operações assíncronas. Aplicativos móveis devem manter o thread de interface do usuário desbloqueado para melhorar a percepção do desempenho. Portanto, no modelo de exibição, use os métodos assíncronos para operações de e/s e gerar eventos para notificar os modos de exibição de alterações de propriedade de forma assíncrona.

O modelo de exibição também é responsável pela coordenação de interações do modo de exibição com as classes de modelo que são necessárias. Normalmente, há uma relação um-para-muitos entre o modelo de exibição e as classes de modelo. O modelo de exibição pode optar por expor classes de modelo diretamente para o modo de exibição para que os controles no modo de exibição podem vincular dados diretamente para eles. Nesse caso, as classes de modelo precisará ser projetados para dar suporte à associação de dados e eventos de notificação de alteração.

Cada modelo de exibição fornece dados de um modelo em um formulário que o modo de exibição facilmente pode consumir. Para fazer isso, o modelo de exibição, às vezes, executa a conversão de dados. Colocar essa conversão de dados no modelo de exibição é uma boa ideia, pois ele fornece propriedades que pode ser associadas para o modo de exibição. Por exemplo, o modelo de exibição pode combinar os valores das duas propriedades para tornar mais fácil para exibição pela exibição.

> [!TIP]
> Centralize as conversões de dados em uma camada de conversão. Também é possível usar conversores como uma camada de conversão de dados separado que fica entre o modelo de exibição e o modo de exibição. Isso pode ser necessário, por exemplo, quando dados exigem a formatação especial que o modelo de exibição não fornece.

Para o modelo de exibição participar da vinculação de dados bidirecional com o modo de exibição que, suas propriedades devem aumentar o `PropertyChanged` eventos. Modelos de exibição atendem a esse requisito ao implementar o `INotifyPropertyChanged` da interface e acionar o `PropertyChanged` evento quando uma propriedade é alterada.

Para coleções, o modo de exibição amigável `ObservableCollection<T>` é fornecido. Essa coleção implementa a notificação de alteração de coleção, liberando o desenvolvedor de implementar o `INotifyCollectionChanged` interface em coleções.

### <a name="model"></a>Modelo

Classes de modelo são as classes de não-visuais que encapsulam os dados do aplicativo. Portanto, o modelo pode ser pensado como que representa o modelo de domínio do aplicativo, que geralmente inclui um modelo de dados, juntamente com a lógica de negócios e validação. Objetos de transferência de dados (DTOs), Plain Old CLR Objects (POCOs) e entidades geradas e objetos de proxy são exemplos de objetos de modelo.

Classes de modelo são normalmente usadas em conjunto com os serviços ou repositórios que encapsulam o acesso a dados e armazenamento em cache.

## <a name="connecting-view-models-to-views"></a>Modelos de exibição está se conectando a modos de exibição

Modelos de exibição podem ser conectados a modos de exibição, usando os recursos de vinculação de dados do xamarin. Forms. Há várias abordagens que podem ser usadas para construir as exibições e exibir modelos e associá-los em tempo de execução. Essas abordagens se enquadram em duas categorias, conhecidas como modo de exibição de composição primeiro e composição de primeira exibição do modelo. Escolhendo entre o modo de exibição de composição primeiro e exibição de composição primeiro do modelo é uma questão de preferência e complexidade. No entanto, todas as abordagens compartilham o mesmo objetivo, que é para o modo de exibição tem um modelo de exibição atribuído à sua propriedade BindingContext.

Com exibição de composição primeiro o aplicativo conceitualmente é composta dos modos de exibição que se conectam a modelos de exibição, que eles dependem. A principal vantagem dessa abordagem é que ele torna fácil a construção de acoplamento fraco, unidade de aplicativos que podem ser testados porque os modelos de exibição não têm nenhuma dependência nos modos de exibição em si. Também é fácil de entender a estrutura do aplicativo após sua estrutura visual, em vez de precisar controlar a execução de código para entender como as classes são criadas e associadas. Além disso, a construção de primeira exibição alinha com o xamarin. Forms sistema de navegação que é responsável por construir páginas quando ocorrer a navegação, que torna uma composição primeiro do modelo de exibição complexo e desalinhados com a plataforma.

Com a exibição de composição do modelo primeiro o aplicativo conceitualmente é composta de modelos de exibição, com um serviço que está sendo responsável por localizar a exibição para um modelo de exibição. Composição de primeira exibição do modelo parece mais natural para alguns desenvolvedores, já que a criação de exibição pode ser abstraída, permitindo que eles se concentrem na estrutura lógica sem interface de usuário do aplicativo. Além disso, ele permite que os modelos de exibição a ser criado por outros modelos de exibição. No entanto, essa abordagem geralmente é complexa e pode se tornar difícil de entender como as diversas partes do aplicativo são criadas e associadas.

> [!TIP]
> Manter modelos de exibição e exibições independentes. A associação de modos de exibição para uma propriedade em uma fonte de dados deve ser a dependência de entidade de segurança do modo de exibição no seu modelo de exibição correspondente. Especificamente, a não referenciar tipos de exibição, como [ `Button` ](xref:Xamarin.Forms.Button) e [ `ListView` ](xref:Xamarin.Forms.ListView), de modelos de exibição. Seguindo os princípios descritos aqui, modelos de exibição podem ser testados isoladamente, reduzindo, portanto, a probabilidade de defeitos de software, limitando o escopo.

As seções a seguir discutem as abordagens principais para se conectar a modelos de exibição a modos de exibição.

### <a name="creating-a-view-model-declaratively"></a>Criando um modelo de exibição de forma declarativa

A abordagem mais simples é para o modo de exibição de forma declarativa instanciar seu modelo de exibição correspondente no XAML. Quando o modo de exibição é construído, o objeto de modelo de exibição correspondente também será construído. Essa abordagem é demonstrada no exemplo de código a seguir:

```xaml
<ContentPage ... xmlns:local="clr-namespace:eShop">  
    <ContentPage.BindingContext>  
        <local:LoginViewModel />  
    </ContentPage.BindingContext>  
    ...  
</ContentPage>
```

Quando o [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) é criado, uma instância das `LoginViewModel` é automaticamente criado e definido como a exibição [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext).

Essa construção declarativa e a atribuição de modelo de exibição pela exibição tem a vantagem de que ele é simple, mas tem a desvantagem de que ele requer um construtor de (sem parâmetros) padrão no modelo de exibição.

### <a name="creating-a-view-model-programmatically"></a>Criar um modelo de exibição de forma programática

Um modo de exibição pode ter o código em que o arquivo code-behind que resulta em um modelo de exibição que está sendo atribuído ao seu [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) propriedade. Geralmente, isso é feito no construtor da exibição, conforme mostrado no exemplo de código a seguir:

```csharp
public LoginView()  
{  
    InitializeComponent();  
    BindingContext = new LoginViewModel(navigationService);  
}
```

A construção através de programação e a atribuição do modelo de exibição dentro de lógica do modo de exibição tem a vantagem que ele é simples. No entanto, a principal desvantagem dessa abordagem é que o modo de exibição precisa fornecer o modelo de exibição com as dependências necessárias. Usar um contêiner de injeção de dependência pode ajudar a manter o acoplamento solto entre a exibição e o modelo de exibição. Para obter mais informações, consulte [injeção de dependência](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).

### <a name="creating-a-view-defined-as-a-data-template"></a>Criando uma exibição definida como um modelo de dados

Um modo de exibição pode ser definido como um modelo de dados e associado com um tipo de modelo de exibição. Modelos de dados podem ser definidos como recursos, ou eles podem ser definidas embutidas dentro do controle que exibirá o modelo de exibição. O conteúdo do controle é a instância de modelo de exibição e o modelo de dados é usado para representar visualmente-lo. Essa técnica é um exemplo de uma situação em que o modelo de exibição é instanciado pela primeira vez, seguida pela criação da exibição.

<a name="automatically_creating_a_view_model_with_a_view_model_locator" />

### <a name="automatically-creating-a-view-model-with-a-view-model-locator"></a>Criando automaticamente um modelo de exibição com um localizador de modelo de exibição

Um localizador de modelo de exibição é uma classe personalizada que gerencia a instanciação de modelos de exibição e sua associação a modos de exibição. No aplicativo móvel do eShopOnContainers, o `ViewModelLocator` classe tem uma propriedade anexada, `AutoWireViewModel`, que é usado para associar modelos de exibição com modos de exibição. No XAML da exibição, essa propriedade anexada é definida como true para indicar que o modelo de exibição deve ser conectado automaticamente ao modo de exibição, conforme mostrado no exemplo de código a seguir:

```xaml
viewModelBase:ViewModelLocator.AutoWireViewModel="true"
```

O `AutoWireViewModel` é uma propriedade associável que é inicializado como false, e quando seu valor é alterado a `OnAutoWireViewModelChanged` manipulador de eventos é chamado. Esse método resolve o modelo de exibição para o modo de exibição. O exemplo de código a seguir mostra como isso é feito:

```csharp
private static void OnAutoWireViewModelChanged(BindableObject bindable, object oldValue, object newValue)  
{  
    var view = bindable as Element;  
    if (view == null)  
    {  
        return;  
    }  

    var viewType = view.GetType();  
    var viewName = viewType.FullName.Replace(".Views.", ".ViewModels.");  
    var viewAssemblyName = viewType.GetTypeInfo().Assembly.FullName;  
    var viewModelName = string.Format(  
        CultureInfo.InvariantCulture, "{0}Model, {1}", viewName, viewAssemblyName);  

    var viewModelType = Type.GetType(viewModelName);  
    if (viewModelType == null)  
    {  
        return;  
    }  
    var viewModel = _container.Resolve(viewModelType);  
    view.BindingContext = viewModel;  
}
```

O `OnAutoWireViewModelChanged` método tenta resolver o modelo de exibição usando uma abordagem baseada em convenção. Essa convenção pressupõe que:

-   Modelos de exibição estão no mesmo assembly como tipos de exibição.
-   Modos de exibição estão em um. Namespace de filho de modos de exibição.
-   Modelos de exibição estão em um. Namespace de filho de ViewModels.
-   Exibir modelo nomes correspondem aos nomes de exibição e terminam com "ViewModel".

Por fim, o `OnAutoWireViewModelChanged` método define o [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) do tipo de exibição para o tipo de modelo de exibição resolvido. Para obter mais informações sobre como resolver o tipo de modelo de exibição, consulte [resolução](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#resolution).

Essa abordagem tem a vantagem de que um aplicativo tem uma única classe que é responsável pela instanciação de modelos de exibição e sua conexão a modos de exibição.

> [!TIP]
> Use um localizador de modelo de exibição para facilitar a substituição. Um localizador de modelo de exibição também pode ser usado como um ponto de substituição para implementações alternativas de dependências, como para dados de tempo de design ou de teste de unidade.

## <a name="updating-views-in-response-to-changes-in-the-underlying-view-model-or-model"></a>Atualizando exibições em resposta às alterações subjacente exibir modelo ou modelo

Todos os modelo de exibição e classes de modelo que são acessíveis para um modo de exibição devem implementar o `INotifyPropertyChanged` interface. Implementar essa interface em um modelo de exibição ou a classe de modelo permite que a classe fornecer notificações de alteração para todos os controles ligados a dados no modo de exibição quando o valor da propriedade subjacente é alterado.

Aplicativos devem ser projetados para o uso correto de notificação de alteração de propriedade, cumprindo os requisitos a seguir:

-   Sempre elevar um `PropertyChanged` evento se o valor da propriedade pública é alterado. Não pressuponha que essa acionando o `PropertyChanged` evento pode ser ignorado devido a dados de Conhecimento de como ocorre a associação de XAML.
-   Sempre elevar um `PropertyChanged` eventos para qualquer calculado propriedades cujos valores são usados por outras propriedades na exibição de modelo ou modelo.
-   Sempre elevar o `PropertyChanged` evento no final do método que faz com que uma propriedade as alterações ou quando o objeto é conhecido para estar em um estado seguro. Acionar o evento interrompe a operação invocando sincronicamente manipuladores do evento. Se isso acontecer no meio de uma operação, ele pode expor o objeto para funções de retorno quando ele está em um estado parcialmente atualizado, que não é seguro. Além disso, é possível que as alterações em cascata ser disparado pelo `PropertyChanged` eventos. Alterações em cascata geralmente requerem atualizações para ser concluída antes da alteração em cascata é segura para execução.
-   Nunca gerando um `PropertyChanged` evento se a propriedade não é alterado. Isso significa que você deve comparar os valores antigo e novo antes de acionar o `PropertyChanged` eventos.
-   Nunca acionando o `PropertyChanged` eventos durante o construtor do modelo de exibição, se você estiver inicializando uma propriedade. Controles ligados a dados no modo de exibição não serão inscritos para receber notificações de alteração no momento.
-   Gerando nunca mais de um `PropertyChanged` evento com o mesmo argumento de nome de propriedade dentro de uma única invocação síncrona de um método público de uma classe. Por exemplo, dada uma `NumberOfItems` propriedade cujo armazenamento de backup é o `_numberOfItems` campo, se um método incrementa `_numberOfItems` 50 vezes durante a execução de um loop, é necessário apenas gerar notificação de alteração de propriedade no `NumberOfItems` uma vez, a propriedade Depois que todo o trabalho for concluído. Para métodos assíncronos, dispare o `PropertyChanged` evento para um nome de propriedade fornecido em cada segmento síncrono de uma cadeia de continuação assíncrona.

O aplicativo móvel de eShopOnContainers usa a `ExtendedBindableObject` notificações de alteração de classe para fornecer que é mostrado no exemplo de código a seguir:

```csharp
public abstract class ExtendedBindableObject : BindableObject  
{  
    public void RaisePropertyChanged<T>(Expression<Func<T>> property)  
    {  
        var name = GetMemberInfo(property).Name;  
        OnPropertyChanged(name);  
    }  

    private MemberInfo GetMemberInfo(Expression expression)  
    {  
        ...  
    }  
}
```

Do Xamarin.Form [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) classe implementa a `INotifyPropertyChanged` interface e fornece um [ `OnPropertyChanged` ](xref:Xamarin.Forms.BindableObject.OnPropertyChanged(System.String)) método. O `ExtendedBindableObject` classe fornece a `RaisePropertyChanged` método para invocar a propriedade notificação de alteração e, ao fazer isso, usa a funcionalidade fornecida pelo `BindableObject` classe.

Cada classe de modelo de exibição no aplicativo móvel do eShopOnContainers deriva de `ViewModelBase` classe, que por sua vez deriva o `ExtendedBindableObject` classe. Portanto, cada classe de modelo de exibição usa o `RaisePropertyChanged` método no `ExtendedBindableObject` classe para fornecer notificação de alteração de propriedade. O exemplo de código a seguir mostra como o aplicativo móvel do eShopOnContainers invoca a notificação de alteração de propriedade usando uma expressão lambda:

```csharp
public bool IsLogin  
{  
    get  
    {  
        return _isLogin;  
    }  
    set  
    {  
        _isLogin = value;  
        RaisePropertyChanged(() => IsLogin);  
    }  
}
```

Observe que usar uma expressão lambda dessa maneira envolve um pouco o desempenho porque a expressão lambda deve ser avaliada para cada chamada. Embora o custo de desempenho é pequeno e normalmente não afeta um aplicativo, os custos podem ser decorrentes quando há que muitas notificações de alteração. No entanto, o benefício dessa abordagem é que ele fornece segurança de tipo de tempo de compilação e o suporte à refatoração ao renomear propriedades.

## <a name="ui-interaction-using-commands-and-behaviors"></a>Interação da interface do usuário usando comandos e comportamentos

Nos aplicativos móveis, ações normalmente são invocadas em resposta a uma ação do usuário, como um clique de botão, que pode ser implementada com a criação de um manipulador de eventos no arquivo code-behind. No entanto, o padrão MVVM, a responsabilidade de implementar a ação se encontra com o modelo de exibição e colocando código no code-behind deve ser evitado.

Comandos fornecem uma maneira conveniente para representar as ações que podem ser associadas a controles na interface do usuário. Eles encapsulam o código que implementa a ação e ajudar a mantê-la separada da sua representação visual no modo de exibição. Xamarin. Forms inclui controles que podem ser conectados de forma declarativa a um comando, e esses controles invocará o comando quando o usuário interage com o controle.

Comportamentos de também permitir que os controles declarativamente estar conectado a um comando. No entanto, os comportamentos podem ser usados para invocar uma ação que está associada com uma variedade de eventos gerados por um controle. Portanto, comportamentos resolvem muitos dos mesmos cenários como controles de comando habilitado, enquanto fornece um maior grau de flexibilidade e controle. Além disso, os comportamentos também podem ser usados para associar a métodos ou objetos de comando com controles que não foram projetados especificamente para interagir com os comandos.

### <a name="implementing-commands"></a>Implementando comandos

Modelos de exibição normalmente expõem propriedades de comando, para a associação do modo de exibição, que são instâncias de objetos que implementam o `ICommand` interface. Fornece uma série de controles do xamarin. Forms uma `Command` propriedade, que pode ser dados associado a um `ICommand` objeto fornecido pelo modelo de exibição. O `ICommand` interface define um `Execute` método, que encapsula a própria operação, um `CanExecute` método, que indica se o comando pode ser invocado e um `CanExecuteChanged` evento que ocorre quando ocorrem alterações que afetam se o comando deve executar. O [ `Command` ](xref:Xamarin.Forms.Command) e [ `Command<T>` ](xref:Xamarin.Forms.Command) implementam classes, fornecidas pelo xamarin. Forms, o `ICommand` interface, onde `T` é o tipo dos argumentos para `Execute`e `CanExecute`.

Dentro de um modelo de exibição, deve haver um objeto do tipo [ `Command` ](xref:Xamarin.Forms.Command) ou [ `Command<T>` ](xref:Xamarin.Forms.Command) para cada propriedade pública no modelo de exibição do tipo `ICommand`. O `Command` ou `Command<T>` construtor requer um `Action` objeto de retorno de chamada que é chamado quando o `ICommand.Execute` método é invocado. O `CanExecute` método é um parâmetro do construtor opcional e é um `Func` que retorna um `bool`.

O código a seguir mostra como uma [ `Command` ](xref:Xamarin.Forms.Command) instância, que representa um comando de registro, é construída, especificando um delegado para o `Register` exibir o método de modelo:

```csharp
public ICommand RegisterCommand => new Command(Register);
```

O comando é exposto para o modo de exibição por meio de uma propriedade que retorna uma referência a um `ICommand`. Quando o `Execute` método é chamado em de [ `Command` ](xref:Xamarin.Forms.Command) do objeto, ele simplesmente encaminha a chamada para o método no modelo de exibição por meio do delegado que foi especificado no `Command` construtor.

Um método assíncrono pode ser invocado por um comando usando o `async` e `await` palavras-chave ao especificar o comando `Execute` delegar. Isso indica que o retorno de chamada é um `Task` e deve ser aguardado. Por exemplo, o código a seguir mostra como uma [ `Command` ](xref:Xamarin.Forms.Command) instância, que representa um comando de entrada, é construída, especificando um delegado para o `SignInAsync` exibir o método de modelo:

```csharp
public ICommand SignInCommand => new Command(async () => await SignInAsync());
```

Parâmetros podem ser passados para o `Execute` e `CanExecute` ações usando o [ `Command<T>` ](xref:Xamarin.Forms.Command) classe para instanciar o comando. Por exemplo, o código a seguir mostra como uma `Command<T>` instância é usada para indicar que o `NavigateAsync` método exigirá um argumento do tipo `string`:

```csharp
public ICommand NavigateCommand => new Command<string>(NavigateAsync);
```

Em ambos os [ `Command` ](xref:Xamarin.Forms.Command) e [ `Command<T>` ](xref:Xamarin.Forms.Command) classes, o delegado a ser o `CanExecute` método em cada construtor é opcional. Se um delegado não for especificado, o `Command` retornarão `true` para `CanExecute`. No entanto, o modelo de exibição pode indicar uma alteração no comando `CanExecute` status chamando o `ChangeCanExecute` método no `Command` objeto. Isso faz com que o `CanExecuteChanged` evento seja acionado. Todos os controles na interface do usuário que estão associados ao comando, em seguida, atualizará seu status habilitado para refletir a disponibilidade do comando associado a dados.

#### <a name="invoking-commands-from-a-view"></a>Chamar comandos de uma exibição

O seguinte exemplo de código mostra como uma [ `Grid` ](xref:Xamarin.Forms.Grid) no `LoginView` associa ao `RegisterCommand` no `LoginViewModel` classe usando um [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) instância:

```xaml
<Grid Grid.Column="1" HorizontalOptions="Center">  
    <Label Text="REGISTER" TextColor="Gray"/>  
    <Grid.GestureRecognizers>  
        <TapGestureRecognizer Command="{Binding RegisterCommand}" NumberOfTapsRequired="1" />  
    </Grid.GestureRecognizers>  
</Grid>
```

Um parâmetro de comando também pode ser opcionalmente definido usando o [ `CommandParameter` ](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) propriedade. O tipo do argumento esperado é especificado na `Execute` e `CanExecute` métodos de destino. O [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) invocará automaticamente o comando de destino quando o usuário interage com o controle anexado. O parâmetro de comando, se fornecido, será ser passado como o argumento para o comando `Execute` delegar.

<a name="implementing_behaviors" />

### <a name="implementing-behaviors"></a>Implementação de comportamentos

Comportamentos permitem que a funcionalidade a ser adicionado aos controles de interface do usuário sem ter a subclasse-los. Em vez disso, a funcionalidade é implementada em uma classe de comportamento e anexada ao controle como se fizesse parte do próprio controle. Os comportamentos permitem que você implemente código que normalmente seria necessário para gravar como code-behind, porque ele interage diretamente com a API do controle, de tal forma que ele pode ser concisa anexado ao controle e empacotado para reutilização em mais de um modo de exibição ou aplicativo. No contexto do MVVM, os comportamentos são uma abordagem útil para conectar controles a comandos.

Um comportamento que é anexado a um controle por meio de propriedades anexadas é conhecido como um *anexados comportamento*. O comportamento, em seguida, pode usar a API exposta do elemento ao qual ele está anexado para adicionar funcionalidade a esse controle, ou outros controles, na árvore visual do modo de exibição. O aplicativo móvel de eShopOnContainers contém o `LineColorBehavior` classe, que é um comportamento anexado. Para obter mais informações sobre esse comportamento, consulte [exibindo erros de validação](~/xamarin-forms/enterprise-application-patterns/validation.md#displaying_validation_errors).

Um comportamento de xamarin. Forms é uma classe que deriva de [ `Behavior` ](xref:Xamarin.Forms.Behavior) ou [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) classe, onde `T `é o tipo do controle ao qual o comportamento deve ser aplicada. Essas classes fornecem `OnAttachedTo` e `OnDetachingFrom` métodos, que devem ser substituídos para fornecer a lógica que será executada quando o comportamento é anexado e desanexado de controles.

No aplicativo móvel do eShopOnContainers, o `BindableBehavior<T>` classe deriva de [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) classe. A finalidade de `BindableBehavior<T>` classe é fornecer uma classe base para comportamentos do xamarin. Forms que exigem o [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) do comportamento a ser definido como o controle anexado.

O `BindableBehavior<T>` classe fornece um substituível `OnAttachedTo` método que define o [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) o comportamento e um substituível `OnDetachingFrom` método limpa o `BindingContext`. Além disso, a classe armazena uma referência ao controle anexado na propriedade `AssociatedObject`.

O aplicativo móvel do eShopOnContainers inclui um `EventToCommandBehavior` classe, que executa um comando em resposta a um evento que ocorrer. Essa classe deriva de `BindableBehavior<T>` de classe para que o comportamento pode executar e associar a um `ICommand` especificado por um `Command` propriedade quando o comportamento é consumido. O exemplo de código a seguir mostra a classe `EventToCommandBehavior`:

```csharp
public class EventToCommandBehavior : BindableBehavior<View>  
{  
    ...  
    protected override void OnAttachedTo(View visualElement)  
    {  
        base.OnAttachedTo(visualElement);  

        var events = AssociatedObject.GetType().GetRuntimeEvents().ToArray();  
        if (events.Any())  
        {  
            _eventInfo = events.FirstOrDefault(e => e.Name == EventName);  
            if (_eventInfo == null)  
                throw new ArgumentException(string.Format(  
                        "EventToCommand: Can't find any event named '{0}' on attached type",   
                        EventName));  

            AddEventHandler(_eventInfo, AssociatedObject, OnFired);  
        }  
    }  

    protected override void OnDetachingFrom(View view)  
    {  
        if (_handler != null)  
            _eventInfo.RemoveEventHandler(AssociatedObject, _handler);  

        base.OnDetachingFrom(view);  
    }  

    private void AddEventHandler(  
            EventInfo eventInfo, object item, Action<object, EventArgs> action)  
    {  
        ...  
    }  

    private void OnFired(object sender, EventArgs eventArgs)  
    {  
        ...  
    }  
}
```

O `OnAttachedTo` e `OnDetachingFrom` métodos são usados para registrar e cancelar o registro de um manipulador de eventos para o evento definido no `EventName` propriedade. Em seguida, quando o evento é acionado, o `OnFired` método é invocado, que executa o comando.

A vantagem de usar o `EventToCommandBehavior` executar um comando quando um evento é acionado, é que os comandos podem ser associados a controles que não foram projetados para interagir com os comandos. Além disso, isso move o código de manipulação de eventos para modelos de exibição, onde ele pode ser testada de unidade.

#### <a name="invoking-behaviors-from-a-view"></a>Comportamentos de invocação de um modo de exibição

O `EventToCommandBehavior` é particularmente útil para anexar um comando a um controle que não oferece suporte a comandos. Por exemplo, o `ProfileView` usa o `EventToCommandBehavior` para executar o `OrderDetailCommand` quando o [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) evento é acionado no [ `ListView` ](xref:Xamarin.Forms.ListView) que lista os pedidos do usuário, conforme mostrado no código a seguir:

```xaml
<ListView>  
    <ListView.Behaviors>  
        <behaviors:EventToCommandBehavior             
            EventName="ItemTapped"  
            Command="{Binding OrderDetailCommand}"  
            EventArgsConverter="{StaticResource ItemTappedEventArgsConverter}" />  
    </ListView.Behaviors>  
    ...  
</ListView>
```

Em tempo de execução, o `EventToCommandBehavior` responderá a interação com o [ `ListView` ](xref:Xamarin.Forms.ListView). Quando um item for selecionado na `ListView`, o [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) evento será acionado, o qual será executada a `OrderDetailCommand` no `ProfileViewModel`. Por padrão, os argumentos do evento para o evento são passados para o comando. Esses dados são convertidos conforme ele é passado entre origem e destino pelo conversor especificado na `EventArgsConverter` propriedade, que retorna o [ `Item` ](xref:Xamarin.Forms.ItemTappedEventArgs.Item) da `ListView` do [ `ItemTappedEventArgs` ](xref:Xamarin.Forms.ItemTappedEventArgs). Portanto, quando o `OrderDetailCommand` é executado, selecionado `Order` é passado como um parâmetro para a ação registrada.

Para obter mais informações sobre comportamentos, consulte [comportamentos](~/xamarin-forms/app-fundamentals/behaviors/index.md).

## <a name="summary"></a>Resumo

O padrão Model-View-ViewModel (MVVM) ajuda a separar a lógica de negócios e apresentação de um aplicativo de sua interface do usuário (UI) de forma precisa. Mantendo uma separação clara entre a lógica do aplicativo e a interface do usuário ajuda a resolver vários problemas de desenvolvimento e pode tornar mais fácil de testar um aplicativo, manter e evoluem. Ele pode também melhoram muito a oportunidades de reutilização de código e permite que os desenvolvedores e designers de interface do usuário mais facilmente colaboram durante o desenvolvimento de suas respectivas partes de um aplicativo.

Usando o MVVM padrão, a interface do usuário do aplicativo e a lógica de negócios e apresentação subjacente é separada em três classes separadas: a exibição, que encapsula a interface do usuário e a interface do usuário lógica; o modelo de exibição, que encapsula a lógica de apresentação e de estado; e o modelo, que encapsula a lógica de negócios e os dados do aplicativo.


## <a name="related-links"></a>Links relacionados

- [Baixe o livro eletrônico (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (amostra)](https://github.com/dotnet-architecture/eShopOnContainers)
