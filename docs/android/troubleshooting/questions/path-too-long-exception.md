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
ms.openlocfilehash: 443c3cc742ceb919e64a781e18c5a97c342abb44
ms.sourcegitcommit: 450106d5f05b4473bf7f5b9100b2eaf18c9110de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67522929"
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>Como resolvo um erro PathTooLongException?

## <a name="cause"></a>Causa

Nomes de caminho gerado em um projeto xamarin. Android podem ser bem longos.
Por exemplo, um caminho semelhante ao seguinte poderá ser gerado durante a compilação:

**C:\\Some\\Directory\\Solution\\Project\\obj\\Debug\\__library_projects__\\Xamarin.Forms.Platform.Android\\library_project_imports\\assets**

No Windows (onde é o comprimento máximo para um caminho [260 caracteres](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)), um **PathTooLongException** pode ser produzida durante a criação do projeto, se um caminho gerado exceder o comprimento máximo. 

## <a name="fix"></a>Correção

O `UseShortFileNames` MSBuild estiver definida como `True` para contornar esse erro por padrão. Quando essa propriedade é definida como `True`, o processo de compilação usa nomes de caminho mais curtos para reduzir a probabilidade de produzir uma **PathTooLongException**.
Por exemplo, quando `UseShortFileNames` é definido como `True`, o caminho acima é reduzido a caminho que é semelhante ao seguinte:

**C:\\Some\\Directory\\Solution\\Project\\obj\\Debug\\lp\\1\\jl\\assets**

Para definir essa propriedade manualmente, adicione a seguinte propriedade de MSBuild para o projeto **. csproj** arquivo:

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
