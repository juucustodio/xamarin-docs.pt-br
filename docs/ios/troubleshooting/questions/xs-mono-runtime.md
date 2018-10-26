---
title: Como definir variáveis de ambiente de tempo de execução Mono para projetos do iOS no Xamarin Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 1176CEA9-C7F1-411B-8F1A-99374E8AFF33
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/31/2017
ms.openlocfilehash: ba74e316f706e5bf22f973de4dad38d94ccd0db9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116927"
---
# <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studio"></a>Como definir variáveis de ambiente de tempo de execução Mono para projetos do iOS no Xamarin Studio?

Se você precisar definir variáveis de ambiente de qualquer tempo de execução para o Mono, eles podem ser definidos **opções de projeto > Executar > geral** página.

Observação: Variáveis de ambiente de coleta de lixo para SGen (MONO\_GC\_PARAMS) conjunto dessa maneira só será usada ao iniciar o Xamarin Studio. Se você iniciar o aplicativo do dispositivo, as configurações para Sgen serão ignoradas. 

Para definir permanentemente uma variável de ambiente para um aplicativo, você precisará adicioná-la como um argumento adicionais do mtouch (para todas as configurações relevantes):

```csharp
   --setenv=NAME=VALUE
```

Para ver as variáveis de ambiente que podem ser definidas, consulte a página do manual Mono: [ http://docs.go-mono.com/?link=man%3amono(1) ](http://docs.go-mono.com/?link=man%3amono(1)) consulte a seção intitulada: `ENVIRONMENT VARIABLES`

![](xs-mono-runtime-images/environment-variables.jpg "Definindo variáveis de ambiente para um projeto")
