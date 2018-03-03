---
title: Imagens
description: "Este artigo aborda o trabalho com imagens e ícones em um aplicativo Xamarin.Mac. Ele descreve a criação e manutenção de imagens necessárias para criar o ícone do aplicativo e usar imagens em código c# e o construtor de Interface do Xcode."
ms.topic: article
ms.prod: xamarin
ms.assetid: 675B9405-D9A7-49F0-94AD-417F10A71D11
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/15/2017
ms.openlocfilehash: f12b2af0c9325796db63fcd65af135f54277ece0
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="images"></a>Imagens

_Este artigo aborda o trabalho com imagens e ícones em um aplicativo Xamarin.Mac. Ele descreve a criação e manutenção de imagens necessárias para criar o ícone do aplicativo e usar imagens em código c# e o construtor de Interface do Xcode._


## <a name="overview"></a>Visão geral

Ao trabalhar com c# e .NET em um aplicativo de Xamarin.Mac, você tem acesso para a mesma imagem e ícone ferramentas que um desenvolvedor trabalhando em *Objective-C* e *Xcode* does.

Há várias maneiras de imagem ativos são usados dentro de um aplicativo macOS (anteriormente conhecido como o Mac OS X). De simplesmente exibir uma imagem como parte da interface do usuário do seu aplicativo, atribuí-la a um controle de interface do usuário, como uma barra de ferramentas ou Item de lista de origem, ao fornecer ícones, Xamarin.Mac torna mais fácil adicionar arte excelente para seus aplicativos macOS das seguintes maneiras : 

- **Elementos de interface do usuário** -imagens podem ser exibidas como planos de fundo ou como parte do seu aplicativo em um modo de exibição de imagem (`NSImageView`).
- **Botão** -imagens podem ser exibidas nos botões (`NSButton`).
- **Imagem da célula** - como parte de um controle de tabela com base em (`NSTableView` ou `NSOutlineView`), imagens podem ser usadas em uma célula de imagem (`NSImageCell`).
- **Item de barra de ferramentas** -imagens podem ser adicionadas à barra de ferramentas (`NSToolbar`) como um Item da barra de ferramentas de imagem (`NSToolbarItem`).
- **Ícone de lista de fonte** - como parte de uma lista de origem (especialmente formatado `NSOutlineView`).
- **Ícone do aplicativo** -uma série de imagens pode ser agrupada em um `.icns` definido e usado como ícone do aplicativo. Consulte nossa [ícone do aplicativo](~/mac/deploy-test/app-icon.md) documentação para obter mais informações.

Além disso, macOS fornece um conjunto de imagens predefinidas que podem ser usados em todo o aplicativo.

[![Um exemplo de execução de aplicativo](image-images/intro01.png "um exemplo de execução de aplicativo")](image-images/intro01-large.png)

