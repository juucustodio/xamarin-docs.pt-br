---
title: Ícones de aplicativo no Xamarin. iOS
description: 'Este documento descreve como trabalhar com vários ícones de aplicativos no Xamarin. iOS: o próprio ícone do aplicativo, ícones de destaque, os ícones de configurações e a arte do iTunes.'
ms.prod: xamarin
ms.assetid: B7791574-4A0F-4CB6-8C18-36D40B5C91EB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/22/2017
ms.openlocfilehash: dd712940ff6e7000ffd9181f808a8ab32093b38d
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70121469"
---
# <a name="application-icons-in-xamarinios"></a>Ícones de aplicativo no Xamarin. iOS

Os tópicos a seguir serão abordados em detalhes:

- [Ícones de aplicativo, Spotlight e configurações](#icon-types) -os diferentes tipos de ícones necessários para um aplicativo Ios.
- [Gerenciando ícones com catálogos de ativos](#managing) -Gerenciando ícones de aplicativos usando catálogos de ativos.
- [trabalho artístico do iTunes](#itunes) -fornecendo a arte-final do iTunes necessária para o método ad hoc de entregar seu aplicativo.

<a name="icon-types" />

## <a name="application-spotlight-and-settings-icons"></a>Ícones de aplicativo, Spotlight e configurações

Da mesma forma que um aplicativo Xamarin. iOS pode usar ativos de imagem para controles de interface do usuário e ícones de documento, os ativos de imagem podem ser usados para fornecer ícones de aplicativo. As capturas de tela a seguir de um iPad ilustram os três usos de ícones no iOS:

- **Ícone de aplicativo** – todo aplicativo IOS deve definir um ícone de aplicativo. Esse é o ícone que o usuário irá tocar na tela inicial do iOS para iniciar o aplicativo. Além disso, esse ícone é usado por Game Center, se aplicável. Exemplo: 

    [![](app-icons-images/000.png "Ícone do aplicativo")](app-icons-images/000-full.png#lightbox)
- **Ícone de destaque** – sempre que o usuário inserir o nome de um aplicativo em uma pesquisa de destaque, esse ícone será exibido. Exemplo: 

    [![](app-icons-images/000a.png "Ícone de destaque")](app-icons-images/000a-full.png#lightbox)
- **Ícone de configurações** – se o usuário inserir o aplicativo de **configurações** em seu dispositivo IOS, esse ícone será exibido no final da lista de **configurações** do aplicativo. Exemplo: 

    [![](app-icons-images/000b.png "Ícone de configurações")](app-icons-images/000b-full.png#lightbox)

Os tamanhos e resoluções de ativos de imagem a seguir serão necessários para dar suporte a todos os tipos de ícone exigidos por um aplicativo Xamarin. iOS destinado ao iOS 5 até o iOS 9 (ou superior):

### <a name="iphone-icon-sizes"></a>Tamanhos de ícone do iPhone

- **iPhone: iOS 9 & 10 (iPhone 6 & 7 Plus)**

    ||vezes|
    |---|---|
    |Ícone do Aplicativo|180x180|
    |Luz|120x120|
    |Configurações|87x87|

- **iPhone: iOS 7 & 8**

    ||1x|2|
    |---|---|---|
    |Ícone do Aplicativo|60x60<sup>1</sup>|120x120|
    |Luz|40x40<sup>2</sup>|80x80|
    |Configurações|-|-|

- **iPhone: iOS 5 & 6**

    ||1x|2|
    |---|---|---|
    |Ícone do Aplicativo|57 x 57|114x114|
    |Luz|29 x 29|58x58|
    |Configurações|29x29<sup>3, 4</sup>|58x58<sup>3, 4</sup>|

### <a name="ipad-icon-sizes"></a>Tamanhos de ícone do iPad

- **iPad: iOS 9 & 10**

    ||2x (iPad Pro)|
    |---|---|
    |Ícone do Aplicativo|167x167<sup>6</sup>|
    |Luz|120x120<sup>6</sup>|
    |Configurações|58x58<sup>5</sup>|

- **iPad: iOS 7 & 8**

    ||1x|2|
    |---|---|---|
    |Ícone do Aplicativo|76 x 76|152x152|
    |Luz|40 x 40|80x80|
    |Configurações|-|-|

- **iPad: iOS 5 & 6**

    ||1x|2|
    |---|---|---|
    |Ícone do Aplicativo|72 x 72|144x144|
    |Luz|50 x 50|100x100|
    |Configurações|29x29<sup>3, 5</sup>|58x58<sup>3, 5</sup>|

 1. O Visual Studio para Mac e o Xcode não dão mais suporte à definição da imagem 1x para iOS 7.
 2. Não há suporte para a definição de uma imagem 1x para iOS 7 ao usar catálogos de ativos.
 3. o iOS 7 & 8 usa os mesmos tamanhos de imagem que o iOS 5 & 6.
 4. Usa as mesmas imagens e tamanhos que o ícone de destaque.
 5. Usa os mesmos ícones de tamanho que o iPhone.
 6. Somente com suporte com conjuntos de imagens do catálogo de ativos.
 
 Para obter mais informações sobre ícones, consulte a documentação de [tamanhos de imagem e ícone](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/IconMatrix.html#//apple_ref/doc/uid/TP40006556-CH27-SW1) da Apple.

<a name="managing" />

## <a name="managing-icons-with-asset-catalogs"></a>Gerenciando ícones com catálogos de ativos

Para ícones, um conjunto `AppIcon` de imagens especial pode ser adicionado `Assets.xcassets` ao arquivo no projeto do aplicativo. Todas as versões da imagem necessárias para dar suporte a todas as resoluções são incluídas no _xcasset_ e agrupadas juntas. Um editor especial no Visual Studio para Mac permite que o desenvolvedor inclua e configure essas imagens graficamente.

Para usar um catálogo de ativos, faça o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Clique `Info.plist` duas vezes no arquivo no **Gerenciador de soluções** para abri-lo para edição.
2. Role para baixo até a seção **ícones de aplicativo** .
3. Na lista suspensa **origem** , verifique se **appIcon** está selecionado: 

    ![](app-icons-images/migrate01.png "Verifique se AppIcon está selecionado")
4. No **Gerenciador de soluções**, clique duas vezes no `Assets.xcassets` arquivo para abri-lo para edição: 

    ![](app-icons-images/asset01.png "O arquivo assets. xcassets no Gerenciador de Soluções")
5. Selecione `AppIcon` na lista de ativos para `Icon Editor`exibir:

    ![](app-icons-images/asset02.png "O editor AppIcon")
6. Clique no tipo de ícone especificado e selecione um arquivo de imagem para o tipo/tamanho necessário ou arraste-o em uma imagem de uma pasta e solte-o no tamanho desejado.
7. Clique no botão **abrir** para incluir a imagem no projeto e defini-la no xcasset.
8. Repita para todas as imagens necessárias.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Clique duas vezes no arquivo **info. plist** na **Gerenciador de soluções**:

    ![](app-icons-images/icon01w.png "Selecione info. plist")
2. Clique na guia **ativos visuais** e clique no botão **usar catálogo de ativos** em **ícones do aplicativo**: 

    ![](app-icons-images/icon02w.png "Selecione a guia ativos visuais")
3. No **Gerenciador de soluções**, expanda a pasta **Catálogo de ativos** : 

    ![](app-icons-images/image009.png "Expandir a pasta do catálogo de ativos")
4. Clique duas vezes no arquivo de **mídia** para abri-lo no editor: 

    ![](app-icons-images/image010.png "Abra o arquivo de mídia no editor")
5. No **Gerenciador de propriedades** , o desenvolvedor pode selecionar os diferentes tipos e tamanhos de ícones necessários.
6. Clique no tipo de ícone fornecido e selecione um arquivo de imagem para o tipo/tamanho necessário.
7. Clique no botão **abrir** para incluir a imagem no projeto e defini-la no xcasset.
8. Repita para todas as imagens necessárias.

-----

Esse é o método preferencial de incluir e gerenciar ativos de imagem que serão usados para fornecer ícones de aplicativo, de destaque e de configurações para um aplicativo.

### <a name="migrating-from-infoplist-to-asset-catalogs"></a>Migrando do info. plist para catálogos de ativos

Para um aplicativo Xamarin. Ios existente usando o `Info.plist` arquivo para gerenciar seus ícones, é altamente recomendável que o desenvolvedor o alterne para usar o ativo de `AppIcons` imagem dentro do `Assets.xcassets`.

Faça o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Clique `Info.plist` duas vezes no arquivo no **Gerenciador de soluções** para abri-lo para edição.
2. Role para baixo até a seção **ícones de aplicativo** .
3. Na lista suspensa **origem** , selecione **migrar para catálogos de ativos**: 

    ![](app-icons-images/migrate02.png "Selecionar migrar para catálogos de ativos")
4. Todos os ícones existentes definidos no `Info.plist` arquivo serão migrados para um `AppIcons` conjunto de imagens adicionado a `Assets.xcassets`: 

     ![](app-icons-images/migrate03.png "A imagem AppIcons definida no assets. xcassets")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Clique `Info.plist` duas vezes no arquivo no **Gerenciador de soluções** para abri-lo para edição.
2. Clique na seção ícones do iPhone: 

    ![](app-icons-images/image007.png "Editor de ícones do RHE iPhone")
3. Role para baixo até a seção **ícones** .
4. Na lista suspensa **Catálogo de ativos** , selecione **usar catálogos de ativos**.
5. Todos os ícones existentes definidos no `Info.plist` arquivo serão migrados para um `Images` conjunto adicionado ao `Assets.xcassets`.
6. Salve as alterações no arquivo `Info.plist`.

-----

<a name="itunes" />

## <a name="itunes-artwork"></a>Arte do iTunes

Se você estiver usando o método ad hoc de entregar o aplicativo (para usuários corporativos ou para testes beta em dispositivos reais), o desenvolvedor também precisará incluir uma 512x512 e uma imagem 1024x1024 que serão usadas para representar o aplicativo no iTunes.

Para especificar a Arte do iTunes, faça o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Clique `Info.plist` duas vezes no arquivo no **Gerenciador de soluções** para abri-lo para edição.
2. Role até a seção **trabalho artístico do iTunes** do editor: 

    ![](app-icons-images/itunes01.png "Role até a seção trabalho artístico do iTunes do editor")
3. Para qualquer imagem ausente, clique na miniatura no editor, selecione o arquivo de imagem para a arte do iTunes desejada na caixa de diálogo abrir arquivo e clique no botão **OK** .
4. Repita essa etapa até que todas as imagens necessárias tenham sido especificadas para o aplicativo.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Clique `Info.plist` duas vezes no arquivo no **Gerenciador de soluções** para abri-lo para edição.

2. Clique na guia **ativos visuais** e expanda a **arte do iTunes**: 

    ![](app-icons-images/itunes01w.png "Editando a arte do iTunes no Visual Studio")
3. Para qualquer imagem ausente, clique na miniatura no editor, selecione o arquivo de imagem para a arte do iTunes desejada na caixa de diálogo abrir arquivo e clique no botão **abrir** .
4. Repita essa etapa até que todas as imagens necessárias tenham sido especificadas para o aplicativo.

-----

## <a name="related-links"></a>Links relacionados

- [Trabalhando com imagens (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Diretrizes personalizadas de criação de imagem e ícone](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html))
