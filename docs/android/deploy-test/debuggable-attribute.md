---
title: Atributo depurável
ms.prod: xamarin
ms.assetid: 1ABF90F1-6A70-45AE-9271-D90DC42807D0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/05/2018
ms.openlocfilehash: e0fb6c1ced4953fd03939f56eb4011d956dfcca6
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69525608"
---
# <a name="debuggable-attribute"></a>Atributo depurável



Para possibilitar a depuração, o Android é compatível com o JDWP (protocolo de transmissão de depuração do Java). Essa é uma tecnologia que permite que ferramentas como ADB se comuniquem com uma JVM. Embora o JDWP seja importante durante o desenvolvimento, ele deve ser desabilitado antes do aplicativo ser publicado.

O JDWP pode ser o valor do atributo `android:debuggable` em um aplicativo Android. O Xamarin.Android fornece as seguintes maneiras de definir esse atributo:

1. Criar um arquivo `AndroidManifext.xml` e configurando o atributo `android:debuggable` nele.
2. Incluir o `ApplicationAttribute` em um arquivo `.CS`, da seguinte forma: `[assembly: Application(Debuggable=false)]`.


Se `AndroidManifest.xml` e `ApplicationAttribute` estiverem presentes, o conteúdo de `AndroidManifest.xml` tem prioridade sobre o que é especificado pelo `ApplicationAttribute`.

Se `AndroidManifest.xml` nem `ApplicationAttribute` estiverem presentes, o valor padrão do atributo `android:debuggable` dependerá da geração ou não de símbolos de depuração. Se os símbolos de depuração estiverem presentes, o Xamarin.Android definirá o atributo `android:debuggable` para `true`.

Observe que o valor do atributo `android:debuggable` NÃO depende necessariamente da configuração de build. É possível que builds de versão definam o atributo `android:debuggable` para true.


## <a name="related-links"></a>Links relacionados

- [Aplicativos depuráveis no Android Market](http://labs.mwrinfosecurity.com/blog/2011/07/07/debuggable-apps-in-android-market/)
