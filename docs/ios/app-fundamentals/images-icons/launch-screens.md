---
title: Inicie as telas para aplicativos xamarin. IOS
description: Este artigo explica como criar um tela de inicialização do aplicativo para todos os dispositivos iOS, em qualquer resolução e orientação, usando um Storyboard de único e unificado.
ms.prod: xamarin
ms.assetid: 31A489CA-756B-4B9B-B386-4BADF18EDD33
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/02/2018
ms.openlocfilehash: 40b8c38e89e96223bbf657ff06356d9fb2e9d9b3
ms.sourcegitcommit: e64c3c10d6a36b3b031d6d4dbff7af74ab2b7f21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2018
ms.locfileid: "40251128"
---
# <a name="launch-screens-for-xamarinios-apps"></a>Inicie as telas para aplicativos xamarin. IOS

_Este artigo explica como criar um tela de inicialização do aplicativo para todos os dispositivos iOS, em qualquer resolução e orientação, usando um Storyboard de único e unificado._

Antes de iOS 8, criar uma tela de inicialização para um aplicativo iOS exigia que o desenvolvedor fornecer um ativo de imagem para cada um dos diversos fatores forma de dispositivo e resoluções no qual o aplicativo foi executado. Desde o lançamento do iOS 8, no entanto, era possível usar um Storyboard de único e unificado para criar uma tela de inicialização que parece correto em todos os casos.

Este breve passo a passo descreve como criar uma tela de inicialização com qualquer um Storyboard fornecidos por padrão em um novo projeto ou com um Storyboard adicionado manualmente a um projeto existente. Em seguida, demonstra como usar o Designer do iOS para adicionar uma exibição de imagem e um rótulo para o Storyboard para definir restrições sobre esses modos de exibição e para verificar se o Storyboard parece correto para vários dispositivos e orientações.

<a name="storyboard" />

## <a name="managing-launch-screens-with-storyboards"></a>Gerenciando as telas de inicialização com Storyboards

No iOS 8 (e posterior), o desenvolvedor pode criar um Storyboard Unified especial para fornecer a tela de inicialização em vez de usar uma ou mais imagens de inicialização estática. Ao criar o lançamento de um Storyboard no Designer do iOS, use as Classes de tamanho e o Layout automático para definir diferentes layouts para ambientes de exibição diferentes. Usando Classes de tamanho e o Layout automático, o desenvolvedor pode criar uma tela de inicialização única que parece bom em todos os dispositivos e exibir os ambientes.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. No Visual Studio para Mac, criar um novo projeto, selecionando **arquivo > nova solução** e, em seguida, escolhendo **aplicativo de exibição única**: 

    ![Janela novo projeto, com um único aplicativo de modo de exibição selecionado](launch-screens-images/launch01.png)

    - Por padrão, um novo projeto inclui um **LaunchScreen.storyboard** arquivo que define a interface de tela de inicialização. 
    - Para adicionar um Storyboard de tela Iniciar para um projeto existente, clique com botão direito no nome do projeto na **painel de soluções** e escolha **Adicionar > novo arquivo...**  e, em seguida, selecione **tela de inicialização**:

    ![A janela do novo arquivo, com tela de inicialização selecionado de iOS](launch-screens-images/launch01b.png)

    - Nomeie o arquivo **LaunchScreen** ou outro nome de sua escolha.

2. Configure o projeto para usar o Storyboard apropriado para sua tela de inicialização:

    - Clique duas vezes o **Info. plist** arquivo na **painel de soluções** para abri-lo para edição.
    - No **imagens de inicialização** seção, certifique-se de que **tela de inicialização** é definido como o nome do Storyboard apropriado:

    ![O seletor de tela de inicialização no Info. plist](launch-screens-images/launch02.png)

    - Por padrão, um novo projeto é configurado para usar **LaunchScreen.storyboard** como sua tela de inicialização.

3. Adicionar uma imagem para o **xcassets** ativo do catálogo para que ele esteja disponível para uso na tela de inicialização. Para obter mais informações, consulte o [adicionar imagens a um conjunto de imagem de catálogo do Asset](~/ios/app-fundamentals/images-icons/displaying-an-image.md) seção o [exibindo uma imagem](~/ios/app-fundamentals/images-icons/displaying-an-image.md) guia.

4. Abra **LaunchScreen.storyboard** para edição clicando nele duas vezes na **painel de soluções**.

