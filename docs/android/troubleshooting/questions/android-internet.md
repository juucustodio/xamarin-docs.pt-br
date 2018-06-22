---
title: Por que minha compilação de versão do Android não pode se conectar à Internet?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A6FE770B-A19A-4BF8-95E9-2CF880D4AFC5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 7f956defd0243e1927746a53e6b3b1b05d98f8d2
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30762078"
---
# <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>Por que minha compilação de versão do Android não pode se conectar à Internet?

## <a name="cause"></a>Causa

A causa mais comum desse problema é que o **INTERNET** permissão é incluída automaticamente em uma compilação de depuração, mas deve ser definida manualmente para um build de versão. Isso ocorre porque a permissão de Internet é usada para permitir que um depurador para anexar ao processo, conforme descrito para "DebugSymbols" [aqui](~/android/deploy-test/building-apps/build-process.md).


## <a name="fix"></a>Correção

Para resolver o problema, você pode exigir a permissão de Internet no manifesto do Android. Isso pode ser feito por meio do editor de manifesto ou sourcecode do manifesto:

-   Corrija no Editor: no seu projeto Android, vá para **Propriedades -> AndroidManifest.xml -> permissões necessárias** e verificar **Internet**

-   Corrija no Sourcecode: Abra o AndroidManifest em um editor de origem e adicionar a marca de permissão dentro de `<Manifest>` marcas:

    ```xml
    <Manifest>
    ...
    <uses-permission android:name="android.permission.INTERNET" />
    </Manifest>
    ```
