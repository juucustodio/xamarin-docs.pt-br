---
title: Como resolvo um erro PathTooLongException?
description: Este artigo explica como resolver um PathTooLongException que pode ocorrer durante a criação de um aplicativo.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/29/2018
ms.openlocfilehash: 4cb3e13ebbe3d9e8aed153528a35ab16c92e2145
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153304"
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>Como resolvo um erro PathTooLongException?

## <a name="cause"></a>Causa

Nomes de caminho gerado em um projeto xamarin. Android podem ser bem longos.
Por exemplo, um caminho semelhante ao seguinte poderá ser gerado durante a compilação:

**C:\\Some\\Directory\\Solution\\Project\\obj\\Debug\\__library_projects__\\Xamarin.Forms.Platform.Android\\library_project_imports\\assets**

No Windows (onde é o comprimento máximo para um caminho [260 caracteres](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)), um **PathTooLongException** pode ser produzida durante a criação do projeto, se um caminho gerado exceder o comprimento máximo. 

## <a name="fix"></a>Correção

Começando com o xamarin. Android 8.0, o `UseShortFileNames` propriedade do MSBuild pode ser definida para contornar esse erro. Quando essa propriedade é definida como `True` (o padrão é `False`), o processo de compilação usa nomes de caminho mais curtos para reduzir a probabilidade de produzir um **PathTooLongException**.
Por exemplo, quando `UseShortFileNames` é definido como `True`, o caminho acima é reduzido a caminho que é semelhante ao seguinte:

**C:\\Some\\Directory\\Solution\\Project\\obj\\Debug\\lp\\1\\jl\\assets**

Para definir essa propriedade, adicione a seguinte propriedade de MSBuild para o projeto **. csproj** arquivo:

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

Se definir esse sinalizador não resolver o **PathTooLongException** erro, outra abordagem é especificar um [raiz comum de saída intermediária](https://blogs.msdn.microsoft.com/kirillosenkov/2015/04/04/using-a-common-intermediate-and-output-directory-for-your-solution/) para projetos em sua solução, definindo `IntermediateOutputPath` no projeto **. csproj** arquivo. Tente usar um caminho relativamente curto. Por exemplo:

```xml
<PropertyGroup>
    <IntermediateOutputPath>C:\Projects\MyApp</IntermediateOutputPath>
</PropertyGroup>
```

Para obter mais informações sobre como definir propriedades de compilação, consulte [processo de compilação](~/android/deploy-test/building-apps/build-process.md).
