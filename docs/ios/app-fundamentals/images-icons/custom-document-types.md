---
title: Ícones de documento personalizado no xamarin. IOS
description: Aborda esse artigo incluindo e gerenciar um ativo de imagem em um aplicativo xamarin. IOS para ser usado como um ícone de tipo de documento personalizado.
ms.prod: xamarin
ms.assetid: 7A3F3C94-2578-4F53-9B8E-25714F48BDD6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/23/2017
ms.openlocfilehash: 155847b4f5f6b3e6070f1afb6219db2d3789a075
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61258095"
---
# <a name="custom-document-icons-in-xamarinios"></a>Ícones de documento personalizado no xamarin. IOS

_Aborda esse artigo incluindo e gerenciar um ativo de imagem em um aplicativo xamarin. IOS para ser usado como um ícone de tipo de documento personalizado._

Se um aplicativo xamarin. IOS dá suporte ao carregamento de um tipo de documento em particular, o desenvolvedor pode fornecer ícones que o sistema usará quando encontra esse tipo de documento, como quando um usuário segura para baixo de um anexo na *aplicativo de email* como mostrado aqui:

 [![](custom-document-types-images/17.png "Um exemplo de ícones do tipo de documento")](custom-document-types-images/17.png#lightbox)

O desenvolvedor pode adicionar informações de tipo de documento para um formato de arquivo, o aplicativo é capaz de abertura, incluindo as entradas de dicionário para o `CFBundleTypeName` cadeia de caracteres e `LSItemContentTypes` matriz em que o aplicativo `Info.plist`. Os ícones para o tipo de documento entrar a `CFBundleTypeIconFiles` matriz. Se um ícone de documento não for fornecido, o iOS derivará um do ícone do aplicativo.
Ícones podem ser fornecidos para vários tamanhos, otimizados para as várias resoluções de dispositivo. 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para atribuir esses valores no Visual Studio para Mac, use o **tipos de documento** seção sob o **avançado** guia o `Info.plist` editor para adicionar o tipo de documento e atribua os ícones de imagem para ele. Por exemplo, aqui está uma captura de tela mostrando o registro para suporte a PDF:

 [![](custom-document-types-images/18.png "A seção de tipos de documento sob a guia Avançado no editor de 'info.'")](custom-document-types-images/18.png#lightbox)
 
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para atribuir esses valores no Visual Studio, use o **tipos de documento** seção sob o **avançado** guia o `Info.plist`:

 ![](custom-document-types-images/doc01w.png "Abra a seção tipos de documento sob a guia Avançado")

Clique o **Adicionar tipo de documento** botão e preencha os campos obrigatórios:

![](custom-document-types-images/doc02w.png "O formulário Adicionar tipo de documento")

-----


Para obter mais informações sobre tipos de documento, consulte da Apple [referência de identificadores de tipo de uniforme](https://developer.apple.com/library/ios/#documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html) e [tópicos de programação de interação de documento para iOS](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Introduction/Introduction.html).


## <a name="related-links"></a>Links relacionados

- [Trabalhando com imagens (amostra)](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Ícone personalizado e diretrizes de criação de imagem](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
