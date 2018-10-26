---
title: Barras de ferramentas do xamarin. Mac
description: Este artigo descreve como trabalhar com barras de ferramentas em um aplicativo xamarin. Mac. Ele aborda as barras de ferramentas de criação e manutenção no Xcode e Interface Builder, expô-las ao código e trabalhar com eles por meio de programação.
ms.prod: xamarin
ms.assetid: C8D228CE-C860-47E1-85FD-69864BF91F20
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 6cb17ae0f60390564a8aa6bdb64ea612aae51b55
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120249"
---
# <a name="toolbars-in-xamarinmac"></a>Barras de ferramentas do xamarin. Mac

_Este artigo descreve como trabalhar com barras de ferramentas em um aplicativo xamarin. Mac. Ele aborda as barras de ferramentas de criação e manutenção no Xcode e Interface Builder, expô-las ao código e trabalhar com eles por meio de programação._

Os desenvolvedores de xamarin. Mac trabalhando com o Visual Studio para Mac têm acesso aos mesmos controles de interface do usuário disponíveis para desenvolvedores macOS trabalhando com o Xcode, incluindo o controle de barra de ferramentas. Como o xamarin. Mac se integra diretamente com Xcode, é possível usar o Interface Builder do Xcode para criar e manter itens da barra de ferramentas. Esses itens de barra de ferramentas também podem ser criados em c#.

Barras de ferramentas no macOS são adicionadas para a seção superior de uma janela e fornecem acesso fácil aos comandos relacionados à sua funcionalidade. Barras de ferramentas podem ser ocultadas, mostradas ou personalizadas pelos usuários do aplicativo, e eles podem apresentar os itens da barra de ferramentas de várias maneiras.

Este artigo aborda os fundamentos de trabalhar com as barras de ferramentas e itens de barra de ferramentas em um aplicativo xamarin. Mac. 

