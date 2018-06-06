---
title: Ícones de aplicativo em xamarin
description: 'Este documento descreve como trabalhar com vários ícones de aplicativo em xamarin: o ícone do aplicativo em si, os ícones de destaque, ícones de configurações e arte final do iTunes.'
ms.prod: xamarin
ms.assetid: B7791574-4A0F-4CB6-8C18-36D40B5C91EB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/22/2017
ms.openlocfilehash: 3c07f2573aa8ac6e28b2cd6bff56a773e6206aea
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783991"
---
# <a name="application-icons-in-xamarinios"></a>Ícones de aplicativo em xamarin

Os tópicos a seguir serão abordados em detalhes:

* [Aplicativo, destaque e ícones de configurações](#icon-types) -os diferentes tipos de ícones, necessárias para um aplicativo iOS.
* [Gerenciando ícones com catálogos ativo](#managing) - Gerenciando ícones de aplicativo usando o catálogo de ativos.
* [iTunes arte final](#itunes) -fornecendo iTunes arte final necessária para o método Ad Hoc de entregar seu aplicativo.

<a name="icon-types" />

## <a name="application-spotlight-and-settings-icons"></a>Aplicativo, destaque e ícones de configurações

Da mesma forma que um aplicativo xamarin pode usar ativos de imagem para controles de interface do usuário e como ícones de documento, os ativos de imagem podem ser usados para fornecer ícones de aplicativo. As seguintes capturas de tela de um iPad ilustra os três usos ícones no iOS:

- **Ícone do aplicativo** -todos os aplicativos iOS devem definir um ícone do aplicativo. Esse é o ícone que o usuário será tocar na tela inicial do iOS para iniciar o aplicativo. Além disso, esse ícone é usado pelo Centro de jogo, se aplicável. Exemplo: 

    [![](app-icons-images/000.png "Ícone do aplicativo")](app-icons-images/000-full.png#lightbox)
- **Ícone de destaque** - sempre que o usuário insere o nome de um aplicativo em uma pesquisa do Spotlight, esse ícone é exibido. Exemplo: 

    [![](app-icons-images/000a.png "Ícone de destaque")](app-icons-images/000a-full.png#lightbox)
- **Ícone de configurações** - se que o usuário insere o **configurações** aplicativo em seu dispositivo iOS, esse ícone será exibido no final o **configurações** lista para o aplicativo. Exemplo: 

    [![](app-icons-images/000b.png "Ícone de configurações")](app-icons-images/000b-full.png#lightbox)

Os seguintes tamanhos de ativos de imagem e resoluções serão necessários para dar suporte a todos os tipos de ícone exigidos por um aplicativo xamarin direcionando o iOS 5, por meio do iOS 9 (ou superior):

### <a name="iphone-icon-sizes"></a>Tamanhos de ícones do iPhone

- **iPhone: iOS 9 e 10 (iPhone 6 e 7 mais)**

    ||x 3|
    |---|---|
    |Ícone do Aplicativo|180x180|
    |Destaque|120x120|
    |Configurações|87x87|

- **iPhone: iOS 7 e 8**

    ||1 x|x 2|
    |---|---|---|
    |Ícone do Aplicativo|60x60<sup>1</sup>|120x120|
    |Destaque|40x40<sup>2</sup>|80x80|
    |Configurações|-|-|

- **iPhone: iOS 5 e 6**

    ||1 x|x 2|
    |---|---|---|
    |Ícone do Aplicativo|57 x 57|114x114|
    |Destaque|29 x 29|58x58|
    |Configurações|29x29<sup>3, 4</sup>|58x58<sup>3, 4</sup>|

### <a name="ipad-icon-sizes"></a>iPad tamanhos de ícones

- **iPad: iOS 9 & 10**

    ||2x (iPad Pro)|
    |---|---|
    |Ícone do Aplicativo|167x167<sup>6</sup>|
    |Destaque|120x120<sup>6</sup>|
    |Configurações|58x58<sup>5</sup>|

- **iPad: iOS 7 & 8**

    ||1 x|x 2|
    |---|---|---|
    |Ícone do Aplicativo|76 x 76|152x152|
    |Destaque|40 x 40|80x80|
    |Configurações|-|-|

- **iPad: iOS 5 & 6**

    ||1 x|x 2|
    |---|---|---|
    |Ícone do Aplicativo|72 x 72|144x144|
    |Destaque|50 x 50|100x100|
    |Configurações|29x29<sup>3, 5</sup>|58x58<sup>3, 5</sup>|

 1. Ambos os Visual Studio para Mac e Xcode não oferece suporte à definição 1 imagem x para iOS 7.
 2. Definindo uma imagem de 1 x para iOS 7, não há suporte ao usar o catálogo de ativos.
 3. iOS 7 e 8 usar os mesmos tamanhos de imagem como iOS 5 e 6.
 4. Usa as mesmas imagens e tamanhos como o ícone de destaque.
 5. Usa os ícones de tamanho mesmo como o iPhone.
 6. Suporte somente com conjuntos de imagem do catálogo de ativos.
 
 Para obter mais informações sobre ícones, consulte da Apple [tamanhos de imagem e ícone](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/IconMatrix.html#//apple_ref/doc/uid/TP40006556-CH27-SW1) documentação.

<a name="managing" />

## <a name="managing-icons-with-asset-catalogs"></a>Gerenciando ícones com catálogos de ativo

Para ícones, especial `AppIcons` o conjunto de imagens pode ser adicionado para o `Assets.xcassets` arquivo no projeto do aplicativo. Todos os versão da imagem necessária para dar suporte a todas as resoluções inclusos no _xcasset_ e agrupados. Um editor especial no Visual Studio para Mac permite que o desenvolvedor incluir e configurar essas imagens graficamente.

Para usar um catálogo de ativos, faça o seguinte:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Clique duas vezes o `Info.plist` arquivo o **Gerenciador de soluções** para abri-lo para edição.
2. Role para baixo até o **ícones de aplicativo** seção.
3. Do **fonte** suspensa lista, certifique-se de **AppIcon** é selecionado: 

    ![](app-icons-images/migrate01.png "Certifique-se de que AppIcons está selecionado")
4. Do **Solution Explorer**, clique duas vezes o `Assets.xcassets` arquivo para abri-lo para edição: 

    ![](app-icons-images/asset01.png "O arquivo Assets.xcassets no Gerenciador de soluções")
5. Selecione `AppIcons` da lista de ativos para exibir o `Icon Editor`: 

    ![](app-icons-images/asset02.png "O editor de AppIcons")
6. Um clique no ícone de tipo fornecido e selecione um arquivo de imagem para o tipo ou o tamanho necessário ou arraste em uma imagem de uma pasta e solte-o no tamanho desejado.
7. Clique o **abrir** para incluir a imagem no projeto e defini-la no xcasset.
8. Repita para todas as imagens necessárias.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Clique duas vezes o **Info. plist** arquivo o **Gerenciador de soluções**:

    ![](app-icons-images/icon01w.png "Selecione Info. plist")
2. Clique no **ativos visuais** guia e clique no **catálogo de ativos de uso** botão em **ícones de aplicativo**: 

    ![](app-icons-images/icon02w.png "Selecione a guia de ativos visuais")
4. Do **Solution Explorer**, expanda o **catálogo de ativos** pasta: 

    ![](app-icons-images/image009.png "Expanda a pasta do catálogo de ativos")
5. Clique duas vezes o **mídia** arquivo para abri-lo no editor: 

    ![](app-icons-images/image010.png "Abra o arquivo de mídia no editor")
6. Sob o **propriedades Explorer** o desenvolvedor pode selecionar os diferentes tipos e tamanhos de ícones necessários.
7. Clique no ícone de tipo fornecido e selecione um arquivo de imagem para o tipo ou o tamanho necessário.
8. Clique o **abrir** para incluir a imagem no projeto e defini-la no xcasset.
9. Repita para todas as imagens necessárias.

-----

Esse é o método preferencial de incluindo e gerenciamento de ativos de imagem que serão usados para fornecer ícones de aplicativo, destaque e configurações para um aplicativo.

### <a name="migrating-from-infoplist-to-asset-catalogs"></a>Migrando de info. plist em catálogos do ativo

Um aplicativo xamarin existente usando o `Info.plist` para gerenciar seu ícones de arquivos, é altamente recomendável que o desenvolvedor alternar para usar o `AppIcons` ativo de imagem dentro de `Assets.xcassets`.

Faça o seguinte:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Clique duas vezes o `Info.plist` arquivo o **Gerenciador de soluções** para abri-lo para edição.
2. Role para baixo até o **ícones de aplicativo** seção.
3. Do **fonte** lista suspensa, selecione **migrar para o ativo catálogos**: 

    ![](app-icons-images/migrate02.png "Selecione migra para os catálogos de ativo")
4. Ícones existentes definido no `Info.plist` arquivo será migrado para um `AppIcons` imagem definido adicionados ao `Assets.xcassets`: 

     ![](app-icons-images/migrate03.png "O conjunto de imagem AppIcons a Assets.xcassets")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Clique duas vezes o `Info.plist` arquivo o **Gerenciador de soluções** para abri-lo para edição.
2. Clique no iPhone seção ícones: 

    ![](app-icons-images/image007.png "Editor de ícones trocador iPhone")
3. Role para baixo até o **ícones** seção.
4. Do **catálogo de ativos** lista suspensa, selecione **uso ativo catálogos**.
5. Nenhum ícone existente definido no `Info.plist` arquivo será migrado para um `Images` adicionado ao conjunto de `Assets.xcassets`.
6. Salve as alterações no arquivo `Info.plist`.

-----

<a name="itunes" />

## <a name="itunes-artwork"></a>Arte do iTunes

Se usar o método Ad Hoc de entregar o aplicativo (para usuários corporativos ou de teste beta dispositivos reais), o desenvolvedor precisa também incluem um 512 x 512 e uma imagem de 1024 x 1024 que será usada para representar o aplicativo na iTunes.

Para especificar a Arte do iTunes, faça o seguinte:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Clique duas vezes o `Info.plist` arquivo o **Gerenciador de soluções** para abri-lo para edição.
2. Role até a **iTunes arte final** seção do editor: 

    ![](app-icons-images/itunes01.png "Role até o seção de arte final do editor do iTunes")
3. Para qualquer imagem ausente, clique na miniatura no editor, selecione o arquivo de imagem para a arte do iTunes desejado na caixa de diálogo Abrir arquivo e clique no **Okey** botão.
4. Repita essa etapa até que todos os necessário imagens foram especificadas para o aplicativo.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Clique duas vezes o `Info.plist` arquivo o **Gerenciador de soluções** para abri-lo para edição.

2. Clique no **ativos visuais** guia e expanda o **iTunes arte final**: 

    ![](app-icons-images/itunes01w.png "Edição iTunes arte final no Visual Studio")
4. Para qualquer imagem ausente, clique na miniatura no editor, selecione o arquivo de imagem para a arte do iTunes desejado na caixa de diálogo Abrir arquivo e clique no **abrir** botão.
5. Repita essa etapa até que todos os necessário imagens foram especificadas para o aplicativo.

-----

## <a name="related-links"></a>Links relacionados

- [Trabalhando com imagens (exemplo)](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Ícone personalizado e diretrizes de criação de imagem](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html))
