---
title: Erro de Designer com RegisterServicePort do iOS
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 929A0080-B126-4744-BF88-A4A1EFBB6CC2
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 62d4a06c62bffb23566f4f59f42a0c980f417c45
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="ios-designer-error-with-registerserviceport"></a>Erro de Designer com RegisterServicePort do iOS

## <a name="sample-error"></a>Erro de exemplo
> Aggregateexception: Um ou mais erros---> System. SystemException: RegisterServicePort (com.xamarin.MTHosting.2a0b1, com.apple.PowerManagement.control): Kernel retornado: -308 (-308): servidor de ipc/mig () foi desfeita

## <a name="explanation"></a>Explicação
Erros com `RegisterServicePort` e mensagens de erro semelhantes como acima geralmente são um problema com spyware/malware no computador. Considere o [comentário sobre este relatório de bug](https://bugzilla.xamarin.com/show_bug.cgi?id=21907#c4) para obter mais informações, junto com o link para o [discussão de Fórum do Apple](https://discussions.apple.com/thread/5596008) sobre como remover uma possível infecção. 

Para ajudar a diagnosticar o problema, abra o aplicativo macOS **Console** e excluir todos os arquivos dentro do **relatórios de diagnóstico do usuário** seção [ http://screencast.com/t/y9i3NKcuMy ](http://screencast.com/t/y9i3NKcuMy). Em seguida, inicie o Visual Studio para Mac e tente usar o designer. Se quaisquer novos arquivos de log exibido nesta seção depois que o designer falhou ao inicializar, salve essas para análise.  

Observe o mais importante para verificar este arquivo: 
> /usr/lib/libimckit.dylib

Independentemente dos resultados acima, se o arquivo já existe, o problema de spyware/malware mencionados acima está presente no seu computador.  

O link a seguir tem as etapas para remover esse spyware/malware: [http://www.thesafemac.com/arg-genieo/](http://www.thesafemac.com/arg-genieo/)  

