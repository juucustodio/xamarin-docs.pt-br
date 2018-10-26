---
title: Parâmetros de nomenclatura com Javadoc
description: Este artigo explica como recuperar os nomes de parâmetro em um projeto de associação do Java usando o Javadoc gerado a partir do projeto Java.
ms.prod: xamarin
ms.assetid: 59E8EF16-1322-486A-BB16-353804B77356
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/20/2017
ms.openlocfilehash: e394377043953a297afed36a3ce0747a3e6d1512
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104408"
---
# <a name="naming-parameters-with-javadoc"></a>Parâmetros de nomenclatura com Javadoc

_Este artigo explica como recuperar os nomes de parâmetro em um projeto de associação do Java usando o Javadoc gerado a partir do projeto Java._


## <a name="overview"></a>Visão geral

Ao associar uma biblioteca Java existente, alguns metadados sobre a API associada serão perdido. Em particular os nomes dos parâmetros para métodos. Nomes de parâmetro aparecerá como `p0`, `p1`, etc. Isso ocorre porque o Java `.class` arquivos não preservam os nomes de parâmetros que foram usados no código-fonte Java. 

Um projeto de associação do xamarin. Android Java pode fornecer os nomes de parâmetro se ele tem acesso ao HTML Javadoc da biblioteca original. 

## <a name="integrating-javadoc-html-into-a-java-binding-project"></a>A integração do Javadoc HTML em um projeto de associação de Java

Integrar o Javadoc HTML em um projeto Java de associação é um processo manual que consiste das seguintes etapas: 

1.  Baixe o Javadoc para a biblioteca
2.  Editar o `.csproj` arquivo e adicione um `<JavaDocPaths>` propriedade:
3.  Limpe e recompile o projeto

Depois que isso for feito, os nomes de parâmetro Java originais devem estar presentes nas APIs associadas por um projeto de associação de Java. 


> [!NOTE]
> Há uma grande quantidade de variação na saída do JavaDoc. A. Cadeia de ferramentas do JAR associação não dá suporte a cada permutação possíveis única e, consequentemente, alguns parâmetros podem não ser chamados corretamente.


## <a name="summary"></a>Resumo

Este artigo abordado como usar o Javadoc em um projeto de associação de Java para fornecer nomes de parâmetro de significado para APIs associadas. 

