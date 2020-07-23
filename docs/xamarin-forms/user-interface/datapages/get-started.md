---
title: Introdução com as páginas de
description: Este artigo explica como começar a criar uma página simples controlada por dados usando Xamarin.Forms DataPages.
ms.prod: xamarin
ms.assetid: 6416E5FA-6384-4298-BAA1-A89381E47210
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 325aa7be9e8a1eb6da816ef4ec6403c2882c6ed4
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86928907"
---
# <a name="getting-started-with-datapages"></a>Introdução com as páginas de

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)

![Esta API está atualmente em visualização](~/media/shared/preview.png)

> [!IMPORTANT]
> As páginas de datarequer uma Xamarin.Forms referência de tema para renderizar. Isso envolve a instalação do [ Xamarin.Forms . ](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/)O pacote NuGet do Theme. base em seu projeto, seguido pelo [ Xamarin.Forms . Theme. Light](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/) ou [ Xamarin.Forms . ](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/)Os pacotes NuGet. Dark do tema.

Para começar a criar uma página controlada por dados simples usando a visualização de DataPages, siga as etapas abaixo. Esta demonstração usa um estilo codificado ("eventos") nas compilações de visualização que funcionam apenas com o formato JSON específico no código.

[![Aplicativo de exemplo DataPages](get-started-images/demo-sml.png)](get-started-images/demo.png#lightbox "Aplicativo de exemplo DataPages")

## <a name="1-add-nuget-packages"></a>1. adicionar pacotes NuGet

Adicione esses pacotes NuGet à sua Xamarin.Forms biblioteca de .net Standard e projetos de aplicativo:

- Xamarin.Forms. Pages
- Xamarin.Forms. Theme. base
- Um NuGet de implementação de tema (por exemplo, Xamarin.Forms. Tema. Light)

## <a name="2-add-theme-reference"></a>2. Adicionar referência de tema

No arquivo **app. XAML** , adicione um personalizado `xmlns:mytheme` para o tema e verifique se o tema é mesclado no dicionário de recursos do aplicativo:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
  xmlns:mytheme="clr-namespace:Xamarin.Forms.Themes;assembly=Xamarin.Forms.Theme.Light"
  x:Class="DataPagesDemo.App">
    <Application.Resources>
        <ResourceDictionary MergedWith="mytheme:LightThemeResources" />
    </Application.Resources>
</Application>
```

> [!IMPORTANT]
> Você também deve seguir as etapas para [carregar assemblies de tema (abaixo)](#troubleshooting) , adicionando um código clichê ao Ios `AppDelegate` e ao Android `MainActivity` . Isso será aprimorado em uma versão de visualização futura.

## <a name="3-add-a-xaml-page"></a>3. adicionar uma página XAML

Adicione uma nova página XAML ao Xamarin.Forms aplicativo e *altere a classe base* de `ContentPage` para `Xamarin.Forms.Pages.ListDataPage` . Isso deve ser feito tanto no C# quanto no XAML:

**Arquivo C#**

```csharp
public partial class SessionDataPage : Xamarin.Forms.Pages.ListDataPage // was ContentPage
{
  public SessionDataPage ()
  {
    InitializeComponent ();
  }
}
```

**Arquivo XAML**

Além de alterar o elemento raiz para `<p:ListDataPage>` o namespace personalizado para `xmlns:p` também deve ser adicionado:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<p:ListDataPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:p="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
             x:Class="DataPagesDemo.SessionDataPage">

    <ContentPage.Content></ContentPage.Content>

</p:ListDataPage>
```

**Subclasse de aplicativo**

Altere o `App` Construtor de classe para que o `MainPage` esteja definido como um `NavigationPage` contendo o novo `SessionDataPage` . Uma página de navegação *deve* ser usada.

```csharp
MainPage = new NavigationPage (new SessionDataPage ());
```

## <a name="3-add-the-datasource"></a>3. adicionar a fonte de fontes

Exclua o `Content` elemento e substitua-o por um `p:ListDataPage.DataSource` para preencher a página com dados. No exemplo abaixo, um arquivo de dados JSON remoto está sendo carregado a partir de uma URL.

> [!NOTE]
> A visualização *requer* um `StyleClass` atributo para fornecer dicas de renderização para a fonte de dados. O `StyleClass="Events"` faz referência a um layout que é predefinido na visualização e contém estilos *codificados* para corresponder à fonte de dados JSON que está sendo usada.

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<p:ListDataPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:p="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
             x:Class="DataPagesDemo.SessionDataPage"
             Title="Sessions" StyleClass="Events">

    <p:ListDataPage.DataSource>
        <p:JsonDataSource Source="http://demo3143189.mockable.io/sessions" />
    </p:ListDataPage.DataSource>

</p:ListDataPage>
```

**Dados JSON**

Um exemplo dos dados JSON da origem da demonstração é mostrado abaixo:

```json
[{
  "end": "2016-04-27T18:00:00Z",
  "start": "2016-04-27T17:15:00Z",
  "abstract": "The new Apple TV has been released, and YOU can be one of the first developers to write apps for it. To make things even better, you can build these apps in C#! This session will introduce the basics of how to create a tvOS app with Xamarin, including: differences between tvOS and iOS APIs, TV user interface best practices, responding to user input, as well as the capabilities and limitations of building apps for a television. Grab some popcorn—this is going to be good!",
  "title": "As Seen On TV … Bringing C# to the Living Room",
  "presenter": "Matthew Soucoup",
  "biography": "Matthew is a Xamarin MVP and Certified Xamarin Developer from Madison, WI. He founded his company Code Mill Technologies and started the Madison Mobile .Net Developers Group.  Matt regularly speaks on .Net and Xamarin development at user groups, code camps and conferences throughout the Midwest. Matt gardens hot peppers, rides bikes, and loves Wisconsin micro-brews and cheese.",
  "image": "http://i.imgur.com/ASj60DP.jpg",
  "avatar": "http://i.imgur.com/ASj60DP.jpg",
  "room": "Crick"
}]
```

## <a name="4-run"></a>4. Execute!

As etapas acima devem resultar em uma página de dados de trabalho:

[![Aplicativo de exemplo DataPages](get-started-images/demo-sml.png)](get-started-images/demo.png#lightbox "Aplicativo de exemplo DataPages")

Isso funciona porque o estilo predefinido **"eventos"** existe no pacote NuGet do tema claro e tem estilos definidos que correspondem à fonte de dados (por exemplo, "título", "imagem", "apresentador").

O "Events" `StyleClass` é criado para exibir o `ListDataPage` controle com um `CardView` controle personalizado que é definido em Xamarin.Forms . Pages. O `CardView` controle tem três propriedades: `ImageSource` , `Text` e `Detail` . O tema é codificado para associar os três campos da fonte de arquivos (do arquivo JSON) a essas propriedades para exibição.

## <a name="5-customize"></a>5. personalizar

O estilo herdado pode ser substituído especificando-se um modelo e usando associações de fonte de dados. O XAML a seguir declara um modelo personalizado para cada linha usando a nova `ListItemControl` `{p:DataSourceBinding}` sintaxe e que está incluída no ** Xamarin.Forms . **NuGet de páginas:

```xaml
<p:ListDataPage.DefaultItemTemplate>
    <DataTemplate>
        <ViewCell>
            <p:ListItemControl
                Title="{p:DataSourceBinding title}"
                Detail="{p:DataSourceBinding room}"
                ImageSource="{p:DataSourceBinding image}"
                DataSource="{Binding Value}"
                HeightRequest="90"
            >
            </p:ListItemControl>
        </ViewCell>
    </DataTemplate>
</p:ListDataPage.DefaultItemTemplate>
```

Ao fornecer `DataTemplate` esse código, o substitui o `StyleClass` e, em vez disso, usa o layout padrão para um `ListItemControl` .

[![Aplicativo de exemplo DataPages](get-started-images/custom-sml.png)](get-started-images/custom.png#lightbox "Aplicativo de exemplo DataPages")

Os desenvolvedores que preferem C# ao XAML também podem criar associações de fonte de dados (Lembre-se de incluir uma `using Xamarin.Forms.Pages;` instrução):

```csharp
SetBinding (TitleProperty, new DataSourceBinding ("title"));
```

É um pouco mais trabalho criar temas do zero, mas versões de visualização futuras tornarão isso mais fácil.

## <a name="troubleshooting"></a>Solução de problemas

## <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>Não foi possível carregar o arquivo ou assembly ' Xamarin.Forms . Theme. Light ' ou uma de suas dependências

Na versão de visualização, os temas podem não ser capazes de carregar em tempo de execução. Adicione o código mostrado abaixo nos projetos relevantes para corrigir esse erro.

**iOS**

No **AppDelegate.cs** , adicione as seguintes linhas após`LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

No **MainActivity.cs** , adicione as seguintes linhas após`LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```

## <a name="related-links"></a>Links Relacionados

- [Exemplo de DataPagesDemo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)
