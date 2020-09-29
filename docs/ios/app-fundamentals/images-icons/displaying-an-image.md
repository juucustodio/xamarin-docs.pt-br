---
title: Exibindo uma imagem no Xamarin. iOS
description: Este artigo aborda a inclusão de um ativo de imagem em um aplicativo Xamarin. iOS e a exibição dessa imagem usando código C# ou atribuindo-a a um controle no designer do iOS.
ms.prod: xamarin
ms.assetid: 60288B12-49E3-4E87-8690-D04A5EC7A664
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/24/2018
ms.openlocfilehash: 709ec5e4652c58bfaa3956d5b860c0fac08cb05b
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437360"
---
# <a name="displaying-an-image-in-xamarinios"></a>Exibindo uma imagem no Xamarin. iOS

_Este artigo aborda a inclusão de um ativo de imagem em um aplicativo Xamarin. iOS e a exibição dessa imagem usando código C# ou atribuindo-a a um controle no designer do iOS._

## <a name="adding-and-organizing-images-in-a-xamarinios-app"></a>Adicionando e organizando imagens em um aplicativo Xamarin. iOS

Ao adicionar uma imagem para uso em um aplicativo Xamarin. iOS, o desenvolvedor usará um _Catálogo de ativos_ para dar suporte a cada dispositivo IOS e à resolução exigida por um aplicativo.

Adicionados no iOS 7, os **conjuntos de imagens de catálogos de ativos** contêm todas as versões ou representações de uma imagem que são necessárias para dar suporte a vários dispositivos e fatores de escala para um aplicativo. Em vez de depender do nome de arquivo de ativos de imagem, os **conjuntos de imagens** usam um arquivo JSON para especificar qual imagem pertence a qual dispositivo e/ou resolução. Essa é a maneira preferida de gerenciar e dar suporte a imagens no iOS (do iOS 9 ou superior).

## <a name="adding-images-to-an-asset-catalog-image-set"></a>Adicionando imagens a um conjunto de imagens do catálogo de ativos

Como mencionado acima, os **conjuntos de imagens de catálogos de ativos** contêm todas as versões ou representações de uma imagem que são necessárias para dar suporte a vários dispositivos e fatores de escala para um aplicativo. Em vez de depender do nome de arquivo de ativos de imagem, os **conjuntos de imagens** usam um arquivo JSON para especificar qual imagem pertence a qual dispositivo e/ou resolução.

Para criar um novo conjunto de imagens e adicionar imagens a ele, faça o seguinte:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. No **Gerenciador de soluções**, clique duas vezes no `Assets.xcassets` arquivo para abri-lo para edição:

    ![Os assets. xcassets no Gerenciador de Soluções](displaying-an-image-images/imageset01.png)
2. Clique com o botão direito do mouse na **lista ativos** e selecione **novo conjunto de imagens**:

    ![Adicionando um novo conjunto de imagens](displaying-an-image-images/imageset02.png)
3. Selecione o novo conjunto de imagens e o editor será exibido:

    ![O editor de conjunto de imagens](displaying-an-image-images/imageset03.png)
4. A partir daqui, arraste as imagens para cada um dos diferentes dispositivos e resoluções necessários.
5. Clique duas vezes no **nome** do novo conjunto de imagens na **lista ativos** para editá-lo: ![ editando o nome do novo conjunto de imagens](displaying-an-image-images/imageset04.png)

Ao usar um **conjunto de imagens** no designer do IOS, basta selecionar o nome do conjunto na lista suspensa no editor de propriedades:

