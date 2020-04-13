---
title: Personalização de associações
description: Você pode personalizar uma vinculação Xamarin.Android editando os metadados que controlam o processo de vinculação. Essas modificações manuais são muitas vezes necessárias para resolver erros de compilação e para moldar a API resultante para que ela seja mais consistente com C#/.NET. Esses guias explicam a estrutura desses metadados, como modificar os metadados e como usar o JavaDoc para recuperar os nomes dos parâmetros do método.
ms.prod: xamarin
ms.assetid: 63C5078D-9E42-4F70-AF8C-8CEEA84FB6AF
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/25/2017
ms.openlocfilehash: 04f3720d8684129476c955819390e91330a7800a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020650"
---
# <a name="customizing-bindings"></a>Personalização de associações

_Você pode personalizar uma vinculação Xamarin.Android editando os metadados que controlam o processo de vinculação. Essas modificações manuais são muitas vezes necessárias para resolver erros de compilação e para moldar a API resultante para que ela seja mais consistente com C#/.NET. Esses guias explicam a estrutura desses metadados, como modificar os metadados e como usar o JavaDoc para recuperar os nomes dos parâmetros do método._

## <a name="overview"></a>Visão geral

Xamarin.Android automatiza grande parte do processo de vinculação; no entanto, em alguns casos, a modificação manual é necessária para resolver os seguintes problemas:

- Resolvendo erros de compilação causados por tipos ausentes, tipos ofuscados, nomes duplicados, problemas de visibilidade de classe e outras situações que não podem ser resolvidas pela ferramenta Xamarin.Android. 

- Alterando o mapeamento que o Xamarin.Android usa para vincular a API do Android a `int` diferentes tipos `enum` em C# (por exemplo, muitos desenvolvedores preferem mapear constantes Java para constantes C#).

- Removendo tipos não utilizados que não precisam ser vinculados. 

- Adicionando tipos que não têm contrapartida na API Java subjacente. 

Você pode fazer algumas ou todas essas alterações modificando os metadados que controlam o processo de vinculação.

## <a name="guides"></a>Guias

Os guias a seguir descrevem os metadados que controlam o processo de vinculação e explicam como modificar esses metadados para resolver esses problemas:

- [Java Bindings Metadata](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) fornece uma visão geral dos metadados que entra em uma vinculação Java.
    Ele descreve as várias etapas manuais que às vezes são necessárias para completar uma biblioteca de vinculação Java, e explica como moldar uma API exposta por uma vinculação para seguir mais de perto as diretrizes de design .NET.

- [Nomindo Parâmetros com Javadoc](~/android/platform/binding-java-library/customizing-bindings/naming-parameters-with-javadoc.md) explica como recuperar nomes de parâmetros em um Projeto de Vinculação Java usando Javadoc produzido a partir do projeto Java vinculado.
