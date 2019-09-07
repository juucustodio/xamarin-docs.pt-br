---
title: Como fazer resolver um erro PathTooLongException?
description: Este artigo explica como resolver um PathTooLongException que pode ocorrer durante a criação de um aplicativo.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/29/2018
ms.openlocfilehash: 915f557db7955dc7b8b9f1bc5e014a683740052b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760809"
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>Como fazer resolver um erro PathTooLongException?

## <a name="cause"></a>Causa

Os nomes de caminho gerados em um projeto Xamarin. Android podem ser muito longos.
Por exemplo, um caminho como o seguinte poderia ser gerado durante uma compilação:

**C:\\Some\\Directory\\Solution\\Project\\obj\\Debug\\__library_projects__\\Xamarin.Forms.Platform.Android\\library_project_imports\\assets**

No Windows (em que o comprimento máximo de um caminho é de [260 caracteres](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)), um **PathTooLongException** poderia ser produzido ao compilar o projeto se um caminho gerado exceder o comprimento máximo. 

## <a name="fix"></a>Correção

A `UseShortFileNames` Propriedade MSBuild é definida como `True` para contornar esse erro por padrão. Quando essa propriedade é definida como `True`, o processo de compilação usa nomes de caminho mais curtos para reduzir a probabilidade de produzir um **PathTooLongException**.
Por exemplo, quando `UseShortFileNames` é definido como `True`, o caminho acima é reduzido para o caminho que é semelhante ao seguinte:

**C:\\alguns\\projetos\\desolução\\de diretório objdebugLP1\\JLassets\\\\\\\\\\**

Para definir essa propriedade manualmente, adicione a seguinte Propriedade do MSBuild ao arquivo Project **. csproj** :

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

Se a definição desse sinalizador não corrigir o erro **PathTooLongException** , outra abordagem será especificar uma [raiz de saída intermediária comum](https://blogs.msdn.microsoft.com/kirillosenkov/2015/04/04/using-a-common-intermediate-and-output-directory-for-your-solution/) para projetos em sua solução definindo `IntermediateOutputPath` no arquivo Project **. csproj** . Tente usar um caminho relativamente curto. Por exemplo:

```xml
<PropertyGroup>
    <IntermediateOutputPath>C:\Projects\MyApp</IntermediateOutputPath>
</PropertyGroup>
```

Para obter mais informações sobre como definir propriedades de compilação, consulte [processo de compilação](~/android/deploy-test/building-apps/build-process.md).
