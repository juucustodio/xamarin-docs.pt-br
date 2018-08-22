---
title: Imagens no xamarin. Mac
description: Este artigo aborda o trabalho com imagens e ícones em um aplicativo xamarin. Mac. Ele descreve a criação e manutenção de imagens necessárias para criar o ícone do seu aplicativo e usar imagens no código c# e no Interface Builder do Xcode.
ms.prod: xamarin
ms.assetid: C6B539C2-FC6A-4C38-B839-32BFFB9B16A7
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/15/2017
ms.openlocfilehash: 3bc3c731729f92ae3c5ad6126166892a236e2eab
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2018
ms.locfileid: "40251235"
---
# <a name="images-in-xamarinmac"></a>Imagens no xamarin. Mac

_Este artigo aborda o trabalho com imagens e ícones em um aplicativo xamarin. Mac. Ele descreve a criação e manutenção de imagens necessárias para criar o ícone do seu aplicativo e usar imagens no código c# e no Interface Builder do Xcode._

## <a name="overview"></a>Visão geral

Ao trabalhar com c# e .NET em um aplicativo xamarin. Mac, você tem acesso para a mesma imagem e ícone das ferramentas de que um desenvolvedor que trabalha *Objective-C* e *Xcode* faz.

Há várias maneiras de imagem ativos são usados dentro de um aplicativo do macOS (anteriormente conhecido como o Mac OS X). De simplesmente exibir uma imagem como parte da interface de usuário do seu aplicativo, atribuindo-o a um controle de interface do usuário como uma barra de ferramentas ou Item de lista de origem, ao fornecimento de ícones, xamarin. Mac torna mais fácil adicionar arte excelente para seus aplicativos macOS das seguintes maneiras : 

- **Elementos de interface do usuário** -imagens podem ser exibidas como planos de fundo ou como parte do seu aplicativo em uma exibição de imagem (`NSImageView`).
- **Botão** -as imagens podem ser exibidas em botões (`NSButton`).
- **Célula de imagem** - como parte de um controle de tabela com base em (`NSTableView` ou `NSOutlineView`), imagens podem ser usadas em uma célula de imagem (`NSImageCell`).
- **Item da barra de ferramentas** -imagens podem ser adicionadas a uma barra de ferramentas (`NSToolbar`) como um Item da barra de ferramentas de imagem (`NSToolbarItem`).
- **Ícone da lista de origem** : como parte de uma lista de origem (especialmente formatado `NSOutlineView`).
- **Ícone do aplicativo** -uma série de imagens pode ser agrupada em um `.icns` definido e usado como ícone do seu aplicativo. Consulte nosso [ícone do aplicativo](~/mac/deploy-test/app-icon.md) documentação para obter mais informações.

Além disso, o macOS fornece um conjunto de imagens predefinidas que podem ser usados em todo o aplicativo.

