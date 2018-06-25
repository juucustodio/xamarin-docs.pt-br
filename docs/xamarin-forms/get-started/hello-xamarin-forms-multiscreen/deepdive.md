---
title: Aprofundamento na multitela do Xamarin.Forms
description: Este artigo apresenta a navegação de página e vinculação de dados em um aplicativo Xamarin.Forms e demonstra seu uso em um aplicativo multiplataforma multitela.
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: e4faa36c-6600-48c0-94c4-b4431103a4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/02/2016
ms.openlocfilehash: 1c7edff3c71b9d7530b2acf21acaa06149156d43
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35242229"
---
# <a name="xamarinforms-multiscreen-deep-dive"></a>Aprofundamento na multitela do Xamarin.Forms

No [Início rápido da multitela do Xamarim.Forms](~/xamarin-forms/get-started/hello-xamarin-forms-multiscreen/quickstart.md), o aplicativo Phoneword foi estendido para incluir uma segunda tela que controla o histórico de chamadas para o aplicativo. Este artigo examina o que foi compilado para entender a navegação de página e associação de dados em um aplicativo Xamarin.Forms.

## <a name="navigation"></a>Navegação

O Xamarin.Forms fornece um modelo de navegação interna que gerencia a navegação e a experiência do usuário de uma pilha de páginas. Este modelo implementa uma pilha UEPS (último a entrar, primeiro a sair) de objetos `Page`. Para mover de uma página para outra, um aplicativo enviará por push uma nova página para essa pilha. Para retornar para a página anterior, o aplicativo exibirá a página atual da pilha.

O Xamarin.Forms tem uma classe [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) que gerencia a pilha de objetos [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/). A classe `NavigationPage` também adicionará uma barra de navegação à parte superior da página que exibe um título e um botão <span class="uiitem">Voltar</span> apropriado para a plataforma, que retornará à página anterior. O exemplo de código a seguir mostra como encapsular um `NavigationPage` em torno da primeira página em um aplicativo:

```csharp
public App ()
{
    ...
    MainPage = new NavigationPage (new MainPage ());
}
```

Todas as instâncias do [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) têm uma propriedade [`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) que expõe métodos para modificar a pilha de páginas. Esses métodos devem ser invocados somente se o aplicativo incluir um [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/). Para navegar até o `CallHistoryPage`, é necessário invocar o método [`PushAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync/p/Xamarin.Forms.Page/) conforme demonstrado no exemplo de código abaixo:

```csharp
async void OnCallHistory(object sender, EventArgs e)
{
    await Navigation.PushAsync (new CallHistoryPage ());
}
```

Isso faz com que o novo objeto `CallHistoryPage` seja enviado para a pilha de navegação. Para retornar programaticamente à página original, o objeto `CallHistoryPage` deve invocar o método [`PopAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/), conforme demonstrado no exemplo de código abaixo:

```csharp
await Navigation.PopAsync();
```

No entanto, no aplicativo Phoneword, esse código não é exigido porque a classe [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) adiciona uma barra de navegação à parte superior da página que inclui um botão <span class="uiitem">Voltar</span> apropriado para a plataforma, que retornará à página anterior.

## <a name="data-binding"></a>Associação de dados

Vinculação de dados é usada para simplificar como um aplicativo Xamarin.Forms exibe e interage com seus dados. Ela estabelece uma conexão entre a interface do usuário e o aplicativo subjacente. A classe [`BindableObject`](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/) contém a maior parte da infraestrutura para dar suporte à vinculação de dados.

A vinculação de dados define a relação entre dois objetos. O objeto de *origem* fornecerá dados. O objeto de *destino* consumirá (e geralmente exibirá) dados do objeto de origem. No aplicativo Phoneword, o destino de associação é o controle [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) que exibe os números de telefone, enquanto a coleção `PhoneNumbers` é a origem da associação.

A coleção `PhoneNumbers` é declarada e inicializada na classe `App`, conforme mostrado no exemplo de código a seguir:

```csharp
public partial class App : Application
{
   public static List<string> PhoneNumbers { get; set; }

   public App ()
   {
     PhoneNumbers = new List<string>();
     ...
   }
   ...
}
```

A instância [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) é declarada e inicializada na classe `CallHistoryPage`, conforme mostrado no exemplo de código a seguir:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage ...
             xmlns:local="clr-namespace:Phoneword;assembly=Phoneword"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    ...
    <ContentPage.Content>
       ...
       <ListView ItemsSource="{x:Static local:App.PhoneNumbers}" />
       ...
    </ContentPage.Content>
</ContentPage>
```

Neste exemplo, o controle [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) exibirá a coleta de dados `IEnumerable` a qual a propriedade [`ItemsSource`](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView.ItemsSource/) está associada. A coleta de dados pode ser objetos de qualquer tipo, mas, por padrão, `ListView` usará o método `ToString` de cada item para exibir o item. A extensão de marcação [`x:Static`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticExtension/) é usada para indicar que a propriedade `ItemsSource` será associada a uma propriedade estática `PhoneNumbers` da classe `App`, que pode ser localizada no namespace `local`.

Para obter mais informações sobre vinculação de dados, veja [Noções básicas de vinculação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md). Para obter mais informações sobre extensões de marcação XAML, consulte [Extensões de marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md).

## <a name="additional-concepts-introduced-in-phoneword"></a>Conceitos adicionais introduzidos no Phoneword

O [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) é responsável por exibir uma coleção de itens na tela. Cada item em `ListView` está contido em uma única célula. Para obter mais informações sobre o uso do controle `ListView`, veja [ListView](~/xamarin-forms/user-interface/listview/index.md).

## <a name="summary"></a>Resumo

Este artigo apresentou navegação de página e vinculação de dados em um aplicativo Xamarin.Forms e demonstrou seu uso em um aplicativo de plataforma cruzada multitela.
