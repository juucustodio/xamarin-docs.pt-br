---
title: Imagens no Xamarin. Mac
description: Este artigo aborda como trabalhar com imagens e ícones em um aplicativo Xamarin. Mac. Ele descreve a criação e a manutenção das imagens necessárias para criar o ícone do aplicativo e usar imagens C# no interface Builder do código e do Xcode.
ms.prod: xamarin
ms.assetid: C6B539C2-FC6A-4C38-B839-32BFFB9B16A7
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/15/2017
ms.openlocfilehash: 99604b59e5557ba5a7aa3d5ba61bc1bff414f000
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770321"
---
# <a name="images-in-xamarinmac"></a>Imagens no Xamarin. Mac

_Este artigo aborda como trabalhar com imagens e ícones em um aplicativo Xamarin. Mac. Ele descreve a criação e a manutenção das imagens necessárias para criar o ícone do aplicativo e usar imagens C# no interface Builder do código e do Xcode._

## <a name="overview"></a>Visão geral

Ao trabalhar com C# o e o .net em um aplicativo Xamarin. Mac, você tem acesso às mesmas ferramentas de imagem e ícone que um desenvolvedor trabalhando no *Objective-C* e no *Xcode* .

Há várias maneiras de os ativos de imagem serem usados dentro de um aplicativo macOS (anteriormente conhecido como Mac OS X). De simplesmente exibir uma imagem como parte da interface do usuário do seu aplicativo para o, atribuí-la a um controle de interface do usuário, como uma barra de ferramentas ou um item de lista de origem, para fornecer ícones, o Xamarin. Mac facilita a adição de um grande trabalho artístico aos seus aplicativos macOS das seguintes maneiras : 

- **Elementos de interface do usuário** -as imagens podem ser exibidas como planos de fundo ou como parte de seu`NSImageView`aplicativo em uma exibição de imagem ().
- As imagens de **botão** podem ser exibidas em botões`NSButton`().
- **Célula de imagem** -como parte de um controle baseado em`NSTableView` tabela `NSOutlineView`(ou), as imagens podem ser usadas em uma`NSImageCell`célula de imagem ().
- **Item de barra de ferramentas** -imagens podem ser adicionadas`NSToolbar`a uma barra de ferramentas ()`NSToolbarItem`como um item de barra de ferramentas de imagem ().
- **Ícone da lista de origem** – como parte de uma lista de origem ( `NSOutlineView`especialmente formatada).
- **Ícone do aplicativo** – uma série de imagens pode ser agrupada em um `.icns` conjunto e usada como o ícone do seu aplicativo. Consulte a documentação do [ícone do aplicativo](~/mac/deploy-test/app-icon.md) para obter mais informações.

Além disso, o macOS fornece um conjunto de imagens predefinidas que podem ser usadas em todo o seu aplicativo.

