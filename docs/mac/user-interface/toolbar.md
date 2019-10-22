---
title: Barras de ferramentas no Xamarin. Mac
description: Este artigo descreve como trabalhar com barras de ferramentas em um aplicativo Xamarin. Mac. Ele aborda a criação e a manutenção de barras de ferramentas no Xcode e Interface Builder, Expo-las ao código e trabalhar com elas de forma programática.
ms.prod: xamarin
ms.assetid: C8D228CE-C860-47E1-85FD-69864BF91F20
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: cd2490bfad880d128f5eaeebd4aac58ad3a4d8fa
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70772717"
---
# <a name="toolbars-in-xamarinmac"></a>Barras de ferramentas no Xamarin. Mac

_Este artigo descreve como trabalhar com barras de ferramentas em um aplicativo Xamarin. Mac. Ele aborda a criação e a manutenção de barras de ferramentas no Xcode e Interface Builder, Expo-las ao código e trabalhar com elas de forma programática._

Os desenvolvedores do Xamarin. Mac que trabalham com Visual Studio para Mac têm acesso aos mesmos controles da interface do usuário disponíveis para que os desenvolvedores do macOS trabalhem com o Xcode, incluindo o controle da barra de ferramentas. Como o Xamarin. Mac se integra diretamente com o Xcode, é possível usar o Interface Builder do Xcode para criar e manter itens da barra de ferramentas. Esses itens da barra de ferramentas também podem C#ser criados no.

As barras de ferramentas no macOS são adicionadas à seção superior de uma janela e fornecem acesso fácil aos comandos relacionados à sua funcionalidade. As barras de ferramentas podem ser ocultas, mostradas ou personalizadas pelos usuários de um aplicativo e podem apresentar itens de barra de ferramentas de várias maneiras.

Este artigo aborda as noções básicas de como trabalhar com barras de ferramentas e itens de barra de ferramentas em um aplicativo Xamarin. Mac. 

