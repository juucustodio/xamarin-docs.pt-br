---
title: "Personalizando associações"
description: "Você pode personalizar uma associação xamarin editando os metadados que controla o processo de ligação. Essas modificações manuais geralmente são necessárias para resolver erros de compilação e formatação a API resultante para que seja mais consistente com o c# / .NET. Esses guias explicam como usar JavaDoc para recuperar os nomes dos parâmetros de método, como modificar os metadados e a estrutura de metadados."
ms.topic: article
ms.prod: xamarin
ms.assetid: 63C5078D-9E42-4F70-AF8C-8CEEA84FB6AF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 09/25/2017
ms.openlocfilehash: e71d497201cc2d8f2b3e2b8b252e5f963806a75b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="customizing-bindings"></a>Personalizando associações

_Você pode personalizar uma associação xamarin editando os metadados que controla o processo de ligação. Essas modificações manuais geralmente são necessárias para resolver erros de compilação e formatação a API resultante para que seja mais consistente com o c# / .NET. Esses guias explicam como usar JavaDoc para recuperar os nomes dos parâmetros de método, como modificar os metadados e a estrutura de metadados._

<a name="overview" />

## <a name="overview"></a>Visão geral
 
Xamarin automatiza grande parte do processo de associação. No entanto, em alguns casos modificação manual é necessária para tratar dos seguintes problemas:

-   Resolvendo erros causados pela falta de tipos, ofuscados tipos, nomes duplicados, problemas de visibilidade de classe e outras situações que não podem ser resolvidas de compilação, as ferramentas xamarin. 

-   Alterar o mapeamento de xamarin usa para associar a API do Android para tipos diferentes em c# (por exemplo, muitos desenvolvedores preferem mapear Java `int` constantes c# `enum` constantes).

-   Removendo tipos não utilizados que não precisam ser associado. 

-   Adicionando tipos que têm um valor correspondente na API do Java subjacente. 

Você pode fazer algumas ou todas essas alterações, modificando os metadados que controla o processo de ligação.

<a name="guides" />

## <a name="guides"></a>Guias

Os guias a seguir descrevem os metadados que controla o processo de ligação e explicam como modificar esses metadados para resolver esses problemas:

-   [Metadados de associações de Java](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) fornece uma visão geral dos metadados que entram em uma associação de Java.
    Descreve as várias etapas manuais que, às vezes, são necessárias para concluir uma biblioteca de ligação de Java e explica como formatar uma API exposta por uma associação a mais de perto, siga as diretrizes de design do .NET.

-   [Nomes de parâmetros com o Javadoc](~/android/platform/binding-java-library/customizing-bindings/naming-parameters-with-javadoc.md) explica como recuperar os nomes de parâmetro em um projeto de vinculação Java usando Javadoc produzido a partir do projeto Java associado.


 

