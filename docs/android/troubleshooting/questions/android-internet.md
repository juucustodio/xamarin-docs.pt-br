---
title: Por que meu build de versão do Android não pode se conectar à Internet?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A6FE770B-A19A-4BF8-95E9-2CF880D4AFC5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 5996cfa3c0a18fc186ea862a2b3d7910594e1281
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027018"
---
# <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>Por que meu build de versão do Android não pode se conectar à Internet?

## <a name="cause"></a>Causa

A causa mais comum deste problema é que a permissão da **INTERNET** é automaticamente incluída em uma compilação de depuração, mas deve ser definida manualmente para uma compilação de versão. Isso ocorre porque a permissão da Internet é usada para permitir que um depurador seja anexado ao processo, conforme descrito para "DebugSymbols" [aqui](~/android/deploy-test/building-apps/build-process.md).

## <a name="fix"></a>Fix

Para resolver o problema, você pode exigir a permissão da Internet no Manifesto Android. Isso pode ser feito através do editor do manifesto ou do código-fonte do manifesto:

- Corrigir no Editor: Em seu projeto Android, vá para **Propriedades -> AndroidManifest.xml -> Permissões Necessárias** e verifique **a Internet**

- Corrigir em Código-fonte: Abra o AndroidManifest em um `<Manifest>` editor-fonte e adicione a tag de permissão dentro das tags:

    ```xml
    <Manifest>
    ...
    <uses-permission android:name="android.permission.INTERNET" />
    </Manifest>
    ```
