---
title: Barras de ferramentas
description: "Este artigo descreve como trabalhar com barras de ferramentas em um aplicativo Xamarin.Mac. Ele aborda as barras de ferramentas de criação e manutenção no Xcode e construtor de Interface, expondo-os para o código e trabalhar com eles por meio de programação."
ms.topic: article
ms.prod: xamarin
ms.assetid: C8D228CE-C860-47E1-85FD-69864BF91F20
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 641f62d5e646607b2ff61db412a5defce63a211d
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="toolbars"></a>Barras de ferramentas

_Este artigo descreve como trabalhar com barras de ferramentas em um aplicativo Xamarin.Mac. Ele aborda as barras de ferramentas de criação e manutenção no Xcode e construtor de Interface, expondo-os para o código e trabalhar com eles por meio de programação._

Os desenvolvedores de Xamarin.Mac trabalhando com o Visual Studio para Mac têm acesso para os mesmos controles de interface do usuário disponíveis para desenvolvedores macOS trabalhando com o Xcode, incluindo o controle de barra de ferramentas. Porque Xamarin.Mac se integra diretamente com o Xcode, é possível usar o construtor de Interface do Xcode para criar e manter itens da barra de ferramentas. Esses itens da barra de ferramentas também podem ser criados em c#.

Barras de ferramentas no macOS são adicionadas à seção superior de uma janela e fornecem acesso fácil aos comandos relacionados à sua funcionalidade. Barras de ferramentas podem ser ocultadas, mostradas ou personalizadas pelos usuários do aplicativo, e eles podem apresentar os itens da barra de ferramentas de várias maneiras.

Este artigo aborda os conceitos básicos de como trabalhar com barras de ferramentas e itens da barra de ferramentas em um aplicativo Xamarin.Mac. 

