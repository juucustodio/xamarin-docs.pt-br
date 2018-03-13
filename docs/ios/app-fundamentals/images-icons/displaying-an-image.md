---
title: Exibir uma imagem
description: "Este artigo aborda incluindo um ativo de imagem em um aplicativo xamarin e exibir essa imagem, usando o código c# ou atribuindo a ele a um controle no Designer de iOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 60288B12-49E3-4E87-8690-D04A5EC7A664
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 1ca537ef47f48886483e99bb33b0d524d16593f8
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="displaying-an-image"></a>Exibir uma imagem

_Este artigo aborda incluindo um ativo de imagem em um aplicativo xamarin e exibir essa imagem, usando o código c# ou atribuindo a ele a um controle no Designer de iOS._

Este artigo aborda os seguintes tópicos em detalhes:

- [Adicionando e organizar imagens em um App Xamarin.iOS](#adding-assets) - abrange ativos de imagem e como elas podem ser incluídas, organizados e gerenciado dentro de um projeto xamarin.
- [Adicionando imagens em catálogos do Asset](#asset-catalogs) -gerenciamento de imagens com catálogos de ativo.
    - [Usando imagens de vetor em catálogos do Asset](#Using-Vector-Images-in-Asset-Catalogs) -todos os tamanhos de imagem, fornecendo um único vetor.
- [Trabalhando com imagens de modelo](#Working-with-Template-Images) -definindo um ativo de imagem para ser uma imagem de modelo, o desenvolvedor pode facilmente colori-lo para que as alterações de tema de interface do usuário do aplicativo, definindo a imagem `Tint` propriedade.
- [Usando imagens com controles](#controls) - abrange usando ativos de imagem incluídos em um projeto de xamarin com controles de interface do usuário, como `UIButton` e `UIImageView` e como trabalhar com imagens em c# usando o `UIImage` do objeto [FromBundle](#frombundle) método.
- [Exibindo uma imagem em um Storyboards](#Displaying-an-Image-in-a-Storyboards) -fornece um exemplo de exibição de uma imagem usando um Storyboard.
- [Exibir uma imagem no código](#Displaying-an-Image-in-Code) -fornece um exemplo de exibição de uma imagem usando o código c#.

<a name="adding-assets" />

## <a name="adding-and-organizing-images-in-a-xamarinios-app"></a>Adicionando e organizar imagens em um aplicativo xamarin

Ao adicionar uma imagem para uso em um aplicativo xamarin, o desenvolvedor usará um _catálogo de ativos_ para oferecer suporte a cada dispositivo iOS e a resolução necessária por um aplicativo.

Adicionado no iOS 7, **conjuntos de imagem de catálogos do Asset** contêm todas as versões ou representações de uma imagem que são necessárias para dar suporte a vários dispositivos e dimensionar fatores para um aplicativo. Em vez de usar o nome de arquivo de ativos de imagem (consulte [imagens independente de resolução e nomenclatura de imagem](~/ios/app-fundamentals/images-icons/displaying-an-image.md)), **conjuntos de imagem** usar um arquivo Json para especificar qual imagem pertence a quais dispositivos e/ou a resolução . Essa é a melhor maneira de gerenciar e dar suporte a imagens no iOS (do iOS 9 ou superior).

<a name="asset-catalogs" />

## <a name="adding-images-to-an-asset-catalog-image-set"></a>Adicionando imagens a uma imagem do catálogo de ativos definida

Como mencionado anteriormente, um **conjuntos de imagem de catálogos do Asset** contêm todas as versões ou representações de uma imagem que são necessárias para dar suporte a vários dispositivos e dimensionar fatores para um aplicativo. Em vez de usar o nome de arquivo de ativos de imagem, **conjuntos de imagem** usar um arquivo Json para especificar qual imagem pertence a quais dispositivos e/ou a resolução.

Para criar um novo conjunto de imagem e adicionar imagens a ela, faça o seguinte:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. No **Solution Explorer**, clique duas vezes o `Assets.xcassets` arquivo para abri-lo para edição:

    ![](displaying-an-image-images/imageset01.png "Assets.xcassets no Gerenciador de soluções")
2. Clique com botão direito no **lista de ativos** e selecione **novo conjunto de imagem**:

    ![](displaying-an-image-images/imageset02.png "Adicionando um novo conjunto de imagem")
3. Selecione o novo conjunto de imagem e o editor será exibido:

    ![](displaying-an-image-images/imageset03.png "O editor de imagem definida")
4. A partir daqui, arraste imagens para cada um dos diferentes dispositivos e e resoluções necessárias. (Observação: que essas resoluções para as resoluções especificadas no [tamanhos de imagem e nomes de arquivo](~/ios/app-fundamentals/images-icons/displaying-an-image.md) documento.)
5. Clique duas vezes o conjunto de imagem nova **nome** no **lista de ativos** para editá-lo: ![ ] (displaying-an-image-images/imageset04.png "editando o nome do novo conjunto de imagem")

Ao usar um **imagem definida** no Designer de iOS, basta selecionar o nome do conjunto na lista suspensa no Editor de propriedades:

![](displaying-an-image-images/imageset06.png "Selecione o nome do conjunto de uma imagem na lista suspensa")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Abra o catálogo de ativos do **Solution Explorer**e no canto superior esquerdo, clique no **mais** botão:

    ![](displaying-an-image-images/asset5.png "Clique no sinal de adição botão")
2. Selecione **Adicionar conjunto de imagem** e o editor de imagem definida será exibido para o novo conjunto de imagem. A partir daqui, arraste imagens para cada um dos diferentes dispositivos e e resoluções necessárias. (Observação: que essas resoluções para as resoluções especificadas no [tamanhos de imagem e nomes de arquivo](~/ios/app-fundamentals/images-icons/displaying-an-image.md) documento):

    ![](displaying-an-image-images/asset7.png "O editor de conjunto de imagens")

### <a name="renaming-an-image-set"></a>Renomeando um conjunto de imagem

Para renomear um conjunto de imagem, faça o seguinte:

1. No **Solution Explorer**, clique duas vezes o **catálogo de ativos** arquivo para abri-lo para edição:

    ![](displaying-an-image-images/rename01.png "O catálogo de ativos no Gerenciador de soluções")
2. Selecione o **imagem definida** renomear:

    ![](displaying-an-image-images/rename02.png "Selecione o conjunto de imagem para renomear")
3. No **propriedades Explorer**, role para baixo e selecione **nome**(sob o **Misc** seção):

    ![](displaying-an-image-images/rename03.png "Selecione o nome na seção Misc")
4. Insira um novo **nome** para o **imagem definida** e salve as alterações.

-----

Ao usar um **imagem definida** no código, fazer referência a ela por nome, chamando o `FromBundle` método o `UIImage` classe. Por exemplo:

```csharp
MonkeyImage.Image = UIImage.FromBundle ("PurpleMonkey");
```

> [!IMPORTANT]
> **Observação**: se as imagens atribuídas a um conjunto de imagem não estão aparecendo corretamente, certifique-se de que o nome de arquivo correto está sendo usado com o `FromBundle` método (o **imagem definida** e não o pai **catálogo de ativos**  nome). Para imagens PNG, o `.png` extensão pode ser omitida. Para outros formatos de imagem, a extensão é necessária (por exemplo. `PurpleMonkey.jpg`).

<a name="Using-Vector-Images-in-Asset-Catalogs" />

### <a name="using-vector-images-in-asset-catalogs"></a>Usando imagens de vetor em catálogos do ativo

A partir do iOS 8, especiais **vetor** classe foi adicionada para **conjuntos de imagem** que permite que o desenvolvedor incluir um **PDF** formatado de imagem de vetor no cassetes em vez disso, incluindo arquivos de bitmap individuais nas resoluções diferentes. Usando esse método, forneça um arquivo único vetor para o `@1x` resolução (formatada como um arquivo PDF de vetor) e o `@2x` e `@3x` versões do arquivo serão geradas em tempo de compilação e incluídas no pacote do aplicativo.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![](displaying-an-image-images/imageset05.png "Imagens de vetor no editor de catálogos de ativo")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](displaying-an-image-images/asset8.png "Imagens de vetor no editor de catálogos de ativo")

-----

Por exemplo, se o desenvolvedor inclui um `MonkeyIcon.pdf` arquivo como o vetor de um catálogo de ativos com uma resolução de 150px x 150px, o bitmap seguinte ativos seriam incluídos no pacote de aplicativo final quando ele foi compilado:

- `MonkeyIcon@1x.png` -150px x 150px resolução.
- `MonkeyIcon@2x.png` -300px x 300px resolução.
- `MonkeyIcon@3x.png` -450px x 450px resolução.

O seguinte deve ser levado em consideração ao usar imagens de vetor PDF em catálogos do Asset:

- Isso não é completa de vetor de suporte, pois o PDF será rasterizado para um bitmap em tempo de compilação e os bitmaps fornecidos no final do aplicativo.
- O tamanho da imagem não pode ser ajustado quando foi definido no catálogo de ativos. Se o desenvolvedor tentar redimensionar a imagem (ou no código ou usando Classes de tamanho e Layout automático) a imagem é distorcida assim como qualquer outro bitmap.
- Catálogos do Asset somente são compatíveis com iOS 7 e maior, se um aplicativo precisar oferecer suporte a iOS 6 ou inferior, não será possível usar os catálogos de ativo.

<a name="Working-with-Template-Images" />

## <a name="working-with-template-images"></a>Trabalhando com imagens de modelo

Com base no design de um aplicativo iOS, pode haver momentos em que o desenvolvedor precisa personalizar uma imagem dentro da Interface do usuário para corresponder a uma alteração no esquema de cores (por exemplo, com base nas preferências do usuário) ou um ícone.

Para obter facilmente esse efeito, alterne o _modo de renderização_ do ativo de imagem para **imagem de modelo**:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](displaying-an-image-images/templateimage01.png "O modo de renderização definido como imagem de modelo")](displaying-an-image-images/templateimage01.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](displaying-an-image-images/templateimage01vs.png "O modo de renderização definido como modelo")](displaying-an-image-images/templateimage01vs.png#lightbox)

-----

Do iOS Designer, atribuir o ativo de imagem a um controle de interface do usuário, então, defina o **tonalidade** para colorir a imagem:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](displaying-an-image-images/templateimage03.png "Definir o tom para colorir a imagem")](displaying-an-image-images/templateimage03.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](displaying-an-image-images/templateimage03vs.png "Definir o tom para colorir a imagem")](displaying-an-image-images/templateimage03vs.png#lightbox)

-----

Opcionalmente, o ativo de imagem e tonalidade podem ser definidas diretamente no código:

```csharp
MyIcon.Image = UIImage.FromBundle ("MessageIcon");
MyIcon.TintColor = UIColor.Red;
```

Para usar uma imagem de modelo completamente do código, faça o seguinte:

```csharp
if (MyIcon.Image != null) {
    var mutableImage = MyIcon.Image.ImageWithRenderingMode (UIImageRenderingMode.AlwaysTemplate);
    MyIcon.Image = mutableImage;
    MyIcon.TintColor = UIColor.Red;
}
```

Porque o `RenderMode` propriedade de um `UIImage` é somente leitura, use o `ImageWithRenderingMode` método para criar uma nova instância da imagem com a configuração de modo de renderização desejada.

Há três possivelmente configurações para `UIImage.RenderMode` por meio de `UIImageRenderingMode` enum:

* `AlwaysOriginal` -Força a imagem a ser renderizada como o arquivo de imagem de origem original sem quaisquer alterações.
* `AlwaysTemplate` -Força a imagem a ser renderizada como uma imagem de modelo por colorir os pixels com especificado `Tint` cor.
* `Automatic` -O processa a imagem como um modelo ou Original com base no ambiente que é usado em. Por exemplo, se a imagem é usada em uma `UIToolBar`, `UINavigationBar`, `UITabBar` ou `UISegmentControl` ele será tratado como um modelo.

<a name="Adding-new-Assets-Collections" />

## <a name="adding-new-assets-collections"></a>Adicionando novas coleções de ativos

Ao trabalhar com imagens em catálogos de ativos pode haver momentos em que uma nova coleção será necessária, em vez de adicionar todas as imagens do aplicativo para o `Assets.xcassets` coleção. Por exemplo, durante a criação de recursos sob demanda.

Para adicionar um novo catálogo de ativos para o projeto:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Com o botão direito no **nome do projeto** no **Solution Explorer** e selecione **adicionar** > **novo arquivo...**
2. Selecione **iOS** > **catálogo de ativos**, insira um **nome** para a coleção e clique no **novo** botão:

    ![](displaying-an-image-images/asset01.png "Criar um novo catálogo de ativos")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. No Gerenciador de soluções, clique duas vezes em **catálogos ativo** pasta e selecione **Adicionar > novo catálogo de ativos**.
2. Dê um nome e clique em **adicionar**:

    ![](displaying-an-image-images/asset1.png "Criar um novo catálogo de ativos")

-----


Aqui, a coleção pode ser editada da mesma maneira como o padrão `Assets.xcassets` coleção automaticamente incluída no projeto.

<a name="controls" />

## <a name="using-images-with-controls"></a>Usando imagens com controles

Além de usar imagens para dar suporte a um aplicativo, o iOS também usa imagens com tipos de controle de aplicativo, como barras de guia, barras de ferramentas, barras de navegação, tabelas e botões. Uma maneira simples para exibir uma imagem em um controle é atribuir uma `UIImage` instância para o controle `Image` propriedade.

<a name="frombundle" />

### <a name="frombundle"></a>FromBundle

O `FromBundle` chamada de método é uma chamada síncrona (bloqueio) que tem um número de recursos de carregamento e o gerenciamento de imagem internos, como o cache de suporte e a manipulação automática de arquivos de imagem para várias resoluções.  

O exemplo a seguir mostra como definir a imagem de um `UITabBarItem` em um `UITabBar`:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

Supondo que `MyImage` é o nome de um ativo de imagem adicionada a um catálogo de ativos acima. Ao trabalhar imagens do catálogo de ativos, basta especificar o nome do conjunto de imagem no `FromBundle` método **PNG** formatado imagens:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

Para qualquer outro formato de imagem, inclua a extensão com o nome. Por exemplo:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage.jpg");
```

Para obter mais informações sobre ícones e imagens, consulte a documentação da Apple em [ícone personalizado e diretrizes de criação de imagem](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html).

<a name="Displaying-an-Image-in-a-Storyboards" />

## <a name="displaying-an-image-in-a-storyboards"></a>Exibindo uma imagem em um Storyboards

Quando uma imagem foi adicionada a um projeto de xamarin usando os catálogos de um ativo, ele poderá ser facilmente exibidos em um Storyboard usando um `UIImageView` no Designer de iOS. Por exemplo, se o ativo de imagem a seguir foram adicionado:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![](displaying-an-image-images/display01.png "Foi adicionado um ativo de imagem de exemplo")

Faça o seguinte para exibi-lo em um Storyboard:

1. Clique duas vezes o `Main.storyboard` arquivo o **Gerenciador de soluções** para abri-lo para edição no Designer de iOS.
2. Selecione um **exibição da imagem** do **caixa de ferramentas**:

     ![](displaying-an-image-images/display02.png "Selecione um modo de exibição de imagem da caixa de ferramentas")
3. Arraste a imagem de exibição para a superfície de design e a posição e dimensioná-lo conforme necessário:

    ![](displaying-an-image-images/display03.png "Uma nova exibição da imagem na superfície de Design")
4. No **Widget** seção o **propriedade Explorer** selecione o **imagem** ativo a ser exibido:

    ![](displaying-an-image-images/display04.png "Selecione o ativo de imagem desejado a ser exibido")
5. No **exibição** seção, use o **modo** para controlar como a imagem será redimensionado quando o **imagem** é redimensionado.
6. Com o **imagem** selecionado, clique nele novamente para adicionar **restrições**:

    ![](displaying-an-image-images/display05.png "Adicionar restrições")
7. Arraste a alça de "T" formatado em cada borda do **imagem** ao lado da tela para a imagem para os lados "fixar" correspondente. Dessa forma, o **imagem** irá diminuir e aumentar conforme a tela é redimensionada.
8. Salve as alterações para o Storyboard.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](displaying-an-image-images/display01vs.png "Foi adicionado um ativo de imagem de exemplo")

Faça o seguinte para exibi-lo em um Storyboard:

1. Clique duas vezes o `Main.storyboard` arquivo o **Gerenciador de soluções** para abri-lo para edição no Designer de iOS.
2. Selecione um **exibição da imagem** do **caixa de ferramentas**:

     ![](displaying-an-image-images/display02vs.png "Selecione um modo de exibição de imagem da caixa de ferramentas")
3. Arraste a imagem de exibição para a superfície de design e a posição e dimensioná-lo conforme necessário:

    ![](displaying-an-image-images/display03vs.png "Uma nova exibição da imagem na superfície de Design")
4. No **Widget** seção o **propriedade Explorer** selecione o **imagem** ativo a ser exibido:

    ![](displaying-an-image-images/display04vs.png "Selecione o ativo de imagem desejado a ser exibido")
5. No **exibição** seção, use o **modo** para controlar como a imagem será redimensionado quando o **imagem** é redimensionado.
6. Com o **imagem** selecionado, clique nele novamente para adicionar **restrições**:

    ![](displaying-an-image-images/display05vs.png "Adicionar restrições")
7. Arraste a alça de "T" formatado em cada borda do **imagem** ao lado da tela para a imagem para os lados "fixar" correspondente. Dessa forma, o **imagem** irá diminuir e aumentar conforme a tela é redimensionada.
8. Salve as alterações para o Storyboard.

-----


<a name="Displaying-an-Image-in-Code" />

## <a name="displaying-an-image-in-code"></a>Exibir uma imagem no código

Como exibir uma imagem em um Storyboard, após a adição de uma imagem para um projeto de xamarin usando os catálogos de um ativo, ele poderá ser facilmente exibido usando o código c#.

Veja o exemplo a seguir:

```csharp
// Create an image view that will fill the
// parent image view and set the image from
// an Image Asset

var imageView = new UIImageView (View.Frame);
imageView.Image = UIImage.FromBundle ("Kemah");

// Add the Image View to the parent view
View.AddSubview (imageView);
```

Esse código cria um novo `UIImageView` e concede a ele um tamanho inicial e posição. Em seguida, ele carrega a imagem de um ativo de imagem adicionada ao projeto e adiciona o `UIImageView` pai `UIView` para exibi-lo.

## <a name="related-links"></a>Links relacionados

- [Trabalhando com imagens (exemplo)](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Ícone personalizado e diretrizes de criação de imagem](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