5. Escolha um dispositivo e a orientação no qual o Storyboard de tela iniciar no iOS Designer de visualização. Abra o painel de seleção do dispositivo na barra de ferramentas inferior e selecione **iPhone 4S** e **retrato**.

    ![A barra de ferramentas de seleção do dispositivo](launch-screens-images/launch05.png)

    - Observe que a seleção de um dispositivo e orientação altera apenas como o Designer do iOS visualiza o design. Independentemente da seleção feita aqui, recentemente adicionados restrições são aplicadas a todos os dispositivos e orientações, a menos que o **Editar características** botão foi usado para especifique de outra forma. 

6. Defina as **plano de fundo** cor do modo de exibição principal do controlador de exibição. Selecione o modo de exibição clicando no meio do controlador de exibição e ajustar a cor do plano de fundo usando o **painel de propriedades**:

    ![Uma única exibição com uma cor de plano de fundo roxo](launch-screens-images/launch06.png)

7. Adicionar um **modo de exibição de imagem** para a tela de inicialização e define sua origem **imagem**:

    - Arraste um **modo de exibição de imagem** da **painel de caixa de ferramentas** para o centro da exibição.
    - Com o **modo de exibição de imagem** selecionado, no **Widget** seção o **painel de propriedades** definir o **imagem** propriedade para a imagem já definido adicionado para o **xcassets** catálogo de ativos. Reposicionar e dimensionar o **modo de exibição de imagem** conforme necessário:
    
    ![Uma exibição de imagem com o conjunto de propriedades de imagem](launch-screens-images/launch07.png)

8. Adicionar um **rótulo** abaixo de **exibição de imagem** e usar o **painel de propriedades** para definir seus atributos: 

    ![Um rótulo com seu conjunto de texto e cor](launch-screens-images/launch08.png)

9. Alternar para o modo de edição de restrição usando o botão direito no **barra de ferramentas de restrições**:
    
    ![O botão de modo de edição de restrição](launch-screens-images/launch09.png)

10. Adicionar restrições para o **modo de exibição de imagem**, definindo sua altura e largura e centralizando-lo horizontalmente e verticalmente:

    ![Um modo de exibição de imagem com restrições de layout](launch-screens-images/launch10.png)

    - Para obter mais detalhes sobre como adicionar restrições, consulte [Layout automático com o Designer do Xamarin para iOS](~/ios/user-interface/designer/designer-auto-layout.md).

11. Adicionar restrições para o **rótulo**, centralizando-la horizontalmente, dando a ele uma altura e largura e posicionando-fixo distância vertical entre a **exibição de imagem**:

    ![Um rótulo com restrições de layout](launch-screens-images/launch11.png)

12. Outros dispositivos e orientações para verificar o design se parece conforme o esperado em todos os cenários de teste. Em casos em que os ajustes precisam ser feitas para um dispositivo específico ou orientação, use o **Editar características** botão para adicionar as restrições para classes de tamanho específico:

    ![A tela de inicialização renderizado como um iPhone X usando a orientação de paisagem](launch-screens-images/launch12.png)

13. Salve as alterações para o Storyboard. Execute o aplicativo em um dispositivo ou simulador, e a tela de inicialização ficará visível enquanto o aplicativo é iniciado.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Crie um novo projeto. No Visual Studio, selecione **arquivo > Novo > projeto > Visual c# > iPhone & iPad > aplicativo iOS (Xamarin)**:

    ![A janela novo projeto, com o aplicativo iOS (Xamarin) selecionada](launch-screens-images/launch01.w157.png)

    Selecione o **aplicativo de exibição única** modelo e clique **Okey**:

    ![Modelo de aplicativo de exibição único](launch-screens-images/launch01-2.w157.png)

2. Se **recursos > LaunchScreen.xib** existe na **Gerenciador de soluções**, excluí-lo clicando duas vezes no arquivo e escolher **excluir**. Esse arquivo será substituído com um Storyboard na próxima etapa.

3. Crie um Storyboard a ser usada como a tela de inicialização. No **Gerenciador de soluções**, clique com botão direito no projeto e escolha **Adicionar > Novo Item...**  seguido por **Storyboard vazio**. Nomeie esse Storyboard **LaunchScreen.storyboard** e clique em **Add**:

    ![Janela Adicionar Novo Item, com um Storyboard vazio selecionado](launch-screens-images/launch03.w157.png)

