---
title: "Ícones de documento personalizadas"
description: "Cobre este artigo incluindo e gerenciando um ativo de imagem em um aplicativo xamarin para ser usado como um ícone de tipo de documento personalizadas."
ms.topic: article
ms.prod: xamarin
ms.assetid: 7A3F3C94-2578-4F53-9B8E-25714F48BDD6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/23/2017
ms.openlocfilehash: 582fcbacbf1959e05773babb1219817ba319a937
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="custom-document-icons"></a>Ícones de documento personalizadas

_Cobre este artigo incluindo e gerenciando um ativo de imagem em um aplicativo xamarin para ser usado como um ícone de tipo de documento personalizadas._

Se um aplicativo xamarin dá suporte ao carregamento de um tipo de documento específico, o desenvolvedor pode fornecer ícones que o sistema usará quando encontra esse tipo de documento, como quando um usuário segura para baixo de um anexo no *aplicativo Mail* como mostrado aqui:

 [ ![](custom-document-types-images/17.png "Um exemplo de ícones do tipo de documento")](custom-document-types-images/17.png)

O desenvolvedor pode adicionar informações de tipo de documento para um formato de arquivo do aplicativo é capaz de abrir, incluindo entradas de dicionário para a `CFBundleTypeName` cadeia de caracteres e `LSItemContentTypes` matriz no aplicativo do `Info.plist`. Os ícones para o tipo de documento entrar a `CFBundleTypeIconFiles` matriz. Se um ícone de documento não for fornecido, o iOS obterão um pelo ícone do aplicativo.
Ícones podem ser fornecidos para vários tamanhos, otimizados para as várias resoluções de dispositivo. 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para atribuir esses valores no Visual Studio para Mac, use o **tipos de documento** seção sob o **avançado** guia o `Info.plist` editor para adicionar o tipo de documento e atribuir os ícones de imagem a ele. Por exemplo, aqui está uma captura de tela mostrando o registro para suporte PDF:

 [ ![](custom-document-types-images/18.png "A seção de tipos de documento na guia avançada no editor 'Info. plist'")](custom-document-types-images/18.png)
 
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para atribuir esses valores no Visual Studio, use o **tipos de documento** seção sob o **avançado** guia o `Info.plist`:

 ![](custom-document-types-images/doc01w.png "Abra a seção de tipos de documento na guia avançada")

Clique o **Adicionar tipo de documento** botão e preencha os campos obrigatórios:

![](custom-document-types-images/doc02w.png "O formulário Adicionar tipo de documento")

-----


Para obter mais informações sobre tipos de documento, consulte da Apple [uniforme referência de identificadores de tipo](http://developer.apple.com/library/ios/#documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html) e [tópicos de programação de interação de documento para iOS](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Introduction/Introduction.html).


## <a name="related-links"></a>Links relacionados

- [Trabalhando com imagens (exemplo)](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Ícone personalizado e diretrizes de criação de imagem](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
