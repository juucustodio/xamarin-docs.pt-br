---
title: Navegação do Shell do Xamarin.Forms
description: Os aplicativos do Shell do Xamarin.Forms podem usar uma experiência baseada em URI que permite a navegação para qualquer página no aplicativo, sem precisar seguir uma hierarquia definida.
ms.prod: xamarin
ms.assetid: 57079D89-D1CB-48BD-9FEE-539CEC29EABB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 747fabd68187dbe48b5e68b40916cef45269c4ae
ms.sourcegitcommit: 0596004d4a0e599c1da1ddd75a6ac928f21191c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66005175"
---
# <a name="xamarinforms-shell-navigation"></a>Navegação do Shell do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)

O Shell do Xamarin.Forms inclui uma experiência de navegação baseada em URI que usa rotas para qualquer página no aplicativo sem precisar seguir uma hierarquia definida. Além disso, eles também oferecem a capacidade de navegar para trás, sem precisar visitar todas as páginas na pilha de navegação.

`Shell` define as seguintes propriedades relacionadas à navegação:

- `BackButtonBehavior`, do tipo `BackButtonBehavior`, uma propriedade anexada que define o comportamento do botão Voltar.
- `CurrentItem`, do tipo `FlyoutItem`, o `FlyoutItem` selecionado no momento.
- `CurrentState`, do tipo `ShellNavigationState`, o estado de navegação atual do `Shell`.
- `Current`, do tipo `Shell`, um alias convertido em tipo para `Application.Current.MainPage`.

As propriedades `BackButtonBehavior`, `CurrentItem` e `CurrentState` são apoiadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), o que significa que elas podem ser destino de vinculações de dados.

A navegação é executada pela invocação do método `GoToAsync`, da classe `Shell`. Quando a navegação estiver prestes a ser executada, um evento `Navigating` será acionado, e um evento `Navigated` será acionado quando a navegação for concluída.

