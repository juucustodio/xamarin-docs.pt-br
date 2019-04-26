---
title: Trabalhando com o watchOS ícones no Xamarin
description: Este documento descreve os vários ícones necessários para um aplicativo watchOS e como configurar uma solução para incluir esses ícones.
ms.prod: xamarin
ms.assetid: EE3D45BD-8091-4C04-BA83-371371D8BEB9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/26/2018
ms.openlocfilehash: 435af10484827826d53b767c2738e3945e0bae42
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61345027"
---
# <a name="working-with-watchos-icons-in-xamarin"></a>Trabalhando com o watchOS ícones no Xamarin

Soluções de Apple Watch exigem dois conjuntos de ícones:

* Os ícones de aplicativo do iOS que serão exibido no iPhone.
* Ícones de Apple Watch que serão renderizados em um círculo no menu de inspeção em telas de notificação. O ícone do aplicativo watch também aparece na [Apple Watch](~/ios/watchos/app-fundamentals/settings.md) aplicativo iOS.

## <a name="apple-watch-icons"></a>Ícones de Apple Watch

| | | |
|-|-|-|
|Ícone de aplicativo do iOS|É exibida no iPhone e inicia o aplicativo pai|![ícone do aplicativo iOS](icons-images/icon-ios.png)|
|Ícone do aplicativo de inspeção|É exibida na tela inicial do Apple Watch|![ícone do aplicativo watchOS](icons-images/icon-home.png)|
||É exibido em notificações de inspeção|![ícone de notificação do watchOS](icons-images/notification-icon.png)|
||Aparece no [Apple Watch aplicativo iOS](~/ios/watchos/app-fundamentals/settings.md)|![ícone do aplicativo de inspeção de iOS](icons-images/watch-app-sml.png)|

## <a name="configuring-your-solution"></a>Configurar sua solução

Para garantir que seu aplicativo iOS e o aplicativo de inspeção mostram o nome correto e o ícone, siga estas instruções para cada projeto:

### <a name="ios-app"></a>Aplicativo iOS

Consulte a [orientar os ícones do aplicativo iOS](~/ios/app-fundamentals/images-icons/app-icons.md) para garantir que os ícones do aplicativo iOS que estão configurados corretamente.

#### <a name="infoplist"></a>Info.plist

A cadeia de caracteres que aparece ao lado de seu aplicativo de inspeção na [aplicativo de configurações do Apple Watch](~/ios/watchos/app-fundamentals/settings.md) é configurado na **Info. plist do aplicativo iOS**.

Confirme se sua **Info. plist** tem um `CFBundleName` chave e valor (Observação: isso é diferente para o `CFBundleDisplayName`, você pode ter ambas):

```xml
<key>CFBundleName</key>
<string>Your App Name</string>
```

### <a name="apple-watch-app"></a>Aplicativo de Apple Watch

Uma vez sua [aplicativo pai](~/ios/watchos/app-fundamentals/parent-app.md) tem configurado seus ícones, você precisa adicionar um catálogo de ativos de ícone do aplicativo para o aplicativo watch.

1. Clique com botão direito no projeto de aplicativo de inspeção e selecione **arquivo > Adicionar > novo arquivo... > iOS > Catálogo de ativos** para adicionar um catálogo de ativos para o projeto.

 ![](icons-images/newasset.png "Adicionar um catálogo de ativos para o projeto")

2. Clique duas vezes no **AppIcon.appiconset/Contents.json** arquivo

  ![](icons-images/xcassets-iconset-sml.png "O conteúdo de AppIcon")

3. Adicione todas as imagens do watchOS, conforme mostrado nesta captura de tela:

  [![](icons-images/appicons-sml.png "Adicione todas as imagens do watchOS, conforme mostrado nesta captura de tela")](icons-images/appicons.png#lightbox)

  Consulte a [diretrizes do ícone da Apple](https://developer.apple.com/design/human-interface-guidelines/watchos/icons-and-images/menu-icons/) para os tamanhos necessários (as dimensões também são mostradas na tela). Lembre-se de que esses ícones serão recortados automaticamente para renderizar em um círculo.

  Sua lista de ícone deve ter esta aparência:

  ![](icons-images/xcassets-complete-sml.png "A lista de ícones no Gerenciador de soluções")

4. Para garantir que o catálogo de ativos está incluído no aplicativo, adicione a seguinte chave e o valor para o **Info. plist do aplicativo Watch**:

```xml
<key>XSAppIconAssets</key>
<string>Images.xcassets/AppIcon.appiconset</string>
```

Você pode verificar os ícones são configurados correto verificando o [aplicativo de configurações do Apple Watch](~/ios/watchos/app-fundamentals/settings.md) no simulador, iPhone ou gerando uma [notificação](~/ios/watchos/platform/notifications.md) e confirmar o ícone é exibido na notificação tela.

> [!NOTE]
> Ícones não podem ter um canal alfa (o aplicativo será rejeitado durante o envio da App Store se houver um canal alfa). Você pode verificar se um canal alfa existe e removê-lo [usando o aplicativo de visualização no Mac OS X](~/ios/watchos/troubleshooting.md#noalpha).


## <a name="related-links"></a>Links relacionados

- [Guia do ícone & imagens watchOS da Apple](https://developer.apple.com/design/human-interface-guidelines/watchos/icons-and-images/)
