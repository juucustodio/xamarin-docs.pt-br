---
title: Ícone do aplicativo para aplicativos Xamarin.Mac
description: Este artigo aborda a criação de imagens necessárias para um ícone de um aplicativo Xamarin.Mac, agrupando as imagens em um arquivo ".icns" e incluindo o ícone no projeto Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 675b9405-d9a7-49f0-94ad-417f10a71d11
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: d0acd44561b220507aafda7af05cd2862f6c9009
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73021709"
---
# <a name="application-icon-for-xamarinmac-apps"></a>Ícone do aplicativo para aplicativos Xamarin.Mac

_Este artigo abrange a criação das imagens necessárias para o ícone de um aplicativo Xamarin.Mac, agrupando as imagens em um arquivo .icns e incluindo o ícone no projeto Xamarin.Mac._

## <a name="overview"></a>Visão geral

Ao trabalhar com C# e .NET em um aplicativo Xamarin.Mac, um desenvolvedor tem acesso às mesmas ferramentas de Imagem e Ícones que um desenvolvedor trabalhando em *Objective-C* e *Xcode*.

Um ícone grande deve transmitir o objetivo principal de uma experiência de aplicativo e dica de Xamarin.Mac que o usuário deve esperar ao usar o aplicativo. Este artigo aborda todas as etapas necessárias para criar os Ativos de Imagem necessários para um ícone, empacotando os ativos em um arquivo `AppIcon.appiconset` e consumindo esse arquivo em um aplicativo de Xamarin.Mac.

![O editor AppIcon.appiconset](app-icon-images/intro01.png "O editor AppIcon.appiconset")

## <a name="application-icon"></a>Ícone do aplicativo

Um ícone grande deve transmitir o objetivo principal de uma experiência de aplicativo e dica de Xamarin.Mac que o usuário deve esperar ao usar um aplicativo. Todos os aplicativos macOS devem incluir vários tamanhos de seu ícone para exibição no Localizador, Compartimento, Barra Inicial e outros locais em todo o computador.

## <a name="designing-the-icon"></a>Projetando o ícone

A Apple sugere as dicas a seguir ao criar o ícone do aplicativo:

- Considere dar uma forma realista e exclusiva ao ícone.
- Se o aplicativo macOS tem um equivalente do iOS, não reutilize o ícone do aplicativo iOS.
- Use imagens universais que pessoas podem reconhecer facilmente.
- Mantenha a simplicidade.
- Use cor e sombra com moderação para ajudar o ícone contar história do aplicativo.
- Evite a mistura de texto real com texto _grego_ ou linhas para sugerir texto.
- Crie uma versão idealizada do assunto do ícone vez de usar uma foto real.
- Evite usar elementos de interface do usuário do macOS em ícones.
- Não use réplicas dos ícones da Apple nos ícones.

Leia as seções [Galeria de Ícone do Aplicativo](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/Gallery.html#//apple_ref/doc/uid/20000957-CH88-SW1) e [Criando Ícones de Aplicativo](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/Designing.html#//apple_ref/doc/uid/20000957-CH87-SW1) das [Diretrizes de Interface Humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) da Apple antes de criar um ícone do aplicativo do Xamarin.Mac.

## <a name="required-image-sizes-and-filenames"></a>Tamanhos de imagem e nomes de arquivos necessários

Como qualquer outro Recurso de Imagem que o desenvolvedor usará em um aplicativo Xamarin.Mac, o ícone do aplicativo precisa fornecer uma versão de resolução Standard e Retina. Novamente, como qualquer outra imagem, use um formato `@2x` ao nomear os arquivos de ícone:

- **Nome de imagem de resolução**  - _padrão_**.** _filename-extension_ (Exemplo: **icon_512x512.png**)
- **Nome de**  - imagem de alta resolução _._**@2x** _filename-extension_ (Exemplo: **icon_512x512@2x.png**)

Por exemplo, para fornecer a versão de 512 x 512 do ícone do aplicativo, o arquivo seria nomeado **icon_512x512.png** e **icon_512x512@2x.png**.

Para garantir que o ícone tenha boa aparência em todos os locais que os usuários o vejam, forneça recursos de tamanhos listados abaixo:

|Nome de arquivo|Tamanho em Pixels|
|---|---|
|icon_512x512@2x.png|1024 x 1024|
|icon_512x512.png|512 x 512|
|icon_256x256@2x.png|512 x 512|
|icon_256x256.png|256 x 256|
|icon_128x128@2x.png|256 x 256|
|icon_128x128.png|128 x 128|
|icon_32x32@2x.png|64 x 64|
|icon_32x32.png|32 x 32|
|icon_16x16@2x.png|32 x 32|
|icon_16x16.png|16 x 16|

Para obter mais informações, consulte a documentação [Fornecer versões de alta resolução de todos os recursos gráficos do aplicativo](https://developer.apple.com/library/mac/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Optimizing/Optimizing.html#//apple_ref/doc/uid/TP40012302-CH7-SW3) da Apple.

## <a name="packaging-the-icon-resources"></a>Empacotamento de recursos de ícone

Com o ícone criado e salvo nos nomes e tamanhos dos arquivos necessários, o Visual Studio para Mac facilita sua atribuição aos ativos de imagem para uso no Xamarin.Mac.

Faça o seguinte:

1. No **Painel de Soluções**, abra **Assets.xcassets** > **AppIcons.appiconset**: 

    ![Edição do AppIcon.appiconset](app-icon-images/intro01.png "Edição do AppIcon.appiconset")
2. Para cada tamanho de ícone necessário, clique no ícone e selecione o arquivo de imagem correspondentes criado acima: 

    [![Selecionando uma imagem de ícone](app-icon-images/intro02.png "Selecionando uma imagem de ícone")](app-icon-images/intro02-large.png#lightbox)
3. Salve suas alterações.

## <a name="using-the-icon"></a>Usar o Ícone

Assim que o arquivo `AppIcon.appiconset` tiver sido criado, ele precisará atribuí-lo ao projeto Xamarin.Mac no Visual Studio para Mac.

Faça o seguinte:

1. Clique duas vezes no **Info.plist** no **Painel de Soluções** para abrir as **Opções de Projeto**.
2. Na seção **Destino de Aplicativo do Mac OS X**, clique em **Ícones de Aplicativo** para selecionar o arquivo `AppIcon.appiconset`: 

    [![Definindo o conjunto de ícones](app-icon-images/icon01.png "Configurando o conjunto de ícones")](app-icon-images/icon01-large.png#lightbox)
3. Salve as alterações.

Quando o aplicativo for executado, o novo ícone será exibido no compartimento:

![Um exemplo de um ícone de aplicativo no dock do macOS](app-icon-images/icon04.png "Um exemplo de um ícone de aplicativo no dock do macOS")

## <a name="summary"></a>Resumo

Este artigo apresentou uma visão detalhada de como trabalhar com imagens necessárias para criar um ícone de aplicativo macOS ícone, um ícone de empacotamento e como incluir um ícone em um projeto Xamarin.Mac.

## <a name="related-links"></a>Links relacionados

- [MacImages (amostra)](https://docs.microsoft.com/samples/xamarin/mac-samples/macimages)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabalhando com Imagens](~/mac/app-fundamentals/image.md)
- [Diretrizes de interface humana macOS – ícones e imagens](https://developer.apple.com/macos/human-interface-guidelines/icons-and-images/image-size-and-resolution/)
- [Sobre a alta resolução para OS X](https://developer.apple.com/library/content/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Introduction/Introduction.html)
- [Construtor Icns](https://itunes.apple.com/us/app/icns-builder/id554660130?mt=12)