> [!NOTE]
> A navegação ainda poderá ser realizada em um aplicativo do Shell do Xamarin.Forms usando a propriedade [Navigation](xref:Xamarin.Forms.NavigableElement.Navigation). Saiba mais em [Navegação hierárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="routes"></a>Rotas

A navegação é executada em um aplicativo Shell, especificando um URI para onde navegar. Os URIs de navegação podem ter três componentes:

- Uma *rota*, que define o caminho para o conteúdo que existe como parte da hierarquia visual do Shell.
- Uma *página*. As páginas que não existam na hierarquia visual do Shell podem ser enviadas por push para a pilha de navegação de qualquer lugar em um aplicativo Shell. Por exemplo, uma página de detalhes do item não será definida na hierarquia visual do Shell, mas poderá ser enviada para a pilha de navegação conforme necessário.
- Um ou mais *parâmetros de consulta*. Parâmetros de consulta são aqueles que podem ser passados para a página de destino durante a navegação.

Quando um URI de navegação incluir todos os três componentes, a estrutura será: //route/page?queryParameters

### <a name="register-routes"></a>Registrar rotas

As rotas podem ser definidas em objetos `FlyoutItem`, `Tab` e `ShellContent` por meio das propriedades `Route`:

```xaml
<Shell ...>
    <FlyoutItem ...
                Route="animals">
        <Tab ...
             Route="domestic">
            <ShellContent ...
                          Route="cats" />
            <ShellContent ...
                          Route="dogs" />
        </Tab>
        <ShellContent ...
                      Route="monkeys" />
        <ShellContent ...
                      Route="elephants" />  
        <ShellContent ...
                      Route="bears" />
    </FlyoutItem>
    <ShellContent ...
                  Route="about" />                  
    ...
</Shell>
```

> [!NOTE]
> Todos os itens na hierarquia do Shell têm uma rota associada a eles. Se uma rota não for definida pelo desenvolvedor, ela será gerada em tempo de execução. No entanto, não há garantia de que as rotas geradas sejam consistentes em sessões de aplicativos diferentes.

Neste caso, do aplicativo de exemplo, a hierarquia de rotas a seguir é criada, que pode ser usada na navegação programática:

```
animals
  domestic
    cats
    dogs
  monkeys
  elephants
  bears
about
```

Para navegar até o objeto `ShellContent` para a rota `dogs`, o URI da rota absoluta é `//animals/domestic/dogs`. Da mesma forma, para navegar até o objeto `ShellContent` para a rota `about`, o URI da rota absoluta é `//about`.

> [!IMPORTANT]
> Nomes duplicados de rota são permitidos. No entanto, rotas duplicadas não são. Uma `ArgumentException` será lançada na inicialização do aplicativo se uma rota duplicada for detectada.

#### <a name="register-page-routes"></a>Registrar rotas de página

No construtor de subclasse do Shell ou em qualquer outro local que seja executado antes da invocação de uma rota, é possível registrar explicitamente rotas adicionais para todas as páginas que não sejam representadas na hierarquia visual do Shell:

```csharp
Routing.RegisterRoute("monkeydetails", typeof(MonkeyDetailPage));
Routing.RegisterRoute("beardetails", typeof(BearDetailPage));
Routing.RegisterRoute("catdetails", typeof(CatDetailPage));
Routing.RegisterRoute("dogdetails", typeof(DogDetailPage));
Routing.RegisterRoute("elephantdetails", typeof(ElephantDetailPage));
```

Este exemplo registra páginas de detalhes do item, que não estão definidas na subclasse do Shell, como rotas. Essas páginas, então, podem ser navegadas usando a navegação baseada em URI de qualquer lugar dentro do aplicativo. As rotas para essas páginas são conhecidas como *rotas globais*.

> [!NOTE]
> As páginas cujas rotas tenham sido registradas com o método `Routing.RegisterRoute` podem ter o registro cancelado com o método `Routing.UnRegisterRoute`, se necessário.

Como alternativa, as páginas podem ser registradas em hierarquias de rota diferentes, se necessário:

```csharp
Routing.RegisterRoute("monkeys/details", typeof(MonkeyDetailPage));
Routing.RegisterRoute("bears/details", typeof(BearDetailPage));
Routing.RegisterRoute("cats/details", typeof(CatDetailPage));
Routing.RegisterRoute("dogs/details", typeof(DogDetailPage));
Routing.RegisterRoute("elephants/details", typeof(ElephantDetailPage));
```

Este exemplo habilita a navegação de páginas contextuais, onde a navegação até a rota `details` partindo da página para a rota `monkeys` exibe o `MonkeyDetailPage`. Da mesma forma, a navegação até a rota `details` partindo da página para a rota `elephants` exibe o `ElephantDetailPage`.

> [!IMPORTANT]
> Atualmente, os nomes de rota duplicados são permitidos ao usar o método `Routing.RegisterRoute`, com o registro duplicado substituindo o registro anterior.

## <a name="perform-navigation"></a>Realizar navegação

Para realizar a navegação, uma referência para a subclasse `Shell` deve ser obtida primeiro. Isso pode ser feito por meio da conversão da propriedade `App.Current.MainPage` em um objeto `Shell`, ou usando a propriedade `Shell.Current`. A navegação pode então ser realizada chamando o método `GoToAsync` no objeto `Shell`. Esse método navega até uma `ShellNavigationState` e retorna um `Task`, que será concluído depois que a animação de navegação for concluída. O objeto `ShellNavigationState` é construído pelo método `GoToAsync`, de uma `string` ou uma `Uri`, e ele tem a propriedade `Location` definida como o argumento `string` ou `Uri`.

Quando uma rota da hierarquia visual do Shell é navegada, uma pilha de navegação não é criada. Mas isso acontece quando uma página que não está na hierarquia visual do Shell é navegada.

> [!NOTE]
> O estado atual de navegação do `Shell` pode ser recuperado por meio da propriedade `Shell.Current.CurrentState`, que inclui o URI da rota exibido na propriedade `Location`.

### <a name="absolute-routes"></a>Rotas absolutas

A navegação pode ser realizada por meio da especificação de um URI absoluto válido como um argumento para o método `GoToAsync`:

```csharp
await Shell.Current.GoToAsync("//animals/monkeys");
```

Este exemplo navega até a página para a rota `monkeys`, com a rota sendo definida em um objeto `ShellContent`. O objeto `ShellContent` que representa a rota `monkeys` é um filho de um objeto `FlyoutItem`, cuja rota é `animals`.

### <a name="relative-routes"></a>Rotas relativas

A navegação também pode ser realizada por meio da especificação de um URI relativo válido como um argumento para o método `GoToAsync`. O sistema de roteamento tentará fazer a correspondência do URI a um objeto `ShellContent`. Portanto, se todas as rotas em um aplicativo forem exclusivas, a navegação poderá ser realizada simplesmente pela especificação do nome da rota exclusiva como um URI relativo:

```csharp
await Shell.Current.GoToAsync("monkeydetails");
```

Este exemplo navega até a página para a rota `monkeydetails`.

Além disso, há suporte para os seguintes formatos de rota relativa:

| Formatar | Descrição |
| --- | --- |
| //*rota* | A hierarquia de rotas será pesquisada à procura da rota especificada, acima da rota exibida atualmente. |
| ///*rota* | A hierarquia de rotas será pesquisada à procura da rota especificada, abaixo da rota exibida atualmente. |

#### <a name="contextual-navigation"></a>Navegação contextual

As rotas relativas habilitam a navegação contextual. Por exemplo, considere a seguinte hierarquia de rotas:

```
monkeys
  details
bears
  details
```

Quando a página registrada para a rota `monkeys` for exibida, a navegação até a rota `details` exibirá a página registrada para a rota `monkeys/details`. Da mesma forma, quando a página registrada para a rota `bears` for exibida, a navegação até a rota `details` exibirá a página registrada para a rota `bears/details`. Saiba mais sobre como registrar as rotas neste exemplo em [Registrar rotas de página](#register-page-routes).

### <a name="invalid-routes"></a>Rotas inválidas

Os seguintes formatos de rota são inválidos:

| Formatar | Explicação |
| --- | --- |
| *rota* ou /*rota* | As rotas na hierarquia visual não podem ser enviadas por push para a pilha de navegação. |
| //*página* ou ///*página* | No momento, as rotas globais não podem ser a única página na pilha de navegação. Portanto, não há suporte para roteamento absoluto para rotas globais. |

O uso de qualquer um desses formatos de rota resulta na geração de uma `Exception`.

> [!IMPORTANT]
> A tentativa de navegar até uma rota não existente resulta na geração de uma exceção `ArgumentException`.

### <a name="debugging-navigation"></a>Depuração de navegação

Algumas das classes de Shell são decoradas com o `DebuggerDisplayAttribute`, que especifica como uma classe ou um campo é exibido pelo depurador. Isso pode ajudar a depurar solicitações de navegação por meio da exibição de dados relacionados à solicitação de navegação. Por exemplo, a captura de tela a seguir mostra as propriedades `CurrentItem` e `CurrentState` do objeto `Shell.Current`:

![Captura de tela do depurador](navigation-images/debugger.png "Depurador")

Neste exemplo, a propriedade `CurrentItem`, do tipo `FlyoutItem`, exibe o título e a rota do objeto `FlyoutItem`. Da mesma forma, a propriedade `CurrentState`, do tipo `ShellNavigationState`, exibe o URI da rota exibida no aplicativo do Shell.

### <a name="tab-class"></a>Classe Tab

A classe `Tab` define uma propriedade `Stack`, do tipo `IReadOnlyList<Page>`, que representa a pilha de navegação atualmente enviada por push na `Tab`. A classe também fornece os seguintes métodos substituíveis de navegação:

- `GetNavigationStack` retorna `IReadOnlyList<Page`>, a pilha de navegação atual.
- `OnInsertPageBefore`, chamado quando `INavigation.InsertPageBefore` é chamado.
- `OnPopAsync` retorna `Task<Page>` e é chamado quando `INavigation.PopAsync` é chamado.
- `OnPopToRootAsync` retorna `Task` e é chamado quando `INavigation.OnPopToRootAsync` é chamado.
- `OnPushAsync` retorna `Task` e é chamado quando `INavigation.PushAsync` é chamado.
- `OnRemovePage`, chamado quando `INavigation.RemovePage` é chamado.

## <a name="navigation-events"></a>Eventos de navegação

A classe `Shell` define um evento `Navigating`, que é acionado quando a navegação está prestes a ser realizada, devido à navegação programática ou à interação do usuário. O objeto `ShellNavigatingEventArgs` que acompanha o evento `Navigating` fornece as seguintes propriedades:

| Propriedade | Tipo | Descrição |
|---|---|---|
| Atual | `ShellNavigationState` | O URI da página atual. |
| Origem | `ShellNavigationSource` | O tipo de navegação que ocorreu. |
| Destino | `ShellNavigationState`  | O URI que representa para onde a navegação se destina. |
| CanCancel  | `bool` | Um valor que indica se é possível cancelar a navegação. |
| Cancelada  | `bool` | Um valor que indica se a navegação foi cancelada. |

Além disso, a classe `ShellNavigatingEventArgs` fornece um método `Cancel` que pode ser usado para cancelar a navegação.

> [!NOTE]
> O evento `Navigated` é acionado pelo método substituível `OnNavigating` na classe `Shell`.

A classe `Shell` também define um evento`Navigated`, que é acionado quando a navegação é concluída. O objeto `ShellNavigatedEventArgs` que acompanha o evento `Navigating` fornece as seguintes propriedades:

| Propriedade | Tipo | Descrição |
|---|---|---|
| Atual | `ShellNavigationState` | O URI da página atual. |
| Voltar| `ShellNavigationState` | O URI da página anterior. |
| Origem  | `ShellNavigationSource` | O tipo de navegação que ocorreu. |

> [!NOTE]
> O evento `Navigating` é acionado pelo método substituível `OnNavigated` na classe `Shell`.

As classes `ShellNavigatedEventArgs` e `ShellNavigatingEventArgs` têm propriedades `Source` do tipo `ShellNavigationSource`. Esta enumeração fornece os seguintes valores:

- `Unknown`
- `Push`
- `Pop`
- `PopToRoot`
- `Insert`
- `Remove`
- `ShellItemChanged`
- `ShellSectionChanged`
- `ShellContentChanged`

Portanto, em um manipulador para o evento `Navigating`, é possível interceptar a navegação e executar ações com base na origem de navegação. Por exemplo, o código a seguir mostra como cancelar a navegação na ordem inversa caso os dados da página não tenham sido salvos:

```csharp
void OnNavigating(object sender, ShellNavigatingEventArgs e)
{
    // Cancel back navigation if data is unsaved
    if (e.Source == ShellNavigationSource.Pop && !dataSaved)
    {
        e.Cancel();
    }
}
```

## <a name="pass-data"></a>Passar dados

Os dados podem ser passados como parâmetros de consulta durante a execução da navegação programática. Neste caso, o código a seguir é executado no aplicativo de exemplo quando um usuário seleciona um elefante no `ElephantsPage`:

```csharp
async void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string elephantName = (e.CurrentSelection.FirstOrDefault() as Animal).Name;
    await Shell.Current.GoToAsync($"//animals/elephants/elephantdetails?name={elephantName}");
}
```

Este exemplo de código recupera o elefante atualmente selecionado em [`CollectionView`](xref:Xamarin.Forms.CollectionView) e navega até a rota `elephantdetails`, passando `elephantName` como um parâmetro de consulta. Os parâmetros de consulta serão codificados em URL para navegação, portanto, "Elefante Indiano" se tornará "Elefante%20Indiano".

Para receber dados, a classe que representa a página a ser navegada, ou a classe do [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) da página, deve ser decorada com um `QueryPropertyAttribute` para cada parâmetro de consulta:

```csharp
[QueryProperty("Name", "name")]
public partial class ElephantDetailPage : ContentPage
{
    public string Name
    {
        set
        {
            BindingContext = ElephantData.Elephants.FirstOrDefault(m => m.Name == Uri.UnescapeDataString(value));
        }
    }
    ...
}
```

O primeiro argumento para o `QueryPropertyAttribute` especifica o nome da propriedade que receberá os dados, com o segundo argumento especificando a identificação do parâmetro de consulta. Portanto, o `QueryPropertyAttribute` no exemplo acima especifica que a propriedade `Name` receberá os dados passados no parâmetro de consulta `name` do URI na chamada de método `GoToAsync`. A propriedade `Name`, em seguida a URL, decodifica o valor do parâmetro de consulta e o utiliza para definir o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) da página para o objeto que será exibido.

> [!NOTE]
> Uma classe pode ser decorada com vários objetos `QueryPropertyAttribute`.

## <a name="back-button-behavior"></a>Comportamento do botão Voltar

A classe `BackButtonBehavior` define as propriedades a seguir, que controlam a aparência e o comportamento do botão Voltar:

- `Command`, do tipo `ICommand`, que é executado quando o botão Voltar é pressionado.
- `CommandParameter`, do tipo `object`, que é o parâmetro passado para `Command`.
- `IconOveride`, do tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), o ícone usado para o botão Voltar.
- `IsEnabled`, do tipo `boolean`, indica se o botão Voltar está habilitado. O valor padrão é `true`.
- `TextOverride`, do tipo `string`, o texto usado para o botão Voltar.

Todas essas propriedades são apoiadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), o que significa que essas propriedades podem ser o destino de vinculações de dados.

A classe `BackButtonBehavior` pode ser consumida por meio da definição da propriedade anexada `Shell.BackButtonBehavior` a um objeto `BackButtonBehavior`:

```xaml
<ContentPage ...>    
    <Shell.BackButtonBehavior>
        <BackButtonBehavior Command="{Binding BackCommand}"
                            IconOverride="back.png" />   
    </Shell.BackButtonBehavior>
    ...
</ContentPage>
```

Este é o código C# equivalente:

```csharp
Shell.SetBackButtonBehavior(this, new BackButtonBehavior
{
    Command = new Command(() =>
    {
        ...
    }),
    IconOverride = "back.png"
});
```

A propriedade `Command` é definida como uma `ICommand` a ser executada quando o botão Voltar é pressionado, e a propriedade `IconOverride` é definida como o ícone usado pelo botão Voltar:

[![Captura de tela da substituição de um ícone do botão Voltar no iOS e no Android](navigation-images/back-button.png "Substituição de ícone do botão Voltar do Shell")](navigation-images/back-button-large.png#lightbox "Substituição de ícone do botão Voltar do Shell")

## <a name="related-links"></a>Links relacionados

- [Xaminals (exemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)
