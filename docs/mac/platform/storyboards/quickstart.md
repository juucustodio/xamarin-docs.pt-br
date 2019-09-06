---
title: Storyboards no Xamarin. Mac – Início Rápido
description: Este documento fornece uma introdução rápida à criação de interfaces de usuário do macOS com storyboards no Xamarin. Mac. Ele descreve como criar um transição e criar uma janela de preferências.
ms.prod: xamarin
ms.assetid: 20719B5D-8147-4E8A-A23C-8D575C7ACCEE
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 05/02/2017
ms.openlocfilehash: 498f73d6e5942fe305dc19effc83b9d976d86ac5
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291912"
---
# <a name="storyboards-in-xamarinmac-quick-start"></a>Storyboards no Xamarin. Mac – Início Rápido

Como uma rápida introdução ao uso de storyboards para definir uma interface do usuário do aplicativo Xamarin. Mac, vamos iniciar um novo projeto Xamarin. Mac. Selecione **Aplicativo** > **Mac** > **Aplicativo Cocoa** e clique no botão **Avançar**:

[![](quickstart-images/qs01.png "Adicionando um novo aplicativo Cocoa")](quickstart-images/qs01.png#lightbox)

Use o **nome** do `MacStoryboard` aplicativo e clique no botão **Avançar** :

[![](quickstart-images/qs02.png "Definindo o nome do aplicativo")](quickstart-images/qs02.png#lightbox)

Use o nome do **projeto** padrão e o **nome da solução** e clique no botão **criar** :

[![](quickstart-images/qs03.png "Os nomes do projeto e da solução")](quickstart-images/qs03.png#lightbox)

No **Gerenciador de soluções**, clique duas vezes no `Main.storyboard` arquivo para abri-lo para edição no interface Builder do Xcode:

[![](quickstart-images/qs04.png "Editando o storyboard no Xcode")](quickstart-images/qs04.png#lightbox)

Como você pode ver acima, o storyboard padrão define a barra de menus do aplicativo e sua janela principal com o modo de exibição controlador e exibição. Para nosso aplicativo de exemplo, vamos criar uma interface do usuário que tenha uma exibição de _conteúdo_ principal em um lado e uma _exibição de Inspetor_ no segundo.

Para fazer isso, precisamos primeiro remover o controlador de exibição padrão e a exibição que vem com o storyboard, selecionando-o em Interface Builder e pressionando a tecla **delete** :

[![](quickstart-images/qs05.png "Removendo o controlador de exibição padrão")](quickstart-images/qs05.png#lightbox)

Em seguida, `split` digite na área de **filtro** , selecione o controlador de exibição de divisão vertical e arraste-o para a _design Surface_:

[![](quickstart-images/qs06.png "Pesquisando o controlador de exibição de divisão")](quickstart-images/qs06.png#lightbox)

Observe que o controlador incluiu automaticamente dois controladores de exibição filho (e suas exibições relacionadas), com conexão com os lados esquerdo e direito da exibição de divisão. Para vincular a exibição de divisão à janela pai, pressione a tecla **Control** , clique no controlador de janela (o círculo azul no quadro do controlador da janela) e arraste uma linha para o controlador de exibição de divisão. Selecione o **conteúdo da janela** do pop-up:

[![](quickstart-images/qs07.png "Configurando a exibição de conteúdo do Windows")](quickstart-images/qs07.png#lightbox)

Isso ligará o elemento de dois elementos de interface usando um transição:

[![](quickstart-images/qs08.png "O transição entre a janela e o conteúdo")](quickstart-images/qs08.png#lightbox)

Queremos posicionar uma exibição de texto no lado esquerdo do modo de exibição de divisão e fazer com que ela preencha automaticamente a área disponível quando a janela ou a exibição dividida for redimensionada. Arraste uma exibição de texto para o controlador de exibição superior anexado à exibição de divisão e clique na restrição de layout automático do **PIN** (o segundo ícone, na parte inferior da design Surface).

[![](quickstart-images/qs09.png "Configurando as restrições")](quickstart-images/qs09.png#lightbox)

A partir daqui, clicaremos em todos os quatro ícones de **i-feixe** ao lado da caixa delimitadora na parte superior do popover de restrições e clicaremos no botão **Adicionar 4 restrições** na parte inferior para adicionar as restrições necessárias.

Se retornarmos para Visual Studio para Mac e executar o projeto, observe que a exibição de texto é redimensionada automaticamente para preencher o lado esquerdo do modo de exibição de divisão à medida que a janela ou a divisão são redimensionadas:

[![](quickstart-images/qs10.png "Um exemplo do aplicativo em execução")](quickstart-images/qs10.png#lightbox)

Como vamos usar o lado direito do modo de exibição de divisão como uma área de Inspetor, queremos que ele tenha um tamanho menor e permitir que ele seja recolhido. Retorne ao Xcode e edite o modo de exibição do lado direito selecionando-o na Design Surface e clicando no **Inspetor de tamanho**. A partir daqui, Insira uma `250`largura de:

[![](quickstart-images/qs11.png "Definindo a largura")](quickstart-images/qs11.png#lightbox)

Em seguida, selecione o item de divisão que representa o lado direito, defina uma **prioridade de retenção** maior e clique na caixa de seleção o **usuário pode recolher** :

[![](quickstart-images/qs12.png "Editando a prioridade de retenção")](quickstart-images/qs12.png#lightbox)

Se retornarmos para Visual Studio para Mac e executar o projeto agora, observe que o lado direito mantém seu tamanho menor e a janela é redimensionada:

[![](quickstart-images/qs13.png "Um exemplo do aplicativo em execução")](quickstart-images/qs13.png#lightbox)

<a name="Defining-a-Presentation-Segue" />

## <a name="defining-a-presentation-segue"></a>Definindo um transição de apresentação

Vamos fazer o layout do lado direito do modo de exibição de divisão para atuar como um inspetor para as propriedades do texto selecionado. Vamos arrastar alguns controles para o modo de exibição inferior para layout da interface do usuário do Inspetor. Para o último controle, queremos exibir um popover que permite ao usuário selecionar de quatro estilos de caractere predefinidos.

Adicionaremos um botão ao inspetor e um controlador de exibição ao Design Surface. Vamos redimensionar o controlador de exibição para que seja o tamanho que queremos que nosso popover seja e adicione quatro botões a ele. Em seguida, vamos **controlar** a tecla de clique no botão no modo de exibição de Inspetor e arrastar para o controlador de exibição que representará nosso popover:

[![](quickstart-images/qs14.png "Arrastando para criar um novo transição")](quickstart-images/qs14.png#lightbox)

No menu pop-up, selecionaremos **popover**: 

[![](quickstart-images/qs15.png "Selecionando o tipo transição")](quickstart-images/qs15.png#lightbox)

Por fim, vamos selecionar o Segue na superfície de Design e defina a **Borda preferencial** para **esquerda**. Em seguida, arrastaremos uma linha do **modo de exibição âncora** para o botão ao qual queremos que a popover seja anexada:

[![](quickstart-images/qs16.png "Arrastando para criar um novo transição")](quickstart-images/qs16.png#lightbox)

Se voltarmos para Visual Studio para Mac, execute o aplicativo e clique no botão **nenhum** no Inspetor, o popover será exibido:

[![](quickstart-images/qs17.png "Um exemplo de transição em execução")](quickstart-images/qs17.png#lightbox)

<a name="Creating-App-Preferences" />

## <a name="creating-app-preferences"></a>Criando preferências de aplicativo

A maioria dos aplicativos macOS padrão fornece uma _caixa de diálogo de preferência_ que permite ao usuário definir várias opções que controlam vários aspectos do aplicativo, como aparência ou contas de usuário.

Para definir uma janela de diálogo de preferência padrão, primeiro arraste um controlador de exibição de guia para a Design Surface:

[![](quickstart-images/qs18.png "Editando o storyboard no Xcode")](quickstart-images/qs18.png#lightbox)

Novamente, isso será fornecido automaticamente com dois controladores de exibição filho anexados. Por exemplo, vamos adicionar um rótulo a cada exibição que será centralizada dentro dele:

[![](quickstart-images/qs19.png "Definindo as restrições")](quickstart-images/qs19.png#lightbox)

Em seguida, queremos exibir a janela Preferências quando o usuário seleciona o item de menu **preferências...** . Na barra de menus, selecione o item de menu Preferências, **controle** chave-clique e arraste uma linha para nosso controlador de exibição de guia:

[![](quickstart-images/qs20.png "Arrastando para criar um transição")](quickstart-images/qs20.png#lightbox)

No pop-up, selecionamos **modal** para mostrar essa janela como uma caixa de diálogo modal:

[![](quickstart-images/qs21.png "Selecionando o tipo transição")](quickstart-images/qs21.png#lightbox)

Se salvarmos nossas alterações, retorne para Visual Studio para Mac, execute o aplicativo e selecione o item de menu **preferências...** , nossa nova caixa de diálogo de preferências será exibida:

[![](quickstart-images/qs22.png "Um exemplo de transição em execução")](quickstart-images/qs22.png#lightbox)

Você pode observar que isso não se parece com uma janela de diálogo de preferência de aplicativo macOS padrão. Para corrigir isso, inclua dois arquivos de imagem na pasta do `Resources` aplicativo Xamarin. Mac na **Gerenciador de soluções** e retorne ao interface Builder do Xcode.

Selecione o controlador de exibição de guia e mude seu **estilo** para **barra de ferramentas**: 

[![](quickstart-images/qs23.png "Definindo o estilo da barra de guias")](quickstart-images/qs23.png#lightbox)

Selecione cada guia e dê a ela um **rótulo** e selecione uma das imagens para representá-la:

[![](quickstart-images/qs24.png "Configurando cada guia no Xcode")](quickstart-images/qs24.png#lightbox)

Se salvarmos nossas alterações, retorne para Visual Studio para Mac, execute o aplicativo e selecione o item de menu **preferências...** , a caixa de diálogo agora será exibida como um aplicativo MacOS padrão:

[![](quickstart-images/qs25.png "Um exemplo da janela de preferências em execução")](quickstart-images/qs25.png#lightbox)

Para obter mais informações, consulte nossa documentação sobre [como trabalhar com imagens](~/mac/app-fundamentals/image.md), [menus](~/mac/user-interface/menu.md), [janelas](~/mac/user-interface/window.md) e [caixas de diálogo](~/mac/user-interface/dialog.md) .

## <a name="related-links"></a>Links relacionados

- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabalhando com o Windows](~/mac/user-interface/window.md)
- [Diretrizes de Interface Humana do macOS](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)
- [Introdução ao Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
