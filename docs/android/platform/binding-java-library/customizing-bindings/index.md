---
title: Personalização de associações
description: Você pode personalizar uma associação xamarin. Android, editando os metadados que controla o processo de associação. Essas modificações manuais geralmente são necessárias para resolver erros de compilação e para formatação a API resultante para que ele seja mais consistente com o C#/.NET. Esses guias explicam a estrutura de metadados, como modificar os metadados e como usar o JavaDoc para recuperar os nomes dos parâmetros de método.
ms.prod: xamarin
ms.assetid: 63C5078D-9E42-4F70-AF8C-8CEEA84FB6AF
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/25/2017
ms.openlocfilehash: 44bff372225ee1bf555eb3eeb34da918830980b4
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102380"
---
# <a name="customizing-bindings"></a>Personalização de associações

_Você pode personalizar uma associação xamarin. Android, editando os metadados que controla o processo de associação. Essas modificações manuais geralmente são necessárias para resolver erros de compilação e para formatação a API resultante para que ele seja mais consistente com o C#/.NET. Esses guias explicam a estrutura de metadados, como modificar os metadados e como usar o JavaDoc para recuperar os nomes dos parâmetros de método._


## <a name="overview"></a>Visão geral
 
Xamarin. Android automatiza a maior parte do processo de associação; No entanto, em alguns casos modificação manual é necessária para resolver os problemas a seguir:

-   Resolvendo erros causados por falta de tipos, tipos ofuscados, nomes duplicados, problemas de visibilidade de classe e outras situações que não podem ser resolvidas de compilação pelas ferramentas xamarin. Android. 

-   Alterar o mapeamento de xamarin. Android usa para associar a API do Android para tipos diferentes em C# (por exemplo, muitos desenvolvedores preferem mapear Java `int` constantes C# `enum` constantes).

-   Removendo tipos não utilizados que não precisa ser associado. 

-   Adição de tipos que não têm uma correspondência na API do Java subjacente. 

Você pode fazer algumas ou todas essas alterações, modificando os metadados que controla o processo de associação.


## <a name="guides"></a>Guias

Os guias a seguir descrevem os metadados que controla o processo de associação e explicam como modificar esses metadados para resolver esses problemas:

-   [Metadados de associações de Java](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) fornece uma visão geral dos metadados que entra em uma associação de Java.
    Ele descreve as diversas etapas manuais que, às vezes, são necessários para concluir uma biblioteca de ligação de Java e explica como formatar uma API exposta por uma associação para mais de perto, siga as diretrizes de design do .NET.

-   [Parâmetros de nomenclatura com Javadoc](~/android/platform/binding-java-library/customizing-bindings/naming-parameters-with-javadoc.md) explica como recuperar os nomes de parâmetro em um projeto de associação do Java usando o Javadoc produzido a partir do projeto Java associado.


 

