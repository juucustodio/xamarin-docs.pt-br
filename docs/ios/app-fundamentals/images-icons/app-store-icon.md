---
title: Ícones da App Store no Xamarin. iOS
description: Este documento descreve como usar catálogos de ativos para gerenciar um ícone de loja de aplicativos para um aplicativo Xamarin. iOS. Anteriormente, os ícones da App Store foram gerenciados com o iTunes Connect.
ms.prod: xamarin
ms.assetid: BFB5665A-F557-46E1-B35E-870CC2026AD9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/26/2017
ms.openlocfilehash: 984ef59a4571379f7b2969ed4f15674f8819e4e4
ms.sourcegitcommit: 0df727caf941f1fa0aca680ec871bfe7a9089e7c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69620335"
---
# <a name="app-store-icons-in-xamarinios"></a>Ícones da App Store no Xamarin. iOS

Antes do Xcode 9, todos os ícones da App Store foram adicionados por meio do iTunes Connect. No entanto, esse não é mais o caso. Os ícones da App Store agora devem ser incluídos como parte do seu pacote de projeto e adicionados em um catálogo de ativos. Os aplicativos que não contêm um ícone de loja de aplicativos serão rejeitados pela Apple.

O ícone da loja de aplicativos é a face do seu aplicativo aos usuários, portanto, ele deve ser fácil de ser lembrado e exibido em um tamanho pequeno. Ícones memoráveis são limpos, simples e imediatamente reconhecíveis.

A Apple sugere as seguintes diretrizes ao criar o ícone do seu aplicativo:

- Torne o ícone apropriado para seu aplicativo.
- Crie um ícone simples, consistente com o design do seu aplicativo.
- Evite usar palavras em seu ícone.
- Pense globalmente: Um único ícone do aplicativo é usado em todos os territórios de repositório.

Uma imagem de 1.024 x 1.024 pixels é necessária para o ícone do aplicativo que será exibido na App Store.  A Apple declarou que o ícone da App Store no catálogo de ativos não pode ser transparente nem conter um canal alfa.

Para obter mais informações, consulte as [diretrizes de interface humana do IOS](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/image-size-and-resolution/)da Apple.

## <a name="adding-an-app-store-icon"></a>Adicionando um ícone de loja de aplicativos

Os ícones de Lojas de Aplicativos devem ser entregues agora por um catálogo de ativos. 

Para adicionar um ícone de loja de aplicativos, faça o seguinte:

1. Localize a imagem **appIcon** definida no arquivo **assets. xcassets** do seu projeto. 
    - Todos os novos projetos devem vir com um arquivo **assets. xcassets** que contém um conjunto de imagens appIcon.
    - Para adicionar um novo catálogo de ativos, clique com o botão direito do mouse no projeto e selecione **adicionar > novo arquivo > catálogo de ativos**.
    - Para adicionar um novo conjunto de imagens de ícone de aplicativo, clique com o botão direito do mouse na área do conjunto de ícones e selecione os **ícones do aplicativo & imagens de inicialização > ícone do novo aplicativo**:

    ![Adicionar nova opção de conjunto de imagens](app-store-icon-images/image1.png)

2. Role até o ícone da **loja de aplicativos** na lista:

    ![Ícone da App Store](app-store-icon-images/image2.png)

3. Clique no ícone e procure sua imagem de 1024 x 1024 pixels. Salve o catálogo de ativos.




## <a name="related-links"></a>Links relacionados

- [Gerenciando ícones com catálogos de ativos](~/ios/app-fundamentals/images-icons/app-icons.md#managing)
