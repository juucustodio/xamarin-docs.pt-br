---
title: Parâmetros de nomeação com Javadoc
description: Este artigo explica como recuperar nomes de parâmetros em um Projeto de Vinculação Java usando Javadoc gerado a partir do projeto Java.
ms.prod: xamarin
ms.assetid: 59E8EF16-1322-486A-BB16-353804B77356
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/20/2017
ms.openlocfilehash: 060c4759d39bc3b8c424ce46dc615644540fe9c2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027675"
---
# <a name="naming-parameters-with-javadoc"></a>Parâmetros de nomeação com Javadoc

_Este artigo explica como recuperar nomes de parâmetros em um Projeto de Vinculação Java usando Javadoc gerado a partir do projeto Java._

## <a name="overview"></a>Visão geral

Ao vincular uma biblioteca Java existente, alguns metadados sobre a API vinculada são perdidos. Em particular os nomes dos parâmetros para os métodos. Os nomes dos `p0`parâmetros aparecerão como , `p1`etc. Isso porque os `.class` arquivos Java não preservam os nomes de parâmetros que foram usados no código fonte Java. 

Um projeto de vinculação Xamarin.Android Java pode fornecer os nomes dos parâmetros se ele tiver acesso ao Javadoc HTML da biblioteca original. 

## <a name="integrating-javadoc-html-into-a-java-binding-project"></a>Integrando Javadoc HTML em um Projeto de Vinculação Java

A integração do Javadoc HTML em um projeto Java Binding é um processo manual que consiste nas seguintes etapas: 

1. Baixe o Javadoc para a biblioteca
2. Edite `.csproj` o arquivo `<JavaDocPaths>` e adicione uma propriedade:
3. Limpe e reconstrua o projeto

Uma vez feito isso, os nomes dos parâmetros java originais devem estar presentes nas APIs vinculadas a um Projeto de Vinculação Java. 

> [!NOTE]
> Há uma grande variação na saída JavaDoc. O. A cadeia de ferramentas de ligação JAR não suporta todas as permutações possíveis e, consequentemente, algum parâmetro pode não ser nomeado corretamente.

## <a name="summary"></a>Resumo

Este artigo cobriu como usar javadoc em um Java Binding Project para fornecer nomes de parâmetros de significado para APIs vinculadas. 
