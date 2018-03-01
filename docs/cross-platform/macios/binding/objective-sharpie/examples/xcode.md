---
title: Exemplo do mundo real usando um projeto Xcode
ms.topic: article
ms.prod: xamarin
ms.assetid: 168AA64C-E181-4937-A1F2-AD095B9A36F2
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: 34efeb2b505f6076623f22f36c2a48a52d6f399f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="real-world-example-using-an-xcode-project"></a>Exemplo do mundo real usando um projeto Xcode


**Este exemplo usa o [biblioteca POP do Facebook](https://github.com/facebook/pop).**

Novo na versão 3.0, objetivo Sharpie oferece suporte a projetos Xcode como entrada. Esses projetos especificam os sinalizadores de compilador necessários para compilar a biblioteca nativa e, portanto, é necessários para associá-lo muito e arquivos de cabeçalho corretos. Objetivo Sharpie selecionará o primeiro _destino_ e sua configuração padrão de um projeto se não for instruído a fazer o contrário.

Antes de objetivo Sharpie tenta analisar os arquivos de projeto e o cabeçalho, ele deve compilá-lo. Projetos geralmente têm as fases de compilação serão corretamente estrutura de arquivos de cabeçalho para consumo externo e integração, portanto, é melhor sempre compilar o projeto completo antes de tentar associá-lo.

<pre>$ <b>git clone https://github.com/facebook/pop.git</b>
Cloning into 'pop'...
   <em>(more git clone output)</em>

$ <b>cd pop</b>
$ <b>sharpie bind pop.xcodeproj -sdk iphoneos9.0</b></pre>