4. Configurar o projeto para usar **LaunchScreen.storyboard** como seu Storyboard de tela Iniciar:

    - Clique duas vezes no arquivo **Info.plist** no **Gerenciador de Soluções** para abri-lo para edição. 
    - Sobre o **ativos visuais** guia, defina **tela de inicialização** para **LaunchScreen**.

    ![O seletor de tela de inicialização no Info. plist](launch-screens-images/launch04-vs.png)

5. Adicione uma imagem a um catálogo de ativos no projeto para que ele esteja disponível para uso na tela de inicialização:

    - No **Gerenciador de soluções**, clique duas vezes em **catálogos de ativos** e selecione **Adicionar catálogo de ativos**. Nomeie esse novo catálogo de ativos **ativos**:

    ![A janela Adicionar Novo Item com o catálogo de ativos selecionados](launch-screens-images/launch05.w157.png)

    - Adicionar um novo conjunto de imagem para o **ativos** catálogo de ativos, conforme descrito na [adicionando imagens a um conjunto de imagem de catálogo do Asset](~/ios/app-fundamentals/images-icons/displaying-an-image.md) seção o [exibindo uma imagem](~/ios/app-fundamentals/images-icons/displaying-an-image.md) guia.

6. Abra **LaunchScreen.storyboard** para edição clicando nele duas vezes na **Gerenciador de soluções**.

    - Para editar um arquivo de Storyboard, Visual Studio precisa de uma conexão ativa para um host de build do Mac. Consulte a [conectando ao Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) guia para obter detalhes.

7. Escolha um dispositivo e a orientação no qual o Storyboard de tela iniciar no iOS Designer de visualização. Abra o painel de seleção do dispositivo na barra de ferramentas inferior e selecione **iPhone 4S** e **retrato**: 
 
    ![A barra de ferramentas de seleção do dispositivo](launch-screens-images/launch07-vs.png)

    - Observe que a seleção de um dispositivo e orientação altera apenas como o Designer do iOS visualiza o design. Independentemente da seleção feita aqui, recentemente adicionados restrições são aplicadas a todos os dispositivos e orientações, a menos que o **Editar características** botão foi usado para especifique de outra forma. 

8. Adicionar um **controlador de exibição** ao Storyboard, arrastando um da **caixa de ferramentas** na superfície de design: 

    ![Controlador de exibição de vazia adicionada à superfície de design](launch-screens-images/launch08-vs.png)

9. Defina as **plano de fundo** cor do modo de exibição principal do controlador de exibição. Selecione o modo de exibição clicando no meio do controlador de exibição e ajustar a cor do plano de fundo usando o a **janela de propriedades**:
    
    ![Uma única exibição com uma cor de plano de fundo roxo](launch-screens-images/launch09-vs.png)

10. Adicionar um **modo de exibição de imagem** para a tela de inicialização e define sua origem **imagem**:

    - Arraste uma **modo de exibição de imagem** da **caixa de ferramentas** para o centro da exibição.
    - Com o **modo de exibição de imagem** ainda selecionado, o **Widget** seção o **janela propriedades** definir o **imagem** propriedade para definir a imagem já foi adicionado para o **ativos** catálogo de ativos. Reposicionar e dimensionar o **modo de exibição de imagem** conforme necessário:
    
    ![Uma exibição de imagem com o conjunto de propriedades de imagem](launch-screens-images/launch10-vs.png)

11. Adicionar um **etiqueta** abaixo de **exibição de imagem**:

    - Arrastar um **rótulo** da **caixa de ferramentas** para a superfície de design, colocando-o abaixo de **modo de exibição de imagem**.
    - Definir atributos para o **etiqueta** usando o **janela propriedades**:

    ![Um rótulo com seu conjunto de texto e cor](launch-screens-images/launch11-vs.png) 

12. Alternar para o modo de edição de restrição usando o botão direito no **barra de ferramentas de restrições**:
    
    ![O botão de modo de edição de restrição](launch-screens-images/launch12-vs.png) 

13. Adicionar restrições para o **modo de exibição de imagem**, definindo sua altura e largura e centralizando-lo horizontalmente e verticalmente:

    ![Um modo de exibição de imagem com restrições de layout](launch-screens-images/launch13-vs.png) 

    - Para obter informações sobre como adicionar restrições, consulte [Layout automático com o Designer do Xamarin para iOS](~/ios/user-interface/designer/designer-auto-layout.md).

