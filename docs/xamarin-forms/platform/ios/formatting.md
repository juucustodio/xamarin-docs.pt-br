---
title: Adicionando formatação específica do iOS
description: Este artigo explica como definir a aparência específica do iOS sem usar um renderizador personalizado do Xamarin. Forms.
ms.prod: xamarin
ms.assetid: CE50E207-D092-4D88-8439-1B51F178E7ED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/29/2016
ms.openlocfilehash: 24d86c54ea4b346e1c165b28c6b62f5a98390d64
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760133"
---
# <a name="adding-ios-specific-formatting"></a>Adicionando formatação específica do iOS

Uma maneira de definir a formatação específica do iOS é criar um [renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) para um controle e definir estilos e cores específicos da plataforma para cada plataforma.

Outras opções para controlar o modo como sua aparência do aplicativo iOS do Xamarin. Forms inclui:

- Configurando opções de exibição no [ **info. plist**](#info-plist)
- Configurando estilos de controle por meio da [ `UIAppearance` API](#uiappearance)

Essas alternativas são discutidas abaixo.

<a name="info-plist"/>

## <a name="customizing-infoplist"></a>Personalizando o info. plist

O arquivo **info. plist** permite que você configure alguns aspectos do processamento de um aplicativo IOS, como como (e se) a barra de status é mostrada.

Por exemplo, o [exemplo todo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo) usa o seguinte código para definir a cor da barra de navegação e a cor do texto em todas as plataformas:

```csharp
var nav = new NavigationPage (new TodoListPage ());
nav.BarBackgroundColor = Color.FromHex("91CA47");
nav.BarTextColor = Color.White;
```

O resultado é mostrado no trecho de tela abaixo. Observe que os itens da barra de status são pretos (isso não pode ser definido no Xamarin. Forms porque ele é um recurso específico da plataforma).

![](theme-images/status-default-sml.png "iOS com temas")

O ideal é que a barra de status também seja branca – algo que possamos fazer diretamente no projeto do iOS. Adicione as seguintes entradas ao **info. plist** para forçar a barra de status a ficar branca:

![](theme-images/info-plist.png "Entradas do iOS info. plist")

ou edite o arquivo **info. plist** correspondente diretamente para incluir:

```xml
<key>UIStatusBarStyle</key>
<string>UIStatusBarStyleLightContent</string>
<key>UIViewControllerBasedStatusBarAppearance</key>
<false/>
```

Agora, quando o aplicativo é executado, a barra de navegação fica verde e seu texto é branco (devido à formatação do Xamarin. Forms) *e* o texto da barra de status também é branco graças à configuração específica do IOS:

![](theme-images/status-white-sml.png "iOS com temas")

<a name="uiappearance"/>

## <a name="uiappearance-api"></a>API UIAppearance

[ A`UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md) pode ser usada para definir propriedades visuais em muitos controles do IOS *sem* precisar criar um [renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

Adicionar uma única linha de código ao método **AppDelegate.cs** `FinishedLaunching` pode estilizar todos os controles de um determinado tipo usando `Appearance` sua propriedade. O código a seguir contém dois exemplos – estilizando globalmente a barra de guias e o controle de alternância:

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

Por padrão, o ícone de barra de guias selecionado em um[`TabbedPage`](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
seria azul:

![](theme-images/tabbar-default.png "Ícone padrão da barra de guias iOS em TabbedPage")

Para alterar esse comportamento, defina a `UITabBar.Appearance` Propriedade:

```csharp
UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

Isso faz com que a guia selecionada fique verde:

![](theme-images/tabbar-custom.png "Ícone da barra de guias verde do iOS em TabbedPage")

O uso dessa API permite que você personalize a aparência do Xamarin. `TabbedPage` Forms no Ios com muito pouco código. Consulte a [receita personalizar guias](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs) para obter mais detalhes sobre como usar um renderizador personalizado para definir uma fonte específica para a guia.

### <a name="uiswitch"></a>UISwitch

O `Switch` controle é outro exemplo que pode ser facilmente estilizado:

```csharp
UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

Essas duas capturas de tela mostram o `UISwitch` controle padrão à esquerda e a versão personalizada (configuração `Appearance`) à direita no [exemplo todo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo):

![](theme-images/switch-default.png "Cor UISwitch padrão") ![](theme-images/switch-custom.png "personalizado UISwitch cor")

### <a name="other-controls"></a>Outros controles

Muitos controles de interface de usuário do IOS podem ter suas cores padrão e outros atributos definidos usando a [ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md).

## <a name="related-links"></a>Links relacionados

- [UIAppearance](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)
- [Personalizar guias](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs)
