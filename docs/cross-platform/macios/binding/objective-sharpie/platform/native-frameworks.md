---
title: Estruturas de associação nativa
description: Este documento descreve como usar o objetivo Sharpie - opção de estrutura para criar uma associação a uma biblioteca distribuído como uma estrutura.
ms.prod: xamarin
ms.assetid: 91AE058A-3A1F-41A9-9DE4-4B96880A1869
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: ca103ee027597813be51e126aaa05f9aa969af35
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855091"
---
# <a name="binding-native-frameworks"></a>Estruturas de associação nativa

Às vezes, uma biblioteca nativa é distribuída como um [framework](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WhatAreFrameworks.html). Objetivo Sharpie fornece um recurso conveniente para associação corretamente definidas estruturas por meio de `-framework` opção.

Por exemplo, a associação a [Adobe Creative SDK Framework](https://creativesdk.adobe.com/downloads.html) para iOS é simples:

<pre>$ <b>sharpie bind \
    -framework AdobeCreativeSDKFoundation.framework \
    -sdk iphoneos8.1</b></pre>

Em alguns casos, uma estrutura especificará uma **Info. plist** que indica em relação a qual o SDK do framework deve ser compilado. Se existe essa informação e não explícita `-sdk` opção for passada, objetivo Sharpie irá inferir isso a partir da estrutura **Info. plist** (ambos o `DTSDKName` chave ou uma combinação da `DTPlatformName` e `DTPlatformVersion`chaves).

O `-framework` opção não permitir que os arquivos de cabeçalho explícito a ser passado. O arquivo de cabeçalho guarda-chuva é escolhido por convenção, com base no nome da estrutura. Se um cabeçalho guarda-chuva não pode ser encontrado, objetivo Sharpie não tentará associar a estrutura e você deve executar manualmente a associação, fornecendo os arquivos de cabeçalho guarda-chuva correto para analisar, junto com quaisquer argumentos de estrutura para clang (como o `-F`opção de estrutura de caminho de pesquisa).

Nos bastidores, especificando `-framework` é apenas um atalho. Os seguintes argumentos de associação são idênticos a `-framework` abreviada acima.
De especial importância é o `-F .` caminho de pesquisa de estrutura fornecido ao clang (Observe o espaço e o período, que são necessárias como parte do comando).

<pre>$ <b>sharpie bind \
    -sdk iphoneos8.1 \
    AdobeCreativeSDKFoundation.framework/Headers/AdobeCreativeSDKFoundation.h \
    -scope AdobeCreativeSDKFoundation.framework/Headers \
    -c -F .</b></pre>

## <a name="related-links"></a>Links relacionados

- [Xamarin University curso: Criação de uma biblioteca de associações do Objective-C](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin University curso: Criar uma biblioteca de associações do Objective-C com objetivo Sharpie](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)

