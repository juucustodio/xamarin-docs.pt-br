---
title: Como resolver um erro PathTooLongException?
description: Este artigo explica como resolver um PathTooLongException que pode ocorrer durante a construção de um aplicativo.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/29/2018
ms.openlocfilehash: ffe88546ff58387865d71268bd64ec05c8aec3c5
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73026785"
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>Como resolver um erro PathTooLongException?

## <a name="cause"></a>Causa

Nomes de caminho gerados em um projeto Xamarin.Android podem ser bastante longos.
Por exemplo, um caminho como o seguinte poderia ser gerado durante uma compilação:

**C:\\\\Alguns\\\\Projetos\\de\\Solução\\de Diretório obj Debug\\\\__library_projects__\\Xamarin.Forms.Platform.Android library_project_imports ativos**

No Windows (onde o comprimento máximo de um caminho é [de 260 caracteres),](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)um **PathTooLongException** poderia ser produzido durante a construção do projeto se um caminho gerado exceder o comprimento máximo. 

## <a name="fix"></a>Fix

A `UseShortFileNames` propriedade MSBuild `True` está definida para contornar esse erro por padrão. Quando esta propriedade `True`é definida para , o processo de compilação usa nomes de caminho mais curtos para reduzir a probabilidade de produzir um **PathTooLongException**.
Por exemplo, `UseShortFileNames` quando `True`é definido para , o caminho acima é encurtado para um caminho semelhante ao seguinte:

**C:\\\\Alguns\\\\Projetos\\de\\Solução\\\\de\\Diretório\\obj Debug lp 1 jl ativos**

Para definir essa propriedade manualmente, adicione a seguinte propriedade MSBuild ao arquivo project **.csproj:**

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

Se a configuração deste sinalizador não corrigir o erro **PathTooLongException,** outra abordagem é `IntermediateOutputPath` especificar uma raiz de saída intermediária [comum](https://blogs.msdn.microsoft.com/kirillosenkov/2015/04/04/using-a-common-intermediate-and-output-directory-for-your-solution/) para projetos em sua solução, configurando no arquivo projeto **.csproj.** Tente usar um caminho relativamente curto. Por exemplo:

```xml
<PropertyGroup>
    <IntermediateOutputPath>C:\Projects\MyApp</IntermediateOutputPath>
</PropertyGroup>
```

Para obter mais informações sobre a configuração de propriedades de compilação, consulte [Processo de compilação](~/android/deploy-test/building-apps/build-process.md).
