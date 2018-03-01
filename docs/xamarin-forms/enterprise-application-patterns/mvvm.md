---
title: MVVM
ms.topic: article
ms.prod: xamarin
ms.assetid: dd8c1813-df44-4947-bcee-1a1ff2334b87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: ac8cf026fba8cac565ad622dcba24834a2ea8098
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="mvvm"></a>MVVM

A experiência do desenvolvedor xamarin. Forms normalmente envolve a criação de uma interface de usuário em XAML e adicionando code-behind que opera na interface do usuário. Como os aplicativos forem modificados e crescem em tamanho e o escopo, podem surgir problemas de manutenção complexa. Esses problemas incluem a ligação forte entre os controles de interface do usuário e a lógica de negócios, o que aumenta o custo de fazer modificações de interface do usuário e a dificuldade de tal código de teste de unidade.

O padrão Model-View-ViewModel (MVVM) ajuda a separar corretamente a lógica de negócios e apresentação de um aplicativo em sua interface de usuário (IU). Manter uma separação clara entre a lógica do aplicativo e a interface do usuário ajuda a resolver vários problemas de desenvolvimento e pode tornar mais fácil de testar, manter e desenvolver um aplicativo. Ele pode melhorar significativamente o oportunidades de reutilização de código e permite que os desenvolvedores e designers de interface do usuário mais facilmente colaboram ao desenvolver suas respectivas partes de um aplicativo.

## <a name="the-mvvm-pattern"></a>O padrão MVVM

Há três componentes principais do padrão MVVM: o modelo, a exibição e o modelo de exibição. Cada atende à finalidade distinta. Figura 2-1 mostra as relações entre os três componentes.

![](mvvm-images/mvvm.png "O padrão MVVM")

**Figura 2-1**: padrão o MVVM

Além de entender as responsabilidades de todos os componentes, também é importante entender como eles interagem entre si. Em um nível alto, o modo de exibição "conhece" o modelo de exibição e o modelo de exibição "conhece" o modelo, mas o modelo é não sabe nada sobre o modelo de exibição e desconhece o modelo de exibição do modo de exibição. Portanto, o modelo de exibição isola o modo de exibição do modelo e permite que o modelo fazer evoluir independentemente do modo de exibição.

Os benefícios de usar o padrão MVVM são da seguinte maneira:

-   Se houver uma implementação existente do modelo que encapsula a lógica de negócios existente, ele pode ser difícil ou arriscado para alterá-lo. Nesse cenário, o modelo de exibição atua como um adaptador para as classes de modelo e permite que você evite fazer nenhuma alteração principal no código de modelo.
-   Os desenvolvedores podem criar testes de unidade para o modelo de exibição e o modelo, sem usar o modo de exibição. Os testes de unidade para o modelo de exibição podem ter exatamente a mesma funcionalidade usada pelo modo de exibição.
-   O interface do usuário do aplicativo pode ser reprojetado sem tocar no código, desde que a exibição é implementada inteiramente em XAML. Portanto, uma nova versão do modo de exibição deve funcionar com o modelo de exibição existente.
-   Designers e desenvolvedores podem trabalhar independentemente e simultaneamente em seus componentes durante o processo de desenvolvimento. Designers podem se concentrar na exibição, enquanto os desenvolvedores podem trabalhar no modelo de exibição e componentes de modelo.

A chave para uso eficiente de MVVM reside na compreensão de como incluir código de aplicativo para as classes corretas e compreender como as classes interagem. As seções a seguir discutem as responsabilidades de cada uma das classes no padrão MVVM.

### <a name="view"></a>Exibir

A exibição é responsável por definir a estrutura, o layout e a aparência de que o usuário vê na tela. Idealmente, cada modo de exibição é definido em XAML, com um limitado por trás do código que não contém a lógica de negócios. No entanto, em alguns casos, o code-behind pode conter lógica de interface do usuário que implementa o comportamento visual difícil de expressar em XAML, como animações.

Em um aplicativo xamarin. Forms, uma exibição é normalmente uma [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)-derivada ou [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)-classe derivada. No entanto, os modos de exibição também podem ser representados por um modelo de dados, que especifica os elementos de interface do usuário a ser usado para representar visualmente um objeto quando ele for exibido. Um modelo de dados como um modo de exibição não tem nenhum code-behind e foi projetado para associar a um tipo de modelo de modo de exibição específico.

