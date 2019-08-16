---
title: Por que meu build de versão do Android não pode se conectar à Internet?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A6FE770B-A19A-4BF8-95E9-2CF880D4AFC5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: dc84ecc0ee3a71cc4e1d4233f4d6d5f22f597b07
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523486"
---
# <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>Por que meu build de versão do Android não pode se conectar à Internet?

## <a name="cause"></a>Causa

A causa mais comum desse problema é que a permissão da **Internet** é incluída automaticamente em uma compilação de depuração, mas deve ser definida manualmente para uma compilação de versão. Isso ocorre porque a permissão da Internet é usada para permitir que um depurador se anexe ao processo, conforme descrito para "DebugSymbols" [aqui](~/android/deploy-test/building-apps/build-process.md).


## <a name="fix"></a>Correção

Para resolver o problema, você pode exigir a permissão de Internet no manifesto do Android. Isso pode ser feito por meio do editor de manifesto ou do SourceCode do manifesto:

- Correção no editor: Em seu projeto Android, acesse **Propriedades-> AndroidManifest. xml-> permissões necessárias** e marque a **Internet**

- Correção no SourceCode: Abra o AndroidManifest em um editor de origem e adicione a marca de permissão `<Manifest>` dentro das marcas:

    ```xml
    <Manifest>
    ...
    <uses-permission android:name="android.permission.INTERNET" />
    </Manifest>
    ```
