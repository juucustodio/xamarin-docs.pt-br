---
title: Ícones de documento personalizados no Xamarin. iOS
description: Este artigo aborda a inclusão e o gerenciamento de um ativo de imagem em um aplicativo Xamarin. iOS a ser usado como um ícone de tipo de documento personalizado.
ms.prod: xamarin
ms.assetid: 7A3F3C94-2578-4F53-9B8E-25714F48BDD6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/23/2017
ms.openlocfilehash: 3a74084db461271ca7fd440ab2c9779f949b30ff
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436846"
---
# <a name="custom-document-icons-in-xamarinios"></a>Ícones de documento personalizados no Xamarin. iOS

_Este artigo aborda a inclusão e o gerenciamento de um ativo de imagem em um aplicativo Xamarin. iOS a ser usado como um ícone de tipo de documento personalizado._

Se um aplicativo Xamarin. iOS der suporte ao carregamento de um tipo de documento específico, o desenvolvedor poderá fornecer ícones que o sistema usará quando encontrar esse tipo de documento, como quando um usuário mantiver um anexo no *aplicativo de email* , como mostrado aqui:

 [![Um exemplo de ícones de tipo de documento](custom-document-types-images/17.png)](custom-document-types-images/17.png#lightbox)

O desenvolvedor pode adicionar informações de tipo de documento para um formato de arquivo que o aplicativo é capaz de abrir, incluindo entradas de dicionário para a `CFBundleTypeName` cadeia de caracteres e a `LSItemContentTypes` matriz no aplicativo `Info.plist` . Os ícones para o tipo de documento entram na `CFBundleTypeIconFiles` matriz. Se um ícone de documento não for fornecido, o iOS irá derivar um do ícone do aplicativo.
Os ícones podem ser fornecidos para vários tamanhos, otimizados para as várias resoluções de dispositivo. 

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Para atribuir esses valores em Visual Studio para Mac, use a seção **tipos de documento** na guia **avançado** no `Info.plist` Editor para adicionar o tipo de documento e atribuir ícones de imagem a ele. Por exemplo, aqui está uma captura de tela mostrando o registro do suporte a PDF:

 [![A seção tipos de documento sob a guia Avançado no editor ' info. plist '](custom-document-types-images/18.png)](custom-document-types-images/18.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Para atribuir esses valores no Visual Studio, use a seção **tipos de documento** na guia **avançado** no `Info.plist` :

 ![Abra a seção tipos de documento na guia Avançado](custom-document-types-images/doc01w.png)

Clique no botão **Adicionar tipo de documento** e preencha os campos obrigatórios:

![O formulário Adicionar tipo de documento](custom-document-types-images/doc02w.png)

-----

Para obter mais informações sobre tipos de documento, consulte [referência de identificadores de tipo uniforme](https://developer.apple.com/library/ios/#documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html) da Apple e [Tópicos de programação de interação de documentos para IOS](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Introduction/Introduction.html).

## <a name="related-links"></a>Links Relacionados

- [Trabalhando com imagens (exemplo)](/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Diretrizes personalizadas de criação de imagem e ícone](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)