---
title: O padrão Model-View-ViewModel
description: Este capítulo explica como o aplicativo móvel eShopOnContainers usa o padrão MVVM para separar com clareza a lógica de negócios e de apresentação do aplicativo de sua interface do usuário.
ms.prod: xamarin
ms.assetid: dd8c1813-df44-4947-bcee-1a1ff2334b87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 78609a6e91a2783d869bafbd88679cfc9be00795
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374154"
---
# <a name="the-model-view-viewmodel-pattern"></a>O padrão Model-View-ViewModel

> [!NOTE]
> Este livro eletrônico foi publicado na Primavera de 2017 e não foi atualizado desde então. Há muito no livro que permanece valioso, mas parte do material está desatualizada.

A Xamarin.Forms experiência do desenvolvedor normalmente envolve a criação de uma interface do usuário em XAML e, em seguida, a adição do code-behind que opera na interface do usuário. À medida que os aplicativos são modificados e aumentam em tamanho e escopo, podem surgir problemas complexos de manutenção. Esses problemas incluem o forte acoplamento entre os controles da interface do usuário e a lógica de negócios, o que aumenta o custo de fazer modificações na interface do usuário e a dificuldade de teste de unidade desse código.

O padrão MVVM (Model-View-ViewModel) ajuda a separar corretamente a lógica de negócios e de apresentação de um aplicativo da interface do usuário. Manter uma separação clara entre a lógica do aplicativo e a interface do usuário ajuda a resolver inúmeros problemas de desenvolvimento e pode facilitar o teste, a manutenção e a evolução de um aplicativo. Ele também pode melhorar consideravelmente as oportunidades de reutilização de código e permite que desenvolvedores e designers de interface do usuário colaborem mais facilmente ao desenvolver suas respectivas partes de um aplicativo.

## <a name="the-mvvm-pattern"></a>O padrão MVVM

Há três componentes principais no padrão MVVM: o modelo, a exibição e o modelo de exibição. Cada um serve para uma finalidade distinta. A Figura 2-1 mostra as relações entre os três componentes.

![O padrão MVVM](mvvm-images/mvvm.png)

**Figura 2-1** : o padrão MVVM

Além de compreender as responsabilidades de cada componente, também é importante entender como eles interagem entre si. Em um alto nível, a exibição "sabe" sobre o modelo de exibição e o modelo de exibição "sabe sobre" o modelo, mas o modelo não reconhece o modelo de exibição e o modelo de exibição não reconhece a exibição. Portanto, o modelo de exibição isola a exibição do modelo e permite que o modelo evolua independentemente da exibição.

Os benefícios de usar o padrão MVVM são os seguintes:

- Se houver uma implementação de modelo existente que encapsula a lógica de negócios existente, pode ser difícil ou arriscar alterá-la. Nesse cenário, o modelo de exibição atua como um adaptador para as classes de modelo e permite que você evite fazer alterações importantes no código do modelo.
- Os desenvolvedores podem criar testes de unidade para o modelo de exibição e o modelo, sem usar a exibição. Os testes de unidade do modelo de exibição podem exercer exatamente a mesma funcionalidade usada pelo modo de exibição.
- A interface do usuário do aplicativo pode ser reformulada sem tocar no código, desde que a exibição seja implementada inteiramente em XAML. Portanto, uma nova versão da exibição deve funcionar com o modelo de exibição existente.
- Designers e desenvolvedores podem trabalhar de forma independente e simultânea em seus componentes durante o processo de desenvolvimento. Os designers podem se concentrar na exibição, enquanto os desenvolvedores podem trabalhar no modelo de exibição e nos componentes de modelo.

A chave para usar o MVVM efetivamente está na compreensão de como fatorar o código do aplicativo nas classes corretas e no entendimento de como as classes interagem. As seções a seguir discutem as responsabilidades de cada uma das classes no padrão MVVM.

### <a name="view"></a>Exibir

