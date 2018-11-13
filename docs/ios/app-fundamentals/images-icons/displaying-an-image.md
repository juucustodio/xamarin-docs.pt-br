---
title: Exibindo uma imagem no xamarin. IOS
description: Este artigo aborda incluindo um ativo de imagem em um aplicativo xamarin. IOS e exibir essa imagem, usando o código c# ou atribuindo-a um controle no Designer do iOS.
ms.prod: xamarin
ms.assetid: 60288B12-49E3-4E87-8690-D04A5EC7A664
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/24/2018
ms.openlocfilehash: 325f4e99e70f88ccf642253720f4229142a169ec
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526554"
---
# <a name="displaying-an-image-in-xamarinios"></a>Exibindo uma imagem no xamarin. IOS

_Este artigo aborda incluindo um ativo de imagem em um aplicativo xamarin. IOS e exibir essa imagem, usando o código c# ou atribuindo-a um controle no Designer do iOS._

## <a name="adding-and-organizing-images-in-a-xamarinios-app"></a>Adicionando e organização de imagens em um aplicativo xamarin. IOS

Ao adicionar uma imagem para uso em um aplicativo xamarin. IOS, o desenvolvedor usará um _catálogo de ativos_ para dar suporte a todos os dispositivos iOS e a resolução necessária por um aplicativo.

Adicionado no iOS 7, **conjuntos de imagem de catálogos de ativos** contêm todas as versões ou representações de uma imagem que são necessárias para dar suporte a vários dispositivos e fatores para um aplicativo de dimensionamento. Em vez de usar o nome de arquivo de ativos de imagem **conjuntos de imagem** usar um arquivo Json para especificar qual imagem pertence a qual dispositivo e/ou resolução. Essa é a maneira preferencial para gerenciar e dar suporte a imagens no iOS (a partir do iOS 9 ou posterior).

## <a name="adding-images-to-an-asset-catalog-image-set"></a>Adicionando imagens a uma imagem do catálogo de ativos definida

Conforme mencionado acima, uma **conjuntos de imagem de catálogos de ativos** contêm todas as versões ou representações de uma imagem que são necessárias para dar suporte a vários dispositivos e fatores para um aplicativo de dimensionamento. Em vez de usar o nome de arquivo de ativos de imagem **conjuntos de imagem** usar um arquivo Json para especificar qual imagem pertence a qual dispositivo e/ou resolução.

Para criar um novo conjunto de imagem e adicionar imagens a ele, faça o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. No **Gerenciador de soluções**, clique duas vezes o `Assets.xcassets` arquivo para abri-lo para edição:

    ![](displaying-an-image-images/imageset01.png "O xcassets no Gerenciador de soluções")
2. Clique com botão direito no **lista de ativos** e selecione **novo conjunto de imagem**:

    ![](displaying-an-image-images/imageset02.png "Adicionar um novo conjunto de imagem")
3. Selecione o novo conjunto de imagens e o editor será exibido:

    ![](displaying-an-image-images/imageset03.png "O editor de conjunto de imagem")
4. A partir daqui, arraste imagens para cada um dos diferentes dispositivos e resoluções necessários. 
5. Clique duas vezes em do novo conjunto de imagens **nome** na **lista Assets** editá-lo: ![](displaying-an-image-images/imageset04.png "editando o nome do novo conjunto de imagens")

Ao usar um **imagem conjunto** no Designer do iOS, basta selecionar o nome do conjunto na lista suspensa no Editor de propriedades:

![](displaying-an-image-images/imageset06.png "Selecione o nome do conjunto de uma imagem na lista suspensa")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Abra o catálogo de ativos do **Gerenciador de soluções**e, no canto superior esquerdo, clique o **Plus** botão:

    ![](displaying-an-image-images/asset5.png "Clique no sinal de adição botão")

2. Selecione **adicionar imagem conjunto** e o editor de conjunto de imagem será exibido para o novo conjunto de imagem. A partir daqui, arraste imagens para cada um dos diferentes dispositivos e resoluções necessários. 

    ![](displaying-an-image-images/asset7.png "O editor de conjunto de imagens")