> [!TIP]
> Evite habilitando e desabilitando elementos de interface do usuário no code-behind. Certifique-se de que os modelos de exibição são responsáveis por definindo as alterações de estado lógico que afetam alguns aspectos da exibição do modo de exibição, como se um comando estiver disponível, ou uma indicação de que uma operação está pendente. Portanto, habilitar e desabilitar a elementos de interface do usuário por associação para exibir as propriedades de modelo, em vez de habilitar e desabilitá-las no code-behind.

Há várias opções para a execução de código no modelo de exibição em resposta às interações no modo de exibição, como um clique de botão ou a seleção de item. Se um controle oferece suporte a comandos, o controle `Command` propriedade pode ser associado a dados para um `ICommand` propriedade no modelo de exibição. Quando o comando do controle é chamado, o código no modelo de exibição será executado. Além de comandos, comportamentos podem ser anexados a um objeto no modo de exibição e podem escutar um comando a ser invocado ou o evento a ser gerado. Em resposta, o comportamento pode invocar um `ICommand` no modelo de exibição ou um método no modelo de exibição.

### <a name="viewmodel"></a>ViewModel

O modelo de exibição implementa propriedades e comandos para o qual o modo de exibição pode associar dados ao e notifica a exibição de qualquer alteração de estado por meio de eventos de notificação de alteração. As propriedades e os comandos que fornece o modelo de exibição definem a funcionalidade a ser oferecida pela interface do usuário, mas o modo de exibição determina como essa funcionalidade será exibido.

> [!TIP]
> Manter a interface do usuário responsiva com operações assíncronas. Aplicativos móveis devem manter o thread de interface do usuário desbloqueado para melhorar a percepção do usuário de desempenho. Portanto, no modelo de exibição, use os métodos assíncronos para operações de e/s e gerar eventos para notificar os modos de exibição de alterações de propriedade de forma assíncrona.

O modelo de exibição também é responsável pela coordenação de interações do modo de exibição com as classes de modelo que são necessárias. Normalmente, há uma relação um-para-muitos entre o modelo de exibição e as classes de modelo. O modelo de exibição pode querer expor classes de modelo diretamente para o modo de exibição para que os controles no modo de exibição podem associar dados diretamente a eles. Nesse caso, as classes de modelo precisará ser projetados para dar suporte a associação de dados e eventos de notificação de alteração.

Cada modelo de exibição fornece dados de um modelo de forma que o modo de exibição pode consumir facilmente. Para fazer isso, o modelo de exibição, às vezes, executa a conversão de dados. Colocar essa conversão de dados no modelo de exibição é uma boa ideia porque ela fornece propriedades que o modo de exibição pode vincular a. Por exemplo, o modelo de exibição pode combinar os valores de duas propriedades para tornar mais fácil para exibir o modo de exibição.

> [!TIP]
> Centralize conversões de dados em uma camada de conversão. Também é possível usar conversores como uma camada de conversão de dados separado que fica entre o modelo de exibição e o modo de exibição. Isso pode ser necessário, por exemplo, quando dados requerem formatação especial que não fornece o modelo de exibição.

Em ordem para o modelo de exibição para participar de associação de dados bidirecional com o modo de exibição, suas propriedades devem aumentar o `PropertyChanged` evento. Exibir modelos satisfazem este requisito Implementando o `INotifyPropertyChanged` interface e gerando o `PropertyChanged` evento quando uma propriedade é alterada.

Para coleções, o modo de exibição amigável `ObservableCollection<T>` é fornecido. Essa coleção implementa a notificação de alteração de coleção, liberando o desenvolvedor de implementar o `INotifyCollectionChanged` interface em coleções.

### <a name="model"></a>Modelo

Classes de modelo são não visual classes que encapsulam os dados do aplicativo. Portanto, o modelo pode ser pensado como que representa o modelo de domínio do aplicativo, que geralmente inclui um modelo de dados junto com a lógica de negócios e validação. Objetos de transferência de dados (DTOs), Plain Old CLR Objects (POCOs) e entidades geradas e objetos de proxy são exemplos de objetos de modelo.

