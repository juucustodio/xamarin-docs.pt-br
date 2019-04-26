---
title: Ícones da App Store no xamarin. IOS
description: Este documento descreve como usar os catálogos de ativos para gerenciar um ícone da App Store para um aplicativo xamarin. IOS. Anteriormente, os ícones de aplicativo Store eram gerenciados com o iTunes Connect.
ms.prod: xamarin
ms.assetid: BFB5665A-F557-46E1-B35E-870CC2026AD9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/26/2017
ms.openlocfilehash: 53e25ae9f4650254f2aaaa03dc8727fae674c9f0
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61258072"
---
# <a name="app-store-icons-in-xamarinios"></a>Ícones da App Store no xamarin. IOS

Antes do Xcode 9 todos os ícones da App Store foram adicionados por meio do iTunes Connect. No entanto, isso não é mais o caso. Ícones de aplicativo Store agora devem ser incluídos como parte do seu pacote do projeto e adicionadas a um catálogo de ativos. Aplicativos que não contêm um ícone da App Store serão rejeitados pela Apple.

O ícone de Store de aplicativo é a aparência do seu aplicativo para usuários, portanto, ele deve ser fácil de lembrar e exibição bem em um tamanho pequeno. Ícones memoráveis são limpos, simples e imediatamente reconhecíveis.

A Apple sugere as seguintes diretrizes ao criar o ícone do seu aplicativo:

- Torne o ícone apropriado para seu aplicativo.
- Crie um ícone simples, consistente com o design do seu aplicativo.
- Evite usar palavras em seu ícone.
- Pense globalmente: Um único ícone do aplicativo é usado em todos os territórios de repositório.

Uma imagem de 1.024 x 1.024 pixels é necessária para o ícone do aplicativo que será exibido na App Store.  A Apple declarou que o ícone da App Store no catálogo de ativos não pode ser transparente nem conter um canal alfa.

Para obter mais informações, consulte da Apple [diretrizes de Interface humana do iOS](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/image-size-and-resolution/).

## <a name="adding-an-app-store-icon"></a>Adicionando um ícone da App Store

Os ícones de Lojas de Aplicativos devem ser entregues agora por um catálogo de ativos. 

Para adicionar um ícone da App Store faça o seguinte:

1. Localize o **AppIcon** imagem definido **xcassets** arquivo do seu projeto. 
    - Todos os novos projetos devem ser acompanhados de um uma **xcassets** arquivo que contém um conjunto de imagens AppIcon.
    - Para adicionar um novo catálogo de ativos, clique com botão direito no seu projeto e selecione **Adicionar > novo arquivo > Catálogo de ativos**.
    - Para adicionar um novo um conjunto de imagens de ícone do aplicativo, clique com botão direito no ícone conjunto área e selecione **imagens de inicialização e ícones do aplicativo > novo ícone do aplicativo**:
    
    ![Adicionar nova opção de conjunto de imagem](app-store-icon-images/image1.png)

2. Role até a **App Store** ícone na lista:

    ![Ícone da App Store](app-store-icon-images/image2.png)

3. Clique no ícone e navegue para a sua imagem de pixel de 1024 x 1024. Salve o catálogo de ativos.




## <a name="related-links"></a>Links relacionados

- [Gerenciando ícones com catálogos de ativos](~/ios/app-fundamentals/images-icons/app-icons.md#managing)