A exibição é responsável por definir a estrutura, o layout e a aparência do que o usuário vê na tela. O ideal é que cada exibição seja definida em XAML, com um code-behind limitado que não contenha lógica de negócios. No entanto, em alguns casos, o code-behind pode conter lógica de interface do usuário que implementa o comportamento visual que é difícil de expressar em XAML, como animações.

Em um Xamarin.Forms aplicativo, uma exibição normalmente é uma [`Page`](xref:Xamarin.Forms.Page) classe derivada ou [`ContentView`](xref:Xamarin.Forms.ContentView) derivada. No entanto, as exibições também podem ser representadas por um modelo de dados, que especifica os elementos da interface do usuário a serem usados para representar visualmente um objeto quando ele é exibido. Um modelo de dados como uma exibição não tem nenhum code-behind e é projetado para ser associado a um tipo de modelo de exibição específico.

> [!TIP]
> Evite habilitar e desabilitar elementos da interface do usuário no code-behind. Verifique se os modelos de exibição são responsáveis por definir alterações de estado lógico que afetem alguns aspectos da exibição do modo de exibição, como se um comando está disponível ou uma indicação de que uma operação está pendente. Portanto, habilite e desabilite elementos da interface do usuário ligando para propriedades do modelo de exibição, em vez de habilitá-los e desabilitá-los no code-behind.

Há várias opções para executar o código no modelo de exibição em resposta a interações na exibição, como um clique de botão ou seleção de item. Se um controle der suporte a comandos, a `Command` Propriedade do controle poderá ser vinculada a dados a uma `ICommand` propriedade no modelo de exibição. Quando o comando do controle for invocado, o código no modelo de exibição será executado. Além dos comandos, os comportamentos podem ser anexados a um objeto na exibição e podem escutar um comando a ser invocado ou o evento a ser gerado. Em resposta, o comportamento pode invocar um `ICommand` no modelo de exibição ou um método no modelo de exibição.

### <a name="viewmodel"></a>ViewModel

O modelo de exibição implementa propriedades e comandos aos quais a exibição pode associar dados e notifica a exibição de quaisquer alterações de estado por meio de eventos de notificação de alteração. As propriedades e os comandos que o modelo de exibição fornece definem a funcionalidade a ser oferecida pela interface do usuário, mas a exibição determina como essa funcionalidade deve ser exibida.

> [!TIP]
> Mantenha a interface do usuário responsiva com operações assíncronas. Os aplicativos móveis devem manter o thread da interface do usuário desbloqueado para melhorar a percepção do usuário do desempenho. Portanto, no modelo de exibição, use métodos assíncronos para operações de e/s e gere eventos para notificar as exibições de propriedade de forma assíncrona.

O modelo de exibição também é responsável por coordenar as interações de exibição com todas as classes de modelo necessárias. Normalmente, há uma relação um-para-muitos entre o modelo de exibição e as classes de modelo. O modelo de exibição pode optar por expor classes de modelo diretamente à exibição para que os controles na exibição possam associar dados diretamente a elas. Nesse caso, as classes de modelo precisarão ser projetadas para dar suporte a vinculação de dados e eventos de notificação de alteração.

Cada modelo de exibição fornece dados de um modelo em um formulário que a exibição pode facilmente consumir. Para fazer isso, o modelo de exibição às vezes executa a conversão de dados. Colocar essa conversão de dados no modelo de exibição é uma boa ideia porque fornece propriedades às quais a exibição pode se associar. Por exemplo, o modelo de exibição pode combinar os valores de duas propriedades para facilitar a exibição pela exibição.

> [!TIP]
> Centralizar conversões de dados em uma camada de conversão. Também é possível usar conversores como uma camada de conversão de dados separada que fica entre o modelo de exibição e a exibição. Isso pode ser necessário, por exemplo, quando os dados exigem formatação especial que o modelo de exibição não fornece.

Para que o modelo de exibição participe da ligação de dados bidirecional com a exibição, suas propriedades devem gerar o `PropertyChanged` evento. Os modelos de exibição atendem a esse requisito implementando a `INotifyPropertyChanged` interface e gerando o `PropertyChanged` evento quando uma propriedade é alterada.