Classes de modelo normalmente são usadas em conjunto com os serviços ou repositórios que encapsulam o acesso a dados e armazenamento em cache.

## <a name="connecting-view-models-to-views"></a>Conectar-se exibir modelos para modos de exibição

Modelos de exibição podem ser conectados a modos de exibição usando os recursos de associação de dados do xamarin. Forms. Há várias abordagens que podem ser usadas para construir os modos de exibição e exibir modelos e associá-los em tempo de execução. Esses métodos se enquadram em duas categorias, conhecidas como composição primeira exibição e composição primeiro do modelo de exibição. Escolhendo entre composição primeira exibição e exibição de composição primeiro modelo é uma questão de preferência e complexidade. No entanto, todas as abordagens compartilham o mesmo objetivo, que é para o modo de exibição tem um modelo de exibição atribuído à sua propriedade BindingContext.

Com o modo de exibição composição primeiro o aplicativo conceitualmente é composta dos modos de exibição que se conectam aos modelos de exibição, que eles dependem. A principal vantagem dessa abordagem é que ela torna mais fácil construir acoplada, aplicativos de teste de unidade porque os modelos de exibição não tem nenhuma dependência entre exibições. Também é fácil de entender a estrutura do aplicativo por sua estrutura visual a seguir, em vez de precisar controlar a execução de código para entender como as classes são criadas e associadas. Além disso, construção de primeiro do modo de exibição de acordo com o xamarin. Forms sistema de navegação que é responsável pela criação de páginas quando ocorre a navegação, que torna uma composição primeiro do modelo de exibição complexa e desalinhados com a plataforma.

Com a exibição de composição de modelo primeiro o aplicativo conceitualmente é composta de exibir modelos, com um serviço que está sendo responsável para localizar o modo de exibição para um modelo de exibição. Exibir modelo primeiro composição parece mais natural para alguns desenvolvedores, desde a criação de exibição pode ser abstraída, possibilitando para focalizar a estrutura lógica sem interface de usuário do aplicativo. Além disso, ele permite exibir modelos a serem criados por outros modelos de exibição. No entanto, essa abordagem normalmente é complexa e pode se tornar difícil de entender como as várias partes do aplicativo são criadas e associadas.

> [!TIP]
> Manter exibições e exibir modelos independentes. A associação de modos de exibição para uma propriedade em uma fonte de dados deve ser uma dependência de entidade de segurança do modo de exibição em seu modelo de exibição correspondente. Especificamente, não fazer referência a tipos de exibição, como [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) e [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/), de modelos de exibição. Seguindo os princípios descritos aqui, modelos de exibição podem ser testados em isolamento, reduzindo a probabilidade de defeitos de software, limitando o escopo.

As seções a seguir discutem as abordagens principais para se conectar a modelos de exibição para modos de exibição.

### <a name="creating-a-view-model-declaratively"></a>Criar um modelo de exibição declarativamente

É a abordagem mais simples para o modo de exibição instanciar declarativamente seu modelo de exibição correspondente em XAML. Quando o modo de exibição é construído, o objeto de modelo de exibição correspondente também será construído. Essa abordagem é demonstrada no exemplo de código a seguir:

```xaml
<ContentPage ... xmlns:local="clr-namespace:eShop">  
    <ContentPage.BindingContext>  
        <local:LoginViewModel />  
    </ContentPage.BindingContext>  
    ...  
</ContentPage>
```

Quando o [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) é criado, uma instância do `LoginViewModel` é automaticamente criada e definida como o modo de exibição [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/).

Essa construção declarativa e atribuição do modelo pelo modo de exibição tem a vantagem de que ele é simple, mas tem a desvantagem é que ele requer um construtor de (sem parâmetros) padrão no modelo de exibição.

### <a name="creating-a-view-model-programmatically"></a>Criar um modelo de exibição de forma programática

Um modo de exibição pode ter o código no arquivo code-behind que resulta em um modelo de exibição que está sendo atribuído ao seu [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) propriedade. Isso geralmente é feito no construtor do modo de exibição, conforme mostrado no exemplo de código a seguir:

```csharp
public LoginView()  
{  
    InitializeComponent();  
    BindingContext = new LoginViewModel(navigationService);  
}
```

