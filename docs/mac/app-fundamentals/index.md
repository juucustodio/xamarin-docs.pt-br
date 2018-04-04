---
title: Conceitos básicos de aplicativo
description: Links neste documento em guias que descrevem vários conceitos necessários para entender o desenvolvimento de aplicativos Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 5A36B3A7-F197-4AC3-A40D-B2C49362FF06
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 12/17/2015
ms.openlocfilehash: 807cf0e16cafc00483cecfc578367bc110657672
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="application-fundamentals"></a>Conceitos básicos de aplicativo

## <a name="common-patterns-and-idiomsmacapp-fundamentalspatternsmd"></a>[Linguagens e padrões comuns](~/mac/app-fundamentals/patterns.md)

Ao longo das APIs de Apple expostos por meio do c#, determinados idiomas e os padrões surgem repetidamente. Se você tiver experiência com programação com xamarin, eles podem parecer familiares. Documentação geralmente fará referência a esses padrões e as linguagens repetidamente, para que ter uma compreensão sólida deles ajudará você a compreender a documentação que você encontrar.

## <a name="understanding-mac-apismacapp-fundamentalsmac-apismd"></a>[APIs de compreensão de Mac](~/mac/app-fundamentals/mac-apis.md)

Para a maior parte do seu tempo de desenvolvimento com Xamarin.Mac, você pode pensar, leitura e gravação em c# sem precisar se preocupar muito com as APIs Objective-C subjacente. No entanto, às vezes, você será necessário para ler a documentação da API da Apple, converter uma resposta de estouro de pilha para uma solução para o seu problema ou comparar a uma amostra existente.

## <a name="working-with-xib-filesmacapp-fundamentalsxibmd"></a>[Trabalhando com arquivos de .xib](~/mac/app-fundamentals/xib.md)

Este artigo aborda o trabalho com arquivos .xib criados no construtor de Interface do Xcode para criar e manter as interfaces de usuário para um aplicativo Xamarin.Mac.

## <a name="storyboardxib-less-user-interface-designmacapp-fundamentalsxibless-uimd"></a>[.Storyboard/.XIB menos design de interface do usuário](~/mac/app-fundamentals/xibless-ui.md)

Este artigo aborda a criação de interface do usuário do aplicativo um Xamarin.Mac diretamente do código c# sem usar o construtor de Interface do Xcode com .storyboard ou .xib arquivos.

## <a name="working-with-imagesmacapp-fundamentalsimagemd"></a>[Trabalhando com imagens](~/mac/app-fundamentals/image.md)

Este artigo aborda o trabalho com imagens e ícones em um aplicativo Xamarin.Mac. Inclui a criação e manutenção de imagens necessárias para criar seu aplicativo ícone e usando imagens em código c# e o construtor de Interface do Xcode.

## <a name="data-binding-and-key-value-codingmacapp-fundamentalsdatabindingmd"></a>[Associação de dados e a codificação de chave-valor](~/mac/app-fundamentals/databinding.md)

Este artigo aborda o uso de chave-valor de codificação e chave-valor observando para permitir a associação de dados para os elementos de interface do usuário no construtor de Interface do Xcode. Usando essa técnica, você reduzir significativamente a quantidade de código em c# que precisa ser escrito para seu aplicativo Xamarin.Mac. 

## <a name="working-with-databasesmacapp-fundamentalsdatabasesmd"></a>[Trabalhando com bancos de dados](~/mac/app-fundamentals/databases.md)

Este artigo aborda o uso de chave-valor observando para permitir a associação de dados com acesso direto aos bancos de dados SQLite para elementos de interface do usuário no construtor de Interface do Xcode e codificação de chave-valor. Ele também aborda o uso do ORM SQLite.NET para fornecer acesso a dados SQLite.

## <a name="working-with-copy-and-pastemacapp-fundamentalscopy-pastemd"></a>[Trabalhando com copiar e colar](~/mac/app-fundamentals/copy-paste.md)

Este artigo aborda o trabalho com a área de trabalho para fornecer a copiar e colar em um aplicativo Xamarin.Mac. Ele mostra como trabalhar com tipos de dados padrão que podem ser compartilhados entre vários aplicativos e como oferecer suporte a dados personalizados em um aplicativo oferecem.

## <a name="sandboxing-a-xamarinmac-appmacapp-fundamentalssandboxingmd"></a>[Um aplicativo Xamarin.Mac de modo seguro](~/mac/app-fundamentals/sandboxing.md)

Este artigo aborda um aplicativo Xamarin.Mac versão na loja de aplicativos de modo seguro. Ele abrange todos os elementos que entram no modo seguro: diretórios de contêiner, direitos, permissões de usuário determinado, separação de privilégios e imposição de kernel.

## <a name="playing-sound-with-avaudioplayermacapp-fundamentalssoundsmd"></a>[Reproduzir som com AVAudioPlayer](~/mac/app-fundamentals/sounds.md)

Este artigo mostra como usar uma classe auxiliar para controlar a reprodução do som usando um AVAudioPlayer.

## <a name="reporting-bugsmacapp-fundamentalstroubleshootingmd"></a>[Relatório de bugs](~/mac/app-fundamentals/troubleshooting.md)

Às vezes, todos os nós presa enquanto estiver trabalhando em um projeto, na incapacidade de obter uma API para funcionar da maneira que desejamos ou tentando solucionar um bug. Nosso objetivo no Xamarin é para ser bem-sucedido ao gravar seus aplicativos móveis e desktop e fornecemos alguns recursos para ajudar.
