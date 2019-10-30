---
title: Conceitos básicos do aplicativo Xamarin. Mac
description: Este documento contém links para guias que descrevem vários conceitos necessários para entender ao desenvolver aplicativos Xamarin. Mac.
ms.prod: xamarin
ms.assetid: 5A36B3A7-F197-4AC3-A40D-B2C49362FF06
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 12/17/2015
ms.openlocfilehash: 2603360162ee9918e83b9f5c74b8086f71d02df8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030093"
---
# <a name="xamarinmac-application-fundamentals"></a>Conceitos básicos do aplicativo Xamarin. Mac

## <a name="common-patterns-and-idiomsmacapp-fundamentalspatternsmd"></a>[Padrões e linguagens comuns](~/mac/app-fundamentals/patterns.md)

Em todas as APIs da Apple C#expostas por meio de, determinados idiomas e padrões surgem repetidamente. Se você tiver experiência com programação com Xamarin. iOS, elas podem parecer familiares. A documentação geralmente se referirá a esses padrões e idiomas repetidamente, então ter um entendimento sólido deles ajudará você a compreender a documentação que encontrar.

## <a name="understanding-mac-apismacapp-fundamentalsmac-apismd"></a>[Noções básicas sobre APIs do Mac](~/mac/app-fundamentals/mac-apis.md)

Por muito tempo desenvolvendo com o Xamarin. Mac, você pode imaginar, ler e escrever C# sem muita preocupação com as APIs de Objective-C subjacentes. No entanto, às vezes você precisará ler a documentação da API da Apple, traduzir uma resposta de Stack Overflow para uma solução para seu problema ou comparar com um exemplo existente.

## <a name="console-appsmacapp-fundamentalsconsolemd"></a>[Aplicativos de console](~/mac/app-fundamentals/console.md)

Você também pode criar aplicativos de console "sem periféricos" que acessam APIs nativas do macOS usando o Xamarin. Mac.

## <a name="working-with-xib-filesmacapp-fundamentalsxibmd"></a>[Trabalhando com arquivos. xib](~/mac/app-fundamentals/xib.md)

Este artigo aborda como trabalhar com arquivos. xib criados no Interface Builder do Xcode para criar e manter interfaces de usuário para um aplicativo Xamarin. Mac.

## <a name="storyboardxib-less-user-interface-designmacapp-fundamentalsxibless-uimd"></a>[. Storyboard/. xib menos design de interface do usuário](~/mac/app-fundamentals/xibless-ui.md)

Este artigo aborda a criação de uma interface do usuário do aplicativo Xamarin. C# Mac diretamente do código sem usar interface Builder do Xcode com arquivos. Storyboard ou. xib.

## <a name="working-with-imagesmacapp-fundamentalsimagemd"></a>[Trabalhando com imagens](~/mac/app-fundamentals/image.md)

Este artigo aborda como trabalhar com imagens e ícones em um aplicativo Xamarin. Mac. Ele aborda a criação e a manutenção das imagens necessárias para criar o ícone do aplicativo e usar imagens C# no interface Builder do código e do Xcode.

## <a name="data-binding-and-key-value-codingmacapp-fundamentalsdatabindingmd"></a>[Associação de dados e codificação de valor de chave](~/mac/app-fundamentals/databinding.md)

Este artigo aborda o uso da codificação de valor-chave e da observação de valor-chave para permitir a vinculação de dados a elementos da interface do usuário no Interface Builder do Xcode. Usando essa técnica, você reduz bastante a quantidade de C# código que precisa ser escrito para seu aplicativo Xamarin. Mac. 

## <a name="working-with-databasesmacapp-fundamentalsdatabasesmd"></a>[Trabalhando com bancos de dados](~/mac/app-fundamentals/databases.md)

Este artigo aborda o uso da codificação de valor-chave e da observação de valor-chave para permitir a vinculação de dados com acesso direto aos bancos de dados do SQLite aos elementos da interface do usuário no Interface Builder do Xcode. Ele também aborda o uso do ORM SQLite.NET para fornecer acesso aos dados do SQLite.

## <a name="working-with-copy-and-pastemacapp-fundamentalscopy-pastemd"></a>[Trabalhando com copiar e colar](~/mac/app-fundamentals/copy-paste.md)

Este artigo aborda como trabalhar com a área de trabalho para fornecer copiar e colar em um aplicativo Xamarin. Mac. Ele mostra como trabalhar com tipos de dados padrão que podem ser compartilhados entre vários aplicativos e como dar suporte a dados personalizados em um aplicativo de dê.

## <a name="sandboxing-a-xamarinmac-appmacapp-fundamentalssandboxingmd"></a>[Área restrita de um aplicativo Xamarin. Mac](~/mac/app-fundamentals/sandboxing.md)

Este artigo aborda a área restrita de um aplicativo Xamarin. Mac para lançamento na loja de aplicativos. Ele abrange todos os elementos que entram na área restrita: diretórios de contêineres, direitos, permissões determinadas pelo usuário, separação de privilégios e imposição de kernel.

## <a name="playing-sound-with-avaudioplayermacapp-fundamentalssoundsmd"></a>[Reprodução de som com AVAudioPlayer](~/mac/app-fundamentals/sounds.md)

Este artigo mostra como usar uma classe auxiliar para controlar a reprodução de som usando um AVAudioPlayer.

## <a name="reporting-bugsmacapp-fundamentalstroubleshootingmd"></a>[Relatando bugs](~/mac/app-fundamentals/troubleshooting.md)

Às vezes, todos estamos presos enquanto trabalhamos em um projeto, na incapacidade de fazer com que uma API funcione da maneira desejada ou tentar contornar um bug. Nossa meta no Xamarin é que você tenha êxito ao escrever seus aplicativos móveis e de desktop, e fornecemos alguns recursos para ajudar.
