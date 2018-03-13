---
title: Inicie a telas
description: "Este artigo explica como criar um aplicativo da tela Iniciar para todos os dispositivos iOS, em qualquer resolução e orientação, usando um Storyboard de unificado único."
ms.topic: article
ms.prod: xamarin
ms.assetid: 31A489CA-756B-4B9B-B386-4BADF18EDD33
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/19/2018
ms.openlocfilehash: 54ec41636f491708ea72585d3889fbbca85c8eb1
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="launch-screens"></a>Inicie a telas

_Este artigo explica como criar um aplicativo da tela Iniciar para todos os dispositivos iOS, em qualquer resolução e orientação, usando um Storyboard de unificado único._

Antes de iOS 8, criando uma tela de inicialização para um aplicativo iOS necessários do desenvolvedor fornecer um ativo de imagem para cada um dos vários fatores e resoluções no qual o aplicativo foi executado. Desde o lançamento do iOS 8, no entanto, ele foi possível usar um Storyboard de unificado único para criar uma tela de inicialização que parece correto em todos os casos.

Este breve passo a passo descreve como criar uma tela iniciar com qualquer um Storyboard fornecidos por padrão em um novo projeto ou um storyboard adicionado manualmente a um projeto existente. Ele demonstra como usar o Designer do iOS para adicionar uma imagem de exibição e um rótulo para o Storyboard para definir restrições sobre os modos de exibição e para verificar que o Storyboard parece correto para vários dispositivos e orientações.

<a name="storyboard" />

## <a name="managing-launch-screens-with-storyboards"></a>Gerenciando as telas de inicialização com Storyboards

No iOS 8 (e posterior), o desenvolvedor pode criar um Storyboard especial unificado para fornecer a tela Iniciar, em vez de usar uma ou mais imagens de inicialização estática. Ao criar o lançamento de um Storyboard no Designer do iOS, use Classes de tamanho e o Layout automático para definir layouts diferentes para ambientes de exibição diferente. Usando Classes de tamanho e o Layout automático, o desenvolvedor pode criar uma tela de inicialização única que parece bom em todos os dispositivos e exibir os ambientes.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. No Visual Studio para Mac, crie um novo projeto selecionando **arquivo > nova solução** e, em seguida, escolhendo **único aplicativo de exibição**: 

    ![A janela do novo projeto, com um único aplicativo de exibição selecionado](launch-screens-images/launch01.png)

    - Por padrão, um novo projeto inclui uma **LaunchScreen.storyboard** arquivo que define a interface de tela Iniciar. 
    - Para adicionar um Storyboard de tela iniciar a um projeto existente, clique no nome do projeto no **solução preenchimento** e escolha **Adicionar > novo arquivo...**  e, em seguida, selecione **tela Iniciar**:

    ![A janela do novo arquivo, com iOS tela Iniciar selecionada](launch-screens-images/launch01b.png)

    - Nomeie o arquivo **LaunchScreen** ou outro nome de sua escolha.

2. Configure o projeto para usar o Storyboard apropriado para sua tela Iniciar:

    - Clique duas vezes o **Info. plist** do arquivo no **solução preenchimento** para abri-lo para edição.
    - No **imagens de inicialização** seção, certifique-se de que **tela Iniciar** é definido como o nome do Storyboard apropriado:

    ![O seletor de tela iniciar no Info. plist](launch-screens-images/launch02.png)

    - Por padrão, um novo projeto é configurado para usar **LaunchScreen.storyboard** como sua tela Iniciar.

3. Adicionar uma imagem para o **Assets.xcassets** ativo para que ele esteja disponível para uso na tela Iniciar do catálogo. Para obter mais informações, consulte o [adicionar imagens a um conjunto de imagem de catálogo Asset](~/ios/app-fundamentals/images-icons/displaying-an-image.md) seção o [exibindo uma imagem](~/ios/app-fundamentals/images-icons/displaying-an-image.md) guia.

4. Abra **LaunchScreen.storyboard** para edição clicando duas vezes no **solução preenchimento**.

5. Escolha um dispositivo e a orientação para visualizar o Storyboard de tela iniciar no Designer de iOS. Abra o painel de seleção do dispositivo na barra de ferramentas inferior e selecione **iPhone 4S** e **retrato**.

    ![A barra de ferramentas de seleção de dispositivo](launch-screens-images/launch05.png)

    - Observe que a seleção de um dispositivo e orientação altera apenas como o Designer do iOS visualiza o design. Independentemente da seleção feita aqui, recém-adicionado restrições são aplicadas em todos os dispositivos e orientações, a menos que o **Editar características** botão foi usado para especificar outra forma. 

6. Definir o **em segundo plano** cor do modo de exibição principal do controlador de exibição. Selecione o modo de exibição clicando no meio do controlador de exibição e ajuste a cor de plano de fundo usando o **propriedades de preenchimento**:

    ![Uma exibição única com uma cor de plano de fundo roxo](launch-screens-images/launch06.png)

7. Adicionar uma **imagem** na tela Iniciar e definir seu código-fonte **imagem**:

    - Arraste um **imagem** do **preenchimento da caixa de ferramentas** para o centro do modo de exibição.
    - Com o **imagem de exibição** selecionado, no **Widget** seção o **propriedades de preenchimento** definido o **imagem** propriedade para a imagem já definida adicionado para o **Assets.xcassets** catálogo de ativos. Reposicione e dimensionar o **imagem** conforme necessário:
    
    ![Uma exibição de imagem com o conjunto de propriedades de imagem](launch-screens-images/launch07.png)

8. Adicionar um **rótulo** abaixo o **imagem** e usar o **propriedades de preenchimento** para definir seus atributos: 

    ![Um rótulo com seu conjunto de texto e cor](launch-screens-images/launch08.png)

9. Alternar para o modo de edição de restrição, usando o botão direito no **barra de ferramentas de restrições**:
    
    ![O botão de modo de edição de restrição](launch-screens-images/launch09.png)

10. Adicionar restrições para o **imagem**, definindo sua altura e largura e centralizá-lo horizontalmente e verticalmente:

    ![Uma exibição de imagem com restrições de layout](launch-screens-images/launch10.png)

    - Para obter mais detalhes sobre como adicionar restrições, consulte [Layout automático com o Designer de Xamarin para iOS](~/ios/user-interface/designer/designer-auto-layout.md).

11. Adicionar restrições para o **rótulo**, centralizando-horizontalmente, dando a ele uma altura e largura e posicionando-fixa verticalmente de distância do **imagem**:

    ![Um rótulo com restrições de layout](launch-screens-images/launch11.png)

12. Outros dispositivos e orientações para verificar que o design é conforme o esperado em todos os cenários de teste. Em casos onde ajustes precisam ser feitas para um dispositivo específico ou a orientação, use o **Editar características** botão Adicionar restrições para classes de tamanho específico:

    ![A tela Iniciar renderizado como um iPhone X usando a orientação de paisagem](launch-screens-images/launch12.png)

13. Salve as alterações para o Storyboard. Executar o aplicativo em um simulador ou dispositivo, e a tela Iniciar ficará visível enquanto o aplicativo está iniciando.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Crie um novo projeto. No Visual Studio, selecione **arquivo > Novo > projeto**e, em seguida, escolha **único aplicativo de exibição (iPhone)**:
    
    ![A janela novo projeto, com um único aplicativo de exibição (iPhone) selecionada](launch-screens-images/launch01-vs.png)

    - Nome do projeto, escolha um local e selecione **Okey**.

2. Se **recursos > LaunchScreen.xib** existe o **Solution Explorer**, excluí-la clicando duas vezes no arquivo e escolher **excluir**. Esse arquivo será substituído com um Storyboard na próxima etapa.

3. Crie um Storyboard para usar como a tela Iniciar. No **Solution Explorer**, com o botão direito no projeto e escolha **Adicionar > Novo Item...**  seguido por **Storyboard vazio**. Nome a este Storyboard **LaunchScreen.storyboard** e clique em **adicionar**:

    ![A janela Adicionar Novo Item com vazio Storyboard selecionado](launch-screens-images/launch03-vs.png)

4. Configurar o projeto para usar **LaunchScreen.storyboard** como seu Storyboard de tela Iniciar:

    - Clique duas vezes no arquivo **Info.plist** no **Gerenciador de Soluções** para abri-lo para edição. 
    - Sobre o **ativos visuais** guia, defina **tela Iniciar** para **LaunchScreen**.

    ![O seletor de tela iniciar no Info. plist](launch-screens-images/launch04-vs.png)

5. Adicione uma imagem a um catálogo de ativos no projeto para que ele esteja disponível para uso na tela Iniciar:

    - No **Gerenciador de soluções**, com o botão direito em **catálogos ativo** e selecione **adicionar o catálogo de ativos**. Nomeie esse novo catálogo de ativos **ativos**:

    ![A janela Adicionar Novo Item com o catálogo de ativos selecionados](launch-screens-images/launch05-vs.png)

    - Adicionar um novo conjunto de imagem para o **ativos** catálogo de ativos, conforme descrito no [adicionar imagens a um conjunto de imagem de catálogo do Asset](~/ios/app-fundamentals/images-icons/displaying-an-image.md) seção o [exibindo uma imagem](~/ios/app-fundamentals/images-icons/displaying-an-image.md) guia.

6. Abra **LaunchScreen.storyboard** para edição clicando duas vezes no **Gerenciador de soluções**.

    - Para editar um arquivo de Storyboard, Visual Studio precisa de uma conexão ativa para um host de compilação de Mac. Consulte o [conectar-se ao Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) guia para obter detalhes.

7. Escolha um dispositivo e a orientação para visualizar o Storyboard de tela iniciar no Designer de iOS. Abra o painel de seleção do dispositivo na barra de ferramentas inferior e selecione **iPhone 4S** e **retrato**: 
 
    ![A barra de ferramentas de seleção de dispositivo](launch-screens-images/launch07-vs.png)

    - Observe que a seleção de um dispositivo e orientação altera apenas como o Designer do iOS visualiza o design. Independentemente da seleção feita aqui, recém-adicionado restrições são aplicadas em todos os dispositivos e orientações, a menos que o **Editar características** botão foi usado para especificar outra forma. 

8. Adicionar um **View Controller** para o Storyboard arrastando um do **caixa de ferramentas** na superfície de design: 

    ![Vazio View Controller adicionado à superfície de design](launch-screens-images/launch08-vs.png)

9. Definir o **em segundo plano** cor do modo de exibição principal do controlador de exibição. Selecione o modo de exibição clicando no meio do controlador de exibição e ajuste a cor de plano de fundo usando o a **janela propriedades**:
    
    ![Uma exibição única com uma cor de plano de fundo roxo](launch-screens-images/launch09-vs.png)

10. Adicionar uma **imagem** na tela Iniciar e definir seu código-fonte **imagem**:

    - Arraste um **imagem** do **caixa de ferramentas** para o centro do modo de exibição.
    - Com o **imagem de exibição** ainda selecionada, o **Widget** seção o **janela propriedades** definido o **imagem** propriedade para definir a imagem já foi adicionado para o **ativos** catálogo de ativos. Reposicione e dimensionar o **imagem** conforme necessário:
    
    ![Uma exibição de imagem com o conjunto de propriedades de imagem](launch-screens-images/launch10-vs.png)

11. Adicionar um **rótulo** abaixo o **exibição da imagem**:

    - Arraste um **rótulo** do **caixa de ferramentas** na superfície de design, colocando-o abaixo de **imagem de exibição**.
    - Definir atributos para o **rótulo** usando o **janela propriedades**:

    ![Um rótulo com seu conjunto de texto e cor](launch-screens-images/launch11-vs.png) 

12. Alternar para o modo de edição de restrição, usando o botão direito no **barra de ferramentas de restrições**:
    
    ![O botão de modo de edição de restrição](launch-screens-images/launch12-vs.png) 

13. Adicionar restrições para o **imagem**, definindo sua altura e largura e centralizá-lo horizontalmente e verticalmente:

    ![Uma exibição de imagem com restrições de layout](launch-screens-images/launch13-vs.png) 

    - Para obter informações sobre como adicionar restrições, consulte [Layout automático com o Designer de Xamarin para iOS](~/ios/user-interface/designer/designer-auto-layout.md).

14. Adicionar restrições para o **rótulo**, centralizando-horizontalmente, dando a ele uma altura e largura e posicionando-fixa verticalmente de distância do **imagem**:
    
    ![Um rótulo com restrições de layout](launch-screens-images/launch14-vs.png) 

15. Outros dispositivos e orientações para verificar que o design é conforme o esperado em todos os cenários de teste. Em casos onde ajustes precisam ser feitas para um dispositivo específico ou a orientação, use o **Editar características** botão Adicionar restrições para classes de tamanho específico:

    ![A tela Iniciar renderizado como um iPhone X usando a orientação de paisagem](launch-screens-images/launch15-vs.png) 

16. Salve as alterações para o Storyboard. Executar o aplicativo em um simulador ou dispositivo, e a tela Iniciar ficará visível enquanto o aplicativo está iniciando.

-----

> [!NOTE]
> **Observação**: um Storyboard usado como uma tela Iniciar _deve_ incluem elementos de interface do usuário somente simples, internos e **não é possível** fazer os cálculos ou derivar de uma classe personalizada.

Para obter mais informações sobre como criar uma tela iniciar com um Storyboard unificado, consulte o [dinâmico telas iniciar](~/ios/user-interface/storyboards/unified-storyboards.md#dynamic-launch-screens) seção o [Unified Storyboards](~/ios/user-interface/storyboards/unified-storyboards.md) guia.

## <a name="migrating-to-launch-screen-storyboards"></a>Migrando para iniciar a tela Storyboards

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Ao atualizar um aplicativo existente para usar Storyboards para suas telas Iniciar, clique com botão direito do **nome do projeto** no **Solution Explorer** e selecione **adicionar**  >  **Novo arquivo...** . Selecione **iOS** > **tela Iniciar** e clique no **novo** botão:

![](launch-screens-images/storyboard02.png "Selecione um tela Iniciar do iOS")

Em seguida, clique duas vezes o `Info.plist` arquivo o **Gerenciador de soluções** para abri-lo para edição. Em **tela Iniciar**, selecione o novo arquivo de Storyboard criado acima.

![](launch-screens-images/storyboard09.png "Selecione o novo arquivo de Storyboard criado acima")


Para usar o novo Storyboard como uma tela inicial, faça o seguinte:

1. Clique duas vezes o `Info.plist` arquivo o **Gerenciador de soluções** para abri-lo para edição.
2. Role até a **imagens de inicialização Universal** seção do editor, abra o **tela Iniciar** lista suspensa e selecione o nome do storyboard criado acima: 

    ![](launch-screens-images/storyboard08.png "Definindo a tela de inicialização para o storyboard")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Clique com botão direito no nome do projeto no **Solution Explorer** e selecione **adicionar** > **novo arquivo...** : 

    ![](launch-screens-images/image012.png "Adicionar novo arquivo")
2. Insira um nome para a tela inicial e clique no **adicionar** botão: 

    ![](launch-screens-images/image013.png "Insira um nome para a tela inicial")
3. No **Solution Explorer**, duas vezes no arquivo de storyboard criado recentemente para abri-lo para edição.
4. Certifique-se de que o **tamanho classe** é definido como **qualquer: qualquer** e **exibição como** é **genérico**: 

    ![](launch-screens-images/image016.png "Certifique-se de que a classe de tamanho é definida como qualquer: qualquer e o modo como é genérico")
5. A tela inicial das Classes de tamanho, os elementos de interface do usuário simples do assembly (como `UIImageView`) e imagens que incluídos no pacote do aplicativo: 

    ![](launch-screens-images/image017.png "Assembly a tela inicial no Designer de iOS")
6. Salve as alterações para o Storyboard.

-----

## <a name="related-links"></a>Links relacionados

- [Telas de inicialização dinâmica (exemplo)](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/)
- [Storyboards unificadas](~/ios/user-interface/storyboards/unified-storyboards.md)
- [Noções básicas do Designer do iOS](~/ios/user-interface/designer/index.md)
- [Adicionando imagens a uma imagem do catálogo de ativos definida](~/ios/app-fundamentals/images-icons/displaying-an-image.md#asset-catalogs)
- [Layout automático com o Designer de Xamarin para iOS](~/ios/user-interface/designer/designer-auto-layout.md)
- [Diretrizes de Interface Humana: Tela de inicialização](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/launch-screen/)
