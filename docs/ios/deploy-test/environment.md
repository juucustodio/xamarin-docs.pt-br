---
title: Ambiente de execução para aplicativos Xamarin.iOS
description: Este documento descreve como configurar variáveis de ambiente temporárias e permanentes para um aplicativo Xamarin.iOS. As variáveis podem ser especificadas nas propriedades de um projeto ou como argumentos adicionais para a ferramenta de empacotamento mtouch.
ms.prod: xamarin
ms.assetid: 9801644A-89BB-4491-AD28-7F3B97D2CD62
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2017
ms.openlocfilehash: b2452c50fa978f3cabb718afe7330bf0fb701ce3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030239"
---
# <a name="execution-environment-for-xamarinios-apps"></a>Ambiente de execução para aplicativos Xamarin.iOS

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
