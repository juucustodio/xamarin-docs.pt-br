---
title: Introdução ao iOS 14
description: Este documento fornece uma descrição de alto nível de algumas APIs do iOS 14 para as quais o Xamarin fornece associações C#.
ms.prod: xamarin
ms.assetid: 4953216e-472b-4484-9c1e-7263ac537f21
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/17/2020
ms.openlocfilehash: e9793617c76813fb68a57213edd8b48529f19ac7
ms.sourcegitcommit: 0c45e3f810947e3d43223aa01bf3e43a0defca65
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90843558"
---
# <a name="introduction-to-ios-14"></a>Introdução ao iOS 14

Siga estas [instruções](~/ios/platform/ios14/get-started.md) para começar.

## <a name="new-control-uicolorwell"></a>Novo controle: UIColorWell

[`UIColorWell`](https://developer.apple.com/documentation/uikit/uicolorwell) é um novo controle UIKit para selecionar cores de uma seleção de amostras, usando um conta-gotas ou inserindo valores manualmente. O controle exibe um botão de cor circular que inicia um formulário modal quando tocado.

![UIColorWell](ios14-images/colorwell.png)

```xaml
<ios:UIColorWell
    SelectedColor="{x:Static ios:UIColor.Red}"
    ValueChanged="OnColorChanged" />
```

```csharp
private void OnColorChanged(object sender, EventArgs e)
{
    var colorWell = (UIColorWell)sender; 
    Debug.WriteLine(colorWell.SelectedColor);
}
```

## <a name="modified-controls"></a>Controles modificados

Vários controles receberam atualizações, mais notavelmente:

- [UIBarButtonItem](https://developer.apple.com/documentation/uikit/uibarbuttonitem) agora pode adicionar um UIMenu que será exibido como um popover.
- O [UIDatePicker](https://developer.apple.com/documentation/uikit/uidatepicker) agora dá suporte a vários estilos: automático (padrão), compacto, embutido e roda.
- O [UISplitViewController](https://developer.apple.com/documentation/uikit/uisplitviewcontroller) agora dá suporte a três colunas: primária, secundária e suplementar.
 
![API de pré-lançamento](~/media/shared/preview.png)

## <a name="embedded-widgetkit-support"></a>Suporte WidgetKit inserido

Esta versão do SDK adiciona suporte para a inserção de extensões WidgetKit escritas em Swift em seu aplicativo Xamarin. iOS principal. Isso permite que você crie aplicativos com suporte de widget hoje mesmo.

Com esse método, você cria um aplicativo "híbrido", criando sua extensão de widget com SwiftUI e inserindo-a em um aplicativo Xamarin. iOS.

Aproveitar o suporte do WidgetKit exigirá algumas alterações manuais no arquivo do projeto.

Adicione uma seção como esta ao seu projeto:

```xml
<AdditionalAppExtensions Include="$(MSBuildProjectDirectory)/../../native">
     <Name>NativeTodayExtension</Name>
     <BuildOutput Condition="'$(Platform)' == 'iPhone'">build/Debug-iphoneos</BuildOutput>
     <BuildOutput Condition="'$(Platform)' == 'iPhoneSimulator'">build/Debug-iphonesimulator</BuildOutput>
</AdditionalAppExtensions>
```

Altere o caminho incluído no primeiro link para apontar para o diretório de compilação de sua extensão de interface do usuário Swift.

Pode ser útil habilitar um local de saída relativo do projeto em seu projeto do Xcode (arquivo → configurações do projeto) para ter um caminho mais simples para localizar:

![Configurações do Xcode](ios14-images/xcode-settings.png)

Este [aplicativo de exemplo](https://github.com/chamons/xamarin-ios-swift-extension/blob/master/App/TestApplication/TestApplication.csproj#L143) usa a serialização JSON para transferir dados de um aplicativo Xamarin. Ios para um widget de exemplo para exibição.

Aqueles interessados em WidgetKit são convidados a fornecer seus [comentários aqui](https://github.com/xamarin/xamarin-macios/issues/8933).

## <a name="related-links"></a>Links relacionados

- [Notas de versão do Xamarin. iOS 14](/xamarin/ios/release-notes/14/14.0)
- [Documentação do UIColorWell](https://developer.apple.com/documentation/uikit/uicolorwell)