Para coleções, é fornecido o modo de exibição amigável `ObservableCollection<T>` . Essa coleção implementa a notificação de alteração de coleção, aliviando o desenvolvedor de ter que implementar a `INotifyCollectionChanged` interface em coleções.

### <a name="model"></a>Modelo

Classes de modelo são classes não visuais que encapsulam os dados do aplicativo. Portanto, o modelo pode ser considerado como representando o modelo de domínio do aplicativo, que geralmente inclui um modelo de dados juntamente com a lógica de validação e negócios. Exemplos de objetos de modelo incluem DTOs (objetos de transferência de dados), POCOs (objetos de CLR antigos) e objetos de proxy e entidade gerados.

As classes de modelo normalmente são usadas em conjunto com serviços ou repositórios que encapsulam o acesso a dados e o cache.

## <a name="connecting-view-models-to-views"></a>Conectando modelos de exibição a exibições

Os modelos de exibição podem ser conectados a exibições usando os recursos de vinculação de dados do Xamarin.Forms . Há muitas abordagens que podem ser usadas para construir exibições e modelos de exibição e associá-las em tempo de execução. Essas abordagens se enquadram em duas categorias, conhecidas como exibição da primeira composição e a primeira composição do modelo de exibição. Escolher entre exibir primeira composição e exibir a primeira composição do modelo é um problema de preferência e complexidade. No entanto, todas as abordagens compartilham o mesmo objetivo, que é para a exibição ter um modelo de exibição atribuído à sua Propriedade BindingContext.

Com a primeira composição da exibição, o aplicativo é conceitualmente composto de exibições que se conectam aos modelos de exibição dos quais dependem. O principal benefício dessa abordagem é que ela facilita a construção de aplicativos de teste de unidade menos rígidos, pois os modelos de exibição não têm nenhuma dependência das próprias exibições. Também é fácil entender a estrutura do aplicativo seguindo sua estrutura visual, em vez de ter que controlar a execução do código para entender como as classes são criadas e associadas. Além disso, a primeira construção é alinhada com o Xamarin.Forms sistema de navegação responsável pela construção de páginas quando ocorre a navegação, o que faz com que um modelo de exibição seja inicialmente construído de forma complexa e desalinhada com a plataforma.

Com a primeira composição do modelo de exibição, o aplicativo é conceitualmente composto por modelos de exibição, sendo que um serviço é responsável por localizar a exibição de um modelo de exibição. A primeira composição do modelo de exibição se sente mais natural para alguns desenvolvedores, já que a criação da exibição pode ser descartada, permitindo que eles se concentrem na estrutura lógica fora da interface do usuário do aplicativo. Além disso, ele permite que os modelos de exibição sejam criados por outros modelos de exibição. No entanto, essa abordagem é geralmente complexa e pode se tornar difícil entender como as várias partes do aplicativo são criadas e associadas.

> [!TIP]
> Mantenha os modelos de exibição e exibições independentes. A associação de exibições a uma propriedade em uma fonte de dados deve ser a dependência principal da exibição em seu modelo de exibição correspondente. Especificamente, não referencie tipos de exibição, como [`Button`](xref:Xamarin.Forms.Button) e [`ListView`](xref:Xamarin.Forms.ListView) , de modelos de exibição. Seguindo os princípios descritos aqui, os modelos de exibição podem ser testados isoladamente, reduzindo, portanto, a probabilidade de defeitos de software, limitando o escopo.

As seções a seguir discutem as principais abordagens para conectar modelos de exibição a exibições.

### <a name="creating-a-view-model-declaratively"></a>Criando um modelo de exibição declarativamente

A abordagem mais simples é a exibição para instanciar declarativamente seu modelo de exibição correspondente em XAML. Quando a exibição é construída, o objeto de modelo de exibição correspondente também será construído. Essa abordagem é demonstrada no exemplo de código a seguir:

