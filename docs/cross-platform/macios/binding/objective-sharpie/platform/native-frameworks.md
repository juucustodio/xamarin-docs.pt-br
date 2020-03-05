---
title: Estruturas de associação nativa
description: Este documento descreve como usar a opção de estrutura de nitidez objetiva para criar uma associação a uma biblioteca distribuída como uma estrutura.
ms.prod: xamarin
ms.assetid: 91AE058A-3A1F-41A9-9DE4-4B96880A1869
author: davidortinau
ms.author: daortin
ms.date: 01/15/2016
ms.openlocfilehash: 0733e2b406f5032a2e2717df66c96dcb28301f09
ms.sourcegitcommit: 24883be72e485e5311dd0eb91f9a22f78eeec11a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2020
ms.locfileid: "78292769"
---
# <a name="binding-native-frameworks"></a>Estruturas de associação nativa

Às vezes, uma biblioteca nativa é distribuída como uma [estrutura](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WhatAreFrameworks.html). A nitidez do objetivo fornece um recurso de conveniência para a associação de estruturas definidas corretamente por meio da opção `-framework`.

Por exemplo, a associação do [Adobe Creative SDK Framework](https://creativesdk.adobe.com/downloads.html) para IOS é simples:

```
$ sharpie bind \
    -framework ./AdobeCreativeSDKFoundation.framework \
    -sdk iphoneos8.1
```

Em alguns casos, uma estrutura especificará um **info. plist** que indica em qual SDK a estrutura deve ser compilada. Se essas informações existirem e nenhuma opção de `-sdk` explícita for passada, a nitidez objetiva inferirá a partir do **info. plist** da estrutura (a tecla `DTSDKName` ou uma combinação das chaves `DTPlatformName` e `DTPlatformVersion`).

A opção `-framework` não permite que arquivos de cabeçalho explícitos sejam passados. O arquivo de cabeçalho de abrangência é escolhido por convenção com base no nome da estrutura. Se um cabeçalho de abrangência não puder ser encontrado, a nitidez do objetivo não tentará associar a estrutura e você deverá executar manualmente a associação fornecendo os arquivos de cabeçalho de chuva corretos a serem analisados, juntamente com quaisquer argumentos de estrutura para Clang (como a opção de caminho de pesquisa do `-F` Framework).

Nos bastidores, especificar `-framework` é apenas um atalho. Os argumentos de ligação a seguir são idênticos ao `-framework` abreviado acima.
De importância especial é o caminho de pesquisa do `-F .` Framework fornecido para Clang (Observe o espaço e o ponto, que são necessários como parte do comando).

```
$ sharpie bind \
    -sdk iphoneos8.1 \
    ./AdobeCreativeSDKFoundation.framework/Headers/AdobeCreativeSDKFoundation.h \
    -scope AdobeCreativeSDKFoundation.framework/Headers \
    -c -F .
```