Antes de continuar, leia o [Hello, Mac](~/mac/get-started/hello-mac.md) artigo — especificamente o [Introdução ao construtor da Interface e Xcode](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [tomadas e ações](~/mac/get-started/hello-mac.md#Outlets_and_Actions) seções — como aborda os principais conceitos e técnicas que serão usadas em todo este guia.

Também dar uma olhada a [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) documento. Explica o `Register` e `Export` atributos usados para conectar-se classes c# para classes Objective-C.

## <a name="introduction-to-toolbars"></a>Introdução às barras de ferramentas

Nenhuma janela em um aplicativo macOS pode incluir uma barra de ferramentas:

![Uma janela de exemplo com uma barra de ferramentas](toolbar-images/info01.png "uma janela de exemplo com uma barra de ferramentas")

Barras de ferramentas fornecem uma maneira fácil para os usuários do seu aplicativo a acessar rapidamente importantes ou os recursos usados com frequência. Por exemplo, um aplicativo de edição de documentos pode fornecer itens da barra de ferramentas para definir a cor do texto, alterando a fonte ou imprimir o documento atual.

Barras de ferramentas podem exibir os itens de três maneiras:

1. **Ícone e texto** 

     ![Uma barra de ferramentas com ícones e texto](toolbar-images/info02.png "uma barra de ferramentas com ícones e texto")

2. **Ícone somente** 

     ![Uma barra de ferramentas somente ícone](toolbar-images/info03.png "uma barra de ferramentas somente ícone")

3. **Somente texto** 

     ![Uma barra de ferramentas somente texto](toolbar-images/info04.png "uma barra de ferramentas somente de texto")

Alternar entre esses modos clicando duas vezes a barra de ferramentas e selecionar um modo de exibição no menu contextual:

![O menu contextual para uma barra de ferramentas](toolbar-images/info05.png "o menu contextual para uma barra de ferramentas")

Use o mesmo menu para exibir a barra de ferramentas em um tamanho menor:

![Uma barra de ferramentas com ícones pequenos](toolbar-images/info06.png "uma barra de ferramentas com ícones pequenos")

O menu também permite personalizar a barra de ferramentas:

![A caixa de diálogo usada para personalizar uma barra de ferramentas](toolbar-images/info07.png "a caixa de diálogo usada para personalizar uma barra de ferramentas")

Ao configurar uma barra de ferramentas no construtor de Interface do Xcode, um desenvolvedor pode fornecer itens da barra de ferramentas adicionais que não fazem parte de sua configuração padrão. Os usuários do aplicativo, em seguida, podem personalizar a barra de ferramentas, adicionar e remover esses itens predefinidos conforme necessário. É claro que, barra de ferramentas pode ser redefinida para sua configuração padrão.

A barra de ferramentas se conecta automaticamente ao **exibição** menu, que permite aos usuários para ocultá-lo, mostrá-la e personalizá-lo:

![Itens relacionados a barra de ferramentas no menu Exibir](toolbar-images/info08.png "itens relacionados a barra de ferramentas no menu Exibir")

Consulte o [funcionalidade interna de Menu](~/mac/user-interface/menu.md) documentação para obter mais detalhes.

Além disso, se a barra de ferramentas está configurada corretamente no construtor de Interface, o aplicativo automaticamente persistirá personalizações em várias inicializações de aplicativo.

As próximas seções deste guia descrevem como criar e manter as barras de ferramentas com o construtor de Interface do Xcode e como trabalhar com eles no código.

## <a name="setting-a-custom-main-window-controller"></a>Configurar um controlador personalizado janela principal

Para expor elementos de interface do usuário ao código c# por meio de saídas e ações, o aplicativo Xamarin.Mac deve usar um controlador de janela personalizados:

1. Abra o storyboard do aplicativo no construtor de Interface do Xcode.
2. Selecione o controlador de janela na superfície de design.
3. Alterne para o **Inspetor de identidade** e digite "WindowController" como o **nome da classe**: 

    [![Configurar um nome de classe personalizada para o controlador de janela](toolbar-images/windowcontroller01.png "configurar um nome de classe personalizada para o controlador de janela")](toolbar-images/windowcontroller01-large.png) 

4. Salve suas alterações e retornar ao Visual Studio para Mac sincronizar.
5. Um **WindowController.cs** arquivo será adicionado ao seu projeto no **solução preenchimento** no Visual Studio para Mac: 

    ![Selecionando WindowController.cs no teclado de solução](toolbar-images/windowcontroller02.png "selecionando WindowController.cs no teclado de solução")

6. Reabra o storyboard no construtor de Interface do Xcode.
7. O **WindowController.h** arquivo estará disponível para uso: 

    [![O arquivo WindowController.h](toolbar-images/windowcontroller03.png "WindowController.h o arquivo")](toolbar-images/windowcontroller03-large.png)

## <a name="creating-and-maintaining-toolbars-in-xcode"></a>Criar e manter as barras de ferramentas no Xcode

Barras de ferramentas são criadas e mantidas com o construtor de Interface do Xcode. Para adicionar uma barra de ferramentas para um aplicativo, edite o storyboard principal do aplicativo (neste caso **Main.storyboard**) clicando duas vezes no **solução preenchimento**:

![Abrindo Main.storyboard no teclado de solução](toolbar-images/edit01.png "abrir Main.storyboard no teclado de solução")

No **Inspetor de biblioteca**, digite "ferramentas" no **caixa de pesquisa** para tornar mais fácil ver todos os itens da barra de ferramentas disponíveis:

![Inspetor de biblioteca, filtrado para mostrar os itens da barra de ferramentas](toolbar-images/edit02.png "o Inspetor de biblioteca, filtrados para mostrar os itens da barra de ferramentas")

Arraste uma barra de ferramentas para a janela no **Editor de Interface**. Com a barra de ferramentas selecionada, configure seu comportamento definindo propriedades de **atributos Inspetor**:

![Inspetor de atributos para uma barra de ferramentas](toolbar-images/edit04.png "o Inspetor de atributos para uma barra de ferramentas")

As propriedades a seguir estão disponíveis:

1. **Exibição** -controla se a barra de ferramentas exibe ícones, texto ou ambos
2. **Visível ao iniciar o** -se selecionada, a barra de ferramentas está visível por padrão.
3. **Personalizável** -se selecionada, os usuários podem editar e personalizar a barra de ferramentas.
4. **Separador** -se selecionada, uma linha horizontal fina separa a barra de ferramentas do conteúdo da janela.
5. **Tamanho** -define o tamanho da barra de ferramentas
6. **Salvamento automático** -se selecionada, o aplicativo persistirá alterações de configuração de barra de ferramentas do usuário em um aplicativo inicia.

Selecione o **Autosave** opção e deixe todas as outras propriedades em suas configurações padrão. 

Depois de abrir a barra de ferramentas de **hierarquia Interface**, exibir a caixa de diálogo de personalização, selecionando um item de barra de ferramentas:

![Personalizando a barra de ferramentas](toolbar-images/edit05.png "Personalizando a barra de ferramentas")

Use esta caixa de diálogo para definir as propriedades de itens que já fazem parte da barra de ferramentas, a barra de ferramentas padrão para o aplicativo de design e fornecer itens da barra de ferramentas adicionais para um usuário selecionar ao personalizar a barra de ferramentas. Para adicionar itens à barra de ferramentas, arraste-os do **Inspetor de biblioteca**:

![O Inspetor de biblioteca](toolbar-images/edit06.png "Inspetor de biblioteca")

Os seguintes itens da barra de ferramentas podem ser adicionados:

- **Item de barra de ferramentas de imagem** -um item de barra de ferramentas com uma imagem personalizada como um ícone.
- **Item de barra de ferramentas de espaço flexível** -flexível espaço usado para justificar os itens da barra de ferramentas subsequentes. Por exemplo, um ou mais itens da barra de ferramentas, seguido por um item de barra de ferramentas do espaço flexível e outro item de barra de ferramentas seria fixar o último item do lado direito da barra de ferramentas.
- **Item de barra de ferramentas de espaço** -fixo espaço entre os itens na barra de ferramentas
- **Item de barra de ferramentas do separador** -visível separador entre dois ou mais itens da barra de ferramentas, para o agrupamento
- **Personalizar a barra de ferramentas Item** -permite que os usuários personalizar a barra de ferramentas
- **Item de barra de ferramentas de impressão** -permite que os usuários imprimam o documento aberto
- **Mostrar cores da barra de ferramentas Item** -exibe o seletor de cor padrão do sistema: 

     ![O seletor de cores do sistema](toolbar-images/edit07.png "o seletor de cores do sistema")

- **Mostrar Item de barra de ferramentas de fonte** -exibe a caixa de diálogo de fonte padrão do sistema: 

     ![O seletor de fonte](toolbar-images/edit08.png "o seletor de fonte")

> [!IMPORTANT]
> Como será visto posteriormente, vários controles Cocoa da interface do usuário padrão, como campos de pesquisa, controles segmentados e controles deslizantes horizontais também podem ser adicionados à barra de ferramentas.

### <a name="adding-an-item-to-a-toolbar"></a>Adicionar um item a uma barra de ferramentas

Para adicionar um item a uma barra de ferramentas, selecione a barra de ferramentas de **hierarquia de Interface** e clique em um de seus itens, fazendo com que a caixa de diálogo de personalização apareça. Em seguida, arraste um novo item do **biblioteca Inspetor** para o **permitido itens da barra de ferramentas** área:

![Os itens da barra de ferramentas permitidos na caixa de diálogo de personalização da barra de ferramentas](toolbar-images/add01.png "a barra de ferramentas de itens permitidos na caixa de diálogo de personalização da barra de ferramentas")

Para certificar-se de que um novo item é parte da barra de ferramentas padrão, arraste-o para o **itens da barra de ferramentas padrão** área: 

![Reorganização de um item da barra de ferramentas arrastando](toolbar-images/add02.png "reordenação de um item da barra de ferramentas arrastando")

Para reordenar itens da barra de ferramentas padrão, arraste-os à esquerda ou direita.

Em seguida, use o **atributos Inspetor** para definir as propriedades padrão para o item:

![Personalizar um item de barra de ferramentas usando o Inspetor de atributos](toolbar-images/add03.png "personalizar um item de barra de ferramentas usando o Inspetor de atributos")

As propriedades a seguir estão disponíveis:

- **Nome da imagem** -imagem para usar como um ícone para o item
- **Rótulo** -texto a ser exibido para o item na barra de ferramentas
- **Rótulo de paleta** -texto a ser exibido para o item a **permitido itens da barra de ferramentas** área
- **Marca** -um identificador exclusivo opcional que ajuda a identificar o item no código.
- **Identificador** -define o tipo de item da barra de ferramentas. Um valor personalizado pode ser usado para selecionar um item da barra de ferramentas no código.
- **Selecionável** -se marcada, o item atuará como um botão de ativar/desativar.

> [!IMPORTANT]
> Adicionar um item para o **permitido itens da barra de ferramentas** área, mas não a barra de ferramentas padrão para fornecer opções de personalização para os usuários. 

### <a name="adding-other-ui-controls-to-a-toolbar"></a>Adicionando outros controles de interface do usuário para uma barra de ferramentas

Vários elementos de interface de usuário Cocoa como campos de pesquisa e controles segmentados também podem ser adicionados à barra de ferramentas.

Para testar isso, abra a barra de ferramentas de **hierarquia Interface** e selecione um item da barra de ferramentas para abrir a caixa de diálogo de personalização. Arraste um **campo de pesquisa** do **biblioteca Inspetor** para o **permitido itens da barra de ferramentas** área:

![Na caixa de diálogo de personalização da barra de ferramentas](toolbar-images/add05.png "usando a caixa de diálogo de personalização da barra de ferramentas")

Aqui, use o construtor de Interface para configurar o campo de pesquisa e expô-lo para o código por meio de uma ação ou tomada.

## <a name="built-in-toolbar-item-support"></a>Suporte de item da barra de ferramentas interna

Vários elementos de interface de usuário Cocoa interagem com os itens da barra de ferramentas padrão por padrão. Por exemplo, arraste um **exibição de texto** para a janela do aplicativo e posicione-o para preencher a área de conteúdo:

[![Adicionando uma exibição de texto para o aplicativo](toolbar-images/edit09.png "adicionando uma exibição de texto para o aplicativo")](toolbar-images/edit09-large.png)

Salve o documento, retorne ao Visual Studio para Mac para sincronizar com o Xcode, executar o aplicativo, digite algum texto, selecione-o e clique no **cores** item da barra de ferramentas. Observe que a exibição de texto funciona automaticamente com o seletor de cores:

![Funcionalidade de barra de ferramentas interna com um seletor de exibição e a cor do texto](toolbar-images/edit10.png "funcionalidade de barra de ferramentas interna com um seletor de exibição e a cor do texto")

## <a name="using-images-with-toolbar-items"></a>Usando imagens com itens da barra de ferramentas

Usando um **Item da barra de ferramentas de imagem**, nenhuma imagem de bitmap é adicionado ao **recursos** pasta (e recebe uma ação de compilação de **recursos de pacote**) pode ser exibido na barra de ferramentas, como um ícone:

1. No Visual Studio para Mac, no **preenchimento de solução**, com o botão direito do **recursos** pasta e selecione **adicionar** > **adicionar arquivos** .
2. Do **adicionar arquivos** caixa de diálogo caixa, navegue até as imagens desejadas, selecione-os e clique no **abrir** botão: 

    [![Selecionando imagens para adicionar](toolbar-images/edit11.png "selecionando imagens para adicionar")](toolbar-images/edit11-large.png)

3. Selecione **cópia**, verifique **usar a mesma ação para todos os arquivos selecionados**e clique em **Okey**:

    ![Selecionando a ação de cópia para adicionar imagens](toolbar-images/edit12.png "selecionando a ação de cópia para adicionar imagens")

4. No **solução preenchimento**, clique duas vezes em **MainWindow.xib** para abri-lo no Xcode.

5. Selecione a barra de ferramentas de **hierarquia de Interface** e clique em um de seus itens para abrir a caixa de diálogo de personalização.

6. Arraste um **Item da barra de ferramentas de imagem** do **biblioteca Inspetor** na barra de ferramentas **permitido itens da barra de ferramentas** área: 

    ![Um Item de barra de ferramentas de imagem adicionada à área de barra de ferramentas de itens permitidos](toolbar-images/edit14.png "um Item de barra de ferramentas de imagem adicionada à área de itens permitidos de barra de ferramentas")

7. No **atributos Inspetor**, selecione a imagem que acabou de ser adicionada no Visual Studio para Mac: 

    ![Definindo uma imagem personalizada para um item de barra de ferramentas](toolbar-images/edit15.png "definindo uma imagem personalizada para um item de barra de ferramentas")

8. Definir o **rótulo** "Lixeira" e o **rótulo de paleta** "Apagar o documento": 

    ![Definindo a barra de ferramentas de item rótulo e o rótulo da paleta](toolbar-images/edit16.png "definindo a barra de ferramentas de item rótulo e o rótulo da paleta")

9. Arraste um **separador de Item de barra de ferramentas** do **biblioteca Inspetor** na barra de ferramentas **permitido itens da barra de ferramentas** área: 

    [![Um Item de barra de ferramentas do separador adicionado à área de barra de ferramentas de itens permitidos](toolbar-images/edit17.png "um separador de barra de ferramentas Item adicionado à área de itens permitidos de barra de ferramentas")](toolbar-images/edit17-large.png)

10. Arraste o item de separador e o item "Lixeira" para o **itens de barra de ferramentas padrão** área e defina a ordem da barra de ferramentas itens da esquerda para a direita, da seguinte maneira (cores, fontes, separador, Lixeira, espaço flexível, impressão): 

    ![Os itens da barra de ferramentas padrão](toolbar-images/edit18.png "os itens da barra de ferramentas padrão")

11. Salve as alterações e retornar ao Visual Studio para Mac sincronizar com o Xcode.

Execute o aplicativo para verificar se a nova barra de ferramentas é exibida por padrão:

![Uma barra de ferramentas com itens padrão personalizado](toolbar-images/edit19.png "uma barra de ferramentas com itens padrão personalizado")

## <a name="exposing-toolbar-items-with-outlets-and-actions"></a>Expondo os itens da barra de ferramentas com tomadas e ações

Para acessar uma barra de ferramentas ou um item de barra de ferramentas no código, ele precisa ser conectado a uma tomada ou uma ação:

1. No **solução preenchimento**, clique duas vezes em **Main.storyboard** para abri-lo no Xcode.
2. Certifique-se de que a classe personalizada "WindowController" foi atribuída ao controlador de janela principal do **Inspetor de identidade**:

    [![Usando o Inspetor de identidade para definir uma classe personalizada para o controlador de janela](toolbar-images/edit20a.png "usando o Inspetor de identidade para definir uma classe personalizada para o controlador de janela")](toolbar-images/edit20a-large.png)

3. Em seguida, selecione o item da barra de ferramentas de **hierarquia Interface**: 

    ![Selecionando o item de barra de ferramentas na hierarquia de Interface](toolbar-images/edit20.png "selecionando o item de barra de ferramentas na hierarquia de Interface")  

4. Abra o **Assistente de exibição**, selecione o **WindowController.h** arquivo e arraste de controle do item de barra de ferramentas para a **WindowController.h** arquivo.
5. Definir o **Conexão** tipo **ação**, digite "trashDocument" para o **nome**e clique no **conectar** botão: 

    [![Configurar uma ação para um item de barra de ferramentas](toolbar-images/edit23.png "configurar uma ação para um item de barra de ferramentas")](toolbar-images/edit23-large.png)

6. Expor o **exibição de texto** como uma tomada chamada "documentEditor" no **ViewController.h** arquivo: 

    [![Configurando uma tomada para o modo de exibição de texto](toolbar-images/edit24.png "Configurando uma tomada para o modo de texto")](toolbar-images/edit24-large.png)

7. Salve suas alterações e retornar ao Visual Studio para Mac sincronizar com o Xcode.

No Visual Studio para Mac, edite o **ViewController.cs** de arquivo e adicione o seguinte código:

```csharp
public void EraseDocument() {
    documentEditor.Value = "";
}
```

Em seguida, edite o **WindowController.cs** de arquivo e adicione o seguinte código na parte inferior da `WindowController` classe:

```csharp
[Export ("trashDocument:")]
void TrashDocument (NSObject sender) {

    var controller = ContentViewController as ViewController;
    controller.EraseDocument ();
}
```

Ao executar o aplicativo, o **Lixeira** item da barra de ferramentas ficará ativo:

![Uma barra de ferramentas com um item da Lixeira do active](toolbar-images/edit25.png "uma barra de ferramentas com um item da Lixeira do active")

Observe que o **Lixeira** agora pode ser usado para excluir o texto do item da barra de ferramentas.

## <a name="disabling-toolbar-items"></a>Desabilitando itens da barra de ferramentas

Para desabilitar um item em uma barra de ferramentas, crie um personalizado `NSToolbarItem` classe e substituir o `Validate` método. Em seguida, no construtor de Interface, atribua o tipo personalizado para o item que você deseja habilitar/desabilitar.

Para criar um personalizado `NSToolbarItem` classe, clique com botão direito no projeto e selecione **adicionar** > **novo arquivo...** . Selecione **geral** > **classe vazia**, digite "ActivatableItem" para o **nome**e clique no **novo** botão: 

![Adicionando uma classe vazia no Visual Studio para Mac](toolbar-images/custom01.png "adicionando uma classe vazia no Visual Studio para Mac")

Em seguida, edite o **ActivatableItem.cs** arquivo como segue:

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

Clique duas vezes em **Main.storyboard** para abri-lo no Xcode. Selecione o **Lixeira** item da barra de ferramentas criada acima e alterar sua classe para "ActivatableItem" no **Inspetor de identidade**:

![Definir uma classe personalizada para um item de barra de ferramentas](toolbar-images/custom02.png "definir uma classe personalizada para um item de barra de ferramentas")

Criar uma tomada chamada `trashItem` para o **Lixeira** item da barra de ferramentas. Salve as alterações e retornar ao Visual Studio para Mac sincronizar com o Xcode. Por fim, abra **MainWindow.cs** e atualize o `AwakeFromNib` método como segue:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Disable trash
    trashItem.Active = false;
}
```

Execute o aplicativo e observe que o **Lixeira** item agora está desabilitado na barra de ferramentas:

![Uma barra de ferramentas com um item de Lixeira inativo](toolbar-images/custom03.png "uma barra de ferramentas com um item de Lixeira inativo")

## <a name="summary"></a>Resumo

Este artigo obteve uma visão detalhada de como trabalhar com barras de ferramentas e itens da barra de ferramentas em um aplicativo Xamarin.Mac. Ele descrito como criar e manter as barras de ferramentas no construtor de Interface do Xcode, como alguns controles de interface do usuário automaticamente funcionam com os itens da barra de ferramentas, como trabalhar com barras de ferramentas no código c# e como habilitar e desabilitar itens da barra de ferramentas.


## <a name="related-links"></a>Links relacionados

- [MacToolbar (exemplo)](https://developer.xamarin.com/samples/mac/MacToolbar/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Diretrizes de Interface humana para barras de ferramentas](https://developer.apple.com/macos/human-interface-guidelines/windows-and-views/toolbars/)
- [Introdução às barras de ferramentas](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Toolbars/Toolbars.html)
