---
title: Adicionando formatação específica do iOS
description: Este artigo explica como definir a aparência específica do iOS sem usar um renderizador personalizado do xamarin. Forms.
ms.prod: xamarin
ms.assetid: CE50E207-D092-4D88-8439-1B51F178E7ED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/29/2016
ms.openlocfilehash: 3b8a440617dedfbe23f869e865b3cedae21d6c5b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946343"
---
# <a name="adding-ios-specific-formatting"></a>Adicionando formatação específica do iOS

Uma maneira de definir específico do iOS formatação é criar uma [renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) para um controle e definir estilos de específico da plataforma e cores para cada plataforma.

Outras opções para controlar a aparência do aplicativo iOS xamarin. Forms incluem:

* Configurando opções de exibição [ **Info. plist**](#info-plist)
* Definindo estilos de controle por meio de [ `UIAppearance` API](#uiappearance)

Essas alternativas são discutidas abaixo.

<a name="info-plist"/>

## <a name="customizing-infoplist"></a>Personalizando o Info. plist

O **Info. plist** arquivo lhe permite configurar alguns aspectos do renderering de um aplicativo iOS, como a barra de status é exibida como (e se).

Por exemplo, o [exemplo de tarefas pendentes](https://developer.xamarin.com/samples/xamarin-forms/Todo/) usa o código a seguir para definir a barra de cor e a cor do texto de navegação em todas as plataformas:

```csharp
var nav = new NavigationPage (new TodoListPage ());
nav.BarBackgroundColor = Color.FromHex("91CA47");
nav.BarTextColor = Color.White;
```

O resultado é mostrado no trecho de tela abaixo. Observe que os itens da barra de status são pretos (isso não pode ser definido dentro do xamarin. Forms porque ele é um recurso específico da plataforma).

![](theme-images/status-default-sml.png "Temas do iOS")

O ideal é que a barra de status também seria branca – algo que podemos pode realizar diretamente no projeto do iOS. Adicione as seguintes entradas para o **Info. plist** para forçar a barra de status para ser branco:

![](theme-images/info-plist.png "Entradas de info. plist do iOS")

ou editar correspondente **Info. plist** arquivo diretamente para incluir:

```xml
<key>UIStatusBarStyle</key>
<string>UIStatusBarStyleLightContent</string>
<key>UIViewControllerBasedStatusBarAppearance</key>
<false/>
```

Agora, quando o aplicativo é executado, a barra de navegação é verde e seu texto é branco (devido à formatação de xamarin. Forms) *e* o texto da barra de status também é brancas, graças à configuração específica do iOS:

![](theme-images/status-white-sml.png "Temas do iOS")

<a name="uiappearance"/>

## <a name="uiappearance-api"></a>UIAppearance API

O [ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md) pode ser usado para definir propriedades visuais em vários controles de iOS *sem* precisar criar um [renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

Adição de uma única linha de código para o **AppDelegate.cs** `FinishedLaunching` método pode definir o estilo de todos os controles de um determinado tipo usando seu `Appearance` propriedade. O código a seguir contém dois exemplos - globalmente a guia de definição de estilo de barra e alterne o controle:

**AppDelegate.cs** no projeto iOS

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

Por padrão, o ícone da barra de guia selecionada em um [`TabbedPage`](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
seria azul:

![](theme-images/tabbar-default.png "Ícone de barra de guia no TabbedPage do iOS padrão")

Para alterar esse comportamento, defina o `UITabBar.Appearance` propriedade:

```csharp
UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

Isso faz com que a guia selecionada ser verde:

![](theme-images/tabbar-custom.png "Ícone da barra de guia no TabbedPage do iOS verde")

Usar essa API permite que você personalize a aparência do xamarin. Forms `TabbedPage` no iOS com muito pouco código. Consulte a [receita Personalizar guias](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs) para obter mais detalhes sobre como usar um renderizador personalizado para definir uma fonte específica para a guia.

### <a name="uiswitch"></a>UISwitch

O `Switch` controle é outro exemplo que pode ser estilizado facilmente:

```csharp
UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

Essas capturas de tela de dois mostram o padrão `UISwitch` controle à esquerda e a versão personalizada (configuração `Appearance`) à direita na [exemplo de tarefas pendentes](https://developer.xamarin.com/samples/xamarin-forms/Todo/):

![](theme-images/switch-default.png "Cor UISwitch padrão") ![](theme-images/switch-custom.png "personalizado UISwitch cor")

### <a name="other-controls"></a>Outros controles

Muitos controles de interface do usuário iOS podem ter suas cores padrão e outros atributos definidos usando o [ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md).



## <a name="related-links"></a>Links relacionados

- [UIAppearance](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)
- [Personalizar guias](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs)
