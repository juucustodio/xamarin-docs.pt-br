---
title: Ícones de aplicativo no xamarin. IOS
description: 'Este documento descreve como trabalhar com vários ícones do aplicativo no xamarin. IOS: o ícone do aplicativo em si, ícones de destaque, os ícones de configurações e arte do iTunes.'
ms.prod: xamarin
ms.assetid: B7791574-4A0F-4CB6-8C18-36D40B5C91EB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/22/2017
ms.openlocfilehash: 1eb3ec77dd478744906625dcfcd9fb843e74577c
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827480"
---
# <a name="application-icons-in-xamarinios"></a>Ícones de aplicativo no xamarin. IOS

Os tópicos a seguir serão abordados em detalhes:

* [Aplicativo, destaque e configurações de ícones](#icon-types) -os diferentes tipos de ícones, necessárias para um aplicativo iOS.
* [Gerenciando ícones com catálogos de ativos](#managing) – Gerenciando ícones de aplicativo usando os catálogos de ativos.
* [Arte do iTunes](#itunes) -fornecer a arte do iTunes necessária para o método de Ad-Hoc de entregar seu aplicativo.

<a name="icon-types" />

## <a name="application-spotlight-and-settings-icons"></a>Aplicativo em destaque e ícones de configurações

Da mesma forma que um aplicativo xamarin. IOS pode usar ativos de imagem para controles de interface do usuário e como ícones de documento, os ativos de imagem podem ser usados para fornecer ícones de aplicativo. As seguintes capturas de tela de um iPad ilustra três usos de ícones do iOS:

- **Ícone do aplicativo** -todos os aplicativos iOS devem definir um ícone do aplicativo. Esse é o ícone que o usuário deverá tocar da tela inicial do iOS para iniciar o aplicativo. Além disso, esse ícone é usado pela Central de jogo, se aplicável. Exemplo: 

    [![](app-icons-images/000.png "Ícone do aplicativo")](app-icons-images/000-full.png#lightbox)
- **Ícone de destaque** : sempre que o usuário insere o nome de um aplicativo em uma pesquisa de destaque, esse ícone é exibido. Exemplo: 

    [![](app-icons-images/000a.png "Ícone de destaque")](app-icons-images/000a-full.png#lightbox)
- **Ícone de configurações** – se o usuário insere o **configurações** aplicativo em seu dispositivo iOS, esse ícone será exibido no final do **configurações** lista para o aplicativo. Exemplo: 

    [![](app-icons-images/000b.png "Ícone de configurações")](app-icons-images/000b-full.png#lightbox)

Os seguintes tamanhos de ativos de imagem e resoluções serão necessários para dar suporte a todos os tipos de ícone exigidos por um aplicativo xamarin. IOS direcionamento ao iOS 5, por meio do iOS 9 (ou posterior):

### <a name="iphone-icon-sizes"></a>Tamanhos de ícones do iPhone

- **iPhone: iOS 9 e 10 (iPhone 6 e 7 Plus)**

    ||3x|
    |---|---|
    |Ícone do Aplicativo|180x180|
    |Em destaque|120x120|
    |Configurações|87x87|

- **iPhone: iOS 7 e 8**

    ||1x|2x|
    |---|---|---|
    |Ícone do Aplicativo|60x60<sup>1</sup>|120x120|
    |Em destaque|40x40<sup>2</sup>|80x80|
    |Configurações|-|-|

- **iPhone: iOS 5 e 6**

    ||1x|2x|
    |---|---|---|
    |Ícone do Aplicativo|57 x 57|114x114|
    |Em destaque|29 x 29|58x58|
    |Configurações|29x29<sup>3, 4</sup>|58x58<sup>3, 4</sup>|

### <a name="ipad-icon-sizes"></a>Tamanhos de ícones do iPad

- **iPad: iOS 9 & 10**

    ||2x (iPad Pro)|
    |---|---|
    |Ícone do Aplicativo|167x167<sup>6</sup>|
    |Em destaque|120x120<sup>6</sup>|
    |Configurações|58x58<sup>5</sup>|

- **iPad: iOS 7 & 8**

    ||1x|2x|
    |---|---|---|
    |Ícone do Aplicativo|76 x 76|152x152|
    |Em destaque|40 x 40|80x80|
    |Configurações|-|-|

- **iPad: iOS 5 & 6**

    ||1x|2x|
    |---|---|---|
    |Ícone do Aplicativo|72 x 72|144x144|
    |Em destaque|50 x 50|100x100|
    |Configurações|29x29<sup>3, 5</sup>|58x58<sup>3, 5</sup>|

 1. Ambos os Visual Studio para Mac e o Xcode não dá suporte à definição de 1 imagem de x para o iOS 7.
 2. Definir uma imagem de 1 x, IOS 7 não é suportado ao usar os catálogos de ativos.
 3. iOS 7 e 8 usam os mesmos tamanhos de imagem como iOS 5 e 6.
 4. Usa as mesmas imagens e tamanhos como o ícone em destaque.
 5. Usa os ícones de tamanho mesmo como o iPhone.
 6. Só tem suporte com conjuntos de imagem de catálogo ativo.
 
 Para obter mais informações sobre ícones, consulte da Apple [tamanhos de imagem e ícone](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/IconMatrix.html#//apple_ref/doc/uid/TP40006556-CH27-SW1) documentação.

<a name="managing" />

## <a name="managing-icons-with-asset-catalogs"></a>Gerenciando ícones com catálogos de ativos

Para ícones, um especial `AppIcon` conjunto de imagens pode ser adicionado para o `Assets.xcassets` arquivo no projeto do aplicativo. Todas as versões da imagem necessária para dar suporte a todas as resoluções são incluídos na _xcasset_ e agrupadas. Um editor especial no Visual Studio para Mac permite ao desenvolvedor incluir e configurar essas imagens graficamente.

Para usar um catálogo de ativos, faça o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Clique duas vezes o `Info.plist` do arquivo na **Gerenciador de soluções** para abri-lo para edição.
2. Role para baixo até a **ícones de aplicativo** seção.
3. Dos **fonte** lista suspensa lista, certifique-se **AppIcon** está selecionada: 

    ![](app-icons-images/migrate01.png "Verifique se que AppIcon está selecionado")
4. Dos **Gerenciador de soluções**, clique duas vezes o `Assets.xcassets` arquivo para abri-lo para edição: 

    ![](app-icons-images/asset01.png "O arquivo xcassets no Gerenciador de soluções")
5. Selecione `AppIcon` na lista de ativos para exibir o `Icon Editor`:

    ![](app-icons-images/asset02.png "O editor AppIcon")
6. Clique no ícone de tipo fornecido e selecione um arquivo de imagem para o tipo/tamanho necessário ou arraste em uma imagem de uma pasta e solte-a no tamanho desejado.
7. Clique o **abrir** botão para incluir a imagem no projeto e defina-o do xcasset.
8. Repita para todas as imagens necessárias.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Clique duas vezes o **Info. plist** arquivo na **Gerenciador de soluções**:

    ![](app-icons-images/icon01w.png "Selecione Info. plist")
2. Clique no **ativos visuais** guia e clique no **usar o catálogo de ativos** botão sob **ícones do aplicativo**: 

    ![](app-icons-images/icon02w.png "Selecione a guia ativos visuais")
4. Dos **Gerenciador de soluções**, expanda o **catálogo de ativos** pasta: 

    ![](app-icons-images/image009.png "Expanda a pasta do catálogo de ativos")
5. Clique duas vezes o **mídia** arquivo para abri-lo no editor: 

    ![](app-icons-images/image010.png "Abra o arquivo de mídia no editor")
6. Sob o **Gerenciador de propriedades** o desenvolvedor pode selecionar os diferentes tipos e tamanhos de ícones necessários.
7. Clique no ícone de tipo fornecido e selecione um arquivo de imagem para o tipo/tamanho necessário.
8. Clique o **abrir** botão para incluir a imagem no projeto e defina-o do xcasset.
9. Repita para todas as imagens necessárias.

-----

Esse é o método preferencial de incluindo e gerenciamento de ativos de imagem que serão usados para fornecer ícones de aplicativo, destaque e configurações para um aplicativo.

### <a name="migrating-from-infoplist-to-asset-catalogs"></a>Migrando do Info. plist para catálogos de ativos

Para um aplicativo xamarin. IOS existente usando o `Info.plist` para gerenciar seus ícones de arquivo, é altamente recomendável que o desenvolvedor alternar para usar o `AppIcons` ativo de imagem dentro de `Assets.xcassets`.

Faça o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Clique duas vezes o `Info.plist` do arquivo na **Gerenciador de soluções** para abri-lo para edição.
2. Role para baixo até a **ícones de aplicativo** seção.
3. Dos **fonte** lista suspensa, selecione **migrar para catálogos de ativos**: 

    ![](app-icons-images/migrate02.png "Selecione migrar para catálogos de ativos")
4. Nenhum ícone existente definido na `Info.plist` arquivo será migrado para um `AppIcons` definido de imagem adicionado ao `Assets.xcassets`: 

     ![](app-icons-images/migrate03.png "O conjunto de imagem AppIcons a xcassets")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Clique duas vezes o `Info.plist` do arquivo na **Gerenciador de soluções** para abri-lo para edição.
2. Clique no iPhone seção ícones: 

    ![](app-icons-images/image007.png "Editor de ícones do iPhone trocador")
3. Role para baixo até a **ícones** seção.
4. Dos **catálogo de ativos** lista suspensa, selecione **catálogos de ativos de uso**.
5. Nenhum ícone existente definido na `Info.plist` arquivo será migrado para um `Images` adicionado ao conjunto de `Assets.xcassets`.
6. Salve as alterações no arquivo `Info.plist`.

-----

<a name="itunes" />

## <a name="itunes-artwork"></a>Arte do iTunes

Se usar o método Ad Hoc de entregar o aplicativo (ou para usuários corporativos ou de teste beta em dispositivos reais), o desenvolvedor também precisa incluir um 512 x 512 e uma imagem de 1024 x 1024 que será usada para representar o aplicativo no iTunes.

Para especificar a Arte do iTunes, faça o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Clique duas vezes o `Info.plist` do arquivo na **Gerenciador de soluções** para abri-lo para edição.
2. Role até a **arte do iTunes** seção do editor: 

    ![](app-icons-images/itunes01.png "Role até a seção de arte final do editor do iTunes")
3. Para qualquer imagem ausente, clique na miniatura no editor, selecione o arquivo de imagem para a arte do iTunes desejada na caixa de diálogo Abrir arquivo e clique no **Okey** botão.
4. Repita essa etapa até que todos os necessários imagens foram especificadas para o aplicativo.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Clique duas vezes o `Info.plist` do arquivo na **Gerenciador de soluções** para abri-lo para edição.

2. Clique no **ativos visuais** guia e expanda o **arte do iTunes**: 

    ![](app-icons-images/itunes01w.png "Edição de arte no Visual Studio do iTunes")
4. Para qualquer imagem ausente, clique na miniatura no editor, selecione o arquivo de imagem para a arte do iTunes desejada na caixa de diálogo Abrir arquivo e clique no **abrir** botão.
5. Repita essa etapa até que todos os necessários imagens foram especificadas para o aplicativo.

-----

## <a name="related-links"></a>Links relacionados

- [Trabalhando com imagens (amostra)](https://developer.xamarin.com/samples/monotouch/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Ícone personalizado e diretrizes de criação de imagem](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html))
