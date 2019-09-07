---
title: Nomeando parâmetros com Javadoc
description: Este artigo explica como recuperar nomes de parâmetro em um projeto de associação Java usando o Javadoc gerado a partir do projeto Java.
ms.prod: xamarin
ms.assetid: 59E8EF16-1322-486A-BB16-353804B77356
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/20/2017
ms.openlocfilehash: fa1fb0656384455322a2d0a3562fc0ee3ca52397
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757596"
---
# <a name="naming-parameters-with-javadoc"></a>Nomeando parâmetros com Javadoc

_Este artigo explica como recuperar nomes de parâmetro em um projeto de associação Java usando o Javadoc gerado a partir do projeto Java._

## <a name="overview"></a>Visão geral

Ao associar uma biblioteca Java existente, alguns metadados sobre a API associada são perdidos. Em particular, os nomes dos parâmetros para os métodos. Os nomes de parâmetro serão `p0`exibidos `p1`como, etc. Isso ocorre porque os arquivos `.class` Java não preservam os nomes de parâmetro que foram usados no código-fonte Java. 

Um projeto de associação Java do Xamarin. Android pode fornecer os nomes de parâmetro se ele tiver acesso ao HTML do Javadoc a partir da biblioteca original. 

## <a name="integrating-javadoc-html-into-a-java-binding-project"></a>Integrando o Javadoc HTML em um projeto de associação Java

A integração do HTML do Javadoc a um projeto de associação Java é um processo manual que consiste nas seguintes etapas: 

1. Baixar o Javadoc para a biblioteca
2. Edite `.csproj` o arquivo e adicione `<JavaDocPaths>` uma propriedade:
3. Limpar e recompilar o projeto

Depois que isso for feito, os nomes de parâmetro Java originais deverão estar presentes nas APIs associadas por um projeto de associação Java. 

> [!NOTE]
> Há uma grande quantidade de variação na saída do JavaDoc. Dos. A associação de JAR ferramentas não dá suporte a todas as permutações possíveis e, consequentemente, alguns parâmetros podem não ser nomeados corretamente.

## <a name="summary"></a>Resumo

Este artigo abordou como usar o Javadoc em um projeto de associação Java para fornecer nomes de parâmetro para APIs associadas. 
