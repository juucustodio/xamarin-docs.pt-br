---
title: Várias janelas para iPad no Xamarin. iOS
description: Adicione suporte a várias janelas aos seus aplicativos iPad.
ms.prod: xamarin
ms.assetid: 524b6f2e-dbdf-11e9-8a34-2a2ae2dbcce4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/20/2019
ms.openlocfilehash: b3f96f342679d8be6d2f8fbc0ad5962ca675d2a5
ms.sourcegitcommit: 09bc69d7119a04684c9e804c5cb113b8b1bb7dfc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213789"
---
# <a name="multiple-windows-for-ipad"></a>Várias janelas para iPad

o iOS 13 agora dá suporte a janelas lado a lado para o mesmo aplicativo no iPad. Isso permite novas experiências e interações de arrastar e soltar entre o Windows. Este documento mostra como configurar seu aplicativo para dar suporte a esse recurso e apresenta esses novos recursos. 

## <a name="project-configuration"></a>Configuração do projeto

Para configurar seu projeto para várias janelas, modifique o `info.plist` para declarar `NSUserActivityTypes` dizendo ao Ios seu aplicativo tratará as atividades desse tipo e `UIApplicationSceneManifest` para habilitar `UIApplicationSupportsMultipleScenes` o para várias janelas `UISceneConfigurations` e associar seu cena com um Storyboard.

```xml
<key>NSUserActivityTypes</key>
<array>
    <string>com.xamarin.Gallery.openDetail</string>
</array>
<key>UIApplicationSceneManifest</key>
<dict>
    <key>UIApplicationSupportsMultipleScenes</key>
    <true/>
    <key>UISceneConfigurations</key>
    <dict>
        <key>UIWindowSceneSessionRoleApplication</key>
        <array>
            <dict>
                <key>UISceneConfigurationName</key>
                <string>Default Configuration</string>
                <key>UISceneDelegateClassName</key>
                <string>SceneDelegate</string>
                <key>UISceneStoryboardFile</key>
                <string>Main</string>
            </dict>
        </array>
    </dict>
</dict>
```

## <a name="opening-multiple-windows"></a>Abrindo várias janelas

Com seu aplicativo aberto e em execução em um iPad, há algumas maneiras de abrir várias janelas desse aplicativo que o iOS manipula como padrão.

- **Exposição do aplicativo** – toque no ícone do aplicativo no Dock para entrar nesse modo enquanto seu aplicativo está aberto.
- **Deslizar** para frente – arraste o ícone do aplicativo do encaixe sobre a parte superior do seu aplicativo em execução para ter uma janela flutuante.
- **Dividir tela** – arraste o ícone do aplicativo do encaixe até a borda da tela do iPad para criar uma nova janela lado a lado.

As interações adicionais para entrar em um modo de janela múltipla estão disponíveis no seu aplicativo.

**Arrastar do aplicativo** -use uma interação de arrastar dentro de seu aplicativo para iniciar `NSUserActivity` um novo, assim como arrastando o ícone do aplicativo nos exemplos anteriores.

Ao usar uma [interação de arrastar e soltar][0], você cria um `NSUserActivity` e associa os dados a serem passados para a nova janela que está solicitando que o Ios abra para você.

```csharp
public UIDragItem [] GetItemsForBeginningDragSession (UICollectionView collectionView, IUIDragSession session, NSIndexPath indexPath)
{
    var selectedPhoto = GetPhoto (indexPath);

    var userActivity = selectedPhoto.OpenDetailUserActivity ();
    var itemProvider = new NSItemProvider (UIImage.FromFile (selectedPhoto.Name));
    itemProvider.RegisterObject (userActivity, NSItemProviderRepresentationVisibility.All);

    var dragItem = new UIDragItem (itemProvider) {
        LocalObject = selectedPhoto
    };

    return new [] { dragItem };
}
```

No código acima, o objeto `selectedPhoto` de modelo tem um método para retornar um `NSUserActivity` chamado `OpenDetailUserActivity()`. Quando o gesto de arrastar estiver concluído, `UIDragItem` o com apresentará o `NSItemProvider` `userActivity` por meio do.

**Ações explícitas** – gestos do usuário em botões ou links oferecem a capacidade de abrir uma nova janela.

No, você pode iniciar um novo `UISceneSession` chamando `RequestSceneSessionActivation`. `UIApplication` Se já existir uma cena existente, você deverá usá-la. Por padrão, uma nova cena será criada para você.

```csharp
pubic void ItemSelected(UICollectionView collectionView, NSIndexPath indexPath)
{
    var userActivity = selectedPhoto.OpenDetailUserActivity ();

    UIApplication.SharedApplication.RequestSceneSessionActivation(
        sceneSession: null,
        userActivity: userActivity,
        options: null,
        errorHandler: null
    );
}
```

Neste exemplo, `userActivity` o é o único valor passado para o `RequestSceneSessionActivation` método a fim de abrir uma nova janela do aplicativo com base em uma ação explícita do usuário; nesse caso, um `ItemSelected` manipulador de um `UICollectionView`.

## <a name="related-links"></a>Links relacionados

- [Arrastar e soltar no Xamarin. iOS][0]

[0]: ~/ios/platform/introduction-to-ios11/drag-and-drop.md
