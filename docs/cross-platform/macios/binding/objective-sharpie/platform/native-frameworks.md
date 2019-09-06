---
title: Estruturas de associação nativa
description: Este documento descreve como usar a opção de estrutura de nitidez objetiva para criar uma associação a uma biblioteca distribuída como uma estrutura.
ms.prod: xamarin
ms.assetid: 91AE058A-3A1F-41A9-9DE4-4B96880A1869
author: conceptdev
ms.author: crdun
ms.date: 01/15/2016
ms.openlocfilehash: 560db570e915cc9bf261f482f03d972973968585
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279037"
---
# <a name="binding-native-frameworks"></a>Estruturas de associação nativa

Às vezes, uma biblioteca nativa é distribuída como uma [estrutura](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WhatAreFrameworks.html). A nitidez do objetivo fornece um recurso de conveniência para a associação de estruturas definidas `-framework` corretamente por meio da opção.

Por exemplo, a associação do [Adobe Creative SDK Framework](https://creativesdk.adobe.com/downloads.html) para IOS é simples:

```
$ sharpie bind \
    -framework AdobeCreativeSDKFoundation.framework \
    -sdk iphoneos8.1
```

Em alguns casos, uma estrutura especificará um **info. plist** que indica em qual SDK a estrutura deve ser compilada. Se essas informações existirem e nenhuma `-sdk` opção explícita for passada, a nitidez do objetivo inferirá isso a partir do **info. plist** da `DTSDKName` estrutura (a chave ou uma `DTPlatformName` combinação `DTPlatformVersion` das chaves e).

A `-framework` opção não permite que arquivos de cabeçalho explícitos sejam passados. O arquivo de cabeçalho de abrangência é escolhido por convenção com base no nome da estrutura. Se não for possível encontrar um cabeçalho de conjunto de abrangência, a nitidez do objetivo não tentará associar a estrutura e você deverá executar manualmente a associação fornecendo os arquivos de cabeçalho de chuva corretos a serem analisados, juntamente com quaisquer argumentos de estrutura para Clang (como o `-F`opção de caminho de pesquisa de estrutura).

Nos bastidores, especificar `-framework` é apenas um atalho. Os argumentos de ligação a seguir são idênticos ao `-framework` abreviado acima.
De importância especial é o `-F .` caminho de pesquisa de estrutura fornecido para Clang (Observe o espaço e o ponto, que são necessários como parte do comando).

```
$ sharpie bind \
    -sdk iphoneos8.1 \
    AdobeCreativeSDKFoundation.framework/Headers/AdobeCreativeSDKFoundation.h \
    -scope AdobeCreativeSDKFoundation.framework/Headers \
    -c -F .
```