### <a name="renaming-an-image-set"></a>Renomeando um conjunto de imagens

Para renomear um conjunto de imagem, faça o seguinte:

1. No **Gerenciador de soluções**, clique duas vezes o **catálogo de ativos** arquivo para abri-lo para edição:

    ![](displaying-an-image-images/rename01.png "O catálogo de ativos no Gerenciador de soluções")
2. Selecione o **imagem conjunto** renomear:

    ![](displaying-an-image-images/rename02.png "Selecione o conjunto de imagem para renomear")
3. No **Gerenciador de propriedades**, role para baixo e selecione **nome**(sob o **Misc** seção):

    ![](displaying-an-image-images/rename03.png "Selecione o nome na seção Misc")
4. Insira um novo **nome** para o **imagem conjunto** e salve as alterações.

-----

Ao usar um **imagem definida** no código, referenciá-lo pelo nome, por meio da chamada a `FromBundle` método da `UIImage` classe. Por exemplo:

```csharp
MonkeyImage.Image = UIImage.FromBundle ("PurpleMonkey");
```

> [!IMPORTANT]
> Se as imagens atribuídas a um conjunto de imagem não estão aparecendo corretamente, certifique-se de que o nome de arquivo correto está sendo usado com o `FromBundle` método (o **imagem conjunto** e não o pai **catálogo de ativos** nome). Para imagens PNG, o `.png` extensão pode ser omitida. Para outros formatos de imagem, a extensão é necessária (por exemplo. `PurpleMonkey.jpg`).

### <a name="using-vector-images-in-asset-catalogs"></a>Usando imagens de vetor em catálogos de ativos

A partir do iOS 8, especial **vetor** foi adicionado à classe **conjuntos de imagem** que permite ao desenvolvedor incluir uma **PDF** formatado imagem vetorial no cassete em vez disso, incluindo arquivos de bitmap individuais em diferentes resoluções. Usando esse método, forneça um arquivo único vetor para o `@1x` resolução (formatada como um arquivo PDF de vetor) e o `@2x` e `@3x` versões do arquivo serão geradas em tempo de compilação e incluídas no pacote do aplicativo.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![](displaying-an-image-images/imageset05.png "Imagens vetoriais no editor de catálogos de ativos")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](displaying-an-image-images/asset8.png "Imagens vetoriais no editor de catálogos de ativos")

-----

Por exemplo, se o desenvolvedor inclui um `MonkeyIcon.pdf` arquivo como o vetor de um catálogo de ativos com uma resolução de 150px x 150px, o bitmap seguinte ativos seriam incluídos no pacote final do aplicativo quando ele foi compilado:

- `MonkeyIcon@1x.png` -150px x 150px resolução.
- `MonkeyIcon@2x.png` -300px 300px resolução.
- `MonkeyIcon@3x.png` -450px x 450px resolução.

A seguir deve ser levados em consideração ao usar imagens de vetor PDF em catálogos de ativos:

- Isso não é completa do vetor de suporte, pois o PDF será rasterizado em um bitmap em tempo de compilação e os bitmaps fornecidos no aplicativo final.
- O tamanho da imagem não pode ser ajustado após ele ter sido definido no catálogo de ativos. Se o desenvolvedor tenta redimensionar a imagem (ou no código ou usando o Layout automático e Classes de tamanho) a imagem será distorcida assim como qualquer outro bitmap.
- Catálogos de ativos somente são compatíveis com iOS 7 e posterior, se precisar de um aplicativo dar suporte ao iOS 6 ou inferior, não é possível usar os catálogos de ativos.

## <a name="working-with-template-images"></a>Trabalhando com imagens de modelo

Com base no design de um aplicativo iOS, pode haver vezes quando o desenvolvedor precisa para personalizar um ícone ou uma imagem dentro de Interface do usuário para corresponder a uma alteração no esquema de cores (por exemplo, com base nas preferências do usuário).

