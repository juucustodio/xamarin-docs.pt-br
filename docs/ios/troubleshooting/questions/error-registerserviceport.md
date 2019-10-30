---
title: Erro de iOS Designer com RegisterServicePort
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 929A0080-B126-4744-BF88-A4A1EFBB6CC2
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 07b7e47ac448fdb9a4297fe03e8f4ea16295fddd
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031133"
---
# <a name="ios-designer-error-with-registerserviceport"></a>Erro de iOS Designer com RegisterServicePort

## <a name="sample-error"></a>Erro de exemplo
> System. AggregateException: um ou mais erros ocorreram---> System. Systemfile: RegisterServicePort (com. xamarin. MTHosting. 2a0b1, com. Apple. PowerManagement. Control): kernel retornado:-308 (-308): (IPC/MiG) servidor morreu

## <a name="explanation"></a>Explicação
Erros com `RegisterServicePort` e mensagens de erro semelhantes, como acima, são geralmente um problema com spyware/malware no computador. Considere o [Comentário sobre este relatório de bugs](https://bugzilla.xamarin.com/show_bug.cgi?id=21907#c4) para obter mais informações, juntamente com o link para a [discussão do fórum da Apple](https://discussions.apple.com/thread/5596008) sobre como remover uma possível infecção. 

Para ajudar a diagnosticar o problema, abra o **console** do aplicativo MacOS e exclua todos os arquivos dentro da seção **relatórios de diagnóstico do usuário** [https://screencast.com/t/y9i3NKcuMy](https://screencast.com/t/y9i3NKcuMy). Em seguida, inicie Visual Studio para Mac e tente usar o designer. Se novos arquivos de log aparecerem nesta seção depois que o designer não for inicializado, salve-os para análise.  

Observe que a coisa mais importante a ser verificada é este arquivo: 
> /usr/lib/libimckit.dylib

Não importa os resultados acima, se esse arquivo existir, o problema de spyware/malware mencionado anteriormente estará presente no seu computador.  

O link a seguir tem as etapas para remover este spyware/malware: [https://www.thesafemac.com/arg-genieo/](https://www.thesafemac.com/arg-genieo/)  