Neste artigo, vamos abordar os fundamentos de trabalhar com imagens e ícones em um aplicativo Xamarin.Mac. É altamente recomendável que você leia o [Hello, Mac](~/mac/get-started/hello-mac.md) artigo primeiro, especificamente o [Introdução ao construtor da Interface e Xcode](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [tomadas e ações](~/mac/get-started/hello-mac.md#Outlets_and_Actions) seções, como ele aborda os principais conceitos e técnicas que será usado neste artigo.


## <a name="adding-images-to-a-xamarinmac-project"></a>Adicionando imagens a um projeto de Xamarin.Mac

Ao adicionar uma imagem para uso em um aplicativo de Xamarin.Mac, há vários locais e maneiras que o desenvolvedor pode incluir o arquivo de imagem à origem do projeto:

- **Árvore de projeto principal [substituído]** -imagens podem ser adicionadas diretamente à árvore de projetos. Ao chamar imagens armazenadas na árvore do projeto principal do código, nenhum local de pasta é especificado. Por exemplo: `NSImage image = NSImage.ImageNamed("tags.png");`. 
- **Pasta de recursos [substituída]** -especiais **recursos** pasta é para qualquer arquivo que se tornem parte do aplicativo do pacote, como o ícone de tela iniciar ou geral imagens (ou qualquer outro arquivo de imagem ou o desenvolvedor deseja adicionar). Ao chamar imagens armazenadas no **recursos** pasta de código, como imagens armazenadas na árvore do projeto principal, nenhum local de pasta é especificado. Por exemplo: `NSImage.ImageNamed("tags.png")`.
- **Personalizado pasta ou subpasta [substituído]** -o desenvolvedor pode adicionar uma pasta personalizada à árvore de origem de projetos e armazenar as imagens lá. O local onde o arquivo é adicionado pode ser aninhado em uma subpasta para ajudar a organizar o projeto ainda mais. Por exemplo, se o desenvolvedor adicionou um `Card` pasta para o projeto e uma subpasta de `Hearts` nessa pasta, em seguida, armazene uma imagem **Jack.png** no `Hearts` pasta `NSImage.ImageNamed("Card/Hearts/Jack.png")` carrega a imagem em tempo de execução.
- **Conjuntos de imagem de catálogo Asset [preferencial]** - adicionado nos X El Capitan, **conjuntos de imagem de catálogos do Asset** contêm todas as versões ou representações de uma imagem que são necessárias para dar suporte a vários dispositivos e dimensionar fatores para o aplicativo. Em vez de usar o nome de arquivo de ativos de imagem (**@1x**,  **@2x** ).

<a name="asset-catalogs" />

### <a name="adding-images-to-an-asset-catalog-image-set"></a>Adicionar imagens a uma imagem de catálogo de ativos definida

Como mencionado anteriormente, um **conjuntos de imagem de catálogos do Asset** contêm todas as versões ou representações de uma imagem que são necessárias para dar suporte a vários dispositivos e dimensionar fatores para seu aplicativo. Em vez de usar o nome de arquivo de ativos de imagem (consulte as imagens de independente de resolução e nomenclatura de imagem acima), **conjuntos de imagem** usar o Editor de ativo para especificar qual imagem pertence a quais dispositivos e/ou a resolução.

1. No **solução preenchimento**, clique duas vezes o **Assets.xcassets** arquivo para abri-lo para edição: 

    ![Selecionando o Assets.xcassets](image-images/imageset01.png "selecionando o Assets.xcassets")
2. Clique com botão direito no **lista de ativos** e selecione **novo conjunto de imagem**: 

    [![Adicionando um novo conjunto de imagem](image-images/imageset02.png "adicionando um novo conjunto de imagem")](image-images/imageset02-large.png)
3. Selecione o novo conjunto de imagem e o editor será exibido: 

    [![Selecionar o novo conjunto de imagem](image-images/imageset03.png "selecionando o novo conjunto de imagem")](image-images/imageset03-large.png)
4. Aqui é possível arrastar imagens para cada um dos diferentes dispositivos e resoluções necessárias. 
5. Clique duas vezes o conjunto de imagem nova **nome** no **lista de ativos** para editá-lo: 

    [![Nome do conjunto de edição da imagem](image-images/imageset04.png "nome do conjunto de edição da imagem")](image-images/imageset04-large.png)
    
Um especial **vetor** classe foi adicionada para **conjuntos de imagem** que nos permite incluir um _PDF_ formatado de imagem de vetor no casset em vez disso, incluindo arquivos de bitmap individuais no as resoluções diferentes. Usando esse método, você fornece um arquivo único vetor para o  **@1x**  resolução (formatada como um arquivo PDF de vetor) e o  **@2x**  e  **@3x**  versões do arquivo serão geradas em tempo de compilação e incluídas no pacote do aplicativo.

[![A imagem de definir a interface de editor](image-images/imageset05.png "a imagem de definir a interface de editor")](image-images/imageset05-large.png)

Por exemplo, se você incluir um `MonkeyIcon.pdf` arquivo como o vetor de um catálogo de ativos com uma resolução de 150px x 150px, o bitmap seguinte ativos seriam incluídos no pacote de aplicativo final quando ele foi compilado:

1. **MonkeyIcon@1x.png** -150px x 150px resolução.
2. **MonkeyIcon@2x.png** -300px x 300px resolução.
3. **MonkeyIcon@3x.png** -450px x 450px resolução.

O seguinte deve ser levado em consideração ao usar imagens de vetor PDF em catálogos do Asset:

- Isso não é completa de vetor de suporte, pois o PDF será rasterizado para um bitmap em tempo de compilação e os bitmaps fornecidos no final do aplicativo.
- Você não pode ajustar o tamanho da imagem quando foi definido no catálogo de ativos. Se você tentar redimensionar a imagem (ou no código ou usando Classes de tamanho e Layout automático) a imagem é distorcida assim como qualquer outro bitmap.

Ao usar um **imagem definida** no construtor de Interface do Xcode, basta selecionar o nome do conjunto na lista suspensa no **Inspetor de atributo**: * *

![Selecionar uma imagem definida no construtor de Interface do Xcode](image-images/imageset06.png "selecionar uma imagem definida no construtor de Interface do Xcode")

<a name="Adding-new-Assets-Collections"/>

### <a name="adding-new-assets-collections"></a>Adicionando novas coleções de ativos

Ao trabalhar com imagens em catálogos de ativos pode haver vezes quando você deseja criar uma nova coleção, em vez de adicionar todas as imagens para o **Assets.xcassets** coleção. Por exemplo, durante a criação de recursos sob demanda.

Para adicionar um novo catálogo de ativos ao seu projeto:

1. Clique com botão direito no projeto no **solução preenchimento** e selecione **adicionar** > **novo arquivo...**
2. Selecione **Mac** > **catálogo de ativos**, insira um **nome** para a coleção e clique no **novo** botão: 

    ![Adicionar um novo catálogo de ativos](image-images/asset01.png "adicionando um novo catálogo de ativos")

Aqui você pode trabalhar com a coleção da mesma maneira como o padrão **Assets.xcassets** coleção automaticamente incluída no projeto.


### <a name="adding-images-to-resources"></a>Adicionando imagens para recursos

> [!IMPORTANT]
> Esse método de trabalhar com imagens em um aplicativo macOS foi substituído pela Apple. Você deve usar [conjuntos de imagem do catálogo de ativos](#asset-catalogs) manager do seu aplicativo imagens em vez disso.

Antes de usar um arquivo de imagem em seu aplicativo Xamarin.Mac (ou em código c# ou do construtor de Interface) precisa ser incluído no projeto de **recursos** pasta como um **recursos do pacote**. Para adicionar um arquivo a um projeto, faça o seguinte:

1. Com o botão direito no **recursos** pasta em seu projeto no **solução preenchimento** e selecione **adicionar** > **adicionar arquivos...** : 

    ![Adicionar um arquivo](image-images/add01.png "adicionar um arquivo")
2. Do **adicionar arquivos** caixa de diálogo, selecione as imagens de arquivos para adicionar ao projeto, selecione `BundleResource` para o **ação de compilação de substituição** e clique no **abrir** botão:

    [![Selecionar arquivos para adicionar](image-images/add02.png "selecionar arquivos para adicionar")](image-images/add02-large.png)
3. Se os arquivos já não estiverem no **recursos** pasta, será perguntado se deseja **cópia**, **mover** ou **Link** os arquivos. Escolha qual todos os conjuntos de suas necessidades, normalmente que serão **cópia**:

    ![Selecionando a ação Adicionar](image-images/add04.png "selecionando a ação Adicionar")
4. Os novos arquivos serão incluídos no projeto e ler para uso: 

    ![Os novos arquivos de imagem adicionados ao painel de solução](image-images/add03.png "os novos arquivos de imagem adicionados ao painel de solução")
5. Repita o processo para os arquivos de imagem necessários.

Você pode usar qualquer png, jpg ou arquivo pdf como uma imagem de origem em seu aplicativo Xamarin.Mac. Na próxima seção, vamos examinar adicionar versões de alta resolução de nosso imagens e ícones para dar suporte a Retina com base Macs.

> [!IMPORTANT]
> Se você estiver adicionando imagens para o **recursos** pasta, você pode deixar o **ação de compilação de substituição** definida como **padrão**. O padrão é de ação de compilação para esta pasta `BundleResource`.

## <a name="provide-high-resolution-versions-of-all-app-graphics-resources"></a>Fornecer versões em alta resolução de todos os recursos de elementos gráficos do aplicativo

Qualquer gráfico ativo que você adiciona a um aplicativo de Xamarin.Mac (ícones, controles personalizados, cursores personalizados, arte final personalizado, etc.) precisam ter versões em alta resolução, além de suas versões de resolução padrão. Isso é necessário para que seu aplicativo será a aparência melhor quando executados em uma exibição de Retina equipado com o computador Mac.


### <a name="adopt-the-2x-naming-convention"></a>Adotar o @2x convenção de nomenclatura

> [!IMPORTANT]
> Esse método de trabalhar com imagens em um aplicativo macOS foi substituído pela Apple. Você deve usar [conjuntos de imagem do catálogo de ativos](#asset-catalogs) manager do seu aplicativo imagens em vez disso.

Quando você cria as versões de uma imagem padrão e de alta resolução, siga a convenção de nomenclatura para o par de imagem ao incluí-los em seu projeto de Xamarin.Mac:

- **Resolução padrão**  - **ImageName.filename extensão** (exemplo: **tags.png**)
- **Alta resolução**   -   **ImageName@2x.filename-extension**  (exemplo:  **tags@2x.png** )

Quando adicionada a um projeto, eles seriam exibida da seguinte maneira:

![Os arquivos de imagem no painel de solução](image-images/add03.png "os arquivos de imagem no painel de solução")

Quando uma imagem é atribuída a um elemento de interface do usuário no construtor de Interface simplesmente verá o arquivo de _ImageName_**.** _extensão de nome de arquivo_ formato (exemplo: **tags.png**). O mesmo para usar uma imagem no código c#, você verá o arquivo de _ImageName_**.** _extensão de nome de arquivo_ formato.

Quando você Xamarin.Mac aplicativo é executado em um Mac, o _ImageName_**.** _extensão de nome de arquivo_ formato imagem será usada em exibições de resolução padrão, o  **ImageName@2x.filename-extension**  imagem será selecionada automaticamente exibição de Retina bases Macs.


## <a name="using-images-in-interface-builder"></a>Usando imagens no construtor de Interface

Qualquer recurso de imagem que você adicionou ao **recursos** pasta no seu Xamarin.Mac projeto e tiver definido a ação de compilação **BundleResource** será automaticamente exibida no construtor de Interface e pode ser selecionados como parte de um elemento de interface do usuário (se trata de imagens).

Para usar uma imagem no construtor de interface, faça o seguinte:

1. Adicionar uma imagem para o **recursos** pasta com um **ação de compilação** de `BundleResource`: 

     ![Um recurso de imagem no painel de solução](image-images/ib00.png "um recurso de imagem no painel de solução")
2. Clique duas vezes o **Main.storyboard** arquivo para abri-lo para edição no construtor de Interface: 

     [![Editando o storyboard principal](image-images/ib01.png "editando o storyboard principal")](image-images/ib01-large.png)
3. Arraste um elemento de interface do usuário que usa imagens para a superfície de design (por exemplo, um **Item da barra de ferramentas de imagem**): 

     ![Editando um item da barra de ferramentas](image-images/ib02.png "editando um item da barra de ferramentas")
4. Selecione a imagem que você adicionou ao **recursos** pasta o **nome da imagem** suspensa: 

     [![Selecionar uma imagem para um item de barra de ferramentas](image-images/ib03.png "selecionar uma imagem para um item de barra de ferramentas")](image-images/ib03-large.png)
5. A imagem selecionada será exibida na superfície de design: 

     ![A imagem que está sendo exibida no editor de barra de ferramentas](image-images/ib04.png "a imagem que está sendo exibida no editor de barra de ferramentas")
6. Salve suas alterações e retornar ao Visual Studio para Mac sincronizar com o Xcode.

As etapas acima funcionam para qualquer elemento de interface do usuário que permite que a propriedade de imagem a ser definido **Inspetor de atributo**. Novamente, se você incluiu um  **@2x**  versão do seu arquivo de imagem, ela será automaticamente usada na exibição de Retina com base em Macs.

> [!IMPORTANT]
> Se a imagem não está disponível na **nome da imagem** suspenso, feche seu projeto .storyboard no Xcode e reabra-o no Visual Studio para Mac. Se a imagem ainda não estiver disponível, certifique-se de que seu **ação de compilação** é `BundleResource` e que a imagem foi adicionada para o **recursos** pasta.

## <a name="using-images-in-c-code"></a>Usando imagens em código c#

Ao carregar uma imagem na memória usando o código c# em seu aplicativo Xamarin.Mac, a imagem será armazenada em um `NSImage` objeto. Se o arquivo de imagem foi incluído no pacote do aplicativo Xamarin.Mac (incluído em recursos), use o seguinte código para carregar a imagem:

```csharp
NSImage image = NSImage.ImageNamed("tags.png");
```

O código acima usa estático `ImageNamed("...")` método o `NSImage` classe para carregar a imagem fornecida na memória do **recursos** pasta, se a imagem não pode ser encontrada, `null` será retornado. Como imagens atribuídas no construtor de Interface, se você incluiu um  **@2x**  versão do seu arquivo de imagem, ela será automaticamente usada na exibição Retina com base em Macs.

Para carregar imagens fora do pacote do aplicativo (Mac do sistema de arquivos), use o seguinte código:

```csharp
NSImage image = new NSImage("/Users/KMullins/Documents/photo.jpg")
```

<a name="Working-with-Template-Images"/>

## <a name="working-with-template-images"></a>Trabalhando com imagens de modelo

Com base no design do seu aplicativo macOS, pode haver ocasiões em que você precisa para personalizar uma imagem dentro da Interface do usuário para corresponder a uma alteração no esquema de cores (por exemplo, com base nas preferências do usuário) ou um ícone.

Para obter esse efeito, alterne o _modo de renderização_ do seu ativo de imagem para **imagem de modelo**:

[![Definindo uma imagem de modelo](image-images/templateimage01.png "definindo uma imagem de modelo")](image-images/templateimage01-large.png)

No construtor de Interface do Xcode, de atribuir o ativo de imagem a um controle de interface do usuário:

![Selecionar uma imagem no construtor de Interface do Xcode](image-images/templateimage02.png "selecionar uma imagem no construtor de Interface do Xcode")

Ou, opcionalmente, defina a origem da imagem no código:

```csharp
MyIcon.Image = NSImage.ImageNamed ("MessageIcon");
```

Adicione a seguinte função pública para o seu controlador de exibição:

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

## <a name="using-images-with-table-views"></a>Usando imagens com modos de exibição de tabela

Para incluir uma imagem como parte da célula de uma `NSTableView`, você precisará alterar como os dados são retornados pela exibição de tabela `NSTableViewDelegate's` `GetViewForItem` método para usar um `NSTableCellView` em vez do típico `NSTextField`. Por exemplo:

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

Há algumas linhas de interesse aqui. Primeiro, para as colunas que você deseja incluir uma imagem, criamos um novo `NSImageView` do tamanho necessário e local, também criamos um novo `NSTextField` e colocar sua posição padrão com base em se estamos usando uma imagem:

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

Em segundo lugar, é necessário incluir a nova imagem de exibição e campo de texto no pai `NSTableCellView`:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

Por fim, é necessário informar o campo de texto que pode reduzir e aumentar com a célula de exibição de tabela:

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

Saída de exemplo:

[![Um exemplo de exibição de uma imagem em um aplicativo](image-images/tables01.png "um exemplo de exibição de uma imagem em um aplicativo")](image-images/tables01-large.png)

Para obter mais informações sobre como trabalhar com modos de exibição de tabela, consulte nosso [modos de exibição de tabela](~/mac/user-interface/table-view.md) documentação.

<a name="Using_Images_with_Outline_Views" />

## <a name="using-images-with-outline-views"></a>Usando imagens com modos de exibição de estrutura de tópicos

Para incluir uma imagem como parte da célula de uma `NSOutlineView`, você precisará alterar como os dados são retornados pela exibição de estrutura de tópicos `NSTableViewDelegate's` `GetView` método para usar um `NSTableCellView` em vez do típico `NSTextField`. Por exemplo:

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

Há algumas linhas de interesse aqui. Primeiro, para as colunas que você deseja incluir uma imagem, criamos um novo `NSImageView` do tamanho necessário e local, também criamos um novo `NSTextField` e colocar sua posição padrão com base em se estamos usando uma imagem:

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

Em segundo lugar, é necessário incluir a nova imagem de exibição e campo de texto no pai `NSTableCellView`:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

Por fim, é necessário informar o campo de texto que pode reduzir e aumentar com a célula de exibição de tabela:

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

Saída de exemplo:

[![Um exemplo de uma imagem que está sendo exibido em uma exibição de estrutura de tópicos](image-images/outline01.png "um exemplo de uma imagem que está sendo exibido em uma exibição de estrutura de tópicos")](image-images/outline01-large.png)

Para obter mais informações sobre como trabalhar com modos de exibição de estrutura de tópicos, consulte nosso [modos de exibição de estrutura de tópicos](~/mac/user-interface/outline-view.md) documentação.


## <a name="summary"></a>Resumo

Este artigo obteve uma visão detalhada de como trabalhar com imagens e ícones em um aplicativo Xamarin.Mac. Estamos viu os diferentes tipos e usos de imagens, como usar imagens e ícones no construtor de Interface do Xcode e como trabalhar com imagens e ícones no código c#.



## <a name="related-links"></a>Links relacionados

- [MacImages (exemplo)](https://developer.xamarin.com/samples/mac/MacImages/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Modos de exibição de tabela](~/mac/user-interface/table-view.md)
- [Modos de exibição de estrutura de tópicos](~/mac/user-interface/outline-view.md)
- [macOS X diretrizes de Interface Humana](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [Sobre alta resolução para OS X](https://developer.apple.com/library/content/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Introduction/Introduction.html)
