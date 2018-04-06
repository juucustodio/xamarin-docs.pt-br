---
title: Ambiente
ms.prod: xamarin
ms.assetid: 9801644A-89BB-4491-AD28-7F3B97D2CD62
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: bc06ce3f3a26842340ce6e19741a8a7dfe8f086d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="environment"></a>Ambiente

O *ambiente de execução* é o conjunto de variáveis de ambiente que influenciam a execução do programa. Variáveis de ambiente podem ser definidas temporariamente nas propriedades do projeto ou permanentemente especificando argumentos adicionais para a ferramenta de empacotamento mtouch.

## <a name="temporary-environment-variables"></a>Variáveis de ambiente temporárias

Variáveis de ambiente temporárias são definidas na janela **Propriedades**/**Opções** do projeto, na seção **Executar > Geral**. Essas variáveis de ambiente estão em vigor somente quando o aplicativo é iniciado usando o Visual Studio para Mac. Se o aplicativo é iniciado manualmente ao tocar nele, essas variáveis de ambiente não são definidas.

## <a name="permanent-environment-variables"></a>Variáveis de ambiente permanentes

Variáveis de ambiente permanentes são definidas especificando argumentos adicionais para a ferramenta de empacotamento mtouch. Essas variáveis de ambiente são compiladas no executável e serão definidas mesmo que o aplicativo não seja iniciado do Visual Studio para Mac.

## <a name="example"></a>Exemplo

```csharp
# log all exceptions to the device log
--setenv:MONO_TRACE=E:all

# to pass multipe environment variables, use --setenv multiple times
--setenv:MONO_TRACE=E:all --setenv:GC_DONT_GC=1
```

