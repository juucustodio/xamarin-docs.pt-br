---
title: Erro de iOS Designer com RegisterServicePort
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 929A0080-B126-4744-BF88-A4A1EFBB6CC2
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: fc4c143d6b5f7c211d24e6e3ed2ed3bb8d264410
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421964"
---
# <a name="ios-designer-error-with-registerserviceport"></a>Erro de iOS Designer com RegisterServicePort

## <a name="sample-error"></a>Erro de amostra
> System.AggregateException: Um ou mais erros ocorreram---> System. SystemException: RegisterServicePort(com.xamarin.MTHosting.2a0b1, com.apple.PowerManagement.control): Kernel retornado: -308 (-308): servidor (ipc/mig) morreu

## <a name="explanation"></a>Explicação
Erros com `RegisterServicePort` e mensagens de erro semelhantes, como acima são normalmente um problema com spyware/malware no computador. . Considere a [comentário sobre este relatório de bug](https://bugzilla.xamarin.com/show_bug.cgi?id=21907#c4) para obter mais informações, juntamente com o link para o [Fórum de discussão do Apple](https://discussions.apple.com/thread/5596008) sobre como remover uma infecção do possível. 

Para ajudar a diagnosticar o problema, abra o aplicativo macOS **Console** e excluir todos os arquivos dentro de **relatórios de diagnóstico do usuário** seção [ http://screencast.com/t/y9i3NKcuMy ](http://screencast.com/t/y9i3NKcuMy). Em seguida, inicie o Visual Studio para Mac e tentar usar o designer. Se quaisquer novos arquivos de log aparecem nesta seção, depois que o designer não conseguiu inicializar, por favor, salve-as para análise.  

Observe que a coisa mais importante para verificar se há é este arquivo: 
> /usr/lib/libimckit.dylib

Não importa os resultados acima, se esse arquivo existir, o problema de malware/spyware mencionados anteriormente está presente no seu computador.  

O link a seguir tem as etapas para remover este spyware/malware: [http://www.thesafemac.com/arg-genieo/](http://www.thesafemac.com/arg-genieo/)  

