---
title: O arquivo IPA tem 0 bytes
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 376BBA27-8694-4E63-9976-BF60349D42D8
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: ba86567213fe76847459df3df9b4450881e0651e
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769226"
---
# <a name="ipa-file-is-0-bytes"></a>O arquivo IPA tem 0 bytes

> [!IMPORTANT]
> Esse problema foi resolvido nas versões recentes do Xamarin. No entanto, se o problema ocorrer na versão mais recente do software, registre um [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com suas informações completas de controle de versão e saída de log de compilação completa.

Houve alguns problemas conhecidos em versões anteriores do Xamarin que poderiam fazer com que o arquivo IPA no Windows fosse de 0 bytes. 

### <a name="fixed-in-xamarin-for-visual-studio-311584"></a>Corrigido no Xamarin para Visual Studio 3.11.584 
- [Bug 24416-a compilação da configuração "ad-hoc" da linha de comando não copia o arquivo IPA para o Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=24416)
- [Bug 24417-alterando "Propriedades do projeto-> opções do IPA do iOS-> nome do pacote" impede que o IPA seja copiado de volta para o Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=24417)
- [Bug 29822-[XVS. iOS 3,11] definindo o número "Build" diferente do número "versão" faz com que o IPA não seja copiado para o Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=29822)

### <a name="fixed-in-xamarin-for-visual-studio-410496"></a>Corrigido no Xamarin para Visual Studio 4.1.0.496
- [Bug 27989-mostrar o arquivo IPA no servidor de compilação falha se o nome do assembly não corresponder ao nome do projeto](https://bugzilla.xamarin.com/show_bug.cgi?id=27989)
