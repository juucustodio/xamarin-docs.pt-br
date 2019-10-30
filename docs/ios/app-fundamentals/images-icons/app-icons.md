---
title: Ícones de aplicativo no Xamarin. iOS
description: 'Este documento descreve como trabalhar com vários ícones de aplicativos no Xamarin. iOS: o próprio ícone do aplicativo, ícones de destaque, os ícones de configurações e a arte do iTunes.'
ms.prod: xamarin
ms.assetid: B7791574-4A0F-4CB6-8C18-36D40B5C91EB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/22/2017
ms.openlocfilehash: 885f5321c10bcbc5389daf7dd7a97d1f9d572499
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73010373"
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

    [![](app-icons-images/000.png "Application Icon")](app-icons-images/000-full.png#lightbox)
- **Ícone de destaque** – sempre que o usuário inserir o nome de um aplicativo em uma pesquisa de destaque, esse ícone será exibido. Exemplo: 

    [![](app-icons-images/000a.png "Spotlight Icon")](app-icons-images/000a-full.png#lightbox)
- **Ícone de configurações** – se o usuário inserir o aplicativo de **configurações** em seu dispositivo IOS, esse ícone será exibido no final da lista de **configurações** do aplicativo. Exemplo: 

    [![](app-icons-images/000b.png "Settings Icon")](app-icons-images/000b-full.png#lightbox)

Os tamanhos e resoluções de ativos de imagem a seguir serão necessários para dar suporte a todos os tipos de ícone exigidos por um aplicativo Xamarin. iOS destinado ao iOS 5 até o iOS 9 (ou superior):

### <a name="iphone-icon-sizes"></a>Tamanhos de ícone do iPhone

- **iPhone: iOS 9 & 10 (iPhone 6 & 7 Plus)**

    ||vezes|
    |---|---|
    |Ícone do Aplicativo|180x180|
    |Luz|120 x 120|
    |Configurações|87x87|

- **iPhone: iOS 7 & 8**

    ||1x|2|
    |---|---|---|
    |Ícone do Aplicativo|60 x 60<sup>1</sup>|120 x 120|
    |Luz|40x40<sup>2</sup>|80x80|
    |Configurações|-|-|

- **iPhone: iOS 5 & 6**

    ||1x|2|
    |---|---|---|
    |Ícone do Aplicativo|57 x 57|114x114|
    |Luz|29 x 29|58x58|
    |Configurações|29X29<sup>3, 4</sup>|58x58<sup>3, 4</sup>|

### <a name="ipad-icon-sizes"></a>Tamanhos de ícone do iPad

- **iPad: iOS 9 & 10**

    ||2x (iPad pro)|
    |---|---|
    |Ícone do Aplicativo|167x167<sup>6</sup>|
    |Luz|120 x 120<sup>6</sup>|
    |Configurações|58x58<sup>5</sup>|

- **iPad: iOS 7 & 8**

    ||1x|2|
    |---|---|---|
    |Ícone do Aplicativo|76 x 76|152 x 152|
    |Luz|40 x 40|80x80|
    |Configurações|-|-|

- **iPad: iOS 5 & 6**

    ||1x|2|
    |---|---|---|
    |Ícone do Aplicativo|72 x 72|144x144|
    |Luz|50 x 50|100x100|
    |Configurações|29X29<sup>3, 5</sup>|58x58<sup>3, 5</sup>|

 1. O Visual Studio para Mac e o Xcode não dão mais suporte à definição da imagem 1x para iOS 7.
 2. Não há suporte para a definição de uma imagem 1x para iOS 7 ao usar catálogos de ativos.
 3. o iOS 7 & 8 usa os mesmos tamanhos de imagem que o iOS 5 & 6.
 4. Usa as mesmas imagens e tamanhos que o ícone de destaque.
 5. Usa os mesmos ícones de tamanho que o iPhone.
 6. Somente com suporte com conjuntos de imagens do catálogo de ativos.

 Para obter mais informações sobre ícones, consulte a documentação de [tamanhos de imagem e ícone](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/IconMatrix.html#//apple_ref/doc/uid/TP40006556-CH27-SW1) da Apple.

<a name="managing" />

## <a name="managing-icons-with-asset-catalogs"></a>Gerenciando ícones com catálogos de ativos

Para ícones, um conjunto de imagens `AppIcon` especial pode ser adicionado ao arquivo de `Assets.xcassets` no projeto do aplicativo. Todas as versões da imagem necessárias para dar suporte a todas as resoluções são incluídas no _xcasset_ e agrupadas juntas. Um editor especial no Visual Studio para Mac permite que o desenvolvedor inclua e configure essas imagens graficamente.

Para usar um catálogo de ativos, faça o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Clique duas vezes no arquivo `Info.plist` no **Gerenciador de soluções** para abri-lo para edição.
2. Role para baixo até a seção **ícones de aplicativo** .
3. Na lista suspensa **origem** , verifique se **appIcon** está selecionado: 

    ![](app-icons-images/migrate01.png "Ensure AppIcon is selected")
4. No **Gerenciador de soluções**, clique duas vezes no arquivo `Assets.xcassets` para abri-lo para edição: 

    ![](app-icons-images/asset01.png "The Assets.xcassets file in the Solution Explorer")
5. Selecione `AppIcon` na lista de ativos para exibir o `Icon Editor`:

    ![](app-icons-images/asset02.png "The AppIcon editor")
6. Clique no tipo de ícone especificado e selecione um arquivo de imagem para o tipo/tamanho necessário ou arraste-o em uma imagem de uma pasta e solte-o no tamanho desejado.
7. Clique no botão **abrir** para incluir a imagem no projeto e defini-la no xcasset.
8. Repita para todas as imagens necessárias.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Clique duas vezes no arquivo **info. plist** na **Gerenciador de soluções**:

    ![](app-icons-images/icon01w.png "Select Info.plist")
2. Clique na guia **ativos visuais** e clique no botão **usar catálogo de ativos** em **ícones do aplicativo**: 

    ![](app-icons-images/icon02w.png "Select the Visual Assets tab")
3. No **Gerenciador de soluções**, expanda a pasta **Catálogo de ativos** : 

    ![](app-icons-images/image009.png "Expand the Asset Catalog folder")
4. Clique duas vezes no arquivo de **mídia** para abri-lo no editor: 

    ![](app-icons-images/image010.png "Open the Media file in the editor")
5. No **Gerenciador de propriedades** , o desenvolvedor pode selecionar os diferentes tipos e tamanhos de ícones necessários.
6. Clique no tipo de ícone fornecido e selecione um arquivo de imagem para o tipo/tamanho necessário.
7. Clique no botão **abrir** para incluir a imagem no projeto e defini-la no xcasset.
8. Repita para todas as imagens necessárias.

-----

Esse é o método preferencial de incluir e gerenciar ativos de imagem que serão usados para fornecer ícones de aplicativo, de destaque e de configurações para um aplicativo.

### <a name="migrating-from-infoplist-to-asset-catalogs"></a>Migrando do info. plist para catálogos de ativos

Para um aplicativo Xamarin. iOS existente usando o arquivo de `Info.plist` para gerenciar seus ícones, é altamente recomendável que o desenvolvedor alterne para usar o ativo de imagem `AppIcons` dentro do `Assets.xcassets`.

Faça o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Clique duas vezes no arquivo `Info.plist` no **Gerenciador de soluções** para abri-lo para edição.
2. Role para baixo até a seção **ícones de aplicativo** .
3. Na lista suspensa **origem** , selecione **migrar para catálogos de ativos**: 

    ![](app-icons-images/migrate02.png "Select Migrate to Asset Catalogs")
4. Todos os ícones existentes definidos no arquivo de `Info.plist` serão migrados para um conjunto de imagens `AppIcons` adicionado ao `Assets.xcassets`: 

     ![](app-icons-images/migrate03.png "The AppIcons Image Set in the Assets.xcassets")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Clique duas vezes no arquivo `Info.plist` no **Gerenciador de soluções** para abri-lo para edição.
2. Clique na seção ícones do iPhone: 

    ![](app-icons-images/image007.png "Rhe iPhone Icons editor")
3. Role para baixo até a seção **ícones** .
4. Na lista suspensa **Catálogo de ativos** , selecione **usar catálogos de ativos**.
5. Todos os ícones existentes definidos no arquivo de `Info.plist` serão migrados para um conjunto de `Images` adicionado ao `Assets.xcassets`.
6. Salve as alterações no arquivo `Info.plist`.

-----

<a name="itunes" />

## <a name="itunes-artwork"></a>Arte do iTunes

Se você estiver usando o método ad hoc de entregar o aplicativo (para usuários corporativos ou para testes beta em dispositivos reais), o desenvolvedor também precisará incluir uma 512x512 e uma imagem 1024x1024 que serão usadas para representar o aplicativo no iTunes.

Para especificar a Arte do iTunes, faça o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Clique duas vezes no arquivo `Info.plist` no **Gerenciador de soluções** para abri-lo para edição.
2. Role até a seção **trabalho artístico do iTunes** do editor: 

    ![](app-icons-images/itunes01.png "Scroll to the iTunes Artwork section of the editor")
3. Para qualquer imagem ausente, clique na miniatura no editor, selecione o arquivo de imagem para a arte do iTunes desejada na caixa de diálogo abrir arquivo e clique no botão **OK** .
4. Repita essa etapa até que todas as imagens necessárias tenham sido especificadas para o aplicativo.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Clique duas vezes no arquivo `Info.plist` no **Gerenciador de soluções** para abri-lo para edição.

2. Clique na guia **ativos visuais** e expanda a **arte do iTunes**: 

    ![](app-icons-images/itunes01w.png "Editing iTunes Artwork in Visual Studio")
3. Para qualquer imagem ausente, clique na miniatura no editor, selecione o arquivo de imagem para a arte do iTunes desejada na caixa de diálogo abrir arquivo e clique no botão **abrir** .
4. Repita essa etapa até que todas as imagens necessárias tenham sido especificadas para o aplicativo.

-----

## <a name="related-links"></a>Links relacionados

- [Trabalhando com imagens (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Diretrizes personalizadas de criação de imagem e ícone](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html))