[![Um exemplo de execução de aplicativo](image-images/intro01.png "um exemplo de execução de aplicativo")](image-images/intro01-large.png#lightbox)

Neste artigo, abordaremos os fundamentos de trabalhar com imagens e ícones em um aplicativo xamarin. Mac. É altamente recomendável que você trabalhe por meio de [Hello, Mac](~/mac/get-started/hello-mac.md) pela primeira vez, especificamente o artigo a [Introdução ao Interface Builder e Xcode](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [saídas e ações](~/mac/get-started/hello-mac.md#outlets-and-actions) seções, como ela aborda os principais conceitos e técnicas que usaremos neste artigo.


## <a name="adding-images-to-a-xamarinmac-project"></a>Adicionando imagens a um projeto xamarin. Mac

Ao adicionar uma imagem para uso em um aplicativo xamarin. Mac, existem vários lugares e maneiras que o desenvolvedor pode incluir o arquivo de imagem para a fonte do projeto:

- **Árvore de projeto principal [preterido]** -imagens podem ser adicionadas diretamente à árvore de projetos. Ao chamar imagens armazenadas na árvore de projeto principal do código, nenhum local de pasta é especificado. Por exemplo: `NSImage image = NSImage.ImageNamed("tags.png");`. 
- **Pasta de recursos [preterida]** -especiais **recursos** pasta é para qualquer arquivo que se tornará parte do aplicativo do pacote, como o ícone de tela de inicialização ou geral imagens (ou qualquer outra imagem ou arquivo o desenvolvedor deseja adicionar). Ao chamar imagens armazenadas em do **recursos** pasta de código, assim como as imagens armazenadas na árvore de projeto principal, nenhum local de pasta é especificada. Por exemplo: `NSImage.ImageNamed("tags.png")`.
- **Personalizado pasta ou subpasta [preterido]** – o desenvolvedor pode adicionar uma pasta personalizada na árvore de origem de projetos e armazenar as imagens de lá. O local onde o arquivo é adicionado pode ser aninhado em uma subpasta para ajudar a organizar o projeto ainda mais. Por exemplo, se o desenvolvedor adicionou uma `Card` pasta para o projeto e uma subpasta do `Hearts` nessa pasta, em seguida, armazenar a imagem **Jack.png** no `Hearts` pasta, `NSImage.ImageNamed("Card/Hearts/Jack.png")` carrega a imagem em tempo de execução.
- **Conjuntos de imagem do catálogo do Asset [preferencial]** – foi adicionado nos X El Capitan, **conjuntos de imagem de catálogos de ativos** contêm todas as versões ou representações de uma imagem que são necessárias para dar suporte a vários dispositivos e fatores de dimensionamento seu aplicativo. Em vez de usar o nome de arquivo de ativos de imagem (**@1x**, **@2x**).

<a name="asset-catalogs" />

### <a name="adding-images-to-an-asset-catalog-image-set"></a>Adicionando imagens a uma imagem do catálogo de ativos definida

Conforme mencionado acima, uma **conjuntos de imagem de catálogos de ativos** contêm todas as versões ou representações de uma imagem que são necessárias para dar suporte a vários dispositivos e fatores para o seu aplicativo de dimensionamento. Em vez de usar o nome de arquivo de ativos de imagem (consulte as imagens independentes de resolução e uma nomenclatura de imagem acima), **conjuntos de imagem** usar o Editor de ativo para especificar qual imagem pertence a qual dispositivo e/ou resolução.

1. No **painel de soluções**, clique duas vezes o **xcassets** arquivo para abri-lo para edição: 

    ![Selecionando o xcassets](image-images/imageset01.png "selecionando o xcassets")
2. Clique com botão direito no **lista de ativos** e selecione **novo conjunto de imagem**: 

    [![Adicionar um novo conjunto de imagem](image-images/imageset02.png "adicionar um novo conjunto de imagem")](image-images/imageset02-large.png#lightbox)
3. Selecione o novo conjunto de imagens e o editor será exibido: 

    [![Selecionando o novo conjunto de imagens](image-images/imageset03.png "selecionando o novo conjunto de imagens")](image-images/imageset03-large.png#lightbox)
4. Aqui é possível arrastar imagens para cada um dos diferentes dispositivos e resoluções necessários. 
5. Clique duas vezes em do novo conjunto de imagens **nome** na **lista Assets** editá-lo: 

    [![Nome do conjunto de edição de imagem](image-images/imageset04.png "nome do conjunto de edição de imagem")](image-images/imageset04-large.png#lightbox)
    
Um especial **vetor** foi adicionado à classe **conjuntos de imagem** que nos permite incluir um _PDF_ formatado de imagem de vetor no casset em vez disso, incluindo arquivos de bitmap individuais no as resoluções diferentes. Usando esse método, você fornecer um arquivo único vetor para o **@1x** resolução (formatada como um arquivo PDF de vetor) e o **@2x** e **@3x** versões do arquivo serão geradas em tempo de compilação e incluídas no pacote do aplicativo.

[![A imagem de definir a interface do editor](image-images/imageset05.png "a imagem de definir a interface do editor")](image-images/imageset05-large.png#lightbox)

Por exemplo, se você incluir um `MonkeyIcon.pdf` arquivo como o vetor de um catálogo de ativos com uma resolução de 150px x 150px, o bitmap seguinte ativos seriam incluídos no pacote final do aplicativo quando ele foi compilado:

1. **MonkeyIcon@1x.png** -150px x 150px resolução.
2. **MonkeyIcon@2x.png** -300px 300px resolução.
3. **MonkeyIcon@3x.png** -450px x 450px resolução.

A seguir deve ser levados em consideração ao usar imagens de vetor PDF em catálogos de ativos:

- Isso não é completa do vetor de suporte, pois o PDF será rasterizado em um bitmap em tempo de compilação e os bitmaps fornecidos no aplicativo final.
- Você não pode ajustar o tamanho da imagem após ela ter sido definida no catálogo de ativos. Se você tentar redimensionar a imagem (ou no código ou usando o Layout automático e Classes de tamanho) a imagem será distorcida assim como qualquer outro bitmap.

Ao usar um **imagem definida** no Interface Builder do Xcode, basta selecionar o nome do conjunto na lista suspensa na **Inspetor de atributo**: * *

![Selecionar uma imagem definido no Interface Builder do Xcode](image-images/imageset06.png "selecionar uma imagem definido no Interface Builder do Xcode")

<a name="Adding-new-Assets-Collections"/>

### <a name="adding-new-assets-collections"></a>Adicionando novas coleções de ativos

Ao trabalhar com imagens nos catálogos de ativos pode haver vezes quando você deseja criar uma nova coleção, em vez de adicionar todas as imagens para o **xcassets** coleção. Por exemplo, durante a criação de recursos sob demanda.

Para adicionar um novo catálogo de ativos ao seu projeto:

1. Clique com botão direito no projeto na **painel de soluções** e selecione **Add** > **novo arquivo...**
2. Selecione **Mac** > **catálogo de ativos**, digite um **nome** para a coleção e clique no **novo** botão: 

    ![Adicionando um novo catálogo de ativos](image-images/asset01.png "adicionando um novo catálogo de ativos")

Aqui você pode trabalhar com a coleção da mesma forma como o padrão **xcassets** coleção automaticamente incluída no projeto.


### <a name="adding-images-to-resources"></a>Adicionando imagens aos recursos

> [!IMPORTANT]
> Esse método de trabalhar com imagens em um aplicativo macOS foi substituído pela Apple. Você deve usar [conjuntos de imagem do catálogo de ativos](#asset-catalogs) ao Gerenciador de seu aplicativo em vez disso, imagens.

Antes de usar um arquivo de imagem em seu aplicativo xamarin. MAC (ou em código c# ou do construtor de Interface) ele deve ser incluído no projeto do **recursos** pasta como um **recursos de pacote**. Para adicionar um arquivo a um projeto, faça o seguinte:

1. Com o botão direito no **recursos** pasta em seu projeto na **painel de soluções** e selecione **Add** > **adicionar arquivos...** : 

    ![Adicionando um arquivo](image-images/add01.png "adicionando um arquivo")
2. Dos **adicionar arquivos** caixa de diálogo, selecione as imagens de arquivos para adicionar ao projeto, selecione `BundleResource` para o **ação de build de substituição** e clique no **abrir** botão:

    [![Selecionar os arquivos a serem adicionados](image-images/add02.png "selecionando os arquivos a serem adicionados")](image-images/add02-large.png#lightbox)
3. Se os arquivos já não estão na **recursos** pasta, você será solicitado se deseja **cópia**, **mover** ou **Link** os arquivos. Escolha a que cada naipes suas necessidades, normalmente que serão **cópia**:

    ![Selecionando a ação add](image-images/add04.png "selecionando a ação Adicionar")
4. Os novos arquivos serão ser incluídos no projeto e de leitura para uso: 

    ![Os novos arquivos de imagem adicionados ao painel de soluções](image-images/add03.png "os novos arquivos de imagem adicionados ao painel de soluções")
5. Repita o processo para quaisquer arquivos de imagem necessários.

Você pode usar qualquer png, jpg ou arquivo pdf como uma imagem de origem em seu aplicativo xamarin. Mac. Na próxima seção, veremos adicionar versões de alta resolução das nossas imagens e ícones para dar suporte a Retina com base em Macs.

> [!IMPORTANT]
> Se você estiver adicionando imagens para o **recursos** pasta, você pode deixar o **ação de build de substituição** definido como **padrão**. O padrão é de ação de compilação para esta pasta `BundleResource`.

## <a name="provide-high-resolution-versions-of-all-app-graphics-resources"></a>Fornecer versões em alta resolução de todos os recursos de gráficos do aplicativo

Qualquer gráfico ativo que você adicionar a um aplicativo xamarin. MAC (ícones, controles personalizados, cursores personalizados, arte final personalizada, etc.) precisa ter versões em alta resolução, além de suas versões de resolução-padrão. Isso é necessário para que seu aplicativo será melhor aparência quando executados em uma tela retina equipado com computador Mac.


### <a name="adopt-the-2x-naming-convention"></a>Adotar a @2x convenção de nomenclatura

> [!IMPORTANT]
> Esse método de trabalhar com imagens em um aplicativo macOS foi substituído pela Apple. Você deve usar [conjuntos de imagem do catálogo de ativos](#asset-catalogs) ao Gerenciador de seu aplicativo em vez disso, imagens.

Quando você cria as versões standard e de alta resolução de uma imagem, siga essa convenção de nomenclatura para o par de imagem ao incluí-los em seu projeto xamarin. Mac:

- **Standard-Resolution**  - **ImageName.filename-extension** (exemplo: **tags.png**)
- **Alta resolução**   -  **ImageName@2x.filename-extension** (exemplo: **tags@2x.png**)

Quando adicionado a um projeto, eles seriam exibida da seguinte maneira:

![Os arquivos de imagem no painel de soluções](image-images/add03.png "os arquivos de imagem no painel de soluções")

Quando uma imagem é atribuída a um elemento de interface do usuário no construtor de Interface você simplesmente escolher o arquivo na _ImageName_**.** _extensão de nome de arquivo_ formato (exemplo: **tags.png**). O mesmo para usar uma imagem em código c#, você vai escolher o arquivo na _ImageName_**.** _extensão de nome de arquivo_ formato.

Quando você o aplicativo xamarin. MAC é executado em um Mac, o _ImageName_**.** _extensão de nome de arquivo_ formato de imagem será usado em exibições de resolução padrão, o **ImageName@2x.filename-extension** imagem será escolhida automaticamente tela retina bases Macs.


## <a name="using-images-in-interface-builder"></a>Usando imagens no construtor de Interface

Qualquer recurso de imagem que você adicionou à **recursos** pasta no seu xamarin. MAC do projeto e definiu a ação de compilação para **BundleResource** aparecerá automaticamente no construtor de Interface e pode ser selecionado como parte de um elemento de interface do usuário (se ele lida com imagens).

Para usar uma imagem no construtor de interface, faça o seguinte:

1. Adicionar uma imagem para o **recursos** pasta com um **Build Action** de `BundleResource`: 

     ![Um recurso de imagem no painel de soluções](image-images/ib00.png "um recurso de imagem no painel de soluções")
2. Clique duas vezes o **Main. Storyboard** arquivo para abri-lo para edição no Interface Builder: 

     [![Editando o storyboard principal](image-images/ib01.png "editando o storyboard principal")](image-images/ib01-large.png#lightbox)
3. Arraste um elemento de interface do usuário que usa imagens para a superfície de design (por exemplo, uma **Item de barra de ferramentas de imagem**): 

     ![Editar um item de barra de ferramentas](image-images/ib02.png "editar um item de barra de ferramentas")
4. Selecione a imagem que você adicionou para a **recursos** pasta na **nome da imagem** lista suspensa: 

     [![Selecionar uma imagem para um item da barra de ferramentas](image-images/ib03.png "selecionando uma imagem para um item da barra de ferramentas")](image-images/ib03-large.png#lightbox)
5. A imagem selecionada será exibida na superfície de design: 

     ![A imagem que está sendo exibida no editor de barra de ferramentas](image-images/ib04.png "a imagem que está sendo exibida no editor de barra de ferramentas")
6. Salve suas alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

As etapas acima funcionarem para qualquer elemento de interface do usuário que permite que sua propriedade de imagem a ser definido **Inspetor de atributo**. Novamente, se você tiver incluído uma **@2x** versão do seu arquivo de imagem, ela será usada automaticamente na tela retina com base em Macs.

> [!IMPORTANT]
> Se a imagem não está disponível na **nome da imagem** menu suspenso, feche o storyboard projeto no Xcode e reabri-lo do Visual Studio para Mac. Se a imagem ainda não estiver disponível, certifique-se de que seu **ação de compilação** é `BundleResource` e que a imagem foi adicionada para o **recursos** pasta.

## <a name="using-images-in-c-code"></a>Usando imagens em código c#

Ao carregar uma imagem na memória usando código c# em seu aplicativo xamarin. Mac, a imagem será armazenada em um `NSImage` objeto. Se o arquivo de imagem foi incluído no pacote do aplicativo xamarin. MAC (incluído no recursos), use o seguinte código para carregar a imagem:

```csharp
NSImage image = NSImage.ImageNamed("tags.png");
```

O código acima usa estático `ImageNamed("...")` método da `NSImage` classe para carregar a imagem fornecida na memória a partir o **recursos** pasta, se a imagem não for encontrada, `null` será retornado. Se você tiver incluído, como imagens atribuídas no Interface Builder, um **@2x** versão do seu arquivo de imagem, ele será automaticamente usado na tela retina com base em Macs.

Para carregar imagens fora do pacote do aplicativo (a partir do sistema de arquivos do Mac), use o seguinte código:

```csharp
NSImage image = new NSImage("/Users/KMullins/Documents/photo.jpg")
```

<a name="Working-with-Template-Images"/>

## <a name="working-with-template-images"></a>Trabalhando com imagens de modelo

Com base no design do seu aplicativo macOS, pode haver ocasiões em que você precisa para personalizar um ícone ou uma imagem dentro de Interface do usuário para corresponder a uma alteração no esquema de cores (por exemplo, com base nas preferências do usuário).

Para obter esse efeito, alterne o _modo de renderização_ do seu ativo de imagem para **imagem de modelo**:

[![Definindo uma imagem de modelo](image-images/templateimage01.png "definindo uma imagem de modelo")](image-images/templateimage01-large.png#lightbox)

Partir Interface Builder do Xcode, atribua o ativo de imagem a um controle de interface do usuário:

![Selecionar uma imagem no Interface Builder do Xcode](image-images/templateimage02.png "selecionando uma imagem no Interface Builder do Xcode")

Ou, opcionalmente, defina a origem da imagem no código:

```csharp
MyIcon.Image = NSImage.ImageNamed ("MessageIcon");
```

Adicione a seguinte função pública ao seu controlador de exibição:

```csharp
public NSImage ImageTintedWithColor (NSImage image, NSColor tint)
{
    var tintedImage = image.Copy () as NSImage;
    var frame = new CGRect (0, 0, image.Size.Width, image.Size.Height);

    // Apply tint
    tintedImage.LockFocus ();
    tint.Set ();
    NSGraphics.RectFill (frame, NSCompositingOperation.SourceAtop);
    tintedImage.UnlockFocus ();
    tintedImage.Template = false;

    // Return tinted image
    return tintedImage;
}
```

Por fim, para uma imagem de modelo de tom, chame essa função em relação à imagem para colorir:

```csharp
MyIcon.Image = ImageTintedWithColor (MyIcon.Image, NSColor.Red);
```

<a name="Using_Images_with_Table_Views" />

## <a name="using-images-with-table-views"></a>Uso de imagens com modos de exibição de tabela

Para incluir uma imagem como parte da célula em uma `NSTableView`, você precisará alterar como os dados são retornados pela exibição de tabela `NSTableViewDelegate's` `GetViewForItem` método a ser usado um `NSTableCellView` em vez de típico `NSTextField`. Por exemplo:

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();
        if (tableColumn.Title == "Product") {
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
        } else {
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
        }
        view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
        view.AddSubview (view.TextField);
        view.Identifier = tableColumn.Title;
        view.TextField.BackgroundColor = NSColor.Clear;
        view.TextField.Bordered = false;
        view.TextField.Selectable = false;
        view.TextField.Editable = true;

        view.TextField.EditingEnded += (sender, e) => {

            // Take action based on type
            switch(view.Identifier) {
            case "Product":
                DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
                break;
            case "Details":
                DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
                break; 
            }
        };
    }

    // Tag view
    view.TextField.Tag = row;

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed ("tags.png");
        view.TextField.StringValue = DataSource.Products [(int)row].Title;
        break;
    case "Details":
        view.TextField.StringValue = DataSource.Products [(int)row].Description;
        break;
    }

    return view;
}
```

Há algumas linhas de interesse aqui. Primeiro, para colunas que queremos incluir uma imagem, criamos um novo `NSImageView` do tamanho necessário e local, também criamos um novo `NSTextField` e coloque sua posição padrão com base em se ou não estamos usando uma imagem:

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

Em segundo lugar, precisamos incluir o novo modo de exibição de imagem e o campo de texto no pai `NSTableCellView`:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

Por fim, precisamos informar ao campo de texto que pode reduzir e crescer junto com a célula de exibição de tabela:

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

Saída de exemplo:

[![Um exemplo de como exibir uma imagem em um aplicativo](image-images/tables01.png "um exemplo de como exibir uma imagem em um aplicativo")](image-images/tables01-large.png#lightbox)

Para obter mais informações sobre como trabalhar com modos de exibição de tabela, consulte nosso [modos de exibição de tabela](~/mac/user-interface/table-view.md) documentação.

<a name="Using_Images_with_Outline_Views" />

## <a name="using-images-with-outline-views"></a>Uso de imagens com modos de exibição de estrutura de tópicos

Para incluir uma imagem como parte da célula em uma `NSOutlineView`, você precisará alterar como os dados são retornados pela exibição de estrutura de tópicos `NSTableViewDelegate's` `GetView` método a ser usado um `NSTableCellView` em vez de típico `NSTextField`. Por exemplo:

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)outlineView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();
        if (tableColumn.Title == "Product") {
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
        } else {
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
        }
        view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
        view.AddSubview (view.TextField);
        view.Identifier = tableColumn.Title;
        view.TextField.BackgroundColor = NSColor.Clear;
        view.TextField.Bordered = false;
        view.TextField.Selectable = false;
        view.TextField.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.TextField.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.TextField.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.TextField.StringValue;
            break;
        case "Details":
            prod.Description = view.TextField.StringValue;
            break; 
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed (product.IsProductGroup ? "tags.png" : "tag.png");
        view.TextField.StringValue = product.Title;
        break;
    case "Details":
        view.TextField.StringValue = product.Description;
        break;
    }

    return view;
}
```

Há algumas linhas de interesse aqui. Primeiro, para colunas que queremos incluir uma imagem, criamos um novo `NSImageView` do tamanho necessário e local, também criamos um novo `NSTextField` e coloque sua posição padrão com base em se ou não estamos usando uma imagem:

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

Em segundo lugar, precisamos incluir o novo modo de exibição de imagem e o campo de texto no pai `NSTableCellView`:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

Por fim, precisamos informar ao campo de texto que pode reduzir e crescer junto com a célula de exibição de tabela:

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

Saída de exemplo:

[![Um exemplo de uma imagem que está sendo exibida em uma exibição de estrutura de tópicos](image-images/outline01.png "um exemplo de uma imagem que está sendo exibida em uma exibição de estrutura de tópicos")](image-images/outline01-large.png#lightbox)

Para obter mais informações sobre como trabalhar com os modos de exibição, consulte nosso [modos de exibição de estrutura de tópicos](~/mac/user-interface/outline-view.md) documentação.


## <a name="summary"></a>Resumo

Este artigo apresentou uma visão detalhada de como trabalhar com imagens e ícones em um aplicativo xamarin. Mac. Podemos viu os diferentes tipos e usos de imagens, como usar imagens e ícones no Interface Builder do Xcode e como trabalhar com imagens e ícones em código c#.



## <a name="related-links"></a>Links relacionados

- [MacImages (amostra)](https://developer.xamarin.com/samples/mac/MacImages/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Modos de exibição de tabela](~/mac/user-interface/table-view.md)
- [Modos de exibição de estrutura de tópicos](~/mac/user-interface/outline-view.md)
- [macOS X diretrizes de Interface Humana](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [Sobre a alta resolução para OS X](https://developer.apple.com/library/content/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Introduction/Introduction.html)