```xaml
<ContentPage ... xmlns:local="clr-namespace:eShop">  
    <ContentPage.BindingContext>  
        <local:LoginViewModel />  
    </ContentPage.BindingContext>  
    ...  
</ContentPage>
```

Quando o [`ContentPage`](xref:Xamarin.Forms.ContentPage) é criado, uma instância do `LoginViewModel` é criada automaticamente e definida como a exibição [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) .

Essa construção declarativa e a atribuição do modelo de exibição pela exibição tem a vantagem de que é simples, mas tem a desvantagem de que ele requer um construtor padrão (sem parâmetros) no modelo de exibição.

### <a name="creating-a-view-model-programmatically"></a>Criando um modelo de exibição programaticamente

Um modo de exibição pode ter código no arquivo code-behind que resulta na atribuição do modelo de exibição à sua [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) propriedade. Isso geralmente é feito no construtor da exibição, conforme mostrado no exemplo de código a seguir:

```csharp
public LoginView()  
{  
    InitializeComponent();  
    BindingContext = new LoginViewModel(navigationService);  
}
```

A construção programática e a atribuição do modelo de exibição dentro do code-behind da exibição tem a vantagem de que é simples. No entanto, a principal desvantagem dessa abordagem é que a exibição precisa fornecer o modelo de exibição com quaisquer dependências necessárias. O uso de um contêiner de injeção de dependência pode ajudar a manter o acoplamento flexível entre a exibição e o modelo de exibição. Para obter mais informações, consulte [injeção de dependência](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).

### <a name="creating-a-view-defined-as-a-data-template"></a>Criando uma exibição definida como um modelo de dados

Uma exibição pode ser definida como um modelo de dados e associada a um tipo de modelo de exibição. Os modelos de dados podem ser definidos como recursos ou podem ser definidos embutidos dentro do controle que exibirá o modelo de exibição. O conteúdo do controle é a instância do modelo de exibição e o modelo de dados é usado para representá-lo visualmente. Essa técnica é um exemplo de uma situação na qual o modelo de exibição é instanciado primeiro, seguido pela criação da exibição.

### <a name="automatically-creating-a-view-model-with-a-view-model-locator"></a>Criando automaticamente um modelo de exibição com um localizador de modelo de exibição

Um localizador de modelo de exibição é uma classe personalizada que gerencia a instanciação de modelos de exibição e sua associação a exibições. No aplicativo móvel eShopOnContainers, a `ViewModelLocator` classe tem uma propriedade anexada, `AutoWireViewModel` , que é usada para associar modelos de exibição com exibições. No XAML da exibição, essa propriedade anexada é definida como true para indicar que o modelo de exibição deve ser conectado automaticamente à exibição, conforme mostrado no exemplo de código a seguir:

```xaml
viewModelBase:ViewModelLocator.AutoWireViewModel="true"
```

A `AutoWireViewModel` propriedade é uma propriedade vinculável que é inicializada como false e quando seu valor altera o `OnAutoWireViewModelChanged` manipulador de eventos é chamado. Esse método resolve o modelo de exibição para a exibição. O exemplo de código a seguir mostra como isso é obtido:

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

O `OnAutoWireViewModelChanged` método tenta resolver o modelo de exibição usando uma abordagem baseada em convenção. Essa convenção pressupõe que:

- Os modelos de exibição estão no mesmo assembly que os tipos de exibição.
- Os modos de exibição estão em um. Exibe o namespace filho.
- Os modelos de exibição estão em um. Namespace filho de ViewModels.
- Os nomes de modelo de exibição correspondem aos nomes de exibição e terminam com "ViewModel".

