---
title: Storyboards no Xamarin.Mac – início rápido
description: Este documento fornece uma introdução de início rápido para a criação de interfaces de usuário com storyboards no Xamarin.Mac de macOS. Descreve como criar um segue e criar uma janela de preferências.
ms.prod: xamarin
ms.assetid: 20719B5D-8147-4E8A-A23C-8D575C7ACCEE
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/02/2017
ms.openlocfilehash: 2bf91a51a55583e2ba8ca1fc09eb3dcd0d9986cf
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792564"
---
# <a name="storyboards-in-xamarinmac--quick-start"></a>Storyboards no Xamarin.Mac – início rápido

Como introdução rápida ao uso de Storyboards para definir a Interface do usuário do aplicativo um Xamarin.Mac, vamos começar um novo projeto de Xamarin.Mac. Selecione **Aplicativo** > **Mac** > **Aplicativo Cocoa** e clique no botão **Avançar**:

[![](quickstart-images/qs01.png "Adicionando um novo aplicativo Cocoa")](quickstart-images/qs01.png#lightbox)

Use o **nome do aplicativo** de `MacStoryboard` e clique no **próximo** botão:

[![](quickstart-images/qs02.png "Definir o nome do aplicativo")](quickstart-images/qs02.png#lightbox)

Use o padrão **nome do projeto** e **nome da solução** e clique no **criar** botão:

[![](quickstart-images/qs03.png "Os nomes de projeto e solução")](quickstart-images/qs03.png#lightbox)

No **Solution Explorer**, clique duas vezes o `Main.storyboard` arquivo para abri-lo para edição no construtor de Interface do Xcode:

[![](quickstart-images/qs04.png "Editando o storyboard no Xcode")](quickstart-images/qs04.png#lightbox)

Como você pode ver acima, o padrão de Storyboard define a barra de menus do aplicativo e a janela principal com ele View Controller e modo de exibição. Para nosso aplicativo de exemplo, estamos criando uma interface do usuário que tem um principal _exibição de conteúdo_ em um lado e uma _exibição Inspetor_ no segundo.

Para fazer isso, é necessário primeiro remover o controlador de exibição padrão e o modo de exibição que acompanha o Storyboard por selecioná-lo no construtor de Interface e pressionando o **excluir** chave:

[![](quickstart-images/qs05.png "Remoção do controlador de exibição padrão")](quickstart-images/qs05.png#lightbox)

Em seguida, digite `split` para o **filtro** , selecione o controlador de exibição de divisão Vertical e arraste-a para o _superfície de Design_:

[![](quickstart-images/qs06.png "Pesquisando o controlador de exibição de divisão")](quickstart-images/qs06.png#lightbox)

Observe que o controlador automaticamente incluído filho dois controladores de exibição (e seus modos de exibição relacionados) com fio-up para a esquerda e à direita do modo divisão. Para vincular o modo de divisão para sua janela pai, pressione a **controle** da chave, clique no controlador de janela (o círculo azul no quadro de janela do controlador) e arraste uma linha para o controlador de exibição de divisão. Selecione **conteúdo da janela** de pop-up:

[![](quickstart-images/qs07.png "Configurar as janelas de exibição de conteúdo")](quickstart-images/qs07.png#lightbox)

Isso ocupará o elemento de dois interface juntos usando uma Segue:

[![](quickstart-images/qs08.png "Segue entre a janela e o conteúdo")](quickstart-images/qs08.png#lightbox)

Queremos colocar um modo de exibição de texto no lado esquerdo do modo divisão e a preencher a área disponível automaticamente quando a janela ou o modo divisão é redimensionado. Arraste uma exibição de texto na parte superior View Controller anexado para o modo de divisão e clique no **Pin** automaticamente a restrição de layout (o segundo ícone da direita na parte inferior da superfície de Design).

[![](quickstart-images/qs09.png "Configurando as restrições")](quickstart-images/qs09.png#lightbox)

Aqui vamos clicar todos os quatro a **i-beam** ícones em torno da caixa na parte superior da Popover restrições e clique no **adicionar restrições de 4** botão na parte inferior para adicionar as restrições necessárias.

Se retornar ao Visual Studio para Mac e executar o projeto, observe que a exibição de texto é redimensionado automaticamente para preencher o lado esquerdo da exibição de divisão, como a janela ou a divisão são redimensionados:

[![](quickstart-images/qs10.png "Um exemplo de como o aplicativo em execução")](quickstart-images/qs10.png#lightbox)

Já que vamos usar o lado direito do modo divisão como uma área do Inspetor, queremos que têm um tamanho menor e permitir que ele esteja recolhida. Retornar para Xcode e editar a exibição para o lado direito selecionando-o na superfície de Design e clicando no **Inspetor de tamanho**. Aqui, insira um **largura** de `250`:

[![](quickstart-images/qs11.png "Definir a largura")](quickstart-images/qs11.png#lightbox)

Selecione Avançar o Item de divisão que representa o lado direito, defina um maior **prioridade mantendo** e clique no **usuário pode recolher** caixa de seleção:

[![](quickstart-images/qs12.png "Editando a prioridade de retenção")](quickstart-images/qs12.png#lightbox)

Se, retorne ao Visual Studio para Mac e executar o projeto agora, observe que o lado direito mantém é menor tamanho e a janela será redimensionada:

[![](quickstart-images/qs13.png "Um exemplo de como o aplicativo em execução")](quickstart-images/qs13.png#lightbox)

<a name="Defining-a-Presentation-Segue" />

## <a name="defining-a-presentation-segue"></a>Definindo uma apresentação atender

Vamos layout lado direito da exibição de divisão para atuar como um Inspetor de propriedades do texto selecionado. Podemos será arraste alguns controles para o modo de exibição inferior ao layout do Inspetor de interface do usuário. Para o último controle, queremos exibir um popover que permite que o usuário selecione de quatro estilos de caractere predefinido.

Vamos adicionar um botão de um controlador de exibição para a superfície de Design e o Inspetor. Vamos redimensioná o controlador de exibição para o tamanho que queremos que nossa Popover e adicionar botões de quatro a ele. Em seguida, ela será **controle** chave-clique no botão na exibição do Inspetor e arraste para o controlador de exibição que representará o nosso popover:

[![](quickstart-images/qs14.png "Arrastar para criar um novo segue")](quickstart-images/qs14.png#lightbox)

No menu pop-up, selecionaremos **Popover**: 

[![](quickstart-images/qs15.png "Selecionar o tipo segue")](quickstart-images/qs15.png#lightbox)

Por fim, vamos selecionar o Segue na superfície de Design e defina o **preferencial borda** para **esquerda**. Em seguida, vai arrastamos uma linha do **exibição âncora** ao botão que queremos popover a ser anexado à:

[![](quickstart-images/qs16.png "Arrastar para criar um novo segue")](quickstart-images/qs16.png#lightbox)

Se voltarmos para Visual Studio para Mac, execute o aplicativo e clique no **nenhum** botão no Inspetor, o popover será exibido:

[![](quickstart-images/qs17.png "Um exemplo de como o segue em execução")](quickstart-images/qs17.png#lightbox)

<a name="Creating-App-Preferences" />

## <a name="creating-app-preferences"></a>Criando as preferências do aplicativo

Aplicativos de macOS mais padrão fornecem um _de diálogo de preferências_ que permite ao usuário definir várias opções que controlam os vários aspectos do aplicativo, como contas de usuário ou de aparência.

Para definir uma janela de caixa de diálogo de preferência padrão, primeiro arraste um controlador de exibição da guia para a superfície de Design:

[![](quickstart-images/qs18.png "Editando o storyboard no Xcode")](quickstart-images/qs18.png#lightbox)

Novamente, isso automaticamente virá com dois filhos exibir controladores anexados. Por exemplo bem, adicionaremos um rótulo para cada modo de exibição será center dentro do elemento:

[![](quickstart-images/qs19.png "Definir as restrições")](quickstart-images/qs19.png#lightbox)

Em seguida, queremos exibir a janela de preferências quando o usuário seleciona o **preferências...**  item de menu. Na barra de menus, selecione o item de menu de preferências, **controle** chave clique e arraste uma linha para nosso guia View Controller:

[![](quickstart-images/qs20.png "Arrastar para criar um segue")](quickstart-images/qs20.png#lightbox)

Na janela pop-up, selecionaremos **Modal** para mostrar esta janela como uma caixa de diálogo Modal:

[![](quickstart-images/qs21.png "Selecionar o tipo segue")](quickstart-images/qs21.png#lightbox)

Se salvamos nossas alterações, retorne ao Visual Studio para Mac, execute o aplicativo e selecione o **preferências...**  item de menu, nosso novo preferências de caixa de diálogo será exibida:

[![](quickstart-images/qs22.png "Um exemplo de como o segue em execução")](quickstart-images/qs22.png#lightbox)

Você poderá notar que isso não parece com um aplicativo padrão macOS janela da caixa de diálogo de preferências. Para corrigir isso, inclua dois arquivos de imagem no aplicativo do Xamarin.Mac `Resources` pasta o **Solution Explorer** e retornar para o construtor de Interface do Xcode.

Selecione a guia View Controller e alternar sua **estilo** para **barra de ferramentas**: 

[![](quickstart-images/qs23.png "Definindo o estilo de barra de guia")](quickstart-images/qs23.png#lightbox)

Selecione cada guia e dê a ele um **rótulo** e selecione uma das imagens para representá-lo:

[![](quickstart-images/qs24.png "Cada guia no Xcode")](quickstart-images/qs24.png#lightbox)

Se salvamos nossas alterações, retorne ao Visual Studio para Mac, execute o aplicativo e selecione o **preferências...**  item de menu, a caixa de diálogo agora será exibida como um aplicativo macOS padrão:

[![](quickstart-images/qs25.png "Um exemplo de janela Preferências em execução")](quickstart-images/qs25.png#lightbox)

Para obter mais informações, consulte nosso [trabalhando com imagens](~/mac/app-fundamentals/image.md), [Menus](~/mac/user-interface/menu.md), [Windows](~/mac/user-interface/window.md) e [caixas de diálogo](~/mac/user-interface/dialog.md) documentação.

## <a name="related-links"></a>Links relacionados

- [MacStoryboard (exemplo)](https://developer.xamarin.com/samples/mac/MacStoryboard/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabalhando com o Windows](~/mac/user-interface/window.md)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introdução ao Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
