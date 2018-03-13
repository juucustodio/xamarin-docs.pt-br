---
title: "Atributo depurável"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1ABF90F1-6A70-45AE-9271-D90DC42807D0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: 65037029d01d499421fd825f72347ae1bebd9966
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="debuggable-attribute"></a>Atributo depurável



Para possibilitar a depuração, o Android é compatível com o JDWP (protocolo de transmissão de depuração do Java). Essa é uma tecnologia que permite que ferramentas como ADB se comuniquem com uma JVM. Embora o JDWP seja importante durante o desenvolvimento, ele deve ser desabilitado antes do aplicativo ser publicado.

O JDWP pode ser o valor do atributo `android:debuggable` em um aplicativo Android. O Xamarin.Android fornece as seguintes maneiras de definir esse atributo:

1.  Criar um arquivo `AndroidManifext.xml` e configurando o atributo `android:debuggable` nele.
1.  Incluir o `ApplicationAttribute` em um arquivo `.CS`, da seguinte forma: `[assembly: Application(Debuggable=false)]`.


Se `AndroidManifest.xml` e `ApplicationAttribute` estiverem ambos presentes, o conteúdo de `AndroidManifest.xml` tem prioridade sobre o que é especificado pelo `ApplicationAttribute`.

Se `AndroidManifest.xml` nem `ApplicationAttribute` estiver presente, o valor padrão do atributo `android:debuggable` dependerá de símbolos de depuração serem ou não gerados. Se os símbolos de depuração estiverem presentes, o Xamarin.Android definirá o atributo `android:debuggable` para `true`.

Observe que o valor do atributo `android:debuggable` NÃO depende necessariamente da configuração de build. É possível que builds de versão definam o atributo `android:debuggable` para true.


## <a name="related-links"></a>Links relacionados

- [Aplicativos depuráveis no Android Market](http://labs.mwrinfosecurity.com/blog/2011/07/07/debuggable-apps-in-android-market/)
