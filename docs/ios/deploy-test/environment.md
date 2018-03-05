---
title: Ambiente
ms.topic: article
ms.prod: xamarin
ms.assetid: 67BFD4E1-276C-4B9F-9BD8-A5218D2BD529
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: ef5cfa2a9eee0d5d01922e5b7b3f89a209396c56
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="environment"></a>Ambiente

O *ambiente de execução* é o conjunto de variáveis de ambiente que influenciam a execução do programa. Variáveis de ambiente podem ser definidas temporariamente nas propriedades do projeto ou permanentemente especificando argumentos adicionais para a ferramenta de empacotamento mtouch.

## <a name="temporary-environment-variables"></a>Variáveis de ambiente temporárias

Variáveis de ambiente temporárias são definidas na janela **Propriedades**/**Opções** do projeto, na seção **Executar > Geral**. Essas variáveis de ambiente estão em vigor somente quando o aplicativo é iniciado usando o Visual Studio para Mac. Se o aplicativo é iniciado manualmente ao tocar nele, essas variáveis de ambiente não são definidas.

## <a name="permanent-environment-variables"></a>Variáveis de ambiente permanentes

Variáveis de ambiente permanentes são definidas especificando argumentos adicionais para a ferramenta de empacotamento mtouch. Essas variáveis de ambiente são compiladas no executável e serão definidas mesmo que o aplicativo não seja iniciado do Xamarin Studio.

## <a name="example"></a>Exemplo

```csharp
# log all exceptions to the device log
--setenv:MONO_TRACE=E:all

# to pass multipe environment variables, use --setenv multiple times
--setenv:MONO_TRACE=E:all --setenv:GC_DONT_GC=1
```

