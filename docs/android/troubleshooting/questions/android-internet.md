---
title: Por que meu build de versão do Android não pode se conectar à Internet?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A6FE770B-A19A-4BF8-95E9-2CF880D4AFC5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: cd27d5c884086cd0fade4364851039fd0cd915a0
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60945455"
---
# <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>Por que meu build de versão do Android não pode se conectar à Internet?

## <a name="cause"></a>Causa

A causa mais comum desse problema é que o **INTERNET** permissão é incluída automaticamente em uma compilação de depuração, mas deve ser definido manualmente para um build de versão. Isso ocorre porque a permissão de Internet é usada para permitir que um depurador anexar ao processo, conforme descrito para "DebugSymbols" [aqui](~/android/deploy-test/building-apps/build-process.md).


## <a name="fix"></a>Correção

Para resolver o problema, você pode exigir a permissão de Internet no manifesto do Android. Isso pode ser feito por meio do editor de manifesto ou sourcecode do manifesto:

-   Corrigi no Editor: No seu projeto Android, acesse **Propriedades -> androidmanifest. XML -> permissões necessárias** e verifique **Internet**

-   Corrigi em Sourcecode: Abra o AndroidManifest em um editor de código-fonte e adicione a marca de permissão dentro de `<Manifest>` marcas:

    ```xml
    <Manifest>
    ...
    <uses-permission android:name="android.permission.INTERNET" />
    </Manifest>
    ```