14. Adicionar restrições para o **rótulo**, centralizando-la horizontalmente, dando a ele uma altura e largura e posicionando-fixo distância vertical entre a **exibição de imagem**:
    
    ![Um rótulo com restrições de layout](launch-screens-images/launch14-vs.png) 

15. Outros dispositivos e orientações para verificar o design se parece conforme o esperado em todos os cenários de teste. Em casos em que os ajustes precisam ser feitas para um dispositivo específico ou orientação, use o **Editar características** botão para adicionar as restrições para classes de tamanho específico:

    ![A tela de inicialização renderizado como um iPhone X usando a orientação de paisagem](launch-screens-images/launch15-vs.png) 

16. Salve as alterações para o Storyboard. Execute o aplicativo em um dispositivo ou simulador, e a tela de inicialização ficará visível enquanto o aplicativo é iniciado.

-----

> [!NOTE]
> Um Storyboard usado como uma tela de inicialização _devem_ incluir elementos de interface do usuário internos, apenas simples e **não é possível** fazer os cálculos ou derivar de uma classe personalizada.

Para obter mais informações sobre como criar uma tela de inicialização com um Storyboard unificado, consulte o [telas de inicialização dinâmica](~/ios/user-interface/storyboards/unified-storyboards.md#dynamic-launch-screens) seção o [Storyboards unificados](~/ios/user-interface/storyboards/unified-storyboards.md) guia.

## <a name="migrating-to-launch-screen-storyboards"></a>Migrando para iniciar Storyboards de tela

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Ao atualizar um aplicativo existente para usar Storyboards para suas telas de inicialização, clique com botão direito do **nome do projeto** na **Gerenciador de soluções** e selecione **Add**  >  **Novo arquivo...** . Selecione **iOS** > **tela de inicialização** e clique no **New** botão:

![](launch-screens-images/storyboard02.png "Selecione uma tela de inicialização do iOS")

Em seguida, clique duas vezes o `Info.plist` arquivo o **Gerenciador de soluções** para abri-lo para edição. Sob **tela de inicialização**, selecione o novo arquivo de Storyboard criado acima.

![](launch-screens-images/storyboard09.png "Selecione o novo arquivo de Storyboard criado acima")


Para usar o novo Storyboard como uma tela de inicialização, faça o seguinte:

1. Clique duas vezes o `Info.plist` do arquivo na **Gerenciador de soluções** para abri-lo para edição.
2. Role até a **imagens de inicialização Universal** seção do editor, abra o **tela de inicialização** lista suspensa e selecione o nome do storyboard criado acima: 

    ![](launch-screens-images/storyboard08.png "Definindo a tela de inicialização ao storyboard")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Clique com botão direito no nome do projeto na **Gerenciador de soluções** e selecione **Add** > **novo arquivo...** : 

    ![](launch-screens-images/image012.png "Adicionar novo arquivo")
2. Insira um nome para a tela de inicialização e clique no **adicionar** botão: 

    ![](launch-screens-images/image013.png "Insira um nome para a tela de inicialização")
3. No **Gerenciador de soluções**, duas vezes no arquivo de storyboard criado recentemente para abri-lo para edição.
4. Certifique-se de que o **classe de tamanho** é definido como **qualquer: qualquer** e o **exibir como** é **genérico**: 

    ![](launch-screens-images/image016.png "Certifique-se de que a classe de tamanho definida para qualquer: qualquer e o modo de exibição como é genérico")
5. A tela de inicialização das Classes de tamanho, os elementos de interface do usuário simples do assembly (como `UIImageView`) e imagens que você incluiu no pacote do aplicativo: 

    ![](launch-screens-images/image017.png "Assembly a tela de inicialização no iOS Designer")
6. Salve as alterações para o Storyboard.

-----

## <a name="related-links"></a>Links relacionados

- [Telas de inicialização dinâmica (amostra)](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/)
- [Storyboards unificadas](~/ios/user-interface/storyboards/unified-storyboards.md)
- [Noções básicas do Designer do iOS](~/ios/user-interface/designer/index.md)
- [Adicionar imagens a uma imagem do catálogo de ativos definida](~/ios/app-fundamentals/images-icons/displaying-an-image.md#adding-images-to-an-asset-catalog-image-set)
- [Layout automático com o Designer do Xamarin para iOS](~/ios/user-interface/designer/designer-auto-layout.md)
- [Diretrizes de Interface Humana: Tela de inicialização](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/launch-screen/)
