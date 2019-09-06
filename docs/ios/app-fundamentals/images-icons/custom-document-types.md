---
title: Ícones de documento personalizados no Xamarin. iOS
description: Este artigo aborda a inclusão e o gerenciamento de um ativo de imagem em um aplicativo Xamarin. iOS a ser usado como um ícone de tipo de documento personalizado.
ms.prod: xamarin
ms.assetid: 7A3F3C94-2578-4F53-9B8E-25714F48BDD6
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 05/23/2017
ms.openlocfilehash: 683587e4857ede20096be731b3cfa3b88b3a668d
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282526"
---
# <a name="custom-document-icons-in-xamarinios"></a>Ícones de documento personalizados no Xamarin. iOS

_Este artigo aborda a inclusão e o gerenciamento de um ativo de imagem em um aplicativo Xamarin. iOS a ser usado como um ícone de tipo de documento personalizado._

Se um aplicativo Xamarin. iOS der suporte ao carregamento de um tipo de documento específico, o desenvolvedor poderá fornecer ícones que o sistema usará quando encontrar esse tipo de documento, como quando um usuário mantiver um anexo no *aplicativo de email* , como mostrado aqui:

 [![](custom-document-types-images/17.png "Um exemplo de ícones de tipo de documento")](custom-document-types-images/17.png#lightbox)

O desenvolvedor pode adicionar informações de tipo de documento para um formato de arquivo que o aplicativo é capaz de abrir, incluindo `CFBundleTypeName` entradas de `LSItemContentTypes` dicionário para a cadeia de `Info.plist`caracteres e a matriz no aplicativo. Os ícones para o tipo de documento entram na `CFBundleTypeIconFiles` matriz. Se um ícone de documento não for fornecido, o iOS irá derivar um do ícone do aplicativo.
Os ícones podem ser fornecidos para vários tamanhos, otimizados para as várias resoluções de dispositivo. 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para atribuir esses valores em Visual Studio para Mac, use a seção **tipos de documento** na `Info.plist` guia Avançado no editor para adicionar o tipo de documento e atribuir ícones de imagem a ele. Por exemplo, aqui está uma captura de tela mostrando o registro do suporte a PDF:

 [![](custom-document-types-images/18.png "A seção tipos de documento sob a guia Avançado no editor ' info. plist '")](custom-document-types-images/18.png#lightbox)
 
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para atribuir esses valores no Visual Studio, use a seção **tipos de documento** na `Info.plist`guia Avançado no:

 ![](custom-document-types-images/doc01w.png "Abra a seção tipos de documento na guia Avançado")

Clique no botão **Adicionar tipo de documento** e preencha os campos obrigatórios:

![](custom-document-types-images/doc02w.png "O formulário Adicionar tipo de documento")

-----


Para obter mais informações sobre tipos de documento, consulte [referência de identificadores de tipo uniforme](https://developer.apple.com/library/ios/#documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html) da Apple e [Tópicos de programação de interação de documentos para IOS](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Introduction/Introduction.html).


## <a name="related-links"></a>Links relacionados

- [Trabalhando com imagens (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Diretrizes personalizadas de criação de imagem e ícone](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
