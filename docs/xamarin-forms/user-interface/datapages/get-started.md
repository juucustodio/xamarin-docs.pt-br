---
title: Guia de Introdução DataSourceControl
description: Este artigo explica como começar a criação de uma página simple controlado por dados usando o xamarin. Forms DataSourceControl.
ms.prod: xamarin
ms.assetid: 6416E5FA-6384-4298-BAA1-A89381E47210
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: e3256787c0bc0852275f663772b8a91a6825a0dd
ms.sourcegitcommit: 34819671c7910d29f018bdb394ddd4a4b0cd3a31
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59675862"
---
# <a name="getting-started-with-datapages"></a>Guia de Introdução DataSourceControl

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)

![](~/media/shared/preview.png "Essa API está atualmente em versão prévia")

> [!IMPORTANT]
> DataSourceControl requer uma [xamarin. Forms tema](~/xamarin-forms/user-interface/themes/index.md) referência ao renderizar.


Para começar a criação de uma página simple controlado por dados usando a visualização DataSourceControl, siga as etapas abaixo. Este usa demonstração um estilo de embutidos em código ("eventos") na visualização compilações que só funciona com o formato JSON específico no código.

[![](get-started-images/demo-sml.png "Aplicativo de exemplo DataSourceControl")](get-started-images/demo.png#lightbox "DataSourceControl exemplo de aplicativo")

## <a name="1-add-nuget-packages"></a>1. Adicionar pacotes NuGet

Adicione esses pacotes do Nuget aos seus projetos de biblioteca e o aplicativo xamarin. Forms .NET Standard:

* Xamarin.Forms.Pages
* Xamarin.Forms.Theme.Base
* Uma implementação de tema (por exemplo, o Nuget Xamarin.Forms.Theme.Light)

## <a name="2-add-theme-reference"></a>2. Adicionar referência de tema

No **App. XAML** do arquivo, adicione um personalizado `xmlns:mytheme` do tema e verifique se o tema é mesclado no dicionário de recursos do aplicativo:

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

**IMPORTANTE:** Você também deve seguir as etapas a serem [carregar assemblies de tema (abaixo)](#loadtheme) adicionando um código clichê para o iOS `AppDelegate` e Android `MainActivity`. Isso será melhorado em uma versão de visualização futura.


## <a name="3-add-a-xaml-page"></a>3. Adicionar uma página XAML

Adicionar uma nova página XAML para o aplicativo xamarin. Forms, e *alterar a classe base* partir `ContentPage` para `Xamarin.Forms.Pages.ListDataPage`. Isso deve ser feito no c# e o XAML:

**Arquivo c#**

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

Além de alterar o elemento raiz ser `<p:ListDataPage>` o espaço para nome personalizado `xmlns:p` também deve ser adicionado:

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

Alterar o `App` construtor de classe, de modo que o `MainPage` é definido como um `NavigationPage` que contém o novo `SessionDataPage`. Uma página da navegação *deve* ser usado.

```csharp
MainPage = new NavigationPage (new SessionDataPage ());
```

## <a name="3-add-the-datasource"></a>3. Adicionar a fonte de dados

Excluir o `Content` elemento e substituí-lo com um `p:ListDataPage.DataSource` para preencher a página com os dados. No exemplo a seguir um Json remoto o arquivo de dados está sendo carregado de uma URL.

**Observação:** a versão prévia *requer* um `StyleClass` atributo para fornecer indícios de renderização para a fonte de dados. O `StyleClass="Events"` refere-se a um layout que é predefinido na visualização e contém estilos *embutidos em código* para corresponder à fonte de dados JSON que está sendo usada.

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

Um exemplo dos dados JSON com o [fonte de demonstração](http://demo3143189.mockable.io/sessions) é mostrado abaixo:

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

## <a name="4-run"></a>4. Executar!

As etapas acima devem resultar em uma página de dados de trabalho:

[![](get-started-images/demo-sml.png "Aplicativo de exemplo DataSourceControl")](get-started-images/demo.png#lightbox "DataSourceControl exemplo de aplicativo")

Isso funciona porque o estilo pré-criados **"Eventos"** existe no pacote Nuget de tema de luz e tem os estilos definidos que correspondem à fonte de dados (por exemplo. "title", "imagem", "apresentador").

"Eventos" `StyleClass` é criado para exibir o `ListDataPage` controle com um personalizado `CardView` controle que é definido em Xamarin.Forms.Pages. O `CardView` controle tem três propriedades: `ImageSource`, `Text`, e `Detail`. O tema está codificado para associar três campos os dados da fonte (do arquivo JSON) para essas propriedades para exibição.

## <a name="5-customize"></a>5. Personalizar

O estilo herdado pode ser substituído especificando um modelo e usando associações de código-fonte de dados. O XAML a seguir declara um modelo personalizado para cada linha usando o novo `ListItemControl` e `{p:DataSourceBinding}` sintaxe que está incluído na **Xamarin.Forms.Pages** Nuget:

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

Fornecendo uma `DataTemplate` esse código substitui o `StyleClass` e, em vez disso, usa o layout padrão para um `ListItemControl`.

[![](get-started-images/custom-sml.png "Aplicativo de exemplo DataSourceControl")](get-started-images/custom.png#lightbox "DataSourceControl exemplo de aplicativo")

Os desenvolvedores que preferem o c# para o XAML pode criar dados de associações de origem muito (Lembre-se de incluir um `using Xamarin.Forms.Pages;` instrução):

```csharp
SetBinding (TitleProperty, new DataSourceBinding ("title"));
```


É um pouco mais trabalho para criar temas do zero (consulte a [guia de temas](~/xamarin-forms/user-interface/themes/index.md)), mas versões prévias futuras tornará isso mais fácil de fazer.


## <a name="troubleshooting"></a>Solução de problemas

<a name="loadtheme" />

## <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>Não foi possível carregar arquivo ou assembly 'Xamarin.Forms.Theme.Light' ou uma de suas dependências

Na versão de visualização, temas podem não ser capazes de carregar no tempo de execução. Adicione o código mostrado abaixo nos projetos relevantes para corrigir esse erro.

**iOS**

No **AppDelegate.cs** adicione as seguintes linhas depois de `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

No **MainActivity.cs** adicione as seguintes linhas depois de `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```



## <a name="related-links"></a>Links relacionados

- [Exemplo de DataPagesDemo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)
