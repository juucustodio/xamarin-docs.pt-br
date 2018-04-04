---
title: Parâmetros de nomeação com Javadoc
description: Este artigo explica como recuperar os nomes de parâmetro em um projeto de vinculação Java usando Javadoc gerado do projeto Java.
ms.prod: xamarin
ms.assetid: 59E8EF16-1322-486A-BB16-353804B77356
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/20/2017
ms.openlocfilehash: 7517e46c5b66123dc4e12fb5562c59f569f249aa
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="naming-parameters-with-javadoc"></a>Parâmetros de nomeação com Javadoc

_Este artigo explica como recuperar os nomes de parâmetro em um projeto de vinculação Java usando Javadoc gerado do projeto Java._


## <a name="overview"></a>Visão geral

Ao associar uma biblioteca Java existente, alguns metadados sobre a API associada são perdido. Em particular os nomes dos parâmetros para métodos. Nomes de parâmetro serão exibido como `p0`, `p1`, etc. Isso ocorre porque o Java `.class` arquivos não preservam os nomes de parâmetros que foram usados no código-fonte Java. 

Um projeto de associação xamarin Java pode fornecer os nomes de parâmetro, se tiver acesso a HTML Javadoc da biblioteca original. 

## <a name="integrating-javadoc-html-into-a-java-binding-project"></a>Integração do Javadoc HTML em um projeto de associação de Java

Integrar o HTML Javadoc um projeto Java de associação é um processo manual que consiste das seguintes etapas: 

1.  Baixe o Javadoc para a biblioteca
2.  Editar o `.csproj` e adicione um `<JavaDocPaths>` propriedade:
3.  Limpe e recompile o projeto

Depois que isso for feito, os nomes de parâmetro de Java originais devem estar presentes nas APIs com um projeto de vinculação de Java. 


> [!NOTE]
> Há uma grande quantidade de variação na saída do JavaDoc. A. JAR ferramentas de associação não oferece suporte a cada permutação possíveis único e consequentemente algum parâmetro pode não ser corretamente nomeado.


## <a name="summary"></a>Resumo

Este artigo coberto como usar Javadoc em um projeto de vinculação de Java para fornecer nomes de parâmetro de significado para APIs associados. 

