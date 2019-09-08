---
title: Personalização de associações
description: Você pode personalizar uma associação do Xamarin. Android editando os metadados que controlam o processo de associação. Essas modificações manuais geralmente são necessárias para resolver erros de compilação e para formatar a API resultante para que seja mais consistente com C#/.net. Esses guias explicam a estrutura desses metadados, como modificar os metadados e como usar o JavaDoc para recuperar os nomes dos parâmetros do método.
ms.prod: xamarin
ms.assetid: 63C5078D-9E42-4F70-AF8C-8CEEA84FB6AF
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/25/2017
ms.openlocfilehash: 37e91070ea10defedbeebdbb06220fabbd2554fc
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756635"
---
# <a name="customizing-bindings"></a>Personalização de associações

_Você pode personalizar uma associação do Xamarin. Android editando os metadados que controlam o processo de associação. Essas modificações manuais geralmente são necessárias para resolver erros de compilação e para formatar a API resultante para que seja mais consistente com C#/.net. Esses guias explicam a estrutura desses metadados, como modificar os metadados e como usar o JavaDoc para recuperar os nomes dos parâmetros do método._

## <a name="overview"></a>Visão geral

O Xamarin. Android automatiza grande parte do processo de ligação; no entanto, em alguns casos, a modificação manual é necessária para resolver os seguintes problemas:

- Resolvendo erros de compilação causados por tipos ausentes, tipos ofuscados, nomes duplicados, problemas de visibilidade de classe e outras situações que não podem ser resolvidas pelas ferramentas do Xamarin. Android. 

- Alterar o mapeamento que o Xamarin. Android usa para associar a API do Android a diferentes C# tipos no (por exemplo, muitos desenvolvedores preferem mapear constantes C# `enum` Java `int` para constantes).

- Removendo tipos não utilizados que não precisam ser associados. 

- Adicionando tipos que não têm nenhum equivalente na API Java subjacente. 

Você pode fazer algumas ou todas essas alterações modificando os metadados que controlam o processo de associação.

## <a name="guides"></a>Guias

Os guias a seguir descrevem os metadados que controlam o processo de associação e explicam como modificar esses metadados para resolver esses problemas:

- Os [metadados de associações Java](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) fornecem uma visão geral dos metadados que entram em uma associação Java.
    Ele descreve as várias etapas manuais que às vezes são necessárias para concluir uma biblioteca de associação Java e explica como formatar uma API exposta por uma associação para seguir melhor as diretrizes de design do .NET.

- A [nomenclatura de parâmetros com Javadoc](~/android/platform/binding-java-library/customizing-bindings/naming-parameters-with-javadoc.md) explica como recuperar nomes de parâmetro em um projeto de associação Java usando o Javadoc produzido do projeto Java associado.
