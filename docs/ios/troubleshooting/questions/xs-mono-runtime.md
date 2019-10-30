---
title: Como definir variáveis de ambiente do Mono Runtime para projetos do iOS no Xamarin Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 1176CEA9-C7F1-411B-8F1A-99374E8AFF33
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/31/2017
ms.openlocfilehash: fdd208122934b6fa8194a644592d1e23c4000d57
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030897"
---
# <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studio"></a>Como definir variáveis de ambiente do Mono Runtime para projetos do iOS no Xamarin Studio?

Se você precisar definir qualquer variável de ambiente de tempo de execução para mono, elas poderão ser definidas nas **Opções de projeto > executar > página Geral** .

Observação: as variáveis de ambiente da coleta de lixo para o SGen (MONO\_GC\_PARAMs) definidas dessa forma serão usadas somente ao iniciar a partir de Xamarin Studio. Se você iniciar o aplicativo do dispositivo, as configurações de SGen serão ignoradas. 

Para definir permanentemente uma variável de ambiente para um aplicativo, você precisa adicioná-la como um argumento mTouch adicional (para todas as configurações relevantes):

```csharp
   --setenv=NAME=VALUE
```

Para ver as variáveis de ambiente que podem ser definidas, consulte a página do Man mono: [http://docs.go-mono.com/?link=man%3amono(1)](http://docs.go-mono.com/?link=man%3amono(1)) consulte a seção intitulada: `ENVIRONMENT VARIABLES`

![](xs-mono-runtime-images/environment-variables.jpg "Setting environment variables for a project")
