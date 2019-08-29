---
title: Trabalhando com ícones watchOS no Xamarin
description: Este documento descreve os vários ícones necessários para um aplicativo watchOS e como configurar uma solução para incluir esses ícones.
ms.prod: xamarin
ms.assetid: EE3D45BD-8091-4C04-BA83-371371D8BEB9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/26/2018
ms.openlocfilehash: f65d4faa0e006517366ab6aacf3f596b18385ccb
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120513"
---
# <a name="working-with-watchos-icons-in-xamarin"></a>Trabalhando com ícones watchOS no Xamarin

Apple Watch soluções exigem dois conjuntos de ícones:

- Os ícones do aplicativo iOS que serão exibidos no iPhone.
- Apple Watch ícones que serão renderizados em um círculo no menu Watch e nas telas de notificação. O ícone inspecionar aplicativo também aparece no aplicativo [Apple Watch](~/ios/watchos/app-fundamentals/settings.md) Ios.

## <a name="apple-watch-icons"></a>Ícones de Apple Watch

| | | |
|-|-|-|
|Ícone do aplicativo iOS|Aparece no iPhone e inicia o aplicativo pai|![ícone do aplicativo iOS](icons-images/icon-ios.png)|
|Ícone do aplicativo Watch|Aparece na tela inicial do Apple Watch|![ícone do aplicativo watchOS](icons-images/icon-home.png)|
||Aparece em notificações de inspeção|![ícone de notificação do watchOS](icons-images/notification-icon.png)|
||Aparece no [aplicativo Apple Watch do IOS](~/ios/watchos/app-fundamentals/settings.md)|![ícone do aplicativo de inspeção do iOS](icons-images/watch-app-sml.png)|

## <a name="configuring-your-solution"></a>Configurando sua solução

Para garantir que o aplicativo iOS e o aplicativo de inspeção mostrem o nome e o ícone corretos, siga estas instruções para cada projeto:

### <a name="ios-app"></a>Aplicativo iOS

Consulte o [Guia de ícones do aplicativo IOS](~/ios/app-fundamentals/images-icons/app-icons.md) para garantir que os ícones do aplicativo IOS estejam configurados corretamente.

#### <a name="infoplist"></a>Info.plist

A cadeia de caracteres que aparece ao lado do aplicativo Watch no [aplicativo de configurações de Apple Watch](~/ios/watchos/app-fundamentals/settings.md) é configurada no **info. plist do aplicativo IOS**.

Confirme se seu **info. plist** tem uma `CFBundleName` chave e um valor (Observação: isso é diferente para `CFBundleDisplayName`o, você pode ter ambos):

```xml
<key>CFBundleName</key>
<string>Your App Name</string>
```

### <a name="apple-watch-app"></a>Apple Watch aplicativo

Depois que seu [aplicativo pai](~/ios/watchos/app-fundamentals/parent-app.md) tiver seus ícones configurados, você precisará adicionar um catálogo de ativos de ícone de aplicativo ao aplicativo Watch.

1. Clique com o botão direito do mouse no projeto do aplicativo de inspeção e selecione **arquivo > adicionar > novo arquivo... > o catálogo de ativos do iOS >** para adicionar um catálogo de ativos ao projeto.

    ![](icons-images/newasset.png "Adicionar um catálogo de ativos ao projeto")

2. Clique duas vezes no arquivo **appIcon. appiconset/Contents. JSON**

    ![](icons-images/xcassets-iconset-sml.png "O conteúdo do AppIcon")

3. Adicione todas as imagens watchOS, conforme mostrado nesta captura de tela:

    [![](icons-images/appicons-sml.png "Adicionar todas as imagens watchOS, conforme mostrado nesta captura de tela")](icons-images/appicons.png#lightbox)

    Consulte as [diretrizes de ícone da Apple](https://developer.apple.com/design/human-interface-guidelines/watchos/icons-and-images/menu-icons/) para obter os tamanhos necessários (as dimensões também são mostradas na tela). Lembre-se de que esses ícones serão recortados automaticamente para renderização em um círculo.

    Sua lista de ícones deve ser semelhante a esta:

    ![](icons-images/xcassets-complete-sml.png "A lista de ícones na Gerenciador de Soluções")

4. Para garantir que o catálogo de ativos seja incluído no aplicativo, adicione a chave e o valor a seguir para o **info. plist do aplicativo de inspeção**:

    ```xml
    <key>XSAppIconAssets</key>
    <string>Images.xcassets/AppIcon.appiconset</string>
    ```

Você pode verificar se os ícones estão configurados corretamente verificando o [aplicativo configurações de Apple Watch](~/ios/watchos/app-fundamentals/settings.md) no simulador de iPhone ou gerando uma [notificação](~/ios/watchos/platform/notifications.md) e confirmando o ícone aparece na tela de notificação.

> [!NOTE]
> Os ícones não podem ter um canal alfa (o aplicativo será rejeitado durante o envio da loja de aplicativos se um canal alfa estiver presente). Você pode verificar se existe um canal alfa e removê-lo [usando o aplicativo de visualização no Mac os X](~/ios/watchos/troubleshooting.md#noalpha).


## <a name="related-links"></a>Links relacionados

- [Ícone de watchOS de imagem da Apple & Guia de imagens](https://developer.apple.com/design/human-interface-guidelines/watchos/icons-and-images/)
