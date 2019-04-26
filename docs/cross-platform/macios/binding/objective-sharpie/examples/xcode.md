---
title: Exemplo do mundo real usando um projeto do Xcode
description: Este documento descreve como usar um projeto do Xcode como uma entrada direta para o objetivo Sharpie, simplificando o processo de criação de C# associações a código Objective-C.
ms.prod: xamarin
ms.assetid: 168AA64C-E181-4937-A1F2-AD095B9A36F2
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: 05c55dc7cd20de2d216d1f267ea5a73631748a0a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61265251"
---
# <a name="real-world-example-using-an-xcode-project"></a>Exemplo do mundo real usando um projeto do Xcode

**Este exemplo usa o [biblioteca de POP do Facebook](https://github.com/facebook/pop).**

Novo na versão 3.0, objetivo Sharpie dá suporte a projetos do Xcode como entrada. Esses projetos especificam os arquivos de cabeçalho corretos e os sinalizadores de compilador necessários para compilar a biblioteca nativa e, portanto, é necessários para associá-lo muito. Objetivo Sharpie selecionará a primeira _destino_ e sua configuração padrão de um projeto se não for instruído a fazer o contrário.

Antes de objetivo Sharpie tenta analisar os arquivos de cabeçalho e de projeto, ele deve criá-la. Projetos geralmente têm as fases de compilação que serão corretamente estruturar a arquivos de cabeçalho para consumo externo e integração, portanto, é melhor sempre compilar o projeto completo antes de tentar associá-lo.

<pre>$ <b>git clone https://github.com/facebook/pop.git</b>
Cloning into 'pop'...
   <em>(more git clone output)</em>

$ <b>cd pop</b>
$ <b>sharpie bind pop.xcodeproj -sdk iphoneos9.0</b></pre>

## <a name="related-links"></a>Links relacionados

- [Xamarin University curso: Compilando uma biblioteca de associações do Objective-C](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin University curso: Criar uma biblioteca de associações do Objective-C com objetivo Sharpie](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)