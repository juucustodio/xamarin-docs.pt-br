---
title: "Por que minha compilação de versão do Android não pode se conectar à Internet?"
ms.topic: article
ms.prod: xamarin
ms.assetid: A6FE770B-A19A-4BF8-95E9-2CF880D4AFC5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: c37a417b84db4028ccea3848a6c152b8ff8d8ead
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
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