Por fim, o `OnAutoWireViewModelChanged` método define o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) do tipo de exibição como o tipo de modelo de exibição resolvido. Para obter mais informações sobre como resolver o tipo de modelo de exibição, consulte [resolução](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#resolution).

Essa abordagem tem a vantagem de que um aplicativo tem uma única classe responsável pela instanciação de modelos de exibição e sua conexão com exibições.

> [!TIP]
> Use um localizador de modelo de exibição para facilitar a substituição. Um localizador de modelo de exibição também pode ser usado como um ponto de substituição para implementações alternativas de dependências, como para teste de unidade ou dados de tempo de design.

## <a name="updating-views-in-response-to-changes-in-the-underlying-view-model-or-model"></a>Atualizando exibições em resposta a alterações no modelo de exibição ou modelo subjacente

Todas as classes modelo de exibição e modelo que são acessíveis a uma exibição devem implementar a `INotifyPropertyChanged` interface. Implementar essa interface em um modelo de exibição ou classe de modelo permite que a classe forneça notificações de alteração a quaisquer controles vinculados a dados na exibição quando o valor da propriedade subjacente é alterado.

Os aplicativos devem ser arquitetados para o uso correto da notificação de alteração de propriedade, atendendo aos seguintes requisitos:

- Sempre gerando um `PropertyChanged` evento se o valor de uma propriedade pública for alterado. Não presuma que a geração do `PropertyChanged` evento pode ser ignorada devido ao conhecimento de como ocorre a associação XAML.
- Sempre gerando um `PropertyChanged` evento para todas as propriedades calculadas cujos valores são usados por outras propriedades no modelo de exibição ou modelo.
- Sempre gerando o `PropertyChanged` evento no final do método que faz uma alteração de propriedade ou quando o objeto é conhecido como em um estado seguro. Gerar o evento interrompe a operação invocando os manipuladores do evento de forma síncrona. Se isso ocorrer no meio de uma operação, ele poderá expor o objeto às funções de retorno de chamada quando ele estiver em um estado inseguro e parcialmente atualizado. Além disso, é possível que as alterações em cascata sejam disparadas por `PropertyChanged` eventos. As alterações em cascata geralmente exigem que as atualizações sejam concluídas antes que a alteração em cascata seja segura para ser executada.
- Nunca gerar um `PropertyChanged` evento se a propriedade não for alterada. Isso significa que você deve comparar os valores antigos e novos antes de gerar o `PropertyChanged` evento.
- Nunca gerará o `PropertyChanged` evento durante um construtor do modelo de exibição se você estiver inicializando uma propriedade. Os controles vinculados a dados na exibição não terão assinatura para receber notificações de alteração neste momento.
- Nunca gera mais de um `PropertyChanged` evento com o mesmo argumento de nome de propriedade em uma única invocação síncrona de um método público de uma classe. Por exemplo, considerando uma `NumberOfItems` propriedade cujo armazenamento de backup é o `_numberOfItems` campo, se um método incrementar `_numberOfItems` 50 vezes durante a execução de um loop, ele só deverá gerar uma notificação de alteração de propriedade na `NumberOfItems` Propriedade uma vez, depois que todo o trabalho for concluído. Para métodos assíncronos, gere o `PropertyChanged` evento para um determinado nome de propriedade em cada segmento síncrono de uma cadeia de continuação assíncrona.

O aplicativo móvel eShopOnContainers usa a `ExtendedBindableObject` classe para fornecer notificações de alteração, que é mostrada no exemplo de código a seguir:

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

A classe do Xamarin. Form [`BindableObject`](xref:Xamarin.Forms.BindableObject) implementa a `INotifyPropertyChanged` interface e fornece um [`OnPropertyChanged`](xref:Xamarin.Forms.BindableObject.OnPropertyChanged(System.String)) método. A `ExtendedBindableObject` classe fornece o `RaisePropertyChanged` método para invocar a notificação de alteração de propriedade e, ao fazer isso, usa a funcionalidade fornecida pela `BindableObject` classe.

Cada classe de modelo de exibição no aplicativo móvel eShopOnContainers deriva da `ViewModelBase` classe, que, por sua vez, deriva da `ExtendedBindableObject` classe. Portanto, cada classe de modelo de exibição usa o `RaisePropertyChanged` método na `ExtendedBindableObject` classe para fornecer notificação de alteração de propriedade. O exemplo de código a seguir mostra como o aplicativo móvel eShopOnContainers invoca a notificação de alteração de propriedade usando uma expressão lambda:

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

Observe que usar uma expressão lambda dessa maneira envolve um pequeno custo de desempenho, pois a expressão lambda deve ser avaliada para cada chamada. Embora o custo de desempenho seja pequeno e, normalmente, não afete um aplicativo, os custos podem ser acumulados quando há muitas notificações de alteração. No entanto, o benefício dessa abordagem é que ela fornece segurança de tipo em tempo de compilação e suporte de refatoração ao renomear as propriedades.

## <a name="ui-interaction-using-commands-and-behaviors"></a>Interação da interface do usuário usando comandos e comportamentos

Em aplicativos móveis, as ações geralmente são invocadas em resposta a uma ação do usuário, como um clique de botão, que pode ser implementado pela criação de um manipulador de eventos no arquivo code-behind. No entanto, no padrão MVVM, a responsabilidade de implementar a ação está no modelo de exibição, e colocar o código no code-behind deve ser evitado.

Os comandos fornecem uma maneira conveniente de representar as ações que podem ser associadas a controles na interface do usuário. Eles encapsulam o código que implementa a ação e ajudam a mantê-lo dissociado de sua representação visual na exibição. Xamarin.Forms inclui controles que podem ser conectados declarativamente a um comando, e esses controles invocarão o comando quando o usuário interage com o controle.

Os comportamentos também permitem que os controles sejam conectados declarativamente a um comando. No entanto, os comportamentos podem ser usados para invocar uma ação associada a um intervalo de eventos gerados por um controle. Portanto, os comportamentos abordam muitos dos mesmos cenários que os controles habilitados para comandos, fornecendo, ao mesmo tempo, um grau maior de flexibilidade e controle. Além disso, os comportamentos também podem ser usados para associar objetos de comando ou métodos a controles que não foram projetados especificamente para interagir com comandos.

### <a name="implementing-commands"></a>Implementando comandos

Os modelos de exibição normalmente expõem propriedades de comando, para associação da exibição, que são instâncias de objeto que implementam a `ICommand` interface. Vários Xamarin.Forms controles fornecem uma `Command` propriedade, que pode ser associada a dados para um `ICommand` objeto fornecido pelo modelo de exibição. A `ICommand` interface define um `Execute` método, que encapsula a própria operação, um `CanExecute` método, que indica se o comando pode ser invocado e um `CanExecuteChanged` evento que ocorre quando ocorrem alterações que afetam se o comando deve ser executado. As [`Command`](xref:Xamarin.Forms.Command) [`Command<T>`](xref:Xamarin.Forms.Command) classes e, fornecidas pelo Xamarin.Forms , implementam a `ICommand` interface, em que `T` é o tipo dos argumentos para `Execute` e `CanExecute` .

Em um modelo de exibição, deve haver um objeto do tipo [`Command`](xref:Xamarin.Forms.Command) ou [`Command<T>`](xref:Xamarin.Forms.Command) para cada propriedade pública no modelo de exibição do tipo `ICommand` . O `Command` `Command<T>` Construtor ou requer um `Action` objeto de retorno de chamada que é chamado quando o `ICommand.Execute` método é invocado. O `CanExecute` método é um parâmetro de Construtor opcional e é um `Func` que retorna um `bool` .

O código a seguir mostra como uma [`Command`](xref:Xamarin.Forms.Command) instância, que representa um comando de registro, é construída especificando um delegado para o `Register` método de modelo de exibição:

```csharp
public ICommand RegisterCommand => new Command(Register);
```

O comando é exposto à exibição por meio de uma propriedade que retorna uma referência a um `ICommand` . Quando o `Execute` método é chamado no [`Command`](xref:Xamarin.Forms.Command) objeto, ele simplesmente encaminha a chamada para o método no modelo de exibição por meio do delegado que foi especificado no `Command` Construtor.

Um método assíncrono pode ser invocado por um comando usando as `async` `await` palavras-chave e ao especificar o delegado do comando `Execute` . Isso indica que o retorno de chamada é a `Task` e deve ser aguardado. Por exemplo, o código a seguir mostra como uma [`Command`](xref:Xamarin.Forms.Command) instância, que representa um comando de entrada, é construída especificando um delegado para o `SignInAsync` método de modelo de exibição:

```csharp
public ICommand SignInCommand => new Command(async () => await SignInAsync());
```

Os parâmetros podem ser passados para `Execute` as `CanExecute` ações e usando a [`Command<T>`](xref:Xamarin.Forms.Command) classe para instanciar o comando. Por exemplo, o código a seguir mostra como uma `Command<T>` instância é usada para indicar que o `NavigateAsync` método exigirá um argumento do tipo `string` :

```csharp
public ICommand NavigateCommand => new Command<string>(NavigateAsync);
```

Nas [`Command`](xref:Xamarin.Forms.Command) [`Command<T>`](xref:Xamarin.Forms.Command) classes e, o delegado para o `CanExecute` método em cada Construtor é opcional. Se um delegado não for especificado, o `Command` será retornado `true` para `CanExecute` . No entanto, o modelo de exibição pode indicar uma alteração no `CanExecute` status do comando chamando o `ChangeCanExecute` método no `Command` objeto. Isso faz com que o `CanExecuteChanged` evento seja gerado. Todos os controles na interface do usuário que estão vinculados ao comando atualizarão seu status habilitado para refletir a disponibilidade do comando associado a dados.

#### <a name="invoking-commands-from-a-view"></a>Invocando comandos de uma exibição

O exemplo de código a seguir mostra como um [`Grid`](xref:Xamarin.Forms.Grid) no é `LoginView` associado ao `RegisterCommand` na `LoginViewModel` classe usando uma [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) instância:

```xaml
<Grid Grid.Column="1" HorizontalOptions="Center">  
    <Label Text="REGISTER" TextColor="Gray"/>  
    <Grid.GestureRecognizers>  
        <TapGestureRecognizer Command="{Binding RegisterCommand}" NumberOfTapsRequired="1" />  
    </Grid.GestureRecognizers>  
</Grid>
```

Um parâmetro de comando também pode ser definido opcionalmente usando a [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) propriedade. O tipo do argumento esperado é especificado nos métodos de `Execute` `CanExecute` destino e. O [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) irá invocar automaticamente o comando de destino quando o usuário interage com o controle anexado. O parâmetro de comando, se fornecido, será passado como o argumento para o delegado do comando `Execute` .

### <a name="implementing-behaviors"></a>Implementando comportamentos

Os comportamentos permitem que a funcionalidade seja adicionada aos controles da interface do usuário sem a necessidade de subclasse deles. Em vez disso, a funcionalidade é implementada em uma classe de comportamento e anexada ao controle como se fizesse parte do próprio controle. Os comportamentos permitem que você implemente código que normalmente teria que escrever como code-behind, pois ele interage diretamente com a API do controle, de forma que possa ser anexado de maneira concisa ao controle e empacotado para reutilização em mais de uma exibição ou aplicativo. No contexto do MVVM, os comportamentos são uma abordagem útil para conectar controles a comandos.

Um comportamento que é anexado a um controle por meio de propriedades anexadas é conhecido como um *comportamento anexado*. O comportamento pode então usar a API exposta do elemento ao qual ele está anexado para adicionar funcionalidade a esse controle, ou outros controles, na árvore visual da exibição. O aplicativo móvel eShopOnContainers contém a `LineColorBehavior` classe, que é um comportamento anexado. Para obter mais informações sobre esse comportamento, consulte [exibindo erros de validação](~/xamarin-forms/enterprise-application-patterns/validation.md#displaying-validation-errors).

Um Xamarin.Forms comportamento é uma classe derivada da [`Behavior`](xref:Xamarin.Forms.Behavior) [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) classe ou, em que `T` é o tipo do controle ao qual o comportamento deve ser aplicado. Essas classes fornecem `OnAttachedTo` `OnDetachingFrom` métodos e, que devem ser substituídos para fornecer lógica que será executada quando o comportamento for anexado e desanexado dos controles.

No aplicativo móvel eShopOnContainers, a `BindableBehavior<T>` classe deriva da [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) classe. A finalidade da `BindableBehavior<T>` classe é fornecer uma classe base para Xamarin.Forms comportamentos que exijam que o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) comportamento seja definido para o controle anexado.

A `BindableBehavior<T>` classe fornece um método substituível `OnAttachedTo` que define o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) do comportamento e um método substituível `OnDetachingFrom` que limpa o `BindingContext` . Além disso, a classe armazena uma referência ao controle anexado na propriedade `AssociatedObject`.

