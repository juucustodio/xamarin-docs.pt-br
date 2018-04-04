---
title: Como definir variáveis de ambiente de tempo de execução Mono para projetos do iOS no Xamarin Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 1176CEA9-C7F1-411B-8F1A-99374E8AFF33
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/31/2017
ms.openlocfilehash: 5f4f3a2de012d35ddca9c1fa830d599d9d5acb17
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studio"></a>Como definir variáveis de ambiente de tempo de execução Mono para projetos do iOS no Xamarin Studio?

Se você precisa definir variáveis de ambiente de qualquer tempo de execução para Mono, eles podem ser definidos **opções de projeto > Executar > geral** página.

Observação: Variáveis de ambiente de coleta de lixo para SGen (MONO\_GC\_PARAMS) conjunto dessa maneira somente será usada ao iniciar o Xamarin Studio. Se você iniciar o aplicativo do dispositivo, as configurações para Sgen serão ignoradas. 

Para definir permanentemente uma variável de ambiente para um aplicativo, você precisa adicionar isso como um argumento mtouch adicionais (para todas as configurações relevantes):

```csharp
   --setenv=NAME=VALUE
```

Para ver as variáveis de ambiente que podem ser definidas, consulte a página do manual Mono: [ http://docs.go-mono.com/?link=man%3amono(1) ](http://docs.go-mono.com/?link=man%3amono(1)) consulte a seção intitulada: `ENVIRONMENT VARIABLES`

![](xs-mono-runtime-images/environment-variables.jpg "Definir variáveis de ambiente para um projeto")
