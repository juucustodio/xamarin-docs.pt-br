---
title: Adicionando formatação específica do iOS
description: Este artigo explica como definir a aparência específica do iOS sem usar um Xamarin.Forms renderizador personalizado.
ms.prod: xamarin
ms.assetid: CE50E207-D092-4D88-8439-1B51F178E7ED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/29/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6bb9156c3f097b517474b70cdacc683d96423417
ms.sourcegitcommit: 63029dd7ea4edb707a53ea936ddbee684a926204
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98609112"
---
# <a name="adding-ios-specific-formatting"></a>Adicionando formatação específica do iOS

Uma maneira de definir a formatação específica do iOS é criar um [renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) para um controle e definir estilos e cores específicos da plataforma para cada plataforma.

Outras opções para controlar o modo como a Xamarin.Forms aparência do aplicativo IOS inclui:

- Configurando opções de exibição no [ **info. plist**](#customizing-infoplist)
- Configurando estilos de controle por meio da [ `UIAppearance` API](#uiappearance-api)

Essas alternativas são discutidas abaixo.

## <a name="customizing-infoplist"></a>Personalizando o info. plist

O arquivo **info. plist** permite que você configure alguns aspectos do processamento de um aplicativo IOS, como como (e se) a barra de status é mostrada.

Por exemplo, o [exemplo todo](/samples/xamarin/xamarin-forms-samples/todo) usa o seguinte código para definir a cor da barra de navegação e a cor do texto em todas as plataformas:

```csharp
var nav = new NavigationPage (new TodoListPage ());
nav.BarBackgroundColor = Color.FromHex("91CA47");
nav.BarTextColor = Color.White;
```

O resultado é mostrado no trecho de tela abaixo. Observe que os itens da barra de status são pretos (isso não pode ser definido no Xamarin.Forms porque ele é um recurso específico da plataforma).

![Captura de tela mostrar iOS com itens de barra de status em texto preto.](theme-images/status-default-sml.png)

O ideal é que a barra de status também seja branca – algo que possamos fazer diretamente no projeto do iOS. Adicione as seguintes entradas ao **info. plist** para forçar a barra de status a ficar branca:

![Entradas do iOS info. plist](theme-images/info-plist.png)

ou edite o arquivo **info. plist** correspondente diretamente para incluir:

```xml
<key>UIStatusBarStyle</key>
<string>UIStatusBarStyleLightContent</string>
<key>UIViewControllerBasedStatusBarAppearance</key>
<false/>
```

Agora, quando o aplicativo é executado, a barra de navegação fica verde e seu texto é branco (devido à Xamarin.Forms formatação) *e* o texto da barra de status também é branco graças à configuração específica do IOS:

![Captura de tela mostrar iOS com itens de barra de status em texto branco.](theme-images/status-white-sml.png)

## <a name="uiappearance-api"></a>API UIAppearance

A [ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md) pode ser usada para definir propriedades visuais em muitos controles do IOS *sem* precisar criar um [renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

Adicionar uma única linha de código ao método **AppDelegate.cs** `FinishedLaunching` pode estilizar todos os controles de um determinado tipo usando sua `Appearance` propriedade. O código a seguir contém dois exemplos – estilizando globalmente a barra de guias e o controle de alternância:

**AppDelegate.cs** no projeto do IOS

```csharp
public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{
  // tab bar
    UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
  // switch
    UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
    // required Xamarin.Forms code
    Forms.Init ();
    LoadApplication (new App ());
    return base.FinishedLaunching (app, options);
}
```

### <a name="uitabbar"></a>UITabBar

Por padrão, o ícone de barra de guias selecionado em um [`TabbedPage`](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
seria azul:

![Ícone padrão da barra de guias iOS em TabbedPage](theme-images/tabbar-default.png)

Para alterar esse comportamento, defina a `UITabBar.Appearance` Propriedade:

```csharp
UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

Isso faz com que a guia selecionada fique verde:

![Ícone da barra de guias verde do iOS em TabbedPage](theme-images/tabbar-custom.png)

O uso dessa API permite que você personalize a aparência do Xamarin.Forms
`TabbedPage` no iOS com muito pouco código. Consulte a [receita personalizar guias](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs) para obter mais detalhes sobre como usar um renderizador personalizado para definir uma fonte específica para a guia.

### <a name="uiswitch"></a>UISwitch

O `Switch` controle é outro exemplo que pode ser facilmente estilizado:

```csharp
UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

Essas duas capturas de tela mostram o `UISwitch` controle padrão à esquerda e a versão personalizada (configuração `Appearance` ) à direita no [exemplo todo](/samples/xamarin/xamarin-forms-samples/todo):

![Cor de UISwitch padrão](theme-images/switch-default.png) ![Cor de UISwitch personalizada](theme-images/switch-custom.png)

### <a name="other-controls"></a>Outros controles

Muitos controles de interface de usuário do iOS podem ter suas cores padrão e outros atributos definidos usando a [ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md).

## <a name="related-links"></a>Links Relacionados

- [UIAppearance](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)
- [Personalizar guias](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs)