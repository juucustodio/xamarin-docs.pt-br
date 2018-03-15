---
title: "Guia de Introdução ao DataPages"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6416E5FA-6384-4298-BAA1-A89381E47210
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 3b5346b6d556f6437d9c9fc17897180fd0b41b1e
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/15/2018
---
# <a name="getting-started-with-datapages"></a>Guia de Introdução ao DataPages

![](~/media/shared/preview.png "Esta API está atualmente em visualização")

> [!IMPORTANT]
> DataPages requer um [xamarin. Forms tema](~/xamarin-forms/user-interface/themes/index.md) referência para processar.


Para começar a criação de uma página simples de unidade de dados usando a visualização DataPages, siga as etapas abaixo. Essa usa demonstração um estilo codificado ("eventos") na visualização compilações funciona apenas com o formato JSON específico no código.

[![](get-started-images/demo-sml.png "Aplicativo de exemplo DataPages")](get-started-images/demo.png#lightbox "DataPages exemplo de aplicativo")

## <a name="1-add-nuget-packages"></a>1. Adicione pacotes NuGet

Adicione esses pacotes do Nuget para seus projetos de aplicativo e xamarin. Forms PCL:

* Xamarin.Forms.Pages
* Xamarin.Forms.Theme.Base
* Uma implementação de tema Nuget (por exemplo. Xamarin.Forms.Themes.Light)

## <a name="2-add-theme-reference"></a>2. Adicionar referência de tema

No **App** de arquivo, adicione um personalizado `xmlns:mytheme` do tema e certifique-se de que o tema é mesclado no dicionário de recursos do aplicativo:

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

**IMPORTANTE:** você também deve seguir as etapas para [carregar assemblies de tema (abaixo)](#loadtheme) adicionando um código clichê para o iOS `AppDelegate` e Android `MainActivity`. Isso será aprimorado em uma versão futura de visualização.


## <a name="3-add-a-xaml-page"></a>3. Adicione uma página XAML

Adicione uma nova página do XAML para o aplicativo xamarin. Forms, e *alterar a classe base* de `ContentPage` para `Xamarin.Forms.Pages.ListDataPage`. Isso precisa ser feito em c# e XAML:

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

Além de alterar o elemento raiz para `<p:ListDataPage>` o espaço para nome personalizado `xmlns:p` também deve ser adicionado:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<p:ListDataPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:p="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
             x:Class="DataPagesDemo.SessionDataPage">

    <ContentPage.Content></ContentPage.Content>

</p:ListDataPage>
```

**Subclasse do aplicativo**

Alterar o `App` construtor de classe para que o `MainPage` é definido como um `NavigationPage` que contém o novo `SessionDataPage`. Uma página da navegação *deve* ser usado.

```csharp
MainPage = new NavigationPage (new SessionDataPage ());
```

## <a name="3-add-the-datasource"></a>3. Adicionar a fonte de dados

Excluir o `Content` elemento e substituí-lo por um `p:ListDataPage.DataSource` para preencher a página com os dados. No exemplo a seguir Json remoto o arquivo de dados está sendo carregado de uma URL.

**Observação:** a visualização *requer* um `StyleClass` atributo para fornecer dicas de processamento para a fonte de dados. O `StyleClass="Events"` refere-se a um layout que é predefinido na visualização e contém estilos *codificado* para coincidir com a fonte de dados JSON sendo usada.

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

Um exemplo dos dados JSON com o [fonte demonstração](http://demo3143189.mockable.io/sessions) é mostrado abaixo:

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

As etapas acima devem resultar em uma página de dados do trabalho:

[![](get-started-images/demo-sml.png "Aplicativo de exemplo DataPages")](get-started-images/demo.png#lightbox "DataPages exemplo de aplicativo")

Isso funciona porque o estilo pré-criado **"Eventos"** existe no pacote Nuget de tema claro e tem os estilos definidos que correspondem a fonte de dados (por exemplo. "title", "imagem", "apresentador").

Os eventos de"" `StyleClass` é criado para exibir o `ListDataPage` controle com um personalizado `CardView` controlar que é definido em Xamarin.Forms.Pages. O `CardView` controle tem três propriedades: `ImageSource`, `Text`, e `Detail`. O tema está codificada vincular três campos da fonte de dados (do arquivo JSON) para essas propriedades para exibição.

## <a name="5-customize"></a>5. Personalizar

O estilo herdado pode ser substituído especificando um modelo e usando associações de fonte de dados. O XAML a seguir declara um modelo personalizado para cada linha usando a nova `ListItemControl` e `{p:DataSourceBinding}` sintaxe que está incluído no **Xamarin.Forms.Pages** Nuget:

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

Fornecendo um `DataTemplate` esse código substitui o `StyleClass` e usa o layout padrão para um `ListItemControl`.

[![](get-started-images/custom-sml.png "Aplicativo de exemplo DataPages")](get-started-images/custom.png#lightbox "DataPages exemplo de aplicativo")

Os desenvolvedores que preferem c# para XAML pode criar dados de origem associações muito (Lembre-se de incluir um `using Xamarin.Forms.Pages;` instrução):

```csharp
SetBinding (TitleProperty, new DataSourceBinding ("title"));
```


É um pouco mais trabalho para criar temas do zero (consulte o [guia temas](~/xamarin-forms/user-interface/themes/index.md)), mas versões preview futura fará isso mais fácil.


## <a name="troubleshooting"></a>Solução de problemas

<a name="loadtheme" />

## <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>Não foi possível carregar arquivo ou assembly 'Xamarin.Forms.Theme.Light' ou uma de suas dependências

Na versão de visualização, temas não poderá carregar em tempo de execução. Adicione o código mostrado abaixo, os projetos relevantes para corrigir esse erro.

**iOS**

No **appdelegate. CS** adicione as seguintes linhas depois de `LoadApplication`

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
