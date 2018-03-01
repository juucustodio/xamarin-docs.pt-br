---
title: "Estruturas de associação nativo"
ms.topic: article
ms.prod: xamarin
ms.assetid: 91AE058A-3A1F-41A9-9DE4-4B96880A1869
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: 4588a879452b4ee49535ac8882977be2c064a43f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="binding-native-frameworks"></a>Estruturas de associação nativo

Às vezes, uma biblioteca nativa é distribuída como um [framework](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WhatAreFrameworks.html). Objetivo Sharpie fornece um recurso conveniente para associação corretamente definidas estruturas por meio de `-framework` opção.

Por exemplo, a associação a [Adobe Creative SDK Framework](https://creativesdk.adobe.com/downloads.html) para iOS é simples:

<pre>$ <b>sharpie bind \
    -framework AdobeCreativeSDKFoundation.framework \
    -sdk iphoneos8.1</b></pre>

Em alguns casos, uma estrutura especificará um **Info. plist** que indica em qual SDK a estrutura deve ser compilada. Se esta informação existir e não explícitas `-sdk` opção for passada, o objetivo Sharpie deduzirá da estrutura de **Info. plist** (ou o `DTSDKName` chave ou uma combinação da `DTPlatformName` e `DTPlatformVersion`chaves).

O `-framework` opção não permitir que os arquivos de cabeçalho explícita a serem passados. O arquivo de cabeçalho de cobertura é escolhido por convenção, com base no nome do framework. Se um cabeçalho de argumento não pode ser encontrado, objetivo Sharpie não tentará vincular a estrutura e você deve executar manualmente a associação, fornecendo os arquivos de cabeçalho corretos abrangente para analisar, juntamente com quaisquer argumentos de framework para clang (como o `-F`opção de estrutura de caminho de pesquisa).

Nos bastidores, especificando `-framework` é apenas um atalho. Os seguintes argumentos de associação são idênticos do `-framework` abreviada acima.
Importância especial é o `-F .` caminho de pesquisa do framework fornecido para clang (Observe o espaço e o período, que são necessários como parte do comando).

<pre>$ <b>sharpie bind \
    -sdk iphoneos8.1 \
    AdobeCreativeSDKFoundation.framework/Headers/AdobeCreativeSDKFoundation.h \
    -scope AdobeCreativeSDKFoundation.framework/Headers \
    -c -F .</b></pre>

