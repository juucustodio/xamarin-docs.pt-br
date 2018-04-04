---
title: Trabalhando com ícones
ms.prod: xamarin
ms.assetid: EE3D45BD-8091-4C04-BA83-371371D8BEB9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: a7cdecaa2ba6a8c9f28ef8598fec31506eee6852
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-icons"></a>Trabalhando com ícones

Soluções de Apple Watch exigem dois conjuntos de ícones:

* Os ícones de aplicativo do iOS que aparecerão no iPhone.
* Ícones de Apple Watch que serão renderizados em um círculo no menu de inspeção em telas de notificação. O ícone do aplicativo inspecionar também aparece no [Apple Watch](~/ios/watchos/app-fundamentals/settings.md) aplicativo iOS.

## <a name="apple-watch-icons"></a>Ícones de Apple Watch

| | | |
|-|-|-|
|Ícone do aplicativo do iOS|Aparece no iPhone e inicia o aplicativo pai|![](icons-images/icon-ios.png)|
|Observe o ícone do aplicativo|É exibida na tela inicial do Apple Watch|![](icons-images/icon-home.png)|
||É exibida em notificações de inspeção|![](icons-images/notification-icon.png)|
||Aparece no [Apple Watch aplicativo iOS](~/ios/watchos/app-fundamentals/settings.md)|![](icons-images/watch-app-sml.png)|

## <a name="configuring-your-solution"></a>Configurando sua solução

Para garantir que seu aplicativo iOS e o aplicativo watch mostram o nome correto e o ícone, siga estas instruções para cada projeto:

### <a name="ios-app"></a>Aplicativo iOS

Consulte o [ícones de aplicativo do iOS guia](~/ios/app-fundamentals/images-icons/app-icons.md) para garantir que os ícones do aplicativo iOS estão configurados corretamente.

#### <a name="infoplist"></a>Info.plist

A cadeia de caracteres que aparece ao lado de seu aplicativo de inspeção no [aplicativo de configurações do Apple Watch](~/ios/watchos/app-fundamentals/settings.md) é configurado no **Info. plist do aplicativo do iOS**.

Confirme se o **Info. plist** tem um `CFBundleName` chave e valor (Observação: isso é diferente para o `CFBundleDisplayName`, pode ter ambas):

```xml
<key>CFBundleName</key>
<string>Your App Name</string>
```

### <a name="apple-watch-app"></a>Aplicativo do Apple Watch

Uma vez o [aplicativo-pai](~/ios/watchos/app-fundamentals/parent-app.md) seus ícones tem configurado, você precisa adicionar um catálogo de ativos de ícone do aplicativo para o aplicativo de inspeção.

1. Clique com botão direito no projeto de aplicativo de inspeção e selecione **arquivo > Adicionar > novo arquivo... > iOS > Catálogo de ativos** para adicionar um catálogo de ativos para o projeto.

 ![](icons-images/newasset.png "Adicionar um catálogo de ativos para o projeto")

2. Clique duas vezes no **AppIcons.appiconset/Contents.json** arquivo

  ![](icons-images/xcassets-iconset-sml.png "O conteúdo de AppIcons")

3. Adicione todas as imagens de watchOS, conforme mostrado nesta captura de tela:

  [![](icons-images/appicons-sml.png "Adicione todas as imagens de watchOS, conforme mostrado nesta captura de tela")](icons-images/appicons.png#lightbox)

  Consulte [diretrizes de ícone da Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/IconandImageSizes.html) para os tamanhos necessários (as dimensões também são mostradas na tela). Lembre-se de que esses ícones serão cortados automaticamente para renderizar em um círculo.

  Lista o ícone deve ter esta aparência:

  ![](icons-images/xcassets-complete-sml.png "A lista de ícone no Gerenciador de soluções")

4. Para garantir que o catálogo de ativos é incluído no aplicativo, adicione a seguinte chave e o valor para o **Info. plist do aplicativo de inspeção**:

```xml
<key>XSAppIconAssets</key>
<string>Images.xcassets/AppIcons.appiconset</string>
```

Você pode verificar os ícones são configurados correto verificando o [aplicativo de configurações do Apple Watch](~/ios/watchos/app-fundamentals/settings.md) no simulador, iPhone ou gerando um [notificação](~/ios/watchos/platform/notifications.md) e confirmar o ícone é exibido na notificação tela.

> [!NOTE]
> Ícones não podem ter um canal alfa (o aplicativo será rejeitado durante o envio da loja de aplicativos se houver um canal alfa). Você pode verificar se um canal alfa existe e removê-lo [usando o aplicativo de visualização no Mac OS X](~/ios/watchos/troubleshooting.md#noalpha).


## <a name="related-links"></a>Links relacionados

- [Guia da Apple ícone & imagens](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/IconandImageSizes.html)