O aplicativo móvel eShopOnContainers inclui uma `EventToCommandBehavior` classe, que executa um comando em resposta a um evento que ocorre. Essa classe deriva da `BindableBehavior<T>` classe para que o comportamento possa se associar a e executar um `ICommand` especificado por uma `Command` propriedade quando o comportamento for consumido. O exemplo de código a seguir mostra a classe `EventToCommandBehavior`:

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

Os `OnAttachedTo` `OnDetachingFrom` métodos e são usados para registrar e cancelar o registro de um manipulador de eventos para o evento definido na `EventName` propriedade. Em seguida, quando o evento é acionado, o `OnFired` método é invocado, que executa o comando.

A vantagem de usar o `EventToCommandBehavior` para executar um comando quando um evento é acionado é que os comandos podem ser associados a controles que não foram projetados para interagir com comandos. Além disso, isso move o código de manipulação de eventos para exibir modelos, em que ele pode ser testado por unidade.

#### <a name="invoking-behaviors-from-a-view"></a>Invocando comportamentos de uma exibição

O `EventToCommandBehavior` é particularmente útil para anexar um comando a um controle que não dá suporte a comandos. Por exemplo, o `ProfileView` usa o `EventToCommandBehavior` para executar o `OrderDetailCommand` quando o [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) evento é acionado no [`ListView`](xref:Xamarin.Forms.ListView) que lista os pedidos do usuário, conforme mostrado no código a seguir:

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

