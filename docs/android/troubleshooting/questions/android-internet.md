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
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027018"
---
# <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>Por que meu build de versão do Android não pode se conectar à Internet?

## <a name="cause"></a>Causa

A causa mais comum desse problema é que a permissão da **Internet** é incluída automaticamente em uma compilação de depuração, mas deve ser definida manualmente para uma compilação de versão. Isso ocorre porque a permissão da Internet é usada para permitir que um depurador se anexe ao processo, conforme descrito para "DebugSymbols" [aqui](~/android/deploy-test/building-apps/build-process.md).

## <a name="fix"></a>Correção

Para resolver o problema, você pode exigir a permissão de Internet no manifesto do Android. Isso pode ser feito por meio do editor de manifesto ou do SourceCode do manifesto:

- Correção no editor: em seu projeto Android, acesse **Propriedades-> AndroidManifest. xml-> permissões necessárias** e verifique a **Internet**

- Correção em SourceCode: Abra o AndroidManifest em um editor de origem e adicione a marca de permissão dentro das marcas de `<Manifest>`:

    ```xml
    <Manifest>
    ...
    <uses-permission android:name="android.permission.INTERNET" />
    </Manifest>
    ```
