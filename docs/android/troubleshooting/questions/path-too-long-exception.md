---
title: Como resolver um erro PathTooLongException?
description: "Este artigo explica como resolver um PathTooLongException que pode ocorrer durante a criação de um aplicativo."
ms.topic: article
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/20/2018
ms.openlocfilehash: f4554178648d1257049d1c21cdc3e141acdb3de7
ms.sourcegitcommit: d450ae06065d8f8c80f3588bc5a614cfd97b5a67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2018
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>Como resolver um erro PathTooLongException?

## <a name="cause"></a>Causa

Nomes de caminho gerado em um projeto do xamarin podem ser muito longos.
Por exemplo, um caminho semelhante ao seguinte pode ser gerado durante uma compilação:

**C:\\Some\\Directory\\Solution\\Project\\obj\\Debug\\__library_projects__\\Xamarin.Forms.Platform.Android\\library_project_imports\\assets**

No Windows (onde é o comprimento máximo para um caminho [260 caracteres](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)), um **PathTooLongException** podem ser produzidos durante a criação do projeto, se um caminho gerado excede o comprimento máximo. 

## <a name="fix"></a>Correção

Começando com o xamarin 8.0, o `UseShortFileNames` MSBuild propriedade pode ser definida para evitar esse erro. Quando essa propriedade é definida como `True` (o padrão é `False`), o processo de compilação usa nomes de caminho mais curto para reduzir a probabilidade de produzir um **PathTooLongException**.
Por exemplo, quando `UseShortFileNames` é definido como `True`, o caminho acima é reduzido ao caminho que é semelhante à seguinte:

**C:\\alguns\\diretório\\solução\\projeto\\obj\\depurar\\lp\\1\\jl\\ativos**

Para definir essa propriedade, adicione a seguinte propriedade de MSBuild para o projeto **. csproj** arquivo:

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

Para obter mais informações sobre propriedades de configuração de compilação, consulte [processo de compilação](~/android/deploy-test/building-apps/build-process.md).