Antes de continuar, leia as [Hello, Mac](~/mac/get-started/hello-mac.md) artigo — especificamente a [Introdução ao Interface Builder e Xcode](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [saídas e ações](~/mac/get-started/hello-mac.md#outlets-and-actions) seções — como ela aborda os principais conceitos e técnicas que serão usadas em todo este guia.

Também dar uma olhada a [expondo c# classes / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [recursos internos de xamarin. Mac](~/mac/internals/how-it-works.md) documento. Ele explica as `Register` e `Export` atributos usados para conectar-se as classes do c# para classes de Objective-C.

## <a name="introduction-to-toolbars"></a>Introdução às barras de ferramentas

Qualquer janela em um aplicativo macOS pode incluir uma barra de ferramentas:

![Um exemplo de janela com uma barra de ferramentas](toolbar-images/info01.png "um exemplo de janela com uma barra de ferramentas")

Barras de ferramentas fornecem uma maneira fácil para os usuários do seu aplicativo para acessar rapidamente importantes ou recursos comumente usados. Por exemplo, um aplicativo de edição de documentos pode fornecer itens de barra de ferramentas para definir a cor do texto, alterando a fonte ou imprimir o documento atual.

Barras de ferramentas podem exibir itens de três maneiras:

1. **Ícone e texto** 

     ![Uma barra de ferramentas com ícones e textos](toolbar-images/info02.png "uma barra de ferramentas com ícones e texto")

2. **Ícone somente** 

     ![Uma barra de ferramentas somente ícone](toolbar-images/info03.png "uma barra de ferramentas somente ícone")

3. **Somente texto** 

     ![Uma barra de ferramentas somente de texto](toolbar-images/info04.png "uma barra de ferramentas somente de texto")

Alternar entre esses modos de barra de ferramentas do botão direito do mouse e selecionando um modo de exibição no menu contextual:

![O menu contextual para uma barra de ferramentas](toolbar-images/info05.png "menu contextual para uma barra de ferramentas")

Use o mesmo menu para exibir a barra de ferramentas em um tamanho menor:

![Uma barra de ferramentas com ícones pequenos](toolbar-images/info06.png "uma barra de ferramentas com ícones pequenos")

O menu também permite personalizar a barra de ferramentas:

![A caixa de diálogo usada para personalizar uma barra de ferramentas](toolbar-images/info07.png "a caixa de diálogo usada para personalizar uma barra de ferramentas")

Ao configurar uma barra de ferramentas no Interface Builder do Xcode, um desenvolvedor pode fornecer itens de barra de ferramentas adicionais que não fazem parte de sua configuração padrão. Os usuários do aplicativo, em seguida, podem personalizar a barra de ferramentas, adicionar e remover esses itens predefinidos conforme necessário. Obviamente, a barra de ferramentas pode ser redefinida para sua configuração padrão.

A barra de ferramentas se conecta automaticamente para o **exibição** menu, que permite que os usuários para ocultá-lo, mostrá-lo e personalizá-lo:

![Itens relacionados a barra de ferramentas no menu Exibir](toolbar-images/info08.png "itens relacionados a barra de ferramentas no menu Exibir")

Consulte a [funcionalidade interna do Menu](~/mac/user-interface/menu.md) documentação para obter mais detalhes.

Além disso, se a barra de ferramentas está configurada corretamente no construtor de Interface, o aplicativo será automaticamente persistir as personalizações da barra de ferramentas entre várias inicializações do aplicativo.

As próximas seções deste guia descrevem como criar e manter barras de ferramentas com o Interface Builder do Xcode e como trabalhar com eles no código.

## <a name="setting-a-custom-main-window-controller"></a>Configurar um controlador de janela principal personalizada

Para expor elementos de interface do usuário ao código c# por meio de saídas e ações, o aplicativo xamarin. Mac deve usar um controlador de janela personalizados:

1. Abra o storyboard do aplicativo no Interface Builder do Xcode.
2. Selecione o controlador de janela na superfície de design.
3. Alterne para o **Inspetor de identidade** e insira "WindowController" como o **nome da classe**: 

    [![Configurar um nome de classe personalizada para o controlador da janela](toolbar-images/windowcontroller01.png "definindo um nome de classe personalizada para o controlador da janela")](toolbar-images/windowcontroller01-large.png#lightbox) 

4. Salve suas alterações e retorne ao Visual Studio para Mac para sincronizar.
5. Um **WindowController.cs** arquivo será adicionado ao seu projeto na **painel de soluções** no Visual Studio para Mac: 

    ![Selecionando WindowController.cs no painel de soluções](toolbar-images/windowcontroller02.png "selecionando WindowController.cs no painel de soluções")

6. Reabra o storyboard no Interface Builder do Xcode.
7. O **WindowController.h** arquivo estará disponível para uso: 

    [![O arquivo WindowController.h](toolbar-images/windowcontroller03.png "WindowController.h o arquivo")](toolbar-images/windowcontroller03-large.png#lightbox)

## <a name="creating-and-maintaining-toolbars-in-xcode"></a>Criar e manter barras de ferramentas do Xcode

Barras de ferramentas são criadas e mantidas com o Interface Builder do Xcode. Para adicionar uma barra de ferramentas a um aplicativo, edite o storyboard de principal do aplicativo (neste caso **Main. Storyboard**) clicando duas vezes na **painel de soluções**:

![Abrir Main. Storyboard no painel de soluções](toolbar-images/edit01.png "abrir Main. Storyboard no painel de soluções")

No **Inspetor de biblioteca**, insira "ferramentas" na **caixa de pesquisa** para torná-lo mais fácil de ver todos os itens da barra de ferramentas disponíveis:

![O Inspetor de biblioteca, filtrado para mostrar os itens da barra de ferramentas](toolbar-images/edit02.png "o Inspetor de biblioteca, filtrado para mostrar os itens da barra de ferramentas")

Arrastar uma barra de ferramentas para a janela na **Editor de Interface**. Com a barra de ferramentas selecionada, configure o comportamento definindo propriedades **Inspetor de atributos**:

![O Inspetor de atributos para uma barra de ferramentas](toolbar-images/edit04.png "o Inspetor de atributos para uma barra de ferramentas")

As seguintes propriedades estão disponíveis:

1. **Exibição** -controla se a barra de ferramentas exibe ícones, texto ou ambos
2. **Visível na inicialização** -se selecionado, a barra de ferramentas está visível por padrão.
3. **Personalizável** -se selecionado, os usuários podem editar e personalizar a barra de ferramentas.
4. **Separador** -se selecionado, uma linha horizontal fina separa a barra de ferramentas do conteúdo da janela.
5. **Tamanho** -define o tamanho da barra de ferramentas
6. **Salvamento automático** -se selecionado, o aplicativo irá persistir alterações de configuração de barra de ferramentas do usuário em inicializações do aplicativo.

Selecione o **salvamento automático** opção e deixar todas as outras propriedades em suas configurações padrão. 

Depois de abrir a barra de ferramentas do **hierarquia de Interface**, abrir a caixa de diálogo de personalização, selecionando um item da barra de ferramentas:

![Personalizando a barra de ferramentas](toolbar-images/edit05.png "Personalizando a barra de ferramentas")

Use essa caixa de diálogo para definir propriedades para itens que já fazem parte da barra de ferramentas, ao design de barra de ferramentas padrão para o aplicativo, e fornecer itens de barra de ferramentas adicionais para um usuário selecionar ao personalizar a barra de ferramentas. Para adicionar itens à barra de ferramentas, arraste-os do **Inspetor de biblioteca**:

![O Inspetor de biblioteca](toolbar-images/edit06.png "o Inspetor de biblioteca")

Os seguintes itens de barra de ferramentas podem ser adicionados:

- **Item da barra de ferramentas de imagem** -um item da barra de ferramentas com uma imagem personalizada como um ícone.
- **Item de barra de ferramentas flexível espaço** -espaço flexível usado para justificar a itens da barra de ferramentas subsequentes. Por exemplo, um ou mais itens de barra de ferramentas seguido de um item da barra de ferramentas flexível de espaço e outro item de barra de ferramentas seria fixar o último item para o lado direito da barra de ferramentas.
- **Item da barra de ferramentas de espaço** -fixa de espaço entre itens na barra de ferramentas
- **Item da barra de ferramentas de separador** -visível separador entre dois ou mais itens de barra de ferramentas, para agrupamento
- **Personalizar barra de ferramentas Item** -permite que os usuários personalizem a barra de ferramentas
- **Item da barra de ferramentas de impressão** -permite que os usuários imprimam o documento aberto
- **Mostrar Item de barra de ferramentas de cores** -exibe o seletor de cores do sistema padrão: 

     ![O seletor de cores do sistema](toolbar-images/edit07.png "o seletor de cores do sistema")

- **Mostrar Item de barra de ferramentas de fonte** -exibe a caixa de diálogo de fonte padrão do sistema: 

     ![O seletor de fonte](toolbar-images/edit08.png "o seletor de fonte")

> [!IMPORTANT]
> Como será visto posteriormente, muitos controles de interface do usuário do Cocoa padrão como campos de pesquisa, controles segmentados e controles deslizantes horizontais também podem ser adicionados à barra de ferramentas.

### <a name="adding-an-item-to-a-toolbar"></a>Adicionar um item a uma barra de ferramentas

Para adicionar um item a uma barra de ferramentas, selecione a barra de ferramentas a **hierarquia de Interface** e clique em um de seus itens, fazendo com que a caixa de diálogo de personalização aparecer. Em seguida, arraste um novo item do **Inspetor de biblioteca** para o **da barra de ferramentas de itens permitidos** área:

![Os itens de barra de ferramentas permitidos na caixa de diálogo de personalização da barra de ferramentas](toolbar-images/add01.png "a itens de barra de ferramentas permitidos na caixa de diálogo de personalização da barra de ferramentas")

Para certificar-se de que um novo item é parte da barra de ferramentas padrão, arraste-o para o **itens de barra de ferramentas padrão** área: 

![Reordenação de um item da barra de ferramentas, arrastando](toolbar-images/add02.png "reordenação de um item da barra de ferramentas, arrastando")

Para reordenar os itens da barra de ferramentas padrão, arraste-os à esquerda ou direita.

Em seguida, use o **Inspetor de atributos** para definir propriedades padrão para o item:

![Personalização de um item de barra de ferramentas usando o Inspetor de atributos](toolbar-images/add03.png "personalização de um item de barra de ferramentas usando o Inspetor de atributos")

As seguintes propriedades estão disponíveis:

- **Nome da imagem** -imagem a ser usada como um ícone para o item
- **Rótulo** -texto a ser exibido para o item na barra de ferramentas
- **Rótulo de paleta** -texto a ser exibido para o item em de **permitido itens da barra de ferramentas** área
- **Marca** – um identificador exclusivo opcional que ajuda a identificar o item no código.
- **Identificador** -define o tipo de item da barra de ferramentas. Um valor personalizado pode ser usado para selecionar um item da barra de ferramentas no código.
- **Selecionável** -se marcada, o item atuará como um botão liga/desliga.

> [!IMPORTANT]
> Adicionar um item para o **permitido itens da barra de ferramentas** área, mas não a barra de ferramentas padrão para fornecer opções de personalização para usuários. 

### <a name="adding-other-ui-controls-to-a-toolbar"></a>Adicionar outros controles de interface do usuário para uma barra de ferramentas

Vários elementos de interface do usuário do Cocoa como pesquisar campos e controles segmentados também podem ser adicionados à barra de ferramentas.

Para testar isso, abra a barra de ferramentas do **hierarquia de Interface** e selecione um item da barra de ferramentas para abrir a caixa de diálogo de personalização. Arraste uma **campo de pesquisa** da **Inspetor de biblioteca** para o **permitido itens da barra de ferramentas** área:

![Na caixa de diálogo de personalização da barra de ferramentas](toolbar-images/add05.png "usando a caixa de diálogo de personalização da barra de ferramentas")

A partir daqui, use o construtor de Interface para configurar o campo de pesquisa e expô-lo para o código por meio de uma ação ou tomada.

## <a name="built-in-toolbar-item-support"></a>Suporte do item de barra de ferramentas interna

Vários elementos de interface do usuário do Cocoa interagem com os itens de barra de ferramentas padrão por padrão. Por exemplo, arraste uma **exibição de texto** para a janela do aplicativo e posicione-o para preencher a área de conteúdo:

[![Adicionando uma exibição de texto para o aplicativo](toolbar-images/edit09.png "adicionando uma exibição de texto para o aplicativo")](toolbar-images/edit09-large.png#lightbox)

Salvar o documento, retorne ao Visual Studio para Mac para sincronizar com Xcode, executar o aplicativo, digite algum texto, selecione-o e clique no **cores** item da barra de ferramentas. Observe que a exibição de texto funciona automaticamente com o seletor de cores:

![Funcionalidade de barra de ferramentas interna com um seletor de exibição e a cor do texto](toolbar-images/edit10.png "funcionalidade de barra de ferramentas interna com um seletor de exibição e a cor do texto")

## <a name="using-images-with-toolbar-items"></a>Uso de imagens com itens de barra de ferramentas

Usando um **Item da barra de ferramentas de imagem**, qualquer imagem de bitmap é adicionado à **recursos** pasta (e recebe uma ação de build **recursos de pacote**) pode ser exibido na barra de ferramentas, como um ícone:

1. No Visual Studio para Mac, no **painel de soluções**, com o botão direito do **recursos** pasta e selecione **Add** > **adicionar arquivos** .
2. Dos **adicionar arquivos** diálogo caixa, navegue até as imagens desejadas, selecione-os e clique no **aberto** botão: 

    [![Selecionar imagens a serem adicionadas](toolbar-images/edit11.png "selecionar as imagens a adicionar")](toolbar-images/edit11-large.png#lightbox)

3. Selecione **cópia**, verifique **usar a mesma ação para todos os arquivos selecionados**e clique em **Okey**:

    ![Selecionando a ação de cópia para as imagens adicionadas](toolbar-images/edit12.png "selecionando a ação de cópia para adicionar imagens")

4. No **painel de soluções**, clique duas vezes em **MainWindow.xib** para abri-lo no Xcode.

5. Selecione a barra de ferramentas a **hierarquia de Interface** e clique em um de seus itens para abrir a caixa de diálogo de personalização.

6. Arraste um **Item de barra de ferramentas de imagem** da **Inspetor de biblioteca** na barra de ferramentas **permitido itens da barra de ferramentas** área: 

    ![Um Item da barra de ferramentas de imagem adicionado à área de barra de ferramentas de itens permitidos](toolbar-images/edit14.png "um Item de barra de ferramentas de imagem adicionado à área de barra de ferramentas de itens permitidos")

7. No **Inspetor de atributos**, selecione a imagem que acabou de ser adicionada no Visual Studio para Mac: 

    ![Definindo uma imagem personalizada para um item da barra de ferramentas](toolbar-images/edit15.png "definindo uma imagem personalizada para um item da barra de ferramentas")

8. Defina as **rótulo** para "Lixeira" e o **paleta rótulo** "Apagar o documento": 

    ![Definir a barra de ferramentas de item rótulo e paleta](toolbar-images/edit16.png "definindo a barra de ferramentas de item rótulo e paleta")

9. Arraste uma **separador de Item de barra de ferramentas** da **Inspetor de biblioteca** na barra de ferramentas **permitido itens da barra de ferramentas** área: 

    [![Um Item da barra de ferramentas de separador adicionado à área de barra de ferramentas de itens permitidos](toolbar-images/edit17.png "um separador de barra de ferramentas do Item adicionado à área de barra de ferramentas de itens permitidos")](toolbar-images/edit17-large.png#lightbox)

10. Arraste o item separador e o item "Lixeira" para o **itens de barra de ferramentas padrão** área e defina a ordem da barra de ferramentas itens da esquerda para a direita, da seguinte maneira (cores, fontes, separador, Lixeira, espaço flexível, impressão): 

    ![Os itens da barra de ferramentas padrão](toolbar-images/edit18.png "os itens da barra de ferramentas padrão")

11. Salvar as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Execute o aplicativo para verificar se a nova barra de ferramentas é exibida por padrão:

![Uma barra de ferramentas com itens padrão personalizado](toolbar-images/edit19.png "uma barra de ferramentas com itens padrão personalizado")

## <a name="exposing-toolbar-items-with-outlets-and-actions"></a>Expor os itens da barra de ferramentas com as saídas e ações

Para acessar uma barra de ferramentas ou item da barra de ferramentas no código, ele deve ser anexado a uma tomada ou uma ação:

1. No **painel de soluções**, clique duas vezes em **Main. Storyboard** para abri-lo no Xcode.
2. Certifique-se de que a classe personalizada "WindowController" foi atribuído ao controlador de janela principal do **Inspetor de identidade**:

    [![Usando o Inspetor de identidade ao definir uma classe personalizada para o controlador de janela](toolbar-images/edit20a.png "usando o Inspetor de identidade ao definir uma classe personalizada para o controlador de janela")](toolbar-images/edit20a-large.png#lightbox)

3. Em seguida, selecione o item na barra de ferramentas do **hierarquia de Interface**: 

    ![Selecionando o item da barra de ferramentas na hierarquia de Interface](toolbar-images/edit20.png "selecionando o item da barra de ferramentas na hierarquia de Interface")  

4. Abra o **Assistente de exibição**, selecione o **WindowController.h** arquivo e o arraste de controle do item de barra de ferramentas para o **WindowController.h** arquivo.
5. Defina o **Conexão** de tipo para **ação**, insira "trashDocument" para o **nome**e clique no **Connect** botão: 

    [![Configurar uma ação para um item da barra de ferramentas](toolbar-images/edit23.png "Configurando uma ação para um item da barra de ferramentas")](toolbar-images/edit23-large.png#lightbox)

6. Expor a **exibição de texto** como uma saída chamada "documentEditor" na **viewcontroller. H** arquivo: 

    [![Configurando uma saída para a exibição de texto](toolbar-images/edit24.png "Configurando uma saída para a exibição de texto")](toolbar-images/edit24-large.png#lightbox)

7. Salve suas alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

No Visual Studio para Mac, edite o **ViewController.cs** arquivo e adicione o código a seguir:

```csharp
public void EraseDocument() {
    documentEditor.Value = "";
}
```

Em seguida, edite o **WindowController.cs** do arquivo e adicione o seguinte código na parte inferior do `WindowController` classe:

```csharp
[Export ("trashDocument:")]
void TrashDocument (NSObject sender) {

    var controller = ContentViewController as ViewController;
    controller.EraseDocument ();
}
```

Ao executar o aplicativo, o **Lixeira** item da barra de ferramentas ficará ativo:

![Uma barra de ferramentas com um item da Lixeira do Active Directory](toolbar-images/edit25.png "uma barra de ferramentas com um item da Lixeira do Active Directory")

Observe que o **Lixeira** item da barra de ferramentas agora pode ser usado para excluir o texto.

## <a name="disabling-toolbar-items"></a>Desabilitando itens de barra de ferramentas

Para desabilitar um item em uma barra de ferramentas, crie um personalizado `NSToolbarItem` de classe e substituir o `Validate` método. Em seguida, no construtor de Interface, atribua o tipo personalizado para o item que você deseja habilitar/desabilitar.

Para criar um personalizado `NSToolbarItem` classe, clique com botão direito no projeto e selecione **Add** > **novo arquivo...** . Selecione **gerais** > **classe vazia**, digite "ActivatableItem" para o **nome**e clique no **novo** botão: 

![Adicionando uma classe vazia no Visual Studio para Mac](toolbar-images/custom01.png "adicionando uma classe vazia no Visual Studio para Mac")

Em seguida, edite o **ActivatableItem.cs** arquivo a ser lido da seguinte maneira:

```csharp
using System;

using Foundation;
using AppKit;

namespace MacToolbar
{
    [Register("ActivatableItem")]
    public class ActivatableItem : NSToolbarItem
    {
        public bool Active { get; set;} = true;

        public ActivatableItem ()
        {
        }

        public ActivatableItem (IntPtr handle) : base (handle)
        {
        }

        public ActivatableItem (NSObjectFlag  t) : base (t)
        {
        }

        public ActivatableItem (string title) : base (title)
        {
        }

        public override void Validate ()
        {
            base.Validate ();
            Enabled = Active;
        }
    }
}
```

Clique duas vezes em **Main. Storyboard** para abri-lo no Xcode. Selecione o **Lixeira** item da barra de ferramentas criado acima e alterar sua classe para "ActivatableItem" na **Inspetor de identidade**:

![Definindo uma classe personalizada para um item da barra de ferramentas](toolbar-images/custom02.png "definindo uma classe personalizada para um item da barra de ferramentas")

Criar uma saída chamada `trashItem` para o **Lixeira** item da barra de ferramentas. Salvar as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode. Por fim, abra **MainWindow.cs** e atualize o `AwakeFromNib` método da seguinte forma:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Disable trash
    trashItem.Active = false;
}
```

Execute o aplicativo e observe que o **Lixeira** item agora está desabilitado na barra de ferramentas:

![Uma barra de ferramentas com um item da Lixeira inativa](toolbar-images/custom03.png "uma barra de ferramentas com um item da Lixeira inativo")

## <a name="summary"></a>Resumo

Este artigo apresentou uma visão detalhada de como trabalhar com barras de ferramentas e itens de barra de ferramentas em um aplicativo xamarin. Mac. Ele descreveu como criar e manter barras de ferramentas no Interface Builder do Xcode, como alguns controles de interface do usuário funcionam automaticamente com os itens da barra de ferramentas, como trabalhar com barras de ferramentas em código c# e como habilitar e desabilitar itens de barra de ferramentas.


## <a name="related-links"></a>Links relacionados

- [MacToolbar (amostra)](https://developer.xamarin.com/samples/mac/MacToolbar/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Diretrizes de Interface humana para barras de ferramentas](https://developer.apple.com/macos/human-interface-guidelines/windows-and-views/toolbars/)
- [Introdução às barras de ferramentas](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Toolbars/Toolbars.html)
