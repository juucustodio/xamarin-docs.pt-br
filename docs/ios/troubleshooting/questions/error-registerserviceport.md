---
title: Erro de iOS Designer com RegisterServicePort
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 929A0080-B126-4744-BF88-A4A1EFBB6CC2
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 815eb638b3e394dcfe4b60d716820c610b01f8a1
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769321"
---
# <a name="ios-designer-error-with-registerserviceport"></a>Erro de iOS Designer com RegisterServicePort

## <a name="sample-error"></a>Erro de exemplo
> System.AggregateException: Um ou mais erros ocorreram---> System. SystemException: RegisterServicePort(com.xamarin.MTHosting.2a0b1, com.apple.PowerManagement.control): Kernel retornado:-308 (-308): (IPC/MiG) morreu do servidor

## <a name="explanation"></a>Explicação
Erros com `RegisterServicePort` e mensagens de erro semelhantes, como acima, são geralmente um problema com spyware/malware no computador. Considere o [Comentário sobre este relatório de bugs](https://bugzilla.xamarin.com/show_bug.cgi?id=21907#c4) para obter mais informações, juntamente com o link para a [discussão do fórum da Apple](https://discussions.apple.com/thread/5596008) sobre como remover uma possível infecção. 

Para ajudar no diagnóstico do problema, abra o **console** do aplicativo MacOS e exclua todos os arquivos dentro da seção [http://screencast.com/t/y9i3NKcuMy](http://screencast.com/t/y9i3NKcuMy) **relatórios de diagnóstico do usuário** . Em seguida, inicie Visual Studio para Mac e tente usar o designer. Se novos arquivos de log aparecerem nesta seção depois que o designer não for inicializado, salve-os para análise.  

Observe que a coisa mais importante a ser verificada é este arquivo: 
> /usr/lib/libimckit.dylib

Não importa os resultados acima, se esse arquivo existir, o problema de spyware/malware mencionado anteriormente estará presente no seu computador.  

O link a seguir tem as etapas para remover este spyware/malware:[http://www.thesafemac.com/arg-genieo/](http://www.thesafemac.com/arg-genieo/)  
