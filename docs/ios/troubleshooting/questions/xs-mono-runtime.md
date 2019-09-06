---
title: Como definir variáveis de ambiente do Mono Runtime para projetos do iOS no Xamarin Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 1176CEA9-C7F1-411B-8F1A-99374E8AFF33
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/31/2017
ms.openlocfilehash: f8e3855b10a20bd4312420f8faf6c68dedde0c67
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292098"
---
# <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studio"></a>Como definir variáveis de ambiente do Mono Runtime para projetos do iOS no Xamarin Studio?

Se você precisar definir qualquer variável de ambiente de tempo de execução para mono, elas poderão ser definidas nas **Opções de projeto > executar > página Geral** .

Observação: As variáveis de ambiente de coleta de lixo\_para\_SGen (mono GC params) definidas dessa forma serão usadas somente ao iniciar a partir de Xamarin Studio. Se você iniciar o aplicativo do dispositivo, as configurações de SGen serão ignoradas. 

Para definir permanentemente uma variável de ambiente para um aplicativo, você precisa adicioná-la como um argumento mTouch adicional (para todas as configurações relevantes):

```csharp
   --setenv=NAME=VALUE
```

Para ver as variáveis de ambiente que podem ser definidas, consulte a página do Man mono:  [http://docs.go-mono.com/?link=man%3amono(1)](http://docs.go-mono.com/?link=man%3amono(1))Consulte a seção intitulada:`ENVIRONMENT VARIABLES`

![](xs-mono-runtime-images/environment-variables.jpg "Configurando variáveis de ambiente para um projeto")