Em tempo de execução, o `EventToCommandBehavior` responderá à interação com o [`ListView`](xref:Xamarin.Forms.ListView) . Quando um item for selecionado no `ListView` , o [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) evento será acionado, o que executará o `OrderDetailCommand` no `ProfileViewModel` . Por padrão, os argumentos de evento para o evento são passados para o comando. Esses dados são convertidos à medida que são passados entre a origem e o destino pelo conversor especificado na `EventArgsConverter` propriedade, que retorna o [`Item`](xref:Xamarin.Forms.ItemTappedEventArgs.Item) do do `ListView` [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs) . Portanto, quando o `OrderDetailCommand` é executado, o selecionado `Order` é passado como um parâmetro para a ação registrada.

Para obter mais informações sobre comportamentos, consulte [comportamentos](~/xamarin-forms/app-fundamentals/behaviors/index.md).

## <a name="summary"></a>Resumo

O padrão MVVM (Model-View-ViewModel) ajuda a separar corretamente a lógica de negócios e de apresentação de um aplicativo da interface do usuário. Manter uma separação clara entre a lógica do aplicativo e a interface do usuário ajuda a resolver inúmeros problemas de desenvolvimento e pode facilitar o teste, a manutenção e a evolução de um aplicativo. Ele também pode melhorar consideravelmente as oportunidades de reutilização de código e permite que desenvolvedores e designers de interface do usuário colaborem mais facilmente ao desenvolver suas respectivas partes de um aplicativo.

Usando o padrão MVVM, a interface do usuário do aplicativo e a apresentação subjacente e a lógica de negócios são separadas em três classes separadas: a exibição, que encapsula a interface do usuário e a lógica da interface do usuário; o modelo de exibição, que encapsula a lógica de apresentação e o estado; e o modelo, que encapsula a lógica de negócios e os dados do aplicativo.

## <a name="related-links"></a>Links Relacionados

- [Download do eBook (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
