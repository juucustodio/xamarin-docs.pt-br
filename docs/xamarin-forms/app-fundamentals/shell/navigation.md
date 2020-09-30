---
title: Xamarin.Forms Navegação do Shell
description: Xamarin.Forms Os aplicativos de shell podem utilizar uma experiência de navegação baseada em URI que permite a navegação para qualquer página no aplicativo, sem a necessidade de seguir uma hierarquia de navegação de conjunto.
ms.prod: xamarin
ms.assetid: 57079D89-D1CB-48BD-9FEE-539CEC29EABB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6cf4932c3265d1d66200ae12ba448a758586f11c
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563140"
---
# <a name="no-locxamarinforms-shell-navigation"></a>Xamarin.Forms Navegação do Shell

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Xamarin.Forms O Shell inclui uma experiência de navegação baseada em URI que usa rotas para navegar para qualquer página no aplicativo, sem precisar seguir uma hierarquia de navegação de conjunto. Além disso, eles também oferecem a capacidade de navegar para trás, sem precisar visitar todas as páginas na pilha de navegação.

`Shell` define as seguintes propriedades relacionadas à navegação:

- `BackButtonBehavior`, do tipo `BackButtonBehavior`, uma propriedade anexada que define o comportamento do botão Voltar.
- `CurrentItem`, do tipo `FlyoutItem`, o `FlyoutItem` selecionado no momento.
- `CurrentState`, do tipo `ShellNavigationState`, o estado de navegação atual do `Shell`.
- `Current`, do tipo `Shell`, um alias convertido em tipo para `Application.Current.MainPage`.

As `BackButtonBehavior` `CurrentItem` Propriedades,, e `CurrentState` são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que essas propriedades podem ser destinos de associações de dados.

A navegação é executada pela invocação do método `GoToAsync`, da classe `Shell`. Quando a navegação estiver prestes a ser executada, um evento `Navigating` será acionado, e um evento `Navigated` será acionado quando a navegação for concluída.

> [!NOTE]
> A navegação ainda pode ser executada em um Xamarin.Forms aplicativo de shell usando a propriedade de [navegação](xref:Xamarin.Forms.NavigableElement.Navigation) . Saiba mais em [Navegação hierárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

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
> Todos os itens na hierarquia do Shell têm uma rota associada a eles. Se uma rota não for definida pelo desenvolvedor, ela será gerada em runtime. No entanto, não há garantia de que as rotas geradas sejam consistentes em sessões de aplicativos diferentes.

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
> Uma `ArgumentException` será lançada na inicialização do aplicativo se uma rota duplicada for detectada. Essa exceção também será gerada se duas ou mais rotas do mesmo nível na hierarquia compartilharem um nome.

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
> Um `ArgumentException` será lançado se o método `Routing.RegisterRoute` tentar registrar a mesma rota em dois ou mais tipos diferentes.

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

### <a name="backwards-navigation"></a>Navegação regressiva

A navegação para trás pode ser executada especificando ".." como o argumento para o `GotoAsync` método:

```csharp
await Shell.Current.GoToAsync("..");
```

A navegação para trás com ".." também pode ser combinada com uma rota, da seguinte maneira:

```csharp
await Shell.Current.GoToAsync("../route");
```

Neste exemplo, o efeito geral é navegar para trás e, em seguida, navegar até a rota especificada.

> [!IMPORTANT]
> Navegar para trás e para uma rota especificada só será possível se a navegação para trás colocar você no local atual na hierarquia de rotas para navegar até a rota especificada.

Da mesma forma, é possível navegar para trás várias vezes e, em seguida, navegar para uma rota especificada:

```csharp
await Shell.Current.GoToAsync("../../route");
```

Neste exemplo, o efeito geral é navegar duas vezes para trás e, em seguida, navegar até a rota especificada.

> [!NOTE]
> Os dados também podem ser passados ao navegar com "..". Para obter mais informações, consulte [transmitir dados](#pass-data).

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
| `Current` | `ShellNavigationState` | O URI da página atual. |
| `Source` | `ShellNavigationSource` | O tipo de navegação que ocorreu. |
| `Target` | `ShellNavigationState`  | O URI que representa para onde a navegação se destina. |
| `CanCancel`  | `bool` | Um valor que indica se é possível cancelar a navegação. |
| `Cancelled`  | `bool` | Um valor que indica se a navegação foi cancelada. |

Além disso, a classe `ShellNavigatingEventArgs` fornece um método `Cancel` que pode ser usado para cancelar a navegação.

> [!NOTE]
> O evento `Navigated` é acionado pelo método substituível `OnNavigating` na classe `Shell`.

A classe `Shell` também define um evento`Navigated`, que é acionado quando a navegação é concluída. O objeto `ShellNavigatedEventArgs` que acompanha o evento `Navigating` fornece as seguintes propriedades:

| Propriedade | Tipo | Descrição |
|---|---|---|
| `Current` | `ShellNavigationState` | O URI da página atual. |
| `Previous`| `ShellNavigationState` | O URI da página anterior. |
| `Source`  | `ShellNavigationSource` | O tipo de navegação que ocorreu. |

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

Este exemplo de código recupera o elefante selecionado no momento no e [`CollectionView`](xref:Xamarin.Forms.CollectionView) navega até a `elephantdetails` rota, passando `elephantName` como um parâmetro de consulta. Os parâmetros de consulta serão codificados em URL para navegação, portanto, "Elefante Indiano" se tornará "Elefante%20Indiano".

Para receber dados, a classe que representa a página que está sendo navegada, ou a classe para a página [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) , deve ser decorada com um `QueryPropertyAttribute` para cada parâmetro de consulta:

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

O primeiro argumento para o `QueryPropertyAttribute` especifica o nome da propriedade que receberá os dados, com o segundo argumento especificando a ID de parâmetro de consulta. Portanto, o `QueryPropertyAttribute` no exemplo acima Especifica que a `Name` Propriedade receberá os dados passados no `name` parâmetro de consulta do URI na `GoToAsync` chamada do método. A `Name` propriedade, então, a URL decodifica o valor do parâmetro de consulta e o usa para definir a [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) da página para o objeto que será exibido.

> [!NOTE]
> Uma classe pode ser decorada com vários objetos `QueryPropertyAttribute`.

## <a name="back-button-behavior"></a>Comportamento do botão Voltar

A classe `BackButtonBehavior` define as propriedades a seguir, que controlam a aparência e o comportamento do botão Voltar:

- `Command`, do tipo `ICommand`, que é executado quando o botão Voltar é pressionado.
- `CommandParameter`, do tipo `object`, que é o parâmetro passado para `Command`.
- `IconOverride`, do tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource) , o ícone usado para o botão voltar.
- `IsEnabled`, do tipo `boolean`, indica se o botão Voltar está habilitado. O valor padrão é `true`.
- `TextOverride`, do tipo `string`, o texto usado para o botão Voltar.

Todas essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados.

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

[![Captura de tela de uma substituição de ícone de botão voltar do Shell, no iOS e no Android](navigation-images/back-button.png "Substituir ícone do botão voltar do Shell")](navigation-images/back-button-large.png#lightbox "Substituir ícone do botão voltar do Shell")

## <a name="related-links"></a>Links relacionados

- [Xaminals (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)