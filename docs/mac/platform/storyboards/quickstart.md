---
title: Storyboards no xamarin. Mac – guia de início rápido
description: Este documento fornece uma introdução de início rápido para criar interfaces do usuário com storyboards no xamarin. MAC de macOS. Ele descreve como criar um segue e criar uma janela de preferências.
ms.prod: xamarin
ms.assetid: 20719B5D-8147-4E8A-A23C-8D575C7ACCEE
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: 7f7d23a01a3c3c6567d6bab45d0abbfb078fb512
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112566"
---
# <a name="storyboards-in-xamarinmac-quick-start"></a>Storyboards no xamarin. Mac – guia de início rápido

Como uma rápida introdução ao uso de Storyboards para definir a Interface de usuário de um aplicativo xamarin. Mac, vamos começar um novo projeto do xamarin. Mac. Selecione **Aplicativo** > **Mac** > **Aplicativo Cocoa** e clique no botão **Avançar**:

[![](quickstart-images/qs01.png "Adicionando um novo aplicativo Cocoa")](quickstart-images/qs01.png#lightbox)

Use o **nome do aplicativo** dos `MacStoryboard` e clique no **próximo** botão:

[![](quickstart-images/qs02.png "Configurando o nome do aplicativo")](quickstart-images/qs02.png#lightbox)

Use o padrão **nome do projeto** e **nome da solução** e clique no **criar** botão:

[![](quickstart-images/qs03.png "Os nomes de projeto e solução")](quickstart-images/qs03.png#lightbox)

No **Gerenciador de soluções**, clique duas vezes o `Main.storyboard` arquivo para abri-lo para edição no Interface Builder do Xcode:

[![](quickstart-images/qs04.png "Editando o storyboard no Xcode")](quickstart-images/qs04.png#lightbox)

Como você pode ver acima, o padrão de Storyboard define a barra de menus do aplicativo e a janela principal com ele controlador de exibição e modo de exibição. Para nosso aplicativo de exemplo, vamos criar uma interface do usuário que tem um principal _exibição de conteúdo_ em um lado e uma _modo de exibição do Inspetor_ no segundo.

Para fazer isso, precisamos primeiro remover o controlador de exibição padrão e o modo de exibição que é fornecido com o Storyboard, selecione-o no Interface Builder e pressionando a **excluir** chave:

[![](quickstart-images/qs05.png "Remoção do controlador de exibição padrão")](quickstart-images/qs05.png#lightbox)

Em seguida, digite `split` para o **filtro** área, selecione o controlador de exibição de divisão Vertical e arraste-a para o _superfície de Design_:

[![](quickstart-images/qs06.png "Pesquisando o controlador de exibição de divisão")](quickstart-images/qs06.png#lightbox)

Observe que o controlador incluído automaticamente filho dois controladores de exibição (e seus modos de exibição relacionados), com fio-up para os lados esquerdo e direito da exibição de divisão. Para vincular o modo de divisão para a janela do pai, pressione a **controle** da chave, clique no controlador de janela (o círculo azul no quadro de janela do controlador) e arraste uma linha para o controlador de exibição de divisão. Selecione **conteúdo da janela** de pop-up:

[![](quickstart-images/qs07.png "Definindo as janelas de exibição de conteúdo")](quickstart-images/qs07.png#lightbox)

Isso ocupará o elemento de dois interface entre si usando um Segue:

[![](quickstart-images/qs08.png "O Segue entre a janela e o conteúdo")](quickstart-images/qs08.png#lightbox)

Queremos colocar uma exibição de texto no lado esquerdo da exibição de divisão e a preencher automaticamente a área disponível quando a janela ou o modo divisão é redimensionado. Arraste uma exibição de texto para o topo anexado do controlador de exibição para o modo de divisão e clique no **Pin** auto restrição de layout (o segundo ícone à direita na parte inferior da superfície de Design).

[![](quickstart-images/qs09.png "Configurando as restrições")](quickstart-images/qs09.png#lightbox)

A partir daqui clicaremos em todos os quatro a **i-beam** ícones em torno da caixa na parte superior do pop-over restrições e clique no **adicionar 4 restrições** botão na parte inferior para adicionar as restrições necessárias.

Se retornar ao Visual Studio para Mac e executar o projeto, observe que a exibição de texto é redimensionado automaticamente para preencher o lado esquerdo da exibição de divisão, como a janela ou a divisão são redimensionados:

[![](quickstart-images/qs10.png "Um exemplo de como o aplicativo em execução")](quickstart-images/qs10.png#lightbox)

Já que vamos usar o lado direito da exibição de divisão como uma área do Inspetor, queremos que ele tem um tamanho menor e permitir que ele ser recolhido. Retornar para o Xcode e editar a exibição para o lado direito selecionando-o na superfície de Design e clicando na **Inspetor de tamanho**. A partir daqui, insira um **largura** de `250`:

[![](quickstart-images/qs11.png "Definindo a largura")](quickstart-images/qs11.png#lightbox)

Selecione Avançar o Item de divisão que representa o lado direito, defina uma maior **prioridade mantendo** e clique no **usuário pode recolher** caixa de seleção:

[![](quickstart-images/qs12.png "Editando a prioridade de espera")](quickstart-images/qs12.png#lightbox)

Se devemos retornar ao Visual Studio para Mac e executar o projeto agora, observe que o lado direito mantém for menor tamanho e a janela é redimensionada:

[![](quickstart-images/qs13.png "Um exemplo de como o aplicativo em execução")](quickstart-images/qs13.png#lightbox)

<a name="Defining-a-Presentation-Segue" />

## <a name="defining-a-presentation-segue"></a>Definindo uma apresentação Segue

Vamos para o layout do lado direito da exibição de divisão para atuar como um inspetor para propriedades do texto selecionado. Arrastaremos alguns controles para o modo de exibição inferior para a interface do usuário do Inspetor de layout. O último controle, queremos exibir um pop-over que permite ao usuário selecionar de quatro estilos de caractere predefinido.

Vamos adicionar um botão para o Inspetor e um controlador de exibição para a superfície de Design. Estamos irá redimensioná o controlador de exibição para o tamanho que queremos que nosso pop-over para ser e adicione os quatro botões a ele. Em seguida, vamos **controle** chave clique no botão na exibição do Inspetor e arraste para o controlador de exibição que representará o nosso pop-over:

[![](quickstart-images/qs14.png "Arrastando para criar um novo segue")](quickstart-images/qs14.png#lightbox)

No menu pop-up, selecionaremos **pop-over**: 

[![](quickstart-images/qs15.png "Selecionar o tipo de segue")](quickstart-images/qs15.png#lightbox)

Por fim, vamos selecionar o Segue na superfície de Design e defina as **Edge preferencial** para **esquerda**. Em seguida, arrastaremos uma linha do **modo de exibição de âncora** ao botão que desejamos que o pop-over para ser anexado a:

[![](quickstart-images/qs16.png "Arrastando para criar um novo segue")](quickstart-images/qs16.png#lightbox)

Se voltarmos para Visual Studio para Mac, execute o aplicativo e clique no **None** botão no Inspetor, o pop-over será exibido:

[![](quickstart-images/qs17.png "Um exemplo de como o segue em execução")](quickstart-images/qs17.png#lightbox)

<a name="Creating-App-Preferences" />

## <a name="creating-app-preferences"></a>Criando as preferências do aplicativo

Os aplicativos macOS mais padrão fornecem uma _caixa de diálogo de preferência_ que permite ao usuário definir várias opções que controlam vários aspectos do aplicativo, como contas de usuário ou a aparência.

Para definir uma janela de caixa de diálogo de preferência padrão, arraste um controlador de exibição da guia para a superfície de Design:

[![](quickstart-images/qs18.png "Editando o storyboard no Xcode")](quickstart-images/qs18.png#lightbox)

Novamente, isso será fornecido automaticamente com filho dois controladores de exibição é anexada. Por exemplo, hospedarei, adicionaremos um rótulo para cada modo de exibição será center dentro dele:

[![](quickstart-images/qs19.png "Definir as restrições")](quickstart-images/qs19.png#lightbox)

Em seguida, queremos exibir a janela de preferências, quando o usuário seleciona o **preferências...**  item de menu. Na barra de menus, selecione o item de menu de preferências **controle** chave clique e arraste uma linha para o nosso controlador de exibição do guia:

[![](quickstart-images/qs20.png "Arrastando para criar um segue")](quickstart-images/qs20.png#lightbox)

Na janela pop-up, selecionaremos **Modal** para mostrar esta janela como uma caixa de diálogo Modal:

[![](quickstart-images/qs21.png "Selecionar o tipo de segue")](quickstart-images/qs21.png#lightbox)

Se salvamos nossas alterações, retorne ao Visual Studio para Mac, execute o aplicativo e selecione o **preferências...**  item de menu, nossas novas preferências do caixa de diálogo será exibida:

[![](quickstart-images/qs22.png "Um exemplo de como o segue em execução")](quickstart-images/qs22.png#lightbox)

Você pode notar que isso não parece com um aplicativo macOS padrão janela da caixa de diálogo de preferência. Para corrigir isso, inclua os dois arquivos de imagem no aplicativo do xamarin. Mac `Resources` pasta na **Gerenciador de soluções** e retornar ao Interface Builder do Xcode.

Selecione o controlador de exibição de guia e o comutador seus **estilo** à **barra de ferramentas**: 

[![](quickstart-images/qs23.png "Definindo o estilo de barra de guia")](quickstart-images/qs23.png#lightbox)

Selecione cada guia e dê a ele uma **rótulo** e selecione uma das imagens para representá-lo:

[![](quickstart-images/qs24.png "Configuração de cada guia no Xcode")](quickstart-images/qs24.png#lightbox)

Se salvamos nossas alterações, retorne ao Visual Studio para Mac, execute o aplicativo e selecione o **preferências...**  item de menu, a caixa de diálogo agora será exibida como um aplicativo macOS padrão:

[![](quickstart-images/qs25.png "Um exemplo de janela Preferências em execução")](quickstart-images/qs25.png#lightbox)

Para obter mais informações, consulte nosso [trabalhando com imagens](~/mac/app-fundamentals/image.md), [Menus](~/mac/user-interface/menu.md), [Windows](~/mac/user-interface/window.md) e [caixas de diálogo](~/mac/user-interface/dialog.md) documentação.

## <a name="related-links"></a>Links relacionados

- [MacStoryboard (amostra)](https://developer.xamarin.com/samples/mac/MacStoryboard/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabalhando com o Windows](~/mac/user-interface/window.md)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introdução ao Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
