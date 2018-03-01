---
title: "Como definir variáveis de ambiente de tempo de execução Mono para projetos do iOS no Xamarin Studio?"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1176CEA9-C7F1-411B-8F1A-99374E8AFF33
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/31/2017
ms.openlocfilehash: 6032ea89aa54719cc4b0fdde67e67f1ec8fb183b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studio"></a>Como definir variáveis de ambiente de tempo de execução Mono para projetos do iOS no Xamarin Studio?

Se você precisa definir variáveis de ambiente de qualquer tempo de execução para Mono, eles podem ser definidos **opções de projeto > Executar > geral** página.

Observação: Variáveis de ambiente de coleta de lixo para SGen (MONO\_GC\_PARAMS) conjunto dessa maneira somente será usada ao iniciar o Xamarin Studio. Se você iniciar o aplicativo do dispositivo, as configurações para Sgen serão ignoradas. 

Para definir permanentemente uma variável de ambiente para um aplicativo, você precisa adicionar isso como um argumento mtouch adicionais (para todas as configurações relevantes):

```csharp
   --setenv=NAME=VALUE
```

Para ver as variáveis de ambiente que podem ser definidas, consulte a página do manual Mono: [http://docs.go-mono.com/?link=man%3amono (1)](http://docs.go-mono.com/?link=man%3amono(1)) consulte a seção intitulada: `ENVIRONMENT VARIABLES`

![](xs-mono-runtime-images/environment-variables.jpg "Definir variáveis de ambiente para um projeto")
