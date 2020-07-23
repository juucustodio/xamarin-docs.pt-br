---
title: Como definir variáveis de ambiente do Mono Runtime para projetos do iOS no Xamarin Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 1176CEA9-C7F1-411B-8F1A-99374E8AFF33
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/31/2017
ms.openlocfilehash: 30585bede5569edfa50ab9f450bcb62e04c8a10d
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938579"
---
# <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studio"></a>Como definir variáveis de ambiente do Mono Runtime para projetos do iOS no Xamarin Studio?

Se você precisar definir qualquer variável de ambiente de tempo de execução para mono, elas poderão ser definidas nas **Opções de projeto > executar > página Geral** .

Observação: o conjunto de variáveis de ambiente de coleta de lixo para o SGen (MONO \_ GC \_ params) definido dessa forma será usado somente ao iniciar a partir de Xamarin Studio. Se você iniciar o aplicativo do dispositivo, as configurações de SGen serão ignoradas. 

Para definir permanentemente uma variável de ambiente para um aplicativo, você precisa adicioná-la como um argumento mTouch adicional (para todas as configurações relevantes):

```csharp
   --setenv=NAME=VALUE
```

Para ver as variáveis de ambiente que podem ser definidas, consulte a página do Man mono: [http://docs.go-mono.com/?link=man%3amono(1)](http://docs.go-mono.com/?link=man%3amono(1)) consulte a seção intitulada:`ENVIRONMENT VARIABLES`

![Configurando variáveis de ambiente para um projeto](xs-mono-runtime-images/environment-variables.jpg)
