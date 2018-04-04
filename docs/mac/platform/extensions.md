---
title: Suporte à extensão de Xamarin.Mac
description: Este artigo aborda o suporte de extensão no Xamarin.Mac versão 2.10 (e superior).
ms.prod: xamarin
ms.assetid: 4148F1BE-DFA0-46B6-9FCD-425A6541F510
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 03936c75d31bfd01e741ad2c09096c925dc9dbfc
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinmac-extension-support"></a>Suporte à extensão de Xamarin.Mac

Na visualização Xamarin.Mac 2.10 foi adicionado suporte para vários pontos de extensão macOS:

- Localizador
- Compartilhar
- Hoje

<a name="Limitations-and-Known-Issues" />

## <a name="limitations-and-known-issues"></a>Limitações e problemas conhecidos

A seguir é apresentadas as limitações e sabe os problemas que podem ocorrer durante o desenvolvimento de extensões no Xamarin.Mac:

* Atualmente, não há nenhum suporte de depuração no Visual Studio para Mac. Depuração de todos os precisará ser feito por meio de **NSLog** e **Console**. Consulte a seção de dicas abaixo para obter detalhes.
* As extensões devem estar contidas em um aplicativo de host, que, quando executado uma vez com o registro com o sistema. Em seguida, deve ser habilitadas no **extensão** seção de **preferências do sistema**. 
* Algumas falhas de extensão podem desestabilizar o aplicativo de host e causar um comportamento estranho. Em particular, **localizador** e o **hoje** seção o **Central de notificações** pode se tornar "obstruído" e não responder. Isso foi experiente em projetos de extensão também no Xcode e estiver relacionado ao Xamarin.Mac no momento. Geralmente isso pode ser visto no log do sistema (via **Console**, consulte dicas para obter detalhes) imprimindo mensagens de erro repetidas. Reiniciar macOS aparece para corrigir esse problema.

<a name="Tips" />

## <a name="tips"></a>Dicas

As dicas a seguir podem ser útil ao trabalhar com extensões no Xamarin.Mac:

- Como atualmente Xamarin.Mac não dá suporte a extensões de depuração, a experiência de depuração dependerá principalmente em execução e `printf` como instruções. No entanto, extensões executados em um processo de área restrita, portanto `Console.WriteLine` não funcionará como em outros aplicativos Xamarin.Mac. Invocar [ `NSLog` diretamente](https://gist.github.com/chamons/e2e409013a449cfbe1f2fbe5547f6554) produzirá mensagens de depuração para o Log do sistema.
- Todas as exceções não capturadas falhará o processo de extensão, fornecendo uma pequena quantidade de informações úteis no **Log do sistema**. Quebra automática de código problemático em um `try/catch` (exceção) bloquear que `NSLog`do antes de lançar pode ser útil.
- O **Log do sistema** pode ser acessado a partir de **Console** aplicativo sob **aplicativos** > **utilitários**:

    [![](extensions-images/extension02.png "O log do sistema")](extensions-images/extension02.png#lightbox)
- Conforme observado acima, executando o aplicativo de host da extensão será registrá-lo com o sistema. Excluindo o pacote de aplicativo com cancelar o registro. 
- Se "perdidas" versões de extensões do aplicativo são registradas, use o seguinte comando para localizá-los (para que podem ser excluídos): `plugin kit -mv`


<a name="Walkthrough-and-Sample-App" />

## <a name="walkthrough-and-sample-app"></a>Aplicativo de exemplo e instruções passo a passo

Desde que o desenvolvedor será criar e trabalhar com extensões de Xamarin.Mac da mesma maneira como as extensões de xamarin, consulte nosso [Introdução a extensões](~/ios/platform/extensions.md) documentação para obter mais detalhes.

Um exemplo de projeto de Xamarin.Mac contendo pequeno, exemplos de funcionamento de cada tipo de extensão podem ser encontrados [aqui](https://developer.xamarin.com/samples/mac/ExtensionSamples/).

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo obteve uma visão geral de trabalhar com as extensões em um aplicativo de Xamarin.Max versão 2.10 (e superior).

## <a name="related-links"></a>Links relacionados

- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [ExtensionSamples](https://developer.xamarin.com/samples/mac/ExtensionSamples/)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