Para obter facilmente esse efeito, alterne o _modo de renderização_ do ativo de imagem para **imagem de modelo**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](displaying-an-image-images/templateimage01.png "O modo de renderização definido como imagem de modelo")](displaying-an-image-images/templateimage01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](displaying-an-image-images/templateimage01vs.png "O modo de renderização definido como modelo")](displaying-an-image-images/templateimage01vs.png#lightbox)

-----

Do Designer do iOS, atribua o ativo de imagem a um controle de interface do usuário, então, configurar o **tonalidade** para colorir a imagem:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](displaying-an-image-images/templateimage03.png "Defina a tonalidade para colorir a imagem")](displaying-an-image-images/templateimage03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](displaying-an-image-images/templateimage03vs.png "Defina a tonalidade para colorir a imagem")](displaying-an-image-images/templateimage03vs.png#lightbox)

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

Porque o `RenderMode` propriedade de um `UIImage` é somente leitura, use o `ImageWithRenderingMode` método para criar uma nova instância da imagem com a configuração desejada do modo de renderização.

Há três, possivelmente, as configurações para `UIImage.RenderMode` por meio de `UIImageRenderingMode` enum:

* `AlwaysOriginal` -Força a imagem a ser renderizado como o arquivo de imagem de origem original sem quaisquer alterações.
* `AlwaysTemplate` -Força a imagem a ser renderizado como uma imagem de modelo por colorir os pixels com especificado `Tint` cor.
* `Automatic` -Seja processa a imagem como um modelo ou Original com base no que ele é usado no ambiente. Por exemplo, se a imagem é usada em uma `UIToolBar`, `UINavigationBar`, `UITabBar` ou `UISegmentControl` ele será tratado como um modelo.

## <a name="adding-new-assets-collections"></a>Adicionando novas coleções de ativos

Ao trabalhar com imagens nos catálogos de ativos pode haver momentos em que uma nova coleção será necessária, em vez de adicionar todas as imagens do aplicativo para o `Assets.xcassets` coleção. Por exemplo, durante a criação de recursos sob demanda.

Para adicionar um novo catálogo de ativos para o projeto:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Com o botão direito no **nome do projeto** na **Gerenciador de soluções** e selecione **Add** > **novo arquivo...**
2. Selecione **iOS** > **catálogo de ativos**, digite um **nome** para a coleção e clique no **novo** botão:

    ![](displaying-an-image-images/asset01.png "Criando um novo catálogo de ativos")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. No Gerenciador de soluções, clique duas vezes em **catálogos de ativos** pasta e selecione **Adicionar > novo catálogo de ativos**.
2. Dê um nome e clique em **adicionar**:

    ![](displaying-an-image-images/asset1.png "Criando um novo catálogo de ativos")

-----

A partir daqui, a coleção possa ser editada da mesma forma como o padrão `Assets.xcassets` coleção automaticamente incluída no projeto.

## <a name="using-images-with-controls"></a>Uso de imagens com controles

Além de usar imagens para oferecer suporte a um aplicativo, o iOS também usa imagens com tipos de controle de aplicativo, como botões, barras de ferramentas, barras de navegação, tabelas e barras de guia. Uma maneira simples de criar uma imagem exibida em um controle é atribuir um `UIImage` instância para o controle `Image` propriedade.

### <a name="frombundle"></a>FromBundle

O `FromBundle` chamada de método é uma chamada síncrona (bloqueio) que tem uma série de recursos de carregamento e o gerenciamento de imagem internos, como armazenamento em cache o suporte e a manipulação automática de arquivos de imagem para várias resoluções.  

O exemplo a seguir mostra como definir a imagem de um `UITabBarItem` em um `UITabBar`:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

Supondo que `MyImage` é o nome de um ativo de imagem adicionado a um catálogo de ativos acima. Ao trabalhar imagens do catálogo de ativos, basta especificar o nome do conjunto de imagem na `FromBundle` método para **PNG** formatado imagens:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

Para qualquer outro formato de imagem, inclua a extensão com o nome. Por exemplo:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage.jpg");
```

Para obter mais informações sobre ícones e imagens, consulte a documentação da Apple no [ícone personalizado e diretrizes de criação de imagem](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html).

## <a name="displaying-an-image-in-a-storyboards"></a>Exibindo uma imagem em um storyboards

Quando uma imagem foi adicionada a um projeto xamarin. IOS usando um catálogos de ativos, ele poderá ser facilmente exibidos em um Storyboard usando um `UIImageView` no Designer do iOS. Por exemplo, se o ativo de imagem a seguir foram adicionado:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![](displaying-an-image-images/display01.png "Foi adicionado um ativo de imagem de exemplo")

Faça o seguinte para exibi-lo em um Storyboard:

1. Clique duas vezes o `Main.storyboard` do arquivo na **Gerenciador de soluções** para abri-lo para edição no Designer do iOS.
2. Selecione uma **modo de exibição de imagem** da **caixa de ferramentas**:

     ![](displaying-an-image-images/display02.png "Selecione uma exibição de imagem da caixa de ferramentas")
3. Arraste a exibição de imagem para a superfície de design e a posição e dimensioná-lo conforme necessário:

    ![](displaying-an-image-images/display03.png "Uma nova exibição de imagem na superfície de Design")
4. No **Widget** seção o **Property Explorer** selecione desejado **imagem** ativo a ser exibido:

    ![](displaying-an-image-images/display04.png "Selecione o ativo de imagem desejado a ser exibido")
5. No **modo de exibição** seção, use o **modo** para controlar como a imagem será redimensionada quando o **exibição de imagem** é redimensionado.
6. Com o **modo de exibição de imagem** selecionado, clique nele novamente para adicionar **restrições**:

    ![](displaying-an-image-images/display05.png "Adicionando restrições")
7. Arraste a alça de "T" em forma em cada borda da **modo de exibição de imagem** correspondente no lado da tela para "fixar" a imagem para os lados. Dessa forma, o **modo de exibição de imagem** será reduzido e cresça conforme a tela for redimensionada.
8. Salve as alterações para o Storyboard.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](displaying-an-image-images/display01vs.png "Foi adicionado um ativo de imagem de exemplo")

Faça o seguinte para exibi-lo em um Storyboard:

1. Clique duas vezes o `Main.storyboard` do arquivo na **Gerenciador de soluções** para abri-lo para edição no Designer do iOS.
2. Selecione uma **modo de exibição de imagem** da **caixa de ferramentas**:

     ![](displaying-an-image-images/display02vs.png "Selecione uma exibição de imagem da caixa de ferramentas")
3. Arraste a exibição de imagem para a superfície de design e a posição e dimensioná-lo conforme necessário:

    ![](displaying-an-image-images/display03vs.png "Uma nova exibição de imagem na superfície de Design")
4. No **Widget** seção o **Property Explorer** selecione desejado **imagem** ativo a ser exibido:

    ![](displaying-an-image-images/display04vs.png "Selecione o ativo de imagem desejado a ser exibido")
5. No **modo de exibição** seção, use o **modo** para controlar como a imagem será redimensionada quando o **exibição de imagem** é redimensionado.
6. Com o **modo de exibição de imagem** selecionado, clique nele novamente para adicionar **restrições**:

    ![](displaying-an-image-images/display05vs.png "Adicionando restrições")
7. Arraste a alça de "T" em forma em cada borda da **modo de exibição de imagem** correspondente no lado da tela para "fixar" a imagem para os lados. Dessa forma, o **modo de exibição de imagem** será reduzido e cresça conforme a tela for redimensionada.
8. Salve as alterações para o Storyboard.

-----

## <a name="displaying-an-image-in-code"></a>Exibindo uma imagem no código

Como exibir uma imagem em um Storyboard, após a adição de uma imagem a um projeto xamarin. IOS usando um catálogos de ativos, ele pode ser facilmente exibido usando um código c#.

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

Esse código cria um novo `UIImageView` e concede a ele um tamanho inicial e posição. Em seguida, ele carrega a imagem de um ativo de imagem adicionado ao projeto e adiciona o `UIImageView` para o pai `UIView` para exibi-lo.

## <a name="related-links"></a>Links relacionados

- [Trabalhando com imagens (amostra)](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Tamanho da imagem e a resolução (Apple)](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/image-size-and-resolution/)
