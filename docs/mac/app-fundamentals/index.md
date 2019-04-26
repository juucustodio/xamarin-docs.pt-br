---
title: Conceitos básicos de aplicativo do xamarin. Mac
description: Este documento leva a guias que descrevem vários conceitos necessários para entender ao desenvolver aplicativos xamarin. Mac.
ms.prod: xamarin
ms.assetid: 5A36B3A7-F197-4AC3-A40D-B2C49362FF06
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 12/17/2015
ms.openlocfilehash: 376286b73c92cba40de183043b86cb4ffb5e699d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61085299"
---
# <a name="xamarinmac-application-fundamentals"></a>Conceitos básicos de aplicativo do xamarin. Mac

## <a name="common-patterns-and-idiomsmacapp-fundamentalspatternsmd"></a>[Linguagens e padrões comuns](~/mac/app-fundamentals/patterns.md)

Ao longo da Apple APIs expostas por meio do c#, determinadas linguagens e padrões surgem repetidamente. Se você tiver experiência com programação com xamarin. IOS, elas podem parecer familiares. Documentação geralmente fará referência a essas linguagens e padrões repetidamente, portanto, ter uma compreensão sólida deles ajudará você a dar sentido a documentação que você encontrar.

## <a name="understanding-mac-apismacapp-fundamentalsmac-apismd"></a>[Noções básicas sobre as APIs do Mac](~/mac/app-fundamentals/mac-apis.md)

Na maior parte do seu tempo de desenvolvimento com o xamarin. Mac, você pode pensar, ler e escrever em c# sem muita preocupação com as APIs do Objective-C subjacente. No entanto, às vezes, você será necessário para ler a documentação da API da Apple, traduzir uma resposta de estouro de pilha para uma solução para o seu problema ou comparado com um exemplo existente.

## <a name="console-appsmacapp-fundamentalsconsolemd"></a>[Aplicativos de console](~/mac/app-fundamentals/console.md)

Você também pode criar aplicativos de console "sem periféricos" que acessam as APIs de macOS nativa usando xamarin. Mac.

## <a name="working-with-xib-filesmacapp-fundamentalsxibmd"></a>[Trabalhando com arquivos. XIB](~/mac/app-fundamentals/xib.md)

Este artigo aborda o trabalho com arquivos. XIB criados no construtor de Interface do Xcode para criar e manter interfaces de usuário para um aplicativo xamarin. Mac.

## <a name="storyboardxib-less-user-interface-designmacapp-fundamentalsxibless-uimd"></a>[.Storyboard/.XIB menos o design da interface do usuário](~/mac/app-fundamentals/xibless-ui.md)

Este artigo aborda a criação de interface do usuário de um aplicativo xamarin. MAC diretamente do código c# sem usar o Interface Builder do Xcode com arquivos. XIB ou de storyboard.

## <a name="working-with-imagesmacapp-fundamentalsimagemd"></a>[Trabalhando com imagens](~/mac/app-fundamentals/image.md)

Este artigo aborda o trabalho com imagens e ícones em um aplicativo xamarin. Mac. Ele aborda a criação e manutenção das imagens necessárias para criar o ícone do seu aplicativo e usar imagens no código c# e no Interface Builder do Xcode.

## <a name="data-binding-and-key-value-codingmacapp-fundamentalsdatabindingmd"></a>[Vinculação de dados e a codificação de chave-valor](~/mac/app-fundamentals/databinding.md)

Este artigo aborda o uso de chave-valor de codificação e chave-valor observando para permitir a vinculação de dados para elementos de interface do usuário no Interface Builder do Xcode. Usando essa técnica, você reduzir consideravelmente a quantidade de código c# que precisa ser escrito para o seu aplicativo xamarin. Mac. 

## <a name="working-with-databasesmacapp-fundamentalsdatabasesmd"></a>[Trabalhando com bancos de dados](~/mac/app-fundamentals/databases.md)

Este artigo aborda o uso de chave-valor de codificação e chave-valor observando para permitir a vinculação de dados com acesso direto aos bancos de dados SQLite para elementos de interface do usuário no Interface Builder do Xcode. Ele também aborda o uso SQLite.NET ORM para fornecer acesso aos dados do SQLite.

## <a name="working-with-copy-and-pastemacapp-fundamentalscopy-pastemd"></a>[Trabalhando com copiar e colar](~/mac/app-fundamentals/copy-paste.md)

Este artigo aborda o trabalho com a área de trabalho para fornecer a copiar e colar em um aplicativo xamarin. Mac. Ele mostra como trabalhar com tipos de dados padrão que podem ser compartilhados entre vários aplicativos e como dar suporte a dados personalizados dentro de um determinado aplicativo.

## <a name="sandboxing-a-xamarinmac-appmacapp-fundamentalssandboxingmd"></a>[Área restrita um aplicativo xamarin. Mac](~/mac/app-fundamentals/sandboxing.md)

Este artigo aborda o modo seguro de um aplicativo xamarin. Mac para lançar na App Store. Ele abrange todos os elementos que entram no modo seguro: os diretórios de contêiner, direitos, permissões de usuário determinado, separação de privilégios e imposição de kernel.

## <a name="playing-sound-with-avaudioplayermacapp-fundamentalssoundsmd"></a>[Reproduzir som com AVAudioPlayer](~/mac/app-fundamentals/sounds.md)

Este artigo mostra como usar uma classe auxiliar para controlar a reprodução do som usando um AVAudioPlayer.

## <a name="reporting-bugsmacapp-fundamentalstroubleshootingmd"></a>[Relatório de bugs](~/mac/app-fundamentals/troubleshooting.md)

Às vezes, todos nós ficar preso enquanto estiver trabalhando em um projeto, incapacidade para uma API para trabalhar da maneira que gostaríamos de obter ou tentar contornar um bug. Nossa meta no Xamarin é para ser bem-sucedido na escrita de seus aplicativos móveis e de desktop e fornecemos alguns recursos para ajudar.
