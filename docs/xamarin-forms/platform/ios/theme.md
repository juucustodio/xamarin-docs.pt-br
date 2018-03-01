---
title: "Adicionando formatação específico do iOS"
ms.topic: article
ms.prod: xamarin
ms.assetid: CE50E207-D092-4D88-8439-1B51F178E7ED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/29/2016
ms.openlocfilehash: c84bc8286ad5f73acb2d7e9eb13467e2c3bfb9b9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="adding-ios-specific-formatting"></a>Adicionando formatação específico do iOS

É uma maneira de definir específico do iOS de formatação criar uma [renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) para um controle e o conjunto específico de plataforma estilos e cores para cada plataforma.

Outras opções para controlar a aparência do aplicativo iOS xamarin. Forms incluem:

* Configurando exibir opções [ **Info. plist**](#info-plist)
* Definindo estilos de controle por meio de [ `UIAppearance` API](#uiappearance)

Essas alternativas são discutidas abaixo.

<a name="info-plist"/>

## <a name="customizing-infoplist"></a>Personalizando Info. plist

O **Info. plist** arquivo lhe permite configurar alguns aspectos de renderering de um aplicativo iOS, como a barra de status é exibida como (e se).

Por exemplo, o [exemplo Todo](https://developer.xamarin.com/samples/xamarin-forms/Todo/) usa o código a seguir para definir a barra de cores e a cor do texto de navegação em todas as plataformas:

```csharp
var nav = new NavigationPage (new TodoListPage ());
nav.BarBackgroundColor = Color.FromHex("91CA47");
nav.BarTextColor = Color.White;
```

O resultado é mostrado no trecho de tela abaixo. Observe que os itens da barra de status são pretos (isso não pode ser definido em xamarin. Forms porque é um recurso específico de plataforma).

![](theme-images/status-default-sml.png "Temas de iOS")

O ideal é a barra de status também é branca - algo que podemos fazer diretamente no projeto de iOS. Adicione as seguintes entradas para o **Info. plist** para forçar a barra de status para ser branco:

![](theme-images/info-plist.png "iOS Info.plist Entries")

ou editar correspondente **Info. plist** arquivo diretamente para incluir:

```xml
<key>UIStatusBarStyle</key>
<string>UIStatusBarStyleLightContent</string>
<key>UIViewControllerBasedStatusBarAppearance</key>
<false/>
```

Agora, quando o aplicativo é executado, a barra de navegação é verde e o texto é branco (devido a formatação xamarin. Forms) *e* o texto da barra de status também é branco obrigado a configuração específica do iOS:

![](theme-images/status-white-sml.png "Temas de iOS")

<a name="uiappearance"/>

## <a name="uiappearance-api"></a>UIAppearance API

O [ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md) pode ser usado para definir propriedades visuais em vários controles de iOS *sem* precisar criar um [renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

Adicionar uma única linha de código para o **appdelegate. CS** `FinishedLaunching` método pode definir o estilo de todos os controles de um determinado tipo usando seus `Appearance` propriedade. O código a seguir contém dois exemplos - globalmente na guia de estilo de barras e alternar o controle:

**Appdelegate. CS** no projeto do iOS

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

Por padrão, o ícone da barra de guia selecionada em uma [ `TabbedPage` ](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md) seria azul:

![](theme-images/tabbar-default.png "IOS padrão ícone da barra de guia em TabbedPage")

Para alterar esse comportamento, defina o `UITabBar.Appearance` propriedade:

```csharp
UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

Isso faz com que a guia selecionada ser verde:

![](theme-images/tabbar-custom.png "Ícone da barra de guia no TabbedPage do iOS verde")

Com essa API permite que você personalize a aparência do xamarin. Forms `TabbedPage` no iOS com muito pouco código. Consulte o [receita Personalizar guias](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/ios/customize-tabs/) para obter mais detalhes sobre como usar um renderizador personalizado para definir uma fonte específica para a guia.

### <a name="uiswitch"></a>UISwitch

O `Switch` controle é outro exemplo que pode ser facilmente estilo:

```csharp
UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

Essas capturas de tela de dois mostram o padrão `UISwitch` controle à esquerda e a versão personalizada (configuração `Appearance`) à direita no [exemplo Todo](https://developer.xamarin.com/samples/xamarin-forms/Todo/):

![](theme-images/switch-default.png "Cor UISwitch padrão") ![ ] (theme-images/switch-custom.png "personalizado UISwitch cor")

### <a name="other-controls"></a>Outros controles

Vários controles de interface de usuário de iOS podem ter suas cores padrão e outros atributos definidos usando o [ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md).



## <a name="related-links"></a>Links relacionados

- [UIAppearance](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)
- [Personalizar guias](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/ios/customize-tabs/)
