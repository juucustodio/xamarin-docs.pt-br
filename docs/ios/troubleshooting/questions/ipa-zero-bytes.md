---
title: O arquivo IPA tem 0 bytes
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 376BBA27-8694-4E63-9976-BF60349D42D8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 4835c980b6b11c92ec1c81dea69f229aa5652275
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61420929"
---
# <a name="ipa-file-is-0-bytes"></a>O arquivo IPA tem 0 bytes

> [!IMPORTANT]
> Esse problema foi resolvido em versões recentes do Xamarin. No entanto, se o problema ocorre na versão mais recente do software, envie uma [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com o controle de versão completo completo e informações saída de log de compilação.



Havia alguns problemas conhecidos em versões anteriores do Xamarin que pode fazer com que o arquivo de IPA no Windows para ser de 0 bytes. 

### <a name="fixed-in-xamarin-for-visual-studio-311584"></a>Corrigido no Xamarin para Visual Studio 3.11.584 
- [Bug 24416 - configuração de construção "Ad Hoc" da linha de comando faz não o arquivo de IPA de cópia para Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=24416)
- [Bug 24417 - alteração "propriedades do projeto de IPA do iOS -> Opções -> nome do pacote" impede que o IPA que está sendo copiado de volta para o Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=24417)
- [Bug 29822 - configuração de [XVS.iOS 3.11] "Build" número diferente de "Versão" número causas IPA não devem ser copiadas para o Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=29822)

### <a name="fixed-in-xamarin-for-visual-studio-410496"></a>Corrigido no Xamarin para Visual Studio 4.1.0.496
- [Bug 27989 - Mostrar arquivo ipa no servidor de compilação falhar se o nome do Assembly não coincide com o nome do projeto](https://bugzilla.xamarin.com/show_bug.cgi?id=27989)