A construção de programação e a atribuição do modelo de exibição na lógica do modo de exibição tem a vantagem de que ele é simples. No entanto, a principal desvantagem dessa abordagem é que a exibição precisa fornecer o modelo de exibição com quaisquer dependências necessárias. Usando um contêiner de injeção de dependência pode ajudar a manter a perder acoplamento entre o modo de exibição e o modelo de exibição. Para obter mais informações, consulte [injeção de dependência](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).

### <a name="creating-a-view-defined-as-a-data-template"></a>Criar um modo de exibição definido como um modelo de dados

Um modo de exibição pode ser definido como um modelo de dados e associado com um tipo de modelo de exibição. Modelos de dados podem ser definidos como recursos, ou eles podem ser definidas embutidas dentro do controle que exiba o modelo de exibição. O conteúdo do controle é a instância do modelo de exibição e o modelo de dados é usado para representar visualmente-lo. Essa técnica é um exemplo de uma situação na qual o modelo de exibição é instanciado primeiro, seguida pela criação da exibição.

<a name="automatically_creating_a_view_model_with_a_view_model_locator" />

### <a name="automatically-creating-a-view-model-with-a-view-model-locator"></a>Criar um modelo de exibição automaticamente com um localizador de modelo de exibição

Um localizador de modelo de exibição é uma classe personalizada que gerencia a instanciação de modelos de exibição e sua associação a modos de exibição. No aplicativo móvel do eShopOnContainers, o `ViewModelLocator` classe tem uma propriedade anexada, `AutoWireViewModel`, que é usada para associar exibir modelos com modos de exibição. No XAML modo de exibição, essa propriedade anexada está definida como true para indicar que o modelo de exibição deve estar conectado automaticamente ao modo de exibição, conforme mostrado no exemplo de código a seguir:

```xaml
viewModelBase:ViewModelLocator.AutoWireViewModel="true"
```

O `AutoWireViewModel` é uma propriedade associável que é inicializado como false, e quando seu valor é alterado de `OnAutoWireViewModelChanged` manipulador de eventos é chamado. Este método resolve o modelo de exibição para o modo de exibição. O exemplo de código a seguir mostra como isso é feito:

```csharp
private static void OnAutoWireViewModelChanged(BindableObject bindable, object oldValue, object newValue)  
{  
    var view = bindable as Element;  
    if (view == null)  
    {  
        return;  
    }  

    var viewType = view.GetType();  
    var viewName = viewType.FullName.Replace(".Views.", ".ViewModels.");  
    var viewAssemblyName = viewType.GetTypeInfo().Assembly.FullName;  
    var viewModelName = string.Format(  
        CultureInfo.InvariantCulture, "{0}Model, {1}", viewName, viewAssemblyName);  

    var viewModelType = Type.GetType(viewModelName);  
    if (viewModelType == null)  
    {  
        return;  
    }  
    var viewModel = _container.Resolve(viewModelType);  
    view.BindingContext = viewModel;  
}
```

O `OnAutoWireViewModelChanged` método tenta resolver o modelo de exibição usando uma abordagem baseado em convenção. Essa convenção pressupõe que:

-   Exibir modelos estão no mesmo assembly como tipos de exibição.
-   Modos de exibição um. Namespace de filho de modos de exibição.
-   Exibir modelos estão em um. ViewModels filho namespace.
-   Exibir modelo nomes correspondem aos nomes de exibição e terminam com "ViewModel".