![Selecione o nome de um conjunto de imagens na lista suspensa](displaying-an-image-images/imageset06.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Abra o catálogo de ativos no **Gerenciador de soluções**e, no canto superior esquerdo, clique no botão de **adição** :

    ![Clique no botão de adição](displaying-an-image-images/asset5.png)

2. Selecione **Adicionar conjunto de imagens** e o editor de conjunto de imagens será exibido para o novo conjunto de imagens. A partir daqui, arraste as imagens para cada um dos diferentes dispositivos e resoluções necessários.

    ![O editor de conjunto de imagens](displaying-an-image-images/asset7.png)

### <a name="renaming-an-image-set"></a>Renomeando um conjunto de imagens

Para renomear um conjunto de imagens, faça o seguinte:

1. No **Gerenciador de soluções**, clique duas vezes no arquivo do **Catálogo de ativos** para abri-lo para edição:

    ![O catálogo de ativos no Gerenciador de Soluções](displaying-an-image-images/rename01.png)
2. Selecione o **conjunto de imagens** a ser renomeado:

    ![Selecione o conjunto de imagens a ser renomeado](displaying-an-image-images/rename02.png)
3. No **Gerenciador de propriedades**, role até a parte inferior e selecione **nome**(na seção **Miscelânea** ):

    ![Selecione o nome na seção de miscelânea](displaying-an-image-images/rename03.png)
4. Insira um novo **nome** para o **conjunto de imagens** e salve as alterações.

-----

Ao usar um **conjunto de imagens** no código, referencie-o por nome chamando o `FromBundle` método da `UIImage` classe. Por exemplo:

```csharp
MonkeyImage.Image = UIImage.FromBundle ("PurpleMonkey");
```

> [!IMPORTANT]
> Se as imagens atribuídas a um conjunto de imagens não estiverem aparecendo corretamente, verifique se o nome do arquivo correto está sendo usado com o `FromBundle` método (o **conjunto de imagens** e não o nome do **Catálogo de ativos** pai). Para imagens PNG, a `.png` extensão pode ser omitida. Para outros formatos de imagem, a extensão é necessária (por exemplo, `PurpleMonkey.jpg`).

### <a name="using-vector-images-in-asset-catalogs"></a>Usando imagens vetoriais em catálogos de ativos

A partir do iOS 8, a classe **vector** especial foi adicionada aos **conjuntos de imagens** que permitem que o desenvolvedor inclua uma imagem de vetor formatada em **PDF** no fita em vez disso, incluindo arquivos de bitmap individuais em resoluções diferentes. Usando esse método, forneça um único arquivo de vetor para a `@1x` resolução (formatado como um arquivo de PDF vetorial) e as `@2x` `@3x` versões e do arquivo serão geradas no momento da compilação e incluídas no pacote do aplicativo.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

![Imagens vetoriais no editor de catálogos de ativos](displaying-an-image-images/imageset05.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Imagens vetoriais no editor de catálogos de ativos](displaying-an-image-images/asset8.png)

-----

Por exemplo, se o desenvolvedor incluir um `MonkeyIcon.pdf` arquivo como o vetor de um catálogo de ativos com uma resolução de 150px x 150px, os seguintes ativos de bitmap seriam incluídos no pacote de aplicativo final quando ele foi compilado:

- `MonkeyIcon@1x.png` -resolução de 150px x 150px.
- `MonkeyIcon@2x.png` -resolução de 300px x 300px.
- `MonkeyIcon@3x.png` -resolução de 450px x 450px.

O seguinte deve ser levado em consideração ao usar imagens de vetor de PDF em catálogos de ativos:

- Esse não é o suporte de vetor completo, pois o PDF será rasterizado para um bitmap em tempo de compilação e os bitmaps enviados no aplicativo final.
- O tamanho da imagem não pode ser ajustado depois de ser definido no catálogo de ativos. Se o desenvolvedor tentar redimensionar a imagem (no código ou usando o layout automático e as classes de tamanho), a imagem será distorcida da mesma forma que qualquer outro bitmap.
- Os catálogos de ativos são compatíveis apenas com o iOS 7 e superior, se um aplicativo precisar oferecer suporte ao iOS 6 ou inferior, ele não poderá usar catálogos de ativos.

## <a name="working-with-template-images"></a>Trabalhando com imagens de modelo

Com base no design de um aplicativo iOS, pode haver ocasiões em que o desenvolvedor precise personalizar um ícone ou uma imagem dentro da interface do usuário para corresponder a uma alteração no esquema de cores (por exemplo, com base nas preferências do usuário).

Para obter esse efeito facilmente, alterne o _modo de renderização_ do ativo da imagem para a **imagem do modelo**:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![O modo de renderização definido como imagem de modelo](displaying-an-image-images/templateimage01.png)](displaying-an-image-images/templateimage01.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![O modo de renderização definido como modelo](displaying-an-image-images/templateimage01vs.png)](displaying-an-image-images/templateimage01vs.png#lightbox)

-----

No designer do iOS, atribua o ativo da imagem a um controle de interface do usuário e, em seguida, defina a **tonalidade** para colorir a imagem:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Definir a tonalidade para colorir a imagem](displaying-an-image-images/templateimage03.png)](displaying-an-image-images/templateimage03.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Definir a tonalidade para colorir a imagem](displaying-an-image-images/templateimage03vs.png)](displaying-an-image-images/templateimage03vs.png#lightbox)

-----

Opcionalmente, o ativo de imagem e a tonalidade podem ser definidos diretamente no código:

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

Como a `RenderMode` propriedade de um `UIImage` é somente leitura, use o `ImageWithRenderingMode` método para criar uma nova instância da imagem com a configuração do modo de processamento desejado.

Há três configurações possivelmente para `UIImage.RenderMode` por meio da `UIImageRenderingMode` enumeração:

- `AlwaysOriginal` – Força a imagem a ser renderizada como o arquivo de imagem de origem original sem nenhuma alteração.
- `AlwaysTemplate` – Força a imagem a ser renderizada como uma imagem de modelo ao colorir os pixels com a `Tint` cor especificada.
- `Automatic` -Renderiza a imagem como um modelo ou original com base no ambiente em que ela é usada. Por exemplo, se a imagem for usada em um `UIToolBar` , `UINavigationBar` `UITabBar` ou `UISegmentControl` ela será tratada como um modelo.

## <a name="adding-new-assets-collections"></a>Adicionando novas coleções de ativos

Ao trabalhar com imagens em catálogos de ativos, pode haver ocasiões em que uma nova coleção será necessária, em vez de adicionar todas as imagens do aplicativo à `Assets.xcassets` coleção. Por exemplo, ao criar recursos sob demanda.

Para adicionar um novo catálogo de ativos ao projeto:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Clique com o botão direito do mouse no **nome do projeto** na **Gerenciador de soluções** e selecione **Adicionar**  >  **novo arquivo...**
2. Selecione **iOS**  >  **Catálogo de ativos**do IOS, insira um **nome** para a coleção e clique no botão **novo** :

    ![Criando um novo catálogo de ativos](displaying-an-image-images/asset01.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Na Gerenciador de Soluções, clique com o botão direito do mouse na pasta **catálogos de ativos** e selecione **Adicionar > novo catálogo de ativos**.
2. Dê um nome a ele e clique em **Adicionar**:

    ![Criando um novo catálogo de ativos](displaying-an-image-images/asset1.png)

-----

A partir daqui, a coleção pode ser trabalhada da mesma maneira que a `Assets.xcassets` coleção padrão incluída automaticamente no projeto.

## <a name="using-images-with-controls"></a>Usando imagens com controles

Além de usar imagens para dar suporte a um aplicativo, o iOS também usa imagens com tipos de controle de aplicativo, como barras de tabulação, barras de ferramentas, barras de navegação, tabelas e botões. Uma maneira simples de fazer uma imagem aparecer em um controle é atribuir uma `UIImage` instância à propriedade do controle `Image` .

### <a name="frombundle"></a>FromBundle

A `FromBundle` chamada de método é uma chamada síncrona (de bloqueio) que tem vários recursos de carregamento e gerenciamento de imagens, como o suporte a cache e o tratamento automático de arquivos de imagem para várias resoluções.

O exemplo a seguir mostra como definir a imagem de um `UITabBarItem` em `UITabBar` :

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

Supondo que `MyImage` seja o nome de um ativo de imagem adicionado a um catálogo de ativos acima. Ao trabalhar com imagens do catálogo de ativos, basta especificar o nome do conjunto de imagens no `FromBundle` método para imagens formatadas para **png** :

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

Para qualquer outro formato de imagem, inclua a extensão com o nome. Por exemplo:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage.jpg");
```

Para obter mais informações sobre ícones e imagens, consulte a documentação da Apple sobre [diretrizes personalizadas de criação de imagem e ícone](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html).

## <a name="displaying-an-image-in-a-storyboard"></a>Exibindo uma imagem em um storyboard

Depois que uma imagem tiver sido adicionada a um projeto Xamarin. iOS usando catálogos de ativos, ela poderá ser facilmente exibida em um storyboard usando um `UIImageView` no designer do Ios. Por exemplo, se o seguinte ativo de imagem tiver sido adicionado:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

![Um ativo de imagem de exemplo foi adicionado](displaying-an-image-images/display01.png)

Faça o seguinte para exibi-lo em um storyboard:

1. Clique duas vezes no `Main.storyboard` arquivo no **Gerenciador de soluções** para abri-lo para edição no designer do Ios.
2. Selecione uma **exibição de imagem** na **caixa de ferramentas**:

     ![Selecione uma exibição de imagem na caixa de ferramentas](displaying-an-image-images/display02.png)
3. Arraste a exibição de imagem para a superfície de design e posicione e dimensione-a conforme necessário:

    ![Uma nova exibição de imagem no Design Surface](displaying-an-image-images/display03.png)
4. Na seção **widget** do Gerenciador de **Propriedades** , selecione o ativo de **imagem** desejado a ser exibido:

    ![Selecione o ativo de imagem desejado a ser exibido](displaying-an-image-images/display04.png)
5. Na seção **Exibir** , use o **modo** para controlar como a imagem será redimensionada quando a exibição de **imagem** for redimensionada.
6. Com a **exibição de imagem** selecionada, clique nela novamente para adicionar **restrições**:

    ![Adicionando restrições](displaying-an-image-images/display05.png)
7. Arraste a alça moldada "T" em cada borda da **exibição de imagem** para o lado correspondente da tela para "fixar" a imagem nos lados. Dessa forma, a **exibição da imagem** será reduzida e aumentada conforme a tela for redimensionada.
8. Salve as alterações no storyboard.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Um ativo de imagem de exemplo foi adicionado](displaying-an-image-images/display01vs.png)

Faça o seguinte para exibi-lo em um storyboard:

1. Clique duas vezes no `Main.storyboard` arquivo no **Gerenciador de soluções** para abri-lo para edição no designer do Ios.
2. Selecione uma **exibição de imagem** na **caixa de ferramentas**:

     ![Selecione uma exibição de imagem na caixa de ferramentas](displaying-an-image-images/display02vs.png)
3. Arraste a exibição de imagem para a superfície de design e posicione e dimensione-a conforme necessário:

    ![Uma nova exibição de imagem no Design Surface](displaying-an-image-images/display03vs.png)
4. Na seção **widget** do Gerenciador de **Propriedades** , selecione o ativo de **imagem** desejado a ser exibido:

    ![Selecione o ativo de imagem desejado a ser exibido](displaying-an-image-images/display04vs.png)
5. Na seção **Exibir** , use o **modo** para controlar como a imagem será redimensionada quando a exibição de **imagem** for redimensionada.
6. Com a **exibição de imagem** selecionada, clique nela novamente para adicionar **restrições**:

    ![Adicionando restrições](displaying-an-image-images/display05vs.png)
7. Arraste a alça moldada "T" em cada borda da **exibição de imagem** para o lado correspondente da tela para "fixar" a imagem nos lados. Dessa forma, a **exibição da imagem** será reduzida e aumentada conforme a tela for redimensionada.
8. Salve as alterações no storyboard.

-----

## <a name="displaying-an-image-in-code"></a>Exibindo uma imagem no código

Assim como a exibição de uma imagem em um storyboard, uma vez que uma imagem foi adicionada a um projeto Xamarin. iOS usando catálogos de ativos, ela pode ser facilmente exibida usando código C#.

Veja o exemplo seguinte:

```csharp
// Create an image view that will fill the
// parent image view and set the image from
// an Image Asset

var imageView = new UIImageView (View.Frame);
imageView.Image = UIImage.FromBundle ("Kemah");

// Add the Image View to the parent view
View.AddSubview (imageView);
```

Esse código cria um novo `UIImageView` e dá a ele um tamanho inicial e uma posição. Em seguida, ele carrega a imagem de um ativo de imagem adicionado ao projeto e adiciona o `UIImageView` ao pai `UIView` para exibi-lo.

## <a name="related-links"></a>Links relacionados

- [Trabalhando com imagens (exemplo)](/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Tamanho e resolução da imagem (Apple)](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/image-size-and-resolution/)