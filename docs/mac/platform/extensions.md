---
title: Suporte à extensão do xamarin. Mac
description: Este documento descreve o suporte do xamarin. Mac para extensões de localizador, compartilhamento e hoje. Ele examina as limitações e problemas conhecidos, links para um aplicativo de exemplo e instruções passo a passo e fornece dicas para trabalhar com extensões.
ms.prod: xamarin
ms.assetid: 4148F1BE-DFA0-46B6-9FCD-425A6541F510
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 60b981a764a2656210730ae0602ff32dc580cd0a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117558"
---
# <a name="xamarinmac-extension-support"></a>Suporte à extensão do xamarin. Mac

Na versão prévia do xamarin. MAC 2.10 foi adicionado suporte para vários pontos de extensão do macOS:

- Localizador
- Compartilhar
- Hoje

<a name="Limitations-and-Known-Issues" />

## <a name="limitations-and-known-issues"></a>Limitações e problemas conhecidos

A seguir é as limitações e conhecer os problemas que podem ocorrer durante o desenvolvimento de extensões no xamarin. Mac:

* Atualmente, não há nenhum suporte à depuração no Visual Studio para Mac. Depuração de todos os precisará ser feito por meio **NSLog** e o **Console**. Consulte a seção de dicas abaixo para obter detalhes.
* As extensões devem estar contidas em um aplicativo de host, que, quando executado uma vez com o registro com o sistema. Eles devem estar habilitados na **extensão** seção **preferências do sistema**. 
* Algumas falhas de extensão podem desestabilizar o aplicativo host e causar um comportamento estranho. Em particular, **Finder** e o **hoje mesmo** seção o **Centro de notificações** podem se tornar "obstruído" e pare de responder. Isso tem sido experiente em projetos de extensão também no Xcode e aparece no momento não relacionado ao xamarin. Mac. Geralmente isso pode ser visto no log do sistema (via **Console**, confira dicas para obter detalhes) imprimir mensagens de erro repetidos. Reiniciar o macOS é exibida para corrigir esse problema.

<a name="Tips" />

## <a name="tips"></a>Dicas

As dicas a seguir podem ser úteis ao trabalhar com extensões no xamarin. Mac:

- Como atualmente xamarin. Mac não dá suporte a extensões de depuração, a experiência de depuração dependerão principalmente execução e `printf` como instruções. No entanto, as extensões executadas em um processo de área restrita, assim `Console.WriteLine` não atuará como faz em outros aplicativos do xamarin. Mac. Invocando [ `NSLog` diretamente](https://gist.github.com/chamons/e2e409013a449cfbe1f2fbe5547f6554) produzirá mensagens de depuração para o Log do sistema.
- O processo de extensão, fornecendo apenas uma pequena quantidade de informações úteis no causará uma pane em exceções não identificadas as **Log do sistema**. Quebra automática de código problemático em uma `try/catch` (exceção) bloquear que `NSLog`do antes de lançar novamente pode ser útil.
- O **Log do sistema** podem ser acessados da **Console** aplicativo sob **aplicativos** > **utilitários**:

    [![](extensions-images/extension02.png "O log do sistema")](extensions-images/extension02.png#lightbox)
- Conforme observado acima, executando o aplicativo de host de extensão registrá-lo com o sistema. A exclusão do pacote de aplicativo com cancelar o registro. 
- Se "isoladas" versões de extensões do aplicativo forem registradas, use o seguinte comando para localizá-los (de modo que possam ser excluídos): `plugin kit -mv`


<a name="Walkthrough-and-Sample-App" />

## <a name="walkthrough-and-sample-app"></a>Aplicativo de exemplo e instruções passo a passo

Uma vez que o desenvolvedor será criar e trabalhar com as extensões do xamarin. MAC da mesma forma como extensões do xamarin. IOS, consulte nosso [Introdução às extensões](~/ios/platform/extensions.md) documentação para obter mais detalhes.

Um exemplo de projeto de xamarin. Mac contendo pequeno, exemplos de funcionamento de cada tipo de extensão podem ser encontrados [aqui](https://developer.xamarin.com/samples/mac/ExtensionSamples/).

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo apresentou uma visão geral de trabalhar com as extensões em um aplicativo Xamarin.Max versão 2.10 (e superior).

## <a name="related-links"></a>Links relacionados

- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [ExtensionSamples](https://developer.xamarin.com/samples/mac/ExtensionSamples/)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
