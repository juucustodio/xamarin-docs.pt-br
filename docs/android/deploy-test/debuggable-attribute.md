---
title: "Atributo depurável"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3BE5EE1E-3FF6-4E95-7C9F-7B443EE3E94C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: db09ebe29b6c404bac892fd76389faf0b9e03d5b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="debuggable-attribute"></a>Atributo depurável

<a name="Overview" />


Para possibilitar a depuração, o Android é compatível com o JDWP (protocolo de transmissão de depuração do Java). Essa é uma tecnologia que permite que ferramentas como ADB se comuniquem com uma JVM. Embora o JDWP seja importante durante o desenvolvimento, ele deve ser desabilitado antes do aplicativo ser publicado.

O JDWP pode ser o valor do atributo `android:debuggable` em um aplicativo Android. O Xamarin.Android fornece as seguintes maneiras de definir esse atributo:

1.  Criar um arquivo `AndroidManifext.xml` e configurando o atributo `android:debuggable` nele.
1.  Incluir o `ApplicationAttribute` em um arquivo `.CS`, da seguinte forma: `[assembly: Application(Debuggable=false)]`.


Se `AndroidManifest.xml` e `ApplicationAttribute` estiverem ambos presentes, o conteúdo de `AndroidManifest.xml` tem prioridade sobre o que é especificado pelo `ApplicationAttribute`.

Se `AndroidManifest.xml` nem `ApplicationAttribute` estiver presente, o valor padrão do atributo `android:debuggable` dependerá de símbolos de depuração serem ou não gerados. Se os símbolos de depuração estiverem presentes, o Xamarin.Android definirá o atributo `android:debuggable` para `true`.

Observe que o valor do atributo `android:debuggable` NÃO depende necessariamente da configuração de build. É possível que builds de versão definam o atributo `android:debuggable` para true.


## <a name="related-links"></a>Links relacionados

- [Aplicativos depuráveis no Android Market](http://labs.mwrinfosecurity.com/blog/2011/07/07/debuggable-apps-in-android-market/)