Antes de continuar, leia o artigo [Olá, Mac](~/mac/get-started/hello-mac.md) – especificamente a [introdução às seções Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [ações](~/mac/get-started/hello-mac.md#outlets-and-actions) , pois ela aborda os principais conceitos e técnicas que serão usados em todo este guia.

Veja também a seção [ C# expondo classes/métodos para Objective-C](~/mac/internals/how-it-works.md) do documento interno do [Xamarin. Mac](~/mac/internals/how-it-works.md) . Ele explica os atributos `Register` e `Export` usados para conectar C# classes a classes Objective-C.

## <a name="introduction-to-toolbars"></a>Introdução às barras de ferramentas

Qualquer janela em um aplicativo macOS pode incluir uma barra de ferramentas:

![Uma janela de exemplo com uma barra de ferramentas](toolbar-images/info01.png "Uma janela de exemplo com uma barra de ferramentas")

As barras de ferramentas fornecem uma maneira fácil para os usuários do seu aplicativo acessarem rapidamente recursos importantes ou comumente usados. Por exemplo, um aplicativo de edição de documentos pode fornecer itens da barra de ferramentas para definir a cor do texto, alterar a fonte ou imprimir o documento atual.

As barras de ferramentas podem exibir itens de três maneiras:

1. **Ícone e texto** 

     ![Uma barra de ferramentas com ícones e texto](toolbar-images/info02.png "Uma barra de ferramentas com ícones e texto")

2. **Somente ícone** 

     ![Uma barra de ferramentas somente de ícones](toolbar-images/info03.png "Uma barra de ferramentas somente de ícones")

3. **Somente texto** 

     ![Uma barra de ferramentas somente texto](toolbar-images/info04.png "Uma barra de ferramentas somente texto")

Alterne entre esses modos clicando com o botão direito do mouse na barra de ferramentas e selecionando um modo de exibição no menu contextual:

![O menu contextual para uma barra de ferramentas](toolbar-images/info05.png "O menu contextual para uma barra de ferramentas")

Use o mesmo menu para exibir a barra de ferramentas em um tamanho menor:

![Uma barra de ferramentas com ícones pequenos](toolbar-images/info06.png "Uma barra de ferramentas com ícones pequenos")

O menu também permite personalizar a barra de ferramentas:

![A caixa de diálogo usada para personalizar uma barra de ferramentas](toolbar-images/info07.png "A caixa de diálogo usada para personalizar uma barra de ferramentas")

Ao configurar uma barra de ferramentas no Interface Builder do Xcode, um desenvolvedor pode fornecer itens extras da barra de ferramentas que não fazem parte de sua configuração padrão. Os usuários do aplicativo podem, então, personalizar a barra de ferramentas, adicionando e removendo esses itens predefinidos conforme necessário. É claro que a barra de ferramentas pode ser redefinida para sua configuração padrão.

A barra de ferramentas conecta-se automaticamente ao menu **Exibir** , que permite aos usuários ocultá-lo, mostrá-lo e personalizá-lo:

![Itens relacionados à barra de ferramentas no menu Exibir](toolbar-images/info08.png "Itens relacionados à barra de ferramentas no menu Exibir")

Consulte a documentação de [funcionalidade de menu interna](~/mac/user-interface/menu.md) para obter mais detalhes.

Além disso, se a barra de ferramentas estiver configurada corretamente no Interface Builder, o aplicativo manterá automaticamente as personalizações da barra de ferramentas em várias inicializações do aplicativo.

As próximas seções deste guia descrevem como criar e manter barras de ferramentas com Interface Builder do Xcode e como trabalhar com elas no código.

## <a name="setting-a-custom-main-window-controller"></a>Configurando um controlador de janela principal personalizado

Para expor elementos da interface C# do usuário a código por meio de saídas e ações, o aplicativo Xamarin. Mac deve usar um controlador de janela personalizado:

1. Abra o storyboard do aplicativo no Interface Builder do Xcode.
2. Selecione o controlador de janela na superfície de design.
3. Alterne para o **Inspetor de identidade** e insira "WindowController" como o **nome da classe**: 

    [![Configurando um nome de classe personalizado para o controlador de janela](toolbar-images/windowcontroller01.png "Configurando um nome de classe personalizado para o controlador de janela")](toolbar-images/windowcontroller01-large.png#lightbox) 

4. Salve as alterações e retorne ao Visual Studio para Mac para sincronização.
5. Um arquivo **WindowController.cs** será adicionado ao seu projeto no **painel de soluções** no Visual Studio para Mac: 

    ![Selecionando WindowController.cs no Painel de Soluções](toolbar-images/windowcontroller02.png "Selecionando WindowController.cs no Painel de Soluções")

6. Reabra o storyboard no Interface Builder do Xcode.
7. O arquivo **WindowController. h** estará disponível para uso: 

    [![O arquivo WindowController. h](toolbar-images/windowcontroller03.png "O arquivo WindowController. h")](toolbar-images/windowcontroller03-large.png#lightbox)

## <a name="creating-and-maintaining-toolbars-in-xcode"></a>Criando e mantendo barras de ferramentas no Xcode

As barras de ferramentas são criadas e mantidas com Interface Builder do Xcode. Para adicionar uma barra de ferramentas a um aplicativo, edite o storyboard principal do aplicativo (nesse caso **Main. Storyboard**) clicando duas vezes nele no **painel de soluções**:

![Abrindo Main. Storyboard no Painel de Soluções](toolbar-images/edit01.png "Abrindo Main. Storyboard no Painel de Soluções")

No **Inspetor de biblioteca**, digite "ferramenta" na **caixa de pesquisa** para facilitar a visualização de todos os itens de barra de ferramentas disponíveis:

![O Inspetor de biblioteca, filtrado para mostrar itens da barra de ferramentas](toolbar-images/edit02.png "O Inspetor de biblioteca, filtrado para mostrar itens da barra de ferramentas")

Arraste uma barra de ferramentas para a janela no **Editor de interface**. Com a barra de ferramentas selecionada, configure seu comportamento definindo propriedades no **Inspetor de atributos**:

![O Inspetor de atributos para uma barra de ferramentas](toolbar-images/edit04.png "O Inspetor de atributos para uma barra de ferramentas")

As seguintes propriedades estão disponíveis:

1. **Exibir** -controla se a barra de ferramentas exibe ícones, texto ou ambos
2. **Visível em Iniciar** – se selecionado, a barra de ferramentas ficará visível por padrão.
3. **Personalizável** -se selecionado, os usuários podem editar e personalizar a barra de ferramentas.
4. **Separador** -se selecionado, uma linha horizontal fina separa a barra de ferramentas do conteúdo da janela.
5. **Tamanho** – define o tamanho da barra de ferramentas
6. **Salvamento automático** – se selecionado, o aplicativo manterá as alterações de configuração da barra de ferramentas de um usuário nas inicializações do aplicativo.

Selecione a opção **AutoSalvar** e deixe todas as outras propriedades em suas configurações padrão. 

Depois de abrir a barra de ferramentas na **hierarquia de interface**, abra a caixa de diálogo de personalização selecionando um item da barra de ferramentas:

![Personalizando a barra de ferramentas](toolbar-images/edit05.png "Personalizando a barra de ferramentas")

Use esta caixa de diálogo para definir as propriedades de itens que já fazem parte da barra de ferramentas, para criar a barra de ferramentas padrão para o aplicativo e para fornecer itens adicionais da barra de ferramentas para que um usuário selecione ao personalizar a barra de ferramentas. Para adicionar itens à barra de ferramentas, arraste-os do **Inspetor de biblioteca**:

![O Inspetor de biblioteca](toolbar-images/edit06.png "O Inspetor de biblioteca")

Os seguintes itens da barra de ferramentas podem ser adicionados:

- **Item da barra de ferramentas de imagem** -um item da barra de ferramentas com uma imagem personalizada como um ícone.
- **Item da barra de ferramentas de espaço flexível** -espaço flexível usado para justificar itens subsequentes na barra de ferramentas. Por exemplo, um ou mais itens da barra de ferramentas seguidos por um item de barra de ferramentas de espaço flexível e outro item da barra de ferramentas fixaria o último item no lado direito da barra de ferramentas.
- **Item da barra de ferramentas de espaço** – espaço fixo entre os itens na barra de ferramentas
- **Item da barra de ferramentas separadora** -um separador visível entre dois ou mais itens da barra de ferramentas, para agrupamento
- **Personalizar item da barra de ferramentas** – permite que os usuários personalizem a barra de ferramentas
- **Imprimir item da barra de ferramentas** – permite que os usuários imprimam o documento aberto
- **Mostrar item da barra de ferramentas cores** – exibe o seletor de cores do sistema padrão: 

     ![O seletor de cores do sistema](toolbar-images/edit07.png "O seletor de cores do sistema")

- **Mostrar item da barra de ferramentas de fonte** – exibe a caixa de diálogo fonte do sistema padrão: 

     ![O seletor de fonte](toolbar-images/edit08.png "O seletor de fonte")

> [!IMPORTANT]
> Como será visto posteriormente, muitos controles padrão da interface do usuário do Cocoa, como campos de pesquisa, controles segmentados e deslizantes horizontais, também podem ser adicionados a uma barra de ferramentas.

### <a name="adding-an-item-to-a-toolbar"></a>Adicionando um item a uma barra de ferramentas

Para adicionar um item a uma barra de ferramentas, selecione a barra de ferramentas na **hierarquia de interface** e clique em um de seus itens, fazendo com que a caixa de diálogo de personalização apareça. Em seguida, arraste um novo item do **Inspetor de biblioteca** para a área de **itens da barra de ferramentas permitida** :

![Os itens da barra de ferramentas permitidos na caixa de diálogo personalização da barra de ferramentas](toolbar-images/add01.png "Os itens da barra de ferramentas permitidos na caixa de diálogo personalização da barra de ferramentas")

Para certificar-se de que um novo item faz parte da barra de ferramentas padrão, arraste-o para a área de **itens da barra de ferramentas padrão** : 

![Reordenando um item da barra de ferramentas arrastando](toolbar-images/add02.png "Reordenando um item da barra de ferramentas arrastando")

Para reordenar itens da barra de ferramentas padrão, arraste-os para a esquerda ou direita.

Em seguida, use o **Inspetor de atributos** para definir as propriedades padrão do item:

![Personalizando um item da barra de ferramentas usando o Inspetor de atributos](toolbar-images/add03.png "Personalizando um item da barra de ferramentas usando o Inspetor de atributos")

As seguintes propriedades estão disponíveis:

- **Nome da imagem** -imagem a ser usada como um ícone para o item
- **Rótulo** -texto a ser exibido para o item na barra de ferramentas
- **Rótulo da paleta** -texto a ser exibido para o item na área de **itens da barra de ferramentas permitida**
- **Marca** -um identificador opcional exclusivo que ajuda a identificar o item no código.
- **Identificador** – define o tipo de item da barra de ferramentas. Um valor personalizado pode ser usado para selecionar um item de barra de ferramentas no código.
- **Selecionável** – se marcada, o item agirá como um botão ligar/desligar.

> [!IMPORTANT]
> Adicione um item à área de **itens da barra de ferramentas permitida** , mas não à barra de ferramentas padrão para fornecer opções de personalização para os usuários. 

### <a name="adding-other-ui-controls-to-a-toolbar"></a>Adicionando outros controles de interface do usuário a uma barra de ferramentas

Vários elementos da interface do usuário do Cocoa, como campos de pesquisa e controles segmentados, também podem ser adicionados a uma barra de ferramentas.

Para testar isso, abra a barra de ferramentas na **hierarquia de interface** e selecione um item de barra de ferramentas para abrir a caixa de diálogo de personalização. Arraste um **campo de pesquisa** do **Inspetor de biblioteca** para a área de **itens da barra de ferramentas permitida** :

![Usando a caixa de diálogo personalização da barra de ferramentas](toolbar-images/add05.png "Usando a caixa de diálogo personalização da barra de ferramentas")

Aqui, use Interface Builder para configurar o campo de pesquisa e expô-lo ao código por meio de uma ação ou tomada.

## <a name="built-in-toolbar-item-support"></a>Suporte ao item da barra de ferramentas interna

Vários elementos da interface do usuário do Cocoa interagem com itens da barra de ferramentas padrão por padrão. Por exemplo, arraste uma **exibição de texto** para a janela do aplicativo e posicione-a para preencher a área de conteúdo:

[![Adicionando uma exibição de texto ao aplicativo](toolbar-images/edit09.png "Adicionando uma exibição de texto ao aplicativo")](toolbar-images/edit09-large.png#lightbox)

Salve o documento, retorne ao Visual Studio para Mac para sincronizar com o Xcode, execute o aplicativo, insira algum texto, selecione-o e clique no item da barra de ferramentas **cores** . Observe que a exibição de texto funciona automaticamente com o seletor de cores:

![Funcionalidade de barra de ferramentas interna com uma exibição de texto e um seletor de cores](toolbar-images/edit10.png "Funcionalidade de barra de ferramentas interna com uma exibição de texto e um seletor de cores")

## <a name="using-images-with-toolbar-items"></a>Usando imagens com itens da barra de ferramentas

Usando um **item da barra de ferramentas de imagem**, qualquer imagem de bitmap adicionada à pasta de **recursos** (e dada uma ação de compilação do **recurso de grupo**) pode ser exibida na barra de ferramentas como um ícone:

1. Em Visual Studio para Mac, na **painel de soluções**, clique com o botão direito do mouse na pasta **recursos** e selecione **Adicionar**  > **Adicionar arquivos**.
2. Na caixa de diálogo **Adicionar arquivos** , navegue até as imagens desejadas, selecione-as e clique no botão **abrir** : 

    [![Selecionando imagens a serem adicionadas](toolbar-images/edit11.png "Selecionando imagens a serem adicionadas")](toolbar-images/edit11-large.png#lightbox)

3. Selecione **copiar**, marque **usar a mesma ação para todos os arquivos selecionados**e clique em **OK**:

    ![Selecionando a ação de cópia para as imagens adicionadas](toolbar-images/edit12.png "Selecionando a ação de cópia para as imagens adicionadas")

4. No **painel de soluções**, clique duas vezes em **MainWindow. xib** para abri-lo no Xcode.

5. Selecione a barra de ferramentas na **hierarquia de interface** e clique em um de seus itens para abrir a caixa de diálogo de personalização.

6. Arraste um **item da barra de ferramentas da imagem** do **Inspetor de biblioteca** para a área de itens da **barra de ferramentas permitida** da barra de ferramentas: 

    ![Um item da barra de ferramentas de imagem adicionado à área itens da barra de ferramentas permitidos](toolbar-images/edit14.png "Um item da barra de ferramentas de imagem adicionado à área itens da barra de ferramentas permitidos")

7. No **Inspetor de atributos**, selecione a imagem que acabou de ser adicionada no Visual Studio para Mac: 

    ![Configurando uma imagem personalizada para um item da barra de ferramentas](toolbar-images/edit15.png "Configurando uma imagem personalizada para um item da barra de ferramentas")

8. Defina o **rótulo** como "Trash" e o **rótulo da paleta** como "Apagar documento": 

    ![Definindo o rótulo do item da barra de ferramentas e o rótulo da paleta](toolbar-images/edit16.png "Definindo o rótulo do item da barra de ferramentas e o rótulo da paleta")

9. Arraste um **item da barra de ferramentas separador** do **Inspetor de biblioteca** para a área de **itens da barra de ferramentas permitida** da barra de ferramentas 

    [![Um item da barra de ferramentas do separador adicionado à área itens da barra de ferramentas permitidos](toolbar-images/edit17.png "Um item da barra de ferramentas do separador adicionado à área itens da barra de ferramentas permitidos")](toolbar-images/edit17-large.png#lightbox)

10. Arraste o item do separador e o item "lixeira" para a área de **itens da barra de ferramentas padrão** e defina a ordem dos itens da barra de ferramentas da esquerda para a direita da seguinte maneira (cores, fontes, separador, lixeira, espaço flexível, impressão): 

    ![Os itens da barra de ferramentas padrão](toolbar-images/edit18.png "Os itens da barra de ferramentas padrão")

11. Salve as alterações e retorne a Visual Studio para Mac para sincronizar com o Xcode.

Execute o aplicativo para verificar se a nova barra de ferramentas é exibida por padrão:

![Uma barra de ferramentas com itens padrão personalizados](toolbar-images/edit19.png "Uma barra de ferramentas com itens padrão personalizados")

## <a name="exposing-toolbar-items-with-outlets-and-actions"></a>Expondo itens da barra de ferramentas com saídas e ações

Para acessar um item da barra de ferramentas ou da barra de ferramentas no código, ele deve ser anexado a uma tomada ou uma ação:

1. Na **painel de soluções**, clique duas vezes em **Main. Storyboard** para abri-lo no Xcode.
2. Verifique se a classe personalizada "WindowController" foi atribuída ao controlador de janela principal no Inspetor de **identidade**:

    [![Usando o Inspetor de identidade para definir uma classe personalizada para o controlador de janela](toolbar-images/edit20a.png "Usando o Inspetor de identidade para definir uma classe personalizada para o controlador de janela")](toolbar-images/edit20a-large.png#lightbox)

3. Em seguida, selecione o item da barra de ferramentas na **hierarquia de interface**: 

    ![Selecionando o item da barra de ferramentas na hierarquia de interface](toolbar-images/edit20.png "Selecionando o item da barra de ferramentas na hierarquia de interface")  

4. Abra o **modo de exibição assistente**, selecione o arquivo **WindowController. h** e arraste-o do item da barra de ferramentas para o arquivo **WindowController. h** .
5. Defina o tipo de **conexão** como **ação**, digite "TrashDocument" para o **nome**e clique no botão **conectar** : 

    [![Configurando uma ação para um item da barra de ferramentas](toolbar-images/edit23.png "Configurando uma ação para um item da barra de ferramentas")](toolbar-images/edit23-large.png#lightbox)

6. Expor a **exibição de texto** como uma tomada chamada "documentEditor" no arquivo **ViewController. h** : 

    [![Configurando uma tomada para a exibição de texto](toolbar-images/edit24.png "Configurando uma tomada para a exibição de texto")](toolbar-images/edit24-large.png#lightbox)

7. Salve as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Em Visual Studio para Mac, edite o arquivo **ViewController.cs** e adicione o seguinte código:

```csharp
public void EraseDocument() {
    documentEditor.Value = "";
}
```

Em seguida, edite o arquivo **WindowController.cs** e adicione o seguinte código à parte inferior da classe `WindowController`:

```csharp
[Export ("trashDocument:")]
void TrashDocument (NSObject sender) {

    var controller = ContentViewController as ViewController;
    controller.EraseDocument ();
}
```

Ao executar o aplicativo, o item da barra de ferramentas da **Lixeira** estará ativo:

![Uma barra de ferramentas com um item de lixo ativo](toolbar-images/edit25.png "Uma barra de ferramentas com um item de lixo ativo")

Observe que o item da barra de ferramentas da **Lixeira** agora pode ser usado para excluir texto.

## <a name="disabling-toolbar-items"></a>Desabilitando itens da barra de ferramentas

Para desabilitar um item em uma barra de ferramentas, crie uma classe de `NSToolbarItem` personalizada e substitua o método `Validate`. Em seguida, em Interface Builder, atribua o tipo personalizado ao item que você deseja habilitar/desabilitar.

Para criar uma classe de `NSToolbarItem` personalizada, clique com o botão direito do mouse no projeto e selecione **adicionar**  > **novo arquivo...** . Selecione **geral**  > **classe vazia**, digite "ActivatableItem" para o **nome**e clique no botão **novo** : 

![Adicionando uma classe vazia no Visual Studio para Mac](toolbar-images/custom01.png "Adicionando uma classe vazia no Visual Studio para Mac")

Em seguida, edite o arquivo **ActivatableItem.cs** para ler da seguinte maneira:

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

Clique duas vezes em **Main. Storyboard** para abri-lo no Xcode. Selecione o item da barra de ferramentas da **Lixeira** criado acima e altere sua classe para "ActivatableItem" no **Inspetor de identidade**:

![Configurando uma classe personalizada para um item da barra de ferramentas](toolbar-images/custom02.png "Configurando uma classe personalizada para um item da barra de ferramentas")

Crie uma tomada chamada `trashItem` para o item da barra de ferramentas da **Lixeira** . Salve as alterações e retorne a Visual Studio para Mac para sincronizar com o Xcode. Por fim, abra **MainWindow.cs** e atualize o método `AwakeFromNib` para ler da seguinte maneira:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Disable trash
    trashItem.Active = false;
}
```

Execute o aplicativo e observe que o item de **lixo** agora está desabilitado na barra de ferramentas:

![Uma barra de ferramentas com um item de lixo inativo](toolbar-images/custom03.png "Uma barra de ferramentas com um item de lixo inativo")

## <a name="summary"></a>Resumo

Este artigo deu uma visão detalhada de como trabalhar com barras de ferramentas e itens de barra de ferramentas em um aplicativo Xamarin. Mac. Ele descreveu como criar e manter barras de ferramentas no Interface Builder do Xcode, como alguns controles da interface do usuário funcionam automaticamente com itens da barra de ferramentas, C# como trabalhar com barras de ferramentas no código e como habilitar e Desabilitar itens da barra de ferramentas.

## <a name="related-links"></a>Links relacionados

- [MacToolbar (exemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/mactoolbar)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Diretrizes de interface humana para barras de ferramentas](https://developer.apple.com/macos/human-interface-guidelines/windows-and-views/toolbars/)
- [Introdução às barras de ferramentas](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Toolbars/Toolbars.html)