[![Um exemplo de execução do aplicativo](image-images/intro01.png "Um exemplo de execução do aplicativo")](image-images/intro01-large.png#lightbox)

Neste artigo, abordaremos as noções básicas de como trabalhar com imagens e ícones em um aplicativo Xamarin. Mac. É altamente recomendável que você trabalhe pelo artigo [Hello, Mac](~/mac/get-started/hello-mac.md) primeiro, especificamente a [introdução às seções Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [ações](~/mac/get-started/hello-mac.md#outlets-and-actions) , pois ela aborda os principais conceitos e técnicas que usaremos em Este artigo.

## <a name="adding-images-to-a-xamarinmac-project"></a>Adicionando imagens a um projeto Xamarin. Mac

Ao adicionar uma imagem para uso em um aplicativo Xamarin. Mac, há vários locais e maneiras pelas quais o desenvolvedor pode incluir o arquivo de imagem na fonte do projeto:

- **Árvore de projeto principal [preterido]** -as imagens podem ser adicionadas diretamente à árvore de projetos. Ao chamar imagens armazenadas na árvore de projeto principal do código, nenhum local de pasta é especificado. Por exemplo: `NSImage image = NSImage.ImageNamed("tags.png");`. 
- **Pasta de recursos [preterido]** – a pasta de **recursos** especiais é para qualquer arquivo que se tornará parte do pacote do aplicativo, como ícone, tela de inicialização ou imagens gerais (ou qualquer outra imagem ou arquivo que o desenvolvedor deseja adicionar). Ao chamar imagens armazenadas na pasta de **recursos** do código, assim como as imagens armazenadas na árvore de projeto principal, nenhum local de pasta é especificado. Por exemplo: `NSImage.ImageNamed("tags.png")`.
- **Pasta personalizada ou subpasta [preterido]** -o desenvolvedor pode adicionar uma pasta personalizada à árvore de origem de projetos e armazenar as imagens nela. O local onde o arquivo é adicionado pode ser aninhado em uma subpasta para ajudar a organizar o projeto. Por exemplo, se o desenvolvedor adicionou uma `Card` pasta ao projeto e uma subpasta do `Hearts` a essa pasta, armazene um conector de imagem **. png** na `Hearts` pasta, `NSImage.ImageNamed("Card/Hearts/Jack.png")` carregaria a imagem em tempo de execução.
- **Conjuntos de imagens do catálogo de ativos [preferencial]** -adicionado no os X El Capitan, os **conjuntos de imagens de catálogos de ativos** contêm todas as versões ou representações de uma imagem que são necessárias para dar suporte a vários dispositivos e fatores de escala para seu aplicativo. Em vez de depender do nome de arquivo de ativos **@1x** de **@2x** imagem (,).

<a name="asset-catalogs" />

### <a name="adding-images-to-an-asset-catalog-image-set"></a>Adicionando imagens a um conjunto de imagens do catálogo de ativos

Como mencionado acima, os **conjuntos de imagens de catálogos de ativos** contêm todas as versões ou representações de uma imagem que são necessárias para dar suporte a vários dispositivos e fatores de escala para seu aplicativo. Em vez de contar com o nome de arquivo de ativos de imagem (consulte as imagens independentes de resolução e a imagem nomenclatura acima), os **conjuntos de imagens** usam o editor de ativos para especificar qual imagem pertence a qual dispositivo e/ou resolução.

1. No **painel de soluções**, clique duas vezes no arquivo **assets. xcassets** para abri-lo para edição: 

    ![Selecionando os ativos. xcassets](image-images/imageset01.png "Selecionando os ativos. xcassets")
2. Clique com o botão direito do mouse na **lista ativos** e selecione **novo conjunto de imagens**: 

    [![Adicionando um novo conjunto de imagens](image-images/imageset02.png "Adicionando um novo conjunto de imagens")](image-images/imageset02-large.png#lightbox)
3. Selecione o novo conjunto de imagens e o editor será exibido: 

    [![Selecionando o novo conjunto de imagens](image-images/imageset03.png "Selecionando o novo conjunto de imagens")](image-images/imageset03-large.png#lightbox)
4. A partir daqui, podemos arrastar imagens para cada um dos diferentes dispositivos e resoluções necessários. 
5. Clique duas vezes no **nome** do novo conjunto de imagens na **lista ativos** para editá-lo: 

    [![Editando o nome do conjunto de imagens](image-images/imageset04.png "Editando o nome do conjunto de imagens")](image-images/imageset04-large.png#lightbox)
    
Uma classe de **vetor** especial, como adicionada a **conjuntos de imagens** que nos permite incluir uma imagem de vetor formatada em _PDF_ no Casset, em vez disso, incluindo arquivos de bitmap individuais em resoluções diferentes. Usando esse método, você fornece um único arquivo de vetor para **@1x** a resolução (formatado como um arquivo PDF vetorial) e **@2x** as **@3x** versões e do arquivo serão geradas no momento da compilação e incluídas no pacote do aplicativo.

[![A interface do editor de conjunto de imagens](image-images/imageset05.png "A interface do editor de conjunto de imagens")](image-images/imageset05-large.png#lightbox)

Por exemplo, se você incluir um `MonkeyIcon.pdf` arquivo como o vetor de um catálogo de ativos com uma resolução de 150px x 150px, os seguintes ativos de bitmap seriam incluídos no pacote de aplicativo final quando ele foi compilado:

1. **MonkeyIcon@1x.png** -resolução de 150px x 150px.
2. **MonkeyIcon@2x.png** -resolução de 300px x 300px.
3. **MonkeyIcon@3x.png** -resolução de 450px x 450px.

O seguinte deve ser levado em consideração ao usar imagens de vetor de PDF em catálogos de ativos:

- Esse não é o suporte de vetor completo, pois o PDF será rasterizado para um bitmap em tempo de compilação e os bitmaps enviados no aplicativo final.
- Você não pode ajustar o tamanho da imagem depois que ela tiver sido definida no catálogo de ativos. Se você tentar redimensionar a imagem (no código ou usando o layout automático e as classes de tamanho), a imagem será distorcida assim como qualquer outro bitmap.

Ao usar um **conjunto de imagens** no interface Builder do Xcode, você pode simplesmente selecionar o nome do conjunto na lista suspensa no **Inspetor de atributo**:

![Selecionando um conjunto de imagens no interface Builder do Xcode](image-images/imageset06.png "Selecionando um conjunto de imagens no interface Builder do Xcode")

<a name="Adding-new-Assets-Collections"/>

### <a name="adding-new-assets-collections"></a>Adicionando novas coleções de ativos

Ao trabalhar com imagens em catálogos de ativos, pode haver ocasiões em que você deseja criar uma nova coleção, em vez de adicionar todas as suas imagens à coleção **assets. xcassets** . Por exemplo, ao criar recursos sob demanda.

Para adicionar um novo catálogo de ativos ao seu projeto:

1. Clique com o botão direito do mouse no projeto na **painel de soluções** e selecione **Adicionar** > **novo arquivo...**
2. Selecione**Catálogo de ativos**do **Mac** > , insira um **nome** para a coleção e clique no botão **novo** : 

    ![Adicionando um novo catálogo de ativos](image-images/asset01.png "Adicionando um novo catálogo de ativos")

A partir daqui, você pode trabalhar com a coleção da mesma maneira que a coleção default **assets. xcassets** incluída automaticamente no projeto.

### <a name="adding-images-to-resources"></a>Adicionando imagens a recursos

> [!IMPORTANT]
> Esse método de trabalhar com imagens em um aplicativo macOS foi preterido pela Apple. Você deve usar os [conjuntos de imagens do catálogo de ativos](#asset-catalogs) para modelar as imagens do aplicativo.

Antes de usar um arquivo de imagem em seu aplicativo Xamarin. Mac (no C# código ou a partir de interface Builder), ele precisa ser incluído na pasta **recursos** do projeto como um **recurso de pacote**. Para adicionar um arquivo a um projeto, faça o seguinte:

1. Clique com o botão direito do mouse na pasta **recursos** em seu projeto na **painel de soluções** e selecione **Adicionar** > **Adicionar arquivos...** : 

    ![Adicionando um arquivo](image-images/add01.png "Adicionando um arquivo")
2. Na caixa de diálogo **Adicionar arquivos** , selecione os arquivos de imagens a serem adicionados ao projeto, `BundleResource` selecione para a **ação substituir compilação** e clique no botão **abrir** :

    [![Selecionando os arquivos a serem adicionados](image-images/add02.png "Selecionando os arquivos a serem adicionados")](image-images/add02-large.png#lightbox)
3. Se os arquivos ainda não estiverem na pasta **recursos** , você será perguntado se deseja **copiar**, **mover** ou **vincular** os arquivos. Escolha o que cada um atende às suas necessidades, normalmente, que será **cópia**:

    ![Selecionando a ação adicionar](image-images/add04.png "Selecionando a ação adicionar")
4. Os novos arquivos serão incluídos no projeto e lidos para uso: 

    ![Os novos arquivos de imagem adicionados à painel de soluções](image-images/add03.png "Os novos arquivos de imagem adicionados à painel de soluções")
5. Repita o processo para todos os arquivos de imagem necessários.

Você pode usar qualquer arquivo PNG, jpg ou PDF como uma imagem de origem em seu aplicativo Xamarin. Mac. Na próxima seção, veremos como adicionar versões de alta resolução de nossas imagens e ícones para dar suporte a Macs baseados em retina.

> [!IMPORTANT]
> Se você estiver adicionando imagens à pasta **recursos** , poderá deixar a **ação substituir compilação** definida como **padrão**. A ação de compilação padrão para essa pasta `BundleResource`é.

## <a name="provide-high-resolution-versions-of-all-app-graphics-resources"></a>Fornecer versões de alta resolução de todos os recursos de gráficos do aplicativo

Qualquer ativo gráfico que você adiciona a um aplicativo Xamarin. Mac (ícones, controles personalizados, cursores personalizados, arte personalizada, etc.) precisa ter versões de alta resolução, além de suas versões de resolução padrão. Isso é necessário para que seu aplicativo tenha a melhor aparência quando executado em um computador Mac equipado com vídeo de retina.

### <a name="adopt-the-2x-naming-convention"></a>Adote a @2x Convenção de nomenclatura

> [!IMPORTANT]
> Esse método de trabalhar com imagens em um aplicativo macOS foi preterido pela Apple. Você deve usar os [conjuntos de imagens do catálogo de ativos](#asset-catalogs) para modelar as imagens do aplicativo.

Quando você cria as versões padrão e de alta resolução de uma imagem, siga esta Convenção de nomenclatura para o par de imagens ao incluí-los em seu projeto do Xamarin. Mac:

- **Padrão-resolução**  - **ImageName. FileName-Extension** (exemplo: **Tags. png**)
- **Alta resolução**   -  **tags@2x.png** (**exemplo:)ImageName@2x.filename-extension**

Quando adicionados a um projeto, eles seriam exibidos da seguinte maneira:

![Os arquivos de imagem no painel de soluções](image-images/add03.png "Os arquivos de imagem no painel de soluções")

Quando uma imagem é atribuída a um elemento de interface do usuário no Interface Builder você simplesmente escolhe o arquivo no _ImageName_ **.** _nome do arquivo-_ formato da extensão (exemplo: **Tags. png**). O mesmo para usar uma imagem no C# código, você escolherá o arquivo no _ImageName_ **.** _nome do arquivo-_ formato da extensão.

Quando o aplicativo Xamarin. Mac é executado em um Mac, o _ImageName_ **.** o _nome de arquivo-imagem de_ formato de extensão será usado em exibições de resolução padrão, a **ImageName@2x.filename-extension** imagem será automaticamente selecionada na tela de exibição da retina com Macs.

## <a name="using-images-in-interface-builder"></a>Usando imagens no Interface Builder

Qualquer recurso de imagem que você adicionou à pasta **Resources** em seu projeto Xamarin. Mac e definiu a ação de Build como **BundleResource** será automaticamente exibido em interface Builder e poderá ser selecionado como parte de um elemento de interface do usuário (se ele manipular imagens).

Para usar uma imagem no construtor de interface, faça o seguinte:

1. Adicione uma imagem à pasta de **recursos** com uma **ação** de compilação `BundleResource`de: 

     ![Um recurso de imagem no painel de soluções](image-images/ib00.png "Um recurso de imagem no painel de soluções")
2. Clique duas vezes no arquivo **Main. Storyboard** para abri-lo para edição no interface Builder: 

     [![Editando o storyboard principal](image-images/ib01.png "Editando o storyboard principal")](image-images/ib01-large.png#lightbox)
3. Arraste um elemento de interface do usuário que usa imagens na superfície de design (por exemplo, um **item de barra de ferramentas de imagem**): 

     ![Editando um item da barra de ferramentas](image-images/ib02.png "Editando um item da barra de ferramentas")
4. Selecione a imagem que você adicionou à pasta **recursos** na lista suspensa **nome da imagem** : 

     [![Selecionando uma imagem para um item da barra de ferramentas](image-images/ib03.png "Selecionando uma imagem para um item da barra de ferramentas")](image-images/ib03-large.png#lightbox)
5. A imagem selecionada será exibida na superfície de design: 

     ![A imagem que está sendo exibida no editor de barra de ferramentas](image-images/ib04.png "A imagem que está sendo exibida no editor de barra de ferramentas")
6. Salve as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

As etapas acima funcionam para qualquer elemento de interface do usuário que permita que sua propriedade de imagem seja definida no **Inspetor de atributo**. Novamente, se você tiver incluído uma **@2x** versão do arquivo de imagem, ela será usada automaticamente em Macs com base em tela de retina.

> [!IMPORTANT]
> Se a imagem não estiver disponível na lista suspensa **nome da imagem** , feche o projeto. Storyboard no Xcode e reabra-o em Visual Studio para Mac. Se a imagem ainda não estiver disponível, verifique se sua ação de `BundleResource` **compilação** é e se a imagem foi adicionada à pasta **recursos** .

## <a name="using-images-in-c-code"></a>Usando imagens no C# código

Ao carregar uma imagem na memória usando C# código em seu aplicativo Xamarin. Mac, a imagem será armazenada em um `NSImage` objeto. Se o arquivo de imagem tiver sido incluído no pacote de aplicativos Xamarin. Mac (incluído nos recursos), use o seguinte código para carregar a imagem:

```csharp
NSImage image = NSImage.ImageNamed("tags.png");
```

O código acima usa o método `ImageNamed("...")` estático `NSImage` da classe para carregar a imagem fornecida na memória da pasta **recursos** , se a imagem não puder ser encontrada, `null` será retornada. Assim como as imagens atribuídas no interface Builder, se você **@2x** tiver incluído uma versão do arquivo de imagem, ela será usada automaticamente em Macs baseados em tela de retina.

Para carregar imagens fora do pacote do aplicativo (do sistema de arquivos Mac), use o seguinte código:

```csharp
NSImage image = new NSImage("/Users/KMullins/Documents/photo.jpg")
```

<a name="Working-with-Template-Images"/>

## <a name="working-with-template-images"></a>Trabalhando com imagens de modelo

Com base no design do seu aplicativo macOS, pode haver ocasiões em que você precisa personalizar um ícone ou imagem dentro da interface do usuário para corresponder a uma alteração no esquema de cores (por exemplo, com base nas preferências do usuário).

Para obter esse efeito, alterne o _modo de renderização_ do seu ativo de imagem para **imagem de modelo**:

[![Configurando uma imagem de modelo](image-images/templateimage01.png "Configurando uma imagem de modelo")](image-images/templateimage01-large.png#lightbox)

Do Interface Builder do Xcode, atribua o ativo da imagem a um controle de interface do usuário:

![Selecionando uma imagem no interface Builder do Xcode](image-images/templateimage02.png "Selecionando uma imagem no interface Builder do Xcode")

Ou, opcionalmente, definir a origem da imagem no código:

```csharp
MyIcon.Image = NSImage.ImageNamed ("MessageIcon");
```

Adicione a seguinte função pública ao controlador de exibição:

```csharp
public NSImage ImageTintedWithColor(NSImage sourceImage, NSColor tintColor)
    => NSImage.ImageWithSize(sourceImage.Size, false, rect => {
        // Draw the original source image
        sourceImage.DrawInRect(rect, CGRect.Empty, NSCompositingOperation.SourceOver, 1f);

        // Apply tint
        tintColor.Set();
        NSGraphics.RectFill(rect, NSCompositingOperation.SourceAtop);

        return true;
    });
```

> [!IMPORTANT]
> Especialmente com o advento do modo escuro no MacOS Mojave, é importante evitar a `LockFocus` API quando reating objetos renderizados `NSImage` personalizados. Essas imagens tornam-se estáticas e não serão atualizadas automaticamente para considerar a aparência ou exibir alterações de densidade.
>
> Ao empregar o mecanismo baseado em manipulador acima, a rerenderização de condições dinâmicas ocorrerá automaticamente quando `NSImage` o for hospedado, por exemplo, em `NSImageView`um.

Por fim, para colorir uma imagem de modelo, chame essa função em relação à imagem para Colorize:

```csharp
MyIcon.Image = ImageTintedWithColor (MyIcon.Image, NSColor.Red);
```

<a name="Using_Images_with_Table_Views" />

## <a name="using-images-with-table-views"></a>Usando imagens com exibições de tabela

Para incluir uma imagem como parte `NSTableView`da célula em um, você precisará alterar a forma como os dados são retornados pelo método da `NSTableViewDelegate's` `GetViewForItem` exibição de tabela para usar um `NSTableCellView` em vez do típico `NSTextField`. Por exemplo:

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

Há algumas linhas de interesse aqui. Primeiro, para colunas que desejamos incluir uma imagem, criamos um novo `NSImageView` tamanho e local necessários, também criamos uma nova `NSTextField` e colocamos sua posição padrão com base em se estamos usando ou não uma imagem:

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

Em segundo lugar, precisamos incluir o novo modo de exibição de imagem e o campo `NSTableCellView`de texto no pai:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

Por fim, precisamos dizer ao campo de texto que ele pode reduzir e aumentar com a célula de exibição de tabela:

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

Saída de exemplo:

[![Um exemplo de exibição de uma imagem em um aplicativo](image-images/tables01.png "Um exemplo de exibição de uma imagem em um aplicativo")](image-images/tables01-large.png#lightbox)

Para obter mais informações sobre como trabalhar com exibições de tabela, consulte a documentação de [exibições de tabela](~/mac/user-interface/table-view.md) .

<a name="Using_Images_with_Outline_Views" />

## <a name="using-images-with-outline-views"></a>Usando imagens com exibições de estrutura de tópicos

Para incluir uma imagem como parte `NSOutlineView`da célula em um, você precisará alterar a forma como os dados são retornados pelo método da `NSTableViewDelegate's` `GetView` exibição de estrutura de tópicos para `NSTableCellView` usar um em vez `NSTextField`do típico. Por exemplo:

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

Há algumas linhas de interesse aqui. Primeiro, para colunas que desejamos incluir uma imagem, criamos um novo `NSImageView` tamanho e local necessários, também criamos uma nova `NSTextField` e colocamos sua posição padrão com base em se estamos usando ou não uma imagem:

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

Em segundo lugar, precisamos incluir o novo modo de exibição de imagem e o campo `NSTableCellView`de texto no pai:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

Por fim, precisamos dizer ao campo de texto que ele pode reduzir e aumentar com a célula de exibição de tabela:

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

Saída de exemplo:

[![Um exemplo de uma imagem sendo exibida em um modo de exibição de estrutura de tópicos](image-images/outline01.png "Um exemplo de uma imagem sendo exibida em um modo de exibição de estrutura de tópicos")](image-images/outline01-large.png#lightbox)

Para obter mais informações sobre como trabalhar com exibições de estrutura de tópicos, consulte a documentação de [exibições de estrutura de tópicos](~/mac/user-interface/outline-view.md) .

## <a name="summary"></a>Resumo

Este artigo deu uma visão detalhada de como trabalhar com imagens e ícones em um aplicativo Xamarin. Mac. Vimos os diferentes tipos e usos de imagens, como usar imagens e ícones no Interface Builder do Xcode e como trabalhar com imagens e ícones no C# código.

## <a name="related-links"></a>Links relacionados

- [MacImages (amostra)](https://docs.microsoft.com/samples/xamarin/mac-samples/macimages)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Exibições de tabela](~/mac/user-interface/table-view.md)
- [Exibições de estrutura de tópicos](~/mac/user-interface/outline-view.md)
- [Diretrizes de interface humana para macOS X](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [Sobre a alta resolução para OS X](https://developer.apple.com/library/content/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Introduction/Introduction.html)
