---
title: Suporte à extensão de Xamarin.Mac
description: Este documento descreve o suporte do Xamarin. Mac para o Finder, o compartilhamento e as extensões atuais. Ele examina as limitações e problemas conhecidos, links para um aplicativo de exemplo e instruções e fornece dicas para trabalhar com extensões.
ms.prod: xamarin
ms.assetid: 4148F1BE-DFA0-46B6-9FCD-425A6541F510
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: 2129281f389c440d9ae746c4b9b06c4ddb32d1dc
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70770036"
---
# <a name="xamarinmac-extension-support"></a>Suporte à extensão de Xamarin.Mac

No Xamarin. Mac 2,10, o suporte foi adicionado para vários pontos de extensão do macOS:

- Localizado
- Compartilhar
- Hoje

<a name="Limitations-and-Known-Issues" />

## <a name="limitations-and-known-issues"></a>Limitações e problemas conhecidos

A seguir estão as limitações e os problemas conhecidos que podem ocorrer ao desenvolver extensões no Xamarin. Mac:

- No momento, não há suporte para depuração no Visual Studio para Mac. Toda a depuração precisará ser feita via **NSLog** e o **console**. Consulte a seção dicas abaixo para obter detalhes.
- As extensões devem estar contidas em um aplicativo host, que, quando executado uma vez com o registro com o sistema. Eles devem ser habilitados na seção **extensão** de **preferências do sistema**. 
- Algumas falhas de extensão podem desestabilizar o aplicativo host e causar um comportamento estranho. Em particular, o **Finder** e a seção de **hoje** do **centro de notificação** podem se tornar "obstruídos" e deixar de responder. Isso também foi observado em projetos de extensão no Xcode e, no momento, parece não relacionado ao Xamarin. Mac. Geralmente, isso pode ser visto no log do sistema (por meio do **console**do, consulte Dicas para obter detalhes) imprimindo mensagens de erro repetidas. A reinicialização do macOS parece corrigir isso.

<a name="Tips" />

## <a name="tips"></a>Dicas

As dicas a seguir podem ser úteis ao trabalhar com extensões no Xamarin. Mac:

- Como o Xamarin. Mac atualmente não oferece suporte a extensões de depuração, a experiência de depuração dependerá principalmente da execução e `printf` instruções like. No entanto, as extensões são executadas em um processo de área restrita, portanto `Console.WriteLine` não funcionará como faz em outros aplicativos Xamarin. Mac. Chamar [`NSLog` diretamente](https://gist.github.com/chamons/e2e409013a449cfbe1f2fbe5547f6554) produzirá mensagens de depuração para o log do sistema.
- Quaisquer exceções não capturadas apresentarão o processo de extensão, fornecendo apenas uma pequena quantidade de informações úteis no **log do sistema**. O encapsulamento de código problemático em um bloco `try/catch` (exceção) que `NSLog` antes da recriação pode ser útil.
- O **log do sistema** pode ser acessado por meio do aplicativo de **Console** em **aplicativos**  > **utilitários**:

    [![](extensions-images/extension02.png "The system log")](extensions-images/extension02.png#lightbox)
- Conforme observado acima, a execução do aplicativo host de extensão o registrará no sistema. Excluindo o pacote de aplicativos com o cancelamento do registro. 
- Se as versões "isoladas" das extensões de um aplicativo forem registradas, use o seguinte comando para localizá-las (para que elas possam ser excluídas): `plugin kit -mv`

<a name="Walkthrough-and-Sample-App" />

## <a name="walkthrough-and-sample-app"></a>Aplicativo de exemplo e instruções

Como o desenvolvedor criará e trabalhará com as extensões do Xamarin. Mac da mesma forma que as extensões do Xamarin. iOS, consulte a documentação [introdução às extensões](~/ios/platform/extensions.md) para obter mais detalhes.

Um projeto Xamarin. Mac de exemplo que contém exemplos pequenos e funcionais de cada tipo de extensão pode ser encontrado [aqui](https://docs.microsoft.com/samples/xamarin/mac-samples/extensionsamples).

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo deu uma olhada rápida no trabalho com extensões em um aplicativo Xamarin. Mac versão 2,10 (e superior).

## <a name="related-links"></a>Links relacionados

- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [ExtensionSamples](https://docs.microsoft.com/samples/xamarin/mac-samples/extensionsamples)
- [Diretrizes de Interface Humana do macOS](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)
