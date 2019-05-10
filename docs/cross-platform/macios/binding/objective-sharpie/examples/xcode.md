---
title: Exemplo do mundo real usando um projeto do Xcode
description: Este documento descreve como usar um projeto do Xcode como uma entrada direta para o objetivo Sharpie, simplificando o processo de criação de C# associações a código Objective-C.
ms.prod: xamarin
ms.assetid: 168AA64C-E181-4937-A1F2-AD095B9A36F2
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: ccfc2f1760d8971e2d824cf65344fa2a5e158c12
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64978362"
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