Por fim, o `OnAutoWireViewModelChanged` método define o [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) do tipo de exibição para o tipo de modelo de exibição resolvido. Para obter mais informações sobre como resolver o tipo de modelo de exibição, consulte [resolução](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#resolution).

Essa abordagem tem a vantagem de um aplicativo tem uma única classe que é responsável pela instanciação de modelos de exibição e sua conexão a modos de exibição.

> [!TIP]
> Use um localizador de modelo de exibição para facilitar a substituição. Um localizador de exibição do modelo também pode ser usado como um ponto de substituição para implementações alternativas de dependências, como para dados de tempo de design ou de teste de unidade.

## <a name="updating-views-in-response-to-changes-in-the-underlying-view-model-or-model"></a>Atualiza exibições em resposta a alterações no subjacente exibem modelo ou modelo

Todos os modelo de exibição e classes de modelo que são acessíveis para um modo de exibição devem implementar o `INotifyPropertyChanged` interface. Implementar essa interface em uma classe de modelo ou um modelo de exibição permite que a classe fornecer notificações de alteração para controles de associação de dados no modo de exibição quando o valor da propriedade subjacente é alterado.

Aplicativo deve ser projetado para o uso correto de notificação de alteração de propriedade, atendendo aos seguintes requisitos:

-   Sempre gerar um `PropertyChanged` evento se alterações de valor da propriedade de um público. Não suponha que aumentar o `PropertyChanged` evento pode ser ignorado devido a dados de Conhecimento de como a associação de XAML ocorre.
-   Sempre gerar um `PropertyChanged` evento para qualquer calculado propriedades cujos valores são usados por outras propriedades na exibição de modelo ou modelo.
-   Sempre gerar o `PropertyChanged` evento no final do método que faz uma propriedade, ou quando o objeto é conhecido para estar em um estado seguro. Gera o evento interrompe a operação por invocar os manipuladores de eventos do modo síncrono. Se isso acontecer no meio de uma operação, ela pode expor o objeto para funções de retorno de chamada quando ele está em um estado inseguro, parcialmente atualizado. Além disso, é possível que as alterações em cascata ser disparado pelo `PropertyChanged` eventos. Alterações em cascata geralmente exigem atualizações para ser concluída antes da alteração em cascata é segura para execução.
-   Nunca gerando um `PropertyChanged` evento se a propriedade não é alterado. Isso significa que você deve comparar os valores novos e antigos antes de acionar o `PropertyChanged` evento.
-   Nunca gerando o `PropertyChanged` eventos durante o construtor de um modelo exibição se estiver inicializando uma propriedade. Controles associados a dados no modo de exibição não serão inscritos para receber notificações de alteração neste momento.
-   Nunca gerar mais de um `PropertyChanged` evento com o mesmo argumento de nome de propriedade em uma única chamada síncrona de um método público de uma classe. Por exemplo, dada uma `NumberOfItems` propriedade cujo armazenamento de backup é o `_numberOfItems` campo, se um incrementos de método `_numberOfItems` 50 vezes durante a execução de um loop, deve apenas gerar notificação de alteração de propriedade no `NumberOfItems` uma vez, a propriedade Depois que todo o trabalho for concluído. Para métodos assíncronos, dispare o `PropertyChanged` evento para um nome de propriedade especificado em cada segmento síncrono de uma cadeia de continuação assíncrona.

Os usos de aplicativo móvel eShopOnContainers o `ExtendedBindableObject` classe para fornecer alterar notificações, que é mostrado no exemplo de código a seguir:

```csharp
public abstract class ExtendedBindableObject : BindableObject  
{  
    public void RaisePropertyChanged<T>(Expression<Func<T>> property)  
    {  
        var name = GetMemberInfo(property).Name;  
        OnPropertyChanged(name);  
    }  

    private MemberInfo GetMemberInfo(Expression expression)  
    {  
        ...  
    }  
}
```

Do Xamarin.Form [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/) classe implementa o `INotifyPropertyChanged` de interface e fornece um [ `OnPropertyChanged` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.OnPropertyChanged/p/System.String/) método. O `ExtendedBindableObject` classe fornece a `RaisePropertyChanged` método para invocar a propriedade de notificação de alteração e isso usa a funcionalidade fornecida pelo `BindableObject` classe.

Cada classe de exibição de modelo no aplicativo móvel eShopOnContainers deriva o `ViewModelBase` classe, que por sua vez deriva de `ExtendedBindableObject` classe. Portanto, cada classe de modelo de exibição usa o `RaisePropertyChanged` método o `ExtendedBindableObject` classe para fornecer notificação de alteração de propriedade. O exemplo de código a seguir mostra como o aplicativo móvel eShopOnContainers invoca a notificação de alteração de propriedade usando uma expressão lambda:

```csharp
public bool IsLogin  
{  
    get  
    {  
        return _isLogin;  
    }  
    set  
    {  
        _isLogin = value;  
        RaisePropertyChanged(() => IsLogin);  
    }  
}
```

Observe que usar uma expressão lambda dessa maneira envolve um pequeno custo de desempenho porque a expressão lambda deve ser avaliada para cada chamada. Embora o custo de desempenho é pequeno e não afetaria normalmente um aplicativo, os custos podem acumular quando há que várias notificações de alteração. No entanto, a vantagem dessa abordagem é que ele fornece suporte à refatoração ao renomear propriedades e segurança de tipo de tempo de compilação.

## <a name="ui-interaction-using-commands-and-behaviors"></a>Usando comandos e comportamentos de interação de interface do usuário

Aplicativos móveis, ações normalmente são invocadas em resposta a uma ação do usuário, como clicar no botão, que pode ser implementada por meio da criação de um manipulador de eventos no arquivo code-behind. No entanto, no padrão MVVM, a responsabilidade para implementar a ação está com o modelo de exibição e código de inserção no code-behind deve ser evitado.

Comandos fornecem uma maneira conveniente para representar as ações que podem ser associadas a controles de interface do usuário. Eles encapsulam o código que implementa a ação e ajudam a mantê-la separada da sua representação visual no modo de exibição. Xamarin. Forms inclui controles que podem ser conectados de forma declarativa para um comando, e esses controles vai invocar o comando quando o usuário interage com o controle.

Comportamentos também permitem que os controles declarativamente estar conectado a um comando. No entanto, os comportamentos podem ser usados para invocar uma ação associada a uma variedade de eventos gerados por um controle. Portanto, comportamentos resolvem muitos dos mesmos cenários como os controles de comando habilitado, proporcionando um maior grau de flexibilidade e controle. Além disso, os comportamentos também podem ser usados para associar métodos ou objetos de comando com os controles que não foram especificamente projetados para interagir com os comandos.

### <a name="implementing-commands"></a>Implementação de comandos

Exibir modelos normalmente expõem propriedades de comando, para a associação do modo de exibição, que são instâncias de objetos que implementam o `ICommand` interface. Uma série de controles do xamarin. Forms fornece um `Command` propriedade, que pode ser dados associados a um `ICommand` objeto fornecido pelo modelo de exibição. O `ICommand` interface define uma `Execute` método, que encapsula a operação em si, um `CanExecute` método, que indica se o comando pode ser chamado e um `CanExecuteChanged` evento que ocorre quando ocorrem alterações que afetam se o comando deve ser executado. O [ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) e [ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) classes, fornecidas pelo xamarin. Forms, implementam a `ICommand` interface, onde `T` é o tipo dos argumentos para `Execute`e `CanExecute`.

Em um modelo de exibição, deve haver um objeto do tipo [ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) ou [ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) para cada propriedade pública no modelo de exibição do tipo `ICommand`. O `Command` ou `Command<T>` construtor requer um `Action` objeto de retorno de chamada que é chamado quando o `ICommand.Execute` método é invocado. O `CanExecute` método é um parâmetro de construtor opcional e é um `Func` que retorna um `bool`.

O código a seguir mostra como um [ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) instância, que representa um comando de registro, é construída com a especificação de um delegado para o `Register` exibir o método de modelo:

```csharp
public ICommand RegisterCommand => new Command(Register);
```

O comando é exposto para o modo de exibição por meio de uma propriedade que retorna uma referência a um `ICommand`. Quando o `Execute` método é chamado no [ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) do objeto, ele simplesmente encaminha a chamada ao método no modelo de exibição por meio do representante que foi especificado no `Command` construtor.

Um método assíncrono pode ser chamado por um comando por meio de `async` e `await` palavras-chave ao especificar o comando `Execute` delegate. Isso indica que o retorno de chamada é um `Task` e deve ser aguardado. Por exemplo, o código a seguir mostra como um [ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) instância, que representa um comando de entrada, é construída com a especificação de um delegado para o `SignInAsync` exibir o método de modelo:

```csharp
public ICommand SignInCommand => new Command(async () => await SignInAsync());
```

Parâmetros podem ser passados para o `Execute` e `CanExecute` ações usando o [ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) classe para criar uma instância de comando. Por exemplo, o código a seguir mostra como um `Command<T>` instância é usada para indicar que o `NavigateAsync` método exige um argumento de tipo `string`:

```csharp
public ICommand NavigateCommand => new Command<string>(NavigateAsync);
```

Em ambos os [ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) e [ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) classes, o representante para a `CanExecute` em cada construtor é opcional. Se não for especificado um delegado, o `Command` retornará `true` para `CanExecute`. No entanto, o modelo de exibição pode indicar uma alteração no comando `CanExecute` status chamando o `ChangeCanExecute` método o `Command` objeto. Isso faz com que o `CanExecuteChanged` evento ser gerado. Os controles na interface de usuário que estão associados ao comando, em seguida, atualizará seu status habilitado para refletir a disponibilidade do comando associado a dados.

#### <a name="invoking-commands-from-a-view"></a>Chamar comandos de uma exibição

O seguinte exemplo de código mostra como um [ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) no `LoginView` associa ao `RegisterCommand` no `LoginViewModel` classe usando um [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/) instância:

```xaml
<Grid Grid.Column="1" HorizontalOptions="Center">  
    <Label Text="REGISTER" TextColor="Gray"/>  
    <Grid.GestureRecognizers>  
        <TapGestureRecognizer Command="{Binding RegisterCommand}" NumberOfTapsRequired="1" />  
    </Grid.GestureRecognizers>  
</Grid>
```

Um parâmetro de comando também pode ser opcionalmente definido usando o [ `CommandParameter` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TapGestureRecognizer.CommandParameter/) propriedade. O tipo do argumento esperado é especificado no `Execute` e `CanExecute` métodos de destino. O [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/) invocará automaticamente o comando de destino quando o usuário interage com o controle anexado. O parâmetro de comando, se fornecido, será passado como o argumento para o comando `Execute` delegate.

<a name="implementing_behaviors" />

### <a name="implementing-behaviors"></a>Implementação de comportamentos

Comportamentos de permitem a funcionalidade a ser adicionado a controles de interface do usuário sem a necessidade de subclasse-los. Em vez disso, a funcionalidade é implementada em uma classe de comportamento e anexada ao controle, como se fosse parte do próprio controle. Comportamentos permitem implementar o código que normalmente seria necessário escrever lógica, porque ele interage diretamente com a API de controle, de forma que ele pode ser maneira concisa anexado ao controle e empacotado para reutilização em mais de um modo de exibição ou aplicativo. No contexto do MVVM, comportamentos são uma abordagem útil para conectar-se controles para comandos.

Um comportamento que é anexado a um controle por meio de propriedades anexadas é conhecido como um *anexado comportamento*. O comportamento pode usar a API exposta do elemento ao qual ele está anexado para adicionar funcionalidade a esse controle ou outros controles, na árvore visual do modo de exibição. O aplicativo móvel eShopOnContainers contém o `LineColorBehavior` classe, que é um comportamento anexado. Para obter mais informações sobre esse comportamento, consulte [exibindo erros de validação](~/xamarin-forms/enterprise-application-patterns/validation.md#displaying_validation_errors).

Um comportamento xamarin. Forms é uma classe que deriva de [ `Behavior` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/) ou [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) classe, onde `T `é o tipo de controle para o qual o comportamento deve ser aplicados. Essas classes fornecem `OnAttachedTo` e `OnDetachingFrom` métodos, que devem ser substituídos para fornecer lógica que será executada quando o comportamento é anexado ao e desanexado de controles.

No aplicativo móvel do eShopOnContainers, o `BindableBehavior<T>` classe deriva o [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) classe. O objetivo do `BindableBehavior<T>` classe é fornecer uma classe base para xamarin. Forms comportamentos que exigem o [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) do comportamento a ser definido como o controle anexado.

O `BindableBehavior<T>` classe fornece um substituível `OnAttachedTo` método que define o [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) o comportamento e um substituível `OnDetachingFrom` método limpa o `BindingContext`. Além disso, a classe armazena uma referência para o controle anexado no `AssociatedObject` propriedade.

O aplicativo móvel eShopOnContainers inclui um `EventToCommandBehavior` classe, que executa um comando em resposta a um evento que ocorrem. Essa classe deriva o `BindableBehavior<T>` classe para que o comportamento pode associar e executar um `ICommand` especificado por um `Command` propriedade quando o comportamento é consumido. O exemplo de código a seguir mostra a classe `EventToCommandBehavior`:

```csharp
public class EventToCommandBehavior : BindableBehavior<View>  
{  
    ...  
    protected override void OnAttachedTo(View visualElement)  
    {  
        base.OnAttachedTo(visualElement);  

        var events = AssociatedObject.GetType().GetRuntimeEvents().ToArray();  
        if (events.Any())  
        {  
            _eventInfo = events.FirstOrDefault(e => e.Name == EventName);  
            if (_eventInfo == null)  
                throw new ArgumentException(string.Format(  
                        "EventToCommand: Can't find any event named '{0}' on attached type",   
                        EventName));  

            AddEventHandler(_eventInfo, AssociatedObject, OnFired);  
        }  
    }  

    protected override void OnDetachingFrom(View view)  
    {  
        if (_handler != null)  
            _eventInfo.RemoveEventHandler(AssociatedObject, _handler);  

        base.OnDetachingFrom(view);  
    }  

    private void AddEventHandler(  
            EventInfo eventInfo, object item, Action<object, EventArgs> action)  
    {  
        ...  
    }  

    private void OnFired(object sender, EventArgs eventArgs)  
    {  
        ...  
    }  
}
```

O `OnAttachedTo` e `OnDetachingFrom` métodos são usados para registrar e cancelar o registro de um manipulador de eventos para o evento definido no `EventName` propriedade. Em seguida, quando o evento é acionado, o `OnFired` método é chamado, que executa o comando.

A vantagem de usar o `EventToCommandBehavior` executar um comando quando um evento é acionado, é que os comandos podem ser associados a controles que não foram projetados para interagir com os comandos. Além disso, isso move o código de manipulação de eventos para exibir modelos, onde ele pode ser um teste de unidade.

#### <a name="invoking-behaviors-from-a-view"></a>Comportamentos de invocação de um modo de exibição

O `EventToCommandBehavior` é particularmente útil para anexar um comando a um controle que não oferece suporte a comandos. Por exemplo, o `ProfileView` usa o `EventToCommandBehavior` para executar o `OrderDetailCommand` quando o [ `ItemTapped` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemTapped/) evento é acionado no [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) que lista os pedidos do usuário, conforme mostrado no código a seguir:

```xaml
<ListView>  
    <ListView.Behaviors>  
        <behaviors:EventToCommandBehavior             
            EventName="ItemTapped"  
            Command="{Binding OrderDetailCommand}"  
            EventArgsConverter="{StaticResource ItemTappedEventArgsConverter}" />  
    </ListView.Behaviors>  
    ...  
</ListView>
```

Em tempo de execução, o `EventToCommandBehavior` responderá à interação com o [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/). Quando um item é selecionado no `ListView`, o [ `ItemTapped` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemTapped/) evento será disparado, que executará o `OrderDetailCommand` no `ProfileViewModel`. Por padrão, os argumentos de evento para o evento são passados para o comando. Esses dados são convertidos como ele é passado entre origem e destino pelo conversor especificado no `EventArgsConverter` propriedade, que retorna o [ `Item` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemTappedEventArgs.Item/) do `ListView` do [ `ItemTappedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemTappedEventArgs/). Portanto, quando o `OrderDetailCommand` é executado, selecionado `Order` é passado como um parâmetro para a ação registrada.

Para obter mais informações sobre comportamentos, consulte [comportamentos](~/xamarin-forms/app-fundamentals/behaviors/index.md).

## <a name="summary"></a>Resumo

O padrão Model-View-ViewModel (MVVM) ajuda a separar corretamente a lógica de negócios e apresentação de um aplicativo em sua interface de usuário (IU). Manter uma separação clara entre a lógica do aplicativo e a interface do usuário ajuda a resolver vários problemas de desenvolvimento e pode tornar mais fácil de testar, manter e desenvolver um aplicativo. Ele pode melhorar significativamente o oportunidades de reutilização de código e permite que os desenvolvedores e designers de interface do usuário mais facilmente colaboram ao desenvolver suas respectivas partes de um aplicativo.

Usando o modelo MVVM padrão, a interface do usuário do aplicativo e a lógica de apresentação e de negócios é separada em três classes separadas: o modo de exibição, que encapsula a interface do usuário e a interface do usuário a lógica; o modelo de exibição, que encapsula a lógica de apresentação e de estado. e o modelo, que encapsula a lógica de negócios e os dados do aplicativo.


## <a name="related-links"></a>Links relacionados

- [Baixar eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
