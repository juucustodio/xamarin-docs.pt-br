---
title: Iniciar telas para aplicativos Xamarin. iOS
description: Este artigo explica como criar uma tela de inicialização de aplicativo para todos os dispositivos iOS, em qualquer resolução e orientação, usando um único storyboard unificado.
ms.prod: xamarin
ms.assetid: 31A489CA-756B-4B9B-B386-4BADF18EDD33
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 05/02/2018
ms.openlocfilehash: d0d5452c2b79fb674e473efd50aaf587d64c4544
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290245"
---
# <a name="launch-screens-for-xamarinios-apps"></a>Iniciar telas para aplicativos Xamarin. iOS

_Este artigo explica como criar uma tela de inicialização de aplicativo para todos os dispositivos iOS, em qualquer resolução e orientação, usando um único storyboard unificado._

Antes do iOS 8, a criação de uma tela de inicialização para um aplicativo iOS exigia que o desenvolvedor fornecesse um ativo de imagem para cada um dos vários fatores forma de dispositivo e resoluções em que o aplicativo poderia ser executado. No entanto, desde o lançamento do iOS 8, é possível usar um único storyboard unificado para criar uma tela de inicialização que parece correta em todos os casos.

Esta breve explicação explica como criar uma tela de inicialização com um storyboard fornecido por padrão em um novo projeto ou com um storyboard adicionado manualmente a um projeto existente. Em seguida, ele demonstra como usar o designer do iOS para adicionar uma exibição de imagem e um rótulo ao storyboard, definir restrições nessas exibições e verificar se o storyboard parece correto para vários dispositivos e orientações.

<a name="storyboard" />

## <a name="managing-launch-screens-with-storyboards"></a>Gerenciando telas de inicialização com storyboards

No iOS 8 (e posterior), o desenvolvedor pode criar um storyboard unificado especial para fornecer a tela de inicialização em vez de usar uma ou mais imagens de inicialização estática. Ao criar um storyboard de lançamento no designer do iOS, use classes de tamanho e layout automático para definir layouts diferentes para diferentes ambientes de exibição. Usando classes de tamanho e layout automático, o desenvolvedor pode criar uma tela de inicialização única que parece boa em todos os dispositivos e ambientes de exibição.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Em Visual Studio para Mac, crie um novo projeto selecionando **arquivo > nova solução** e, em seguida, escolhendo **aplicativo de modo de exibição único**: 

    ![A janela novo projeto, com o aplicativo de exibição única selecionado](launch-screens-images/launch01.png)

    - Por padrão, um novo projeto inclui um arquivo **LaunchScreen. Storyboard** que define a interface da tela de inicialização. 
    - Para adicionar um storyboard da tela de inicialização a um projeto existente, clique com o botão direito do mouse no nome do projeto na **painel de soluções** e escolha **Adicionar > novo arquivo...** e, em seguida, selecione **tela de inicialização**:

    ![A janela novo arquivo, com a tela inicialização do iOS selecionada](launch-screens-images/launch01b.png)

    - Nomeie o arquivo **LaunchScreen** ou outro nome de sua escolha.

2. Configure o projeto para usar o storyboard apropriado para sua tela de inicialização:

    - Clique duas vezes no arquivo **info. plist** no **painel de soluções** para abri-lo para edição.
    - Na seção **Iniciar imagens** , verifique se a **tela iniciar** está definida com o nome do storyboard apropriado:

    ![O seletor de tela de inicialização no info. plist](launch-screens-images/launch02.png)

    - Por padrão, um novo projeto é configurado para usar **LaunchScreen. Storyboard** como tela de inicialização.

3. Adicione uma imagem ao catálogo de ativos do assets **. xcassets** para que ele esteja disponível para uso na tela de inicialização. Para obter mais informações, consulte a seção [adicionando imagens a um conjunto de imagens do catálogo de ativos](~/ios/app-fundamentals/images-icons/displaying-an-image.md) do guia [exibindo uma imagem](~/ios/app-fundamentals/images-icons/displaying-an-image.md) .

4. Abra **LaunchScreen. Storyboard** para edição clicando duas vezes nele no **painel de soluções**.

5. Escolha um dispositivo e uma orientação para visualizar o storyboard da tela de inicialização no designer do iOS. Abra o painel seleção de dispositivo na barra de ferramentas inferior e selecione **iPhone 4S** e **retrato**.

    ![A barra de ferramentas de seleção de dispositivo](launch-screens-images/launch05.png)

    - Observe que a seleção de um dispositivo e orientação altera apenas a forma como o designer do iOS visualiza o design. Independentemente da seleção feita aqui, as restrições adicionadas recentemente são aplicadas em todos os dispositivos e orientações, a menos que o botão **Editar características** tenha sido usado para especificar o contrário. 

6. Defina a cor da tela de **fundo** da exibição principal do controlador de exibição. Selecione o modo de exibição clicando no meio do controlador de exibição e ajuste a cor do plano de fundo usando o **painel de propriedades**:

    ![Uma única exibição com uma cor de fundo roxa](launch-screens-images/launch06.png)

7. Adicione uma **exibição de imagem** à tela de inicialização e defina sua **imagem**de origem:

    - Arraste um **modo de exibição de imagem** do **painel caixa de ferramentas** para o centro da exibição.
    - Com a **exibição de imagem** selecionada, na seção **widget** do **painel de propriedades** defina a propriedade **imagem** como o conjunto de imagens já adicionado ao catálogo de ativos do assets **. xcassets** . Reposicione e dimensione a **exibição de imagem** conforme necessário:
    
    ![Um modo de exibição de imagem com seu conjunto de propriedades de imagem](launch-screens-images/launch07.png)

8. Adicione um **rótulo** abaixo da **exibição de imagem** e use o **painel de propriedades** para definir seus atributos: 

    ![Um rótulo com seu conjunto de cores e texto](launch-screens-images/launch08.png)

9. Alterne para o modo de edição de restrição usando o botão direito na **barra de ferramentas restrições**:
    
    ![O botão modo de edição de restrição](launch-screens-images/launch09.png)

10. Adicione restrições à **exibição de imagem**, definindo sua altura e largura e centralizando-a horizontalmente e verticalmente:

    ![Uma exibição de imagem com restrições de layout](launch-screens-images/launch10.png)

    - Para obter mais detalhes sobre como adicionar restrições, consulte [layout automático com o Xamarin designer para IOS](~/ios/user-interface/designer/designer-auto-layout.md).

11. Adicione restrições ao **rótulo**, centralize-o horizontalmente, dando a ele uma altura e uma largura e posicionando-o verticalmente uma distância fixa na **exibição de imagem**:

    ![Um rótulo com restrições de layout](launch-screens-images/launch11.png)

12. Teste outros dispositivos e orientações para verificar se o design se parece com o esperado em todos os cenários. Nos casos em que os ajustes precisam ser feitos para um dispositivo ou uma orientação específica, use o botão **Editar características** para adicionar restrições para classes de tamanho específicas:

    ![A tela de inicialização renderizada como um iPhone X usando a orientação paisagem](launch-screens-images/launch12.png)

13. Salve as alterações no storyboard. Execute o aplicativo em um simulador ou dispositivo e a tela de inicialização ficará visível enquanto o aplicativo estiver sendo iniciado.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Crie um novo projeto. No Visual Studio, selecione **arquivo > novo > projeto > Visual C# > iPhone & iPad > aplicativo IOS (Xamarin)** :

    ![A janela novo projeto, com aplicativo iOS (Xamarin) selecionado](launch-screens-images/launch01.w157.png)

    Selecione o modelo **aplicativo de modo de exibição único** e clique em **OK**:

    ![Modelo de aplicativo de modo de exibição único](launch-screens-images/launch01-2.w157.png)

2. Se houver **recursos > LaunchScreen. xib** no **Gerenciador de soluções**, exclua-o clicando com o botão direito do mouse no arquivo e escolhendo **excluir**. Esse arquivo será substituído por um storyboard na próxima etapa.

3. Crie um Storyboard para usar como a tela de inicialização. Na **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e escolha **Adicionar > novo item...** seguido por **storyboard vazio**. Nomeie este storyboard **LaunchScreen. Storyboard** e clique em **Adicionar**:

    ![A janela Adicionar novo item, com storyboard vazio selecionado](launch-screens-images/launch03.w157.png)

4. Configure o projeto para usar **LaunchScreen. Storyboard** como o storyboard da tela de inicialização:

    - Clique duas vezes no arquivo **Info.plist** no **Gerenciador de Soluções** para abri-lo para edição. 
    - Na guia **ativos visuais** , defina **tela de inicialização** como **LaunchScreen**.

    ![O seletor de tela de inicialização no info. plist](launch-screens-images/launch04-vs.png)

5. Adicione uma imagem a um catálogo de ativos no projeto para que ele esteja disponível para uso na tela de inicialização:

    - Na **Gerenciador de soluções**, clique com o botão direito do mouse em **catálogos de ativos** e selecione **Adicionar Catálogo de ativos**. Nomeie os novos **ativos**do catálogo de ativos:

    ![A janela Adicionar novo item, com o catálogo de ativos selecionado](launch-screens-images/launch05.w157.png)

    - Adicione uma nova imagem definida ao catálogo de ativos de **ativos** , conforme descrito na seção [adicionando imagens a um conjunto de imagens do catálogo de ativos](~/ios/app-fundamentals/images-icons/displaying-an-image.md) do guia [exibindo uma imagem](~/ios/app-fundamentals/images-icons/displaying-an-image.md) .

6. Abra **LaunchScreen. Storyboard** para edição clicando duas vezes nele no **Gerenciador de soluções**.

    - Para editar um arquivo de storyboard, o Visual Studio precisa de uma conexão ativa com um host de Build do Mac. Consulte o guia [conectando-se ao Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) para obter detalhes.

7. Escolha um dispositivo e uma orientação para visualizar o storyboard da tela de inicialização no designer do iOS. Abra o painel de seleção de dispositivo na barra de ferramentas inferior e selecione **iPhone 4S** e **retrato**: 
 
    ![A barra de ferramentas de seleção de dispositivo](launch-screens-images/launch07-vs.png)

    - Observe que a seleção de um dispositivo e orientação altera apenas a forma como o designer do iOS visualiza o design. Independentemente da seleção feita aqui, as restrições adicionadas recentemente são aplicadas em todos os dispositivos e orientações, a menos que o botão **Editar características** tenha sido usado para especificar o contrário. 

8. Adicione um **controlador de exibição** ao storyboard arrastando um da **caixa de ferramentas** para a superfície de design: 

    ![Um controlador de exibição vazio adicionado à superfície de design](launch-screens-images/launch08-vs.png)

9. Defina a cor da tela de **fundo** da exibição principal do controlador de exibição. Selecione o modo de exibição clicando no meio do controlador de exibição e ajuste a cor do plano de fundo usando a **janela Propriedades**:
    
    ![Uma única exibição com uma cor de fundo roxa](launch-screens-images/launch09-vs.png)

10. Adicione uma **exibição de imagem** à tela de inicialização e defina sua **imagem**de origem:

    - Arraste um **modo de exibição de imagem** da **caixa de ferramentas** para o centro da exibição.
    - Com a **exibição de imagem** ainda selecionada, na **seção widget** da **janela Propriedades** , defina a propriedade **imagem** como o conjunto de imagens já adicionado ao catálogo de ativos de **ativos** . Reposicione e dimensione a **exibição de imagem** conforme necessário:
    
    ![Um modo de exibição de imagem com seu conjunto de propriedades de imagem](launch-screens-images/launch10-vs.png)

11. Adicione um **rótulo** abaixo da **exibição de imagem**:

    - Arraste um **rótulo** da **caixa de ferramentas** para a superfície de design, colocando-o abaixo da **exibição de imagem**.
    - Defina atributos para o **rótulo** usando a **janela Propriedades**:

    ![Um rótulo com seu conjunto de cores e texto](launch-screens-images/launch11-vs.png) 

12. Alterne para o modo de edição de restrição usando o botão direito na **barra de ferramentas restrições**:
    
    ![O botão modo de edição de restrição](launch-screens-images/launch12-vs.png) 

13. Adicione restrições à **exibição de imagem**, definindo sua altura e largura e centralizando-a horizontalmente e verticalmente:

    ![Uma exibição de imagem com restrições de layout](launch-screens-images/launch13-vs.png) 

    - Para obter informações sobre como adicionar restrições, consulte [layout automático com o Xamarin designer para IOS](~/ios/user-interface/designer/designer-auto-layout.md).

14. Adicione restrições ao **rótulo**, centralize-o horizontalmente, dando a ele uma altura e uma largura e posicionando-o verticalmente uma distância fixa na **exibição de imagem**:
    
    ![Um rótulo com restrições de layout](launch-screens-images/launch14-vs.png) 

15. Teste outros dispositivos e orientações para verificar se o design se parece com o esperado em todos os cenários. Nos casos em que os ajustes precisam ser feitos para um dispositivo ou uma orientação específica, use o botão **Editar características** para adicionar restrições para classes de tamanho específicas:

    ![A tela de inicialização renderizada como um iPhone X usando a orientação paisagem](launch-screens-images/launch15-vs.png) 

16. Salve as alterações no storyboard. Execute o aplicativo em um simulador ou dispositivo e a tela de inicialização ficará visível enquanto o aplicativo estiver sendo iniciado.

-----

> [!NOTE]
> Um storyboard usado como uma tela de inicialização _deve_ incluir apenas elementos de interface do usuário simples e internos e **não pode** fazer cálculos ou derivar de uma classe personalizada.

Para obter mais informações sobre como criar uma tela de inicialização com um storyboard unificado, consulte a seção [telas de inicialização dinâmica](~/ios/user-interface/storyboards/unified-storyboards.md#dynamic-launch-screens) do guia de [storyboards unificados](~/ios/user-interface/storyboards/unified-storyboards.md) .

## <a name="migrating-to-launch-screen-storyboards"></a>Migrando para iniciar storyboards da tela

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Ao atualizar um aplicativo existente para usar storyboards para suas telas de inicialização, clique com o botão direito do mouse no **nome do projeto** na **Gerenciador de soluções** e selecione **Adicionar** > **novo arquivo...** . Selecione**tela de inicialização** do **Ios** > e clique no botão **novo** :

![](launch-screens-images/storyboard02.png "Selecionar uma tela de inicialização do iOS")

Em seguida, clique `Info.plist` duas vezes no arquivo no **Gerenciador de soluções** para abri-lo para edição. Na **tela iniciar**, selecione o novo arquivo de storyboard criado acima.

![](launch-screens-images/storyboard09.png "Selecione o novo arquivo de storyboard criado acima")


Para usar o novo storyboard como uma tela de inicialização, faça o seguinte:

1. Clique `Info.plist` duas vezes no arquivo no **Gerenciador de soluções** para abri-lo para edição.
2. Role até a seção **imagens de inicialização universal** do editor, abra a lista suspensa **tela de inicialização** e selecione o nome do storyboard criado acima: 

    ![](launch-screens-images/storyboard08.png "Configurando a tela de inicialização para o storyboard")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Clique com o botão direito do mouse no nome do projeto na **Gerenciador de soluções** e selecione **Adicionar** > **novo arquivo...** : 

    ![](launch-screens-images/image012.png "Adicionar novo arquivo")
2. Insira um nome para a tela de inicialização e clique no botão **Adicionar** : 

    ![](launch-screens-images/image013.png "Insira um nome para a tela de inicialização")
3. No **Gerenciador de soluções**, clique duas vezes no arquivo de storyboard recém-criado para abri-lo para edição.
4. Verifique se a **classe size** está definida como **any: any** e a **exibição como** é **genérica**: 

    ![](launch-screens-images/image016.png "Certifique-se de que a classe size esteja definida como Any: qualquer e a exibição como seja genérica")
5. Assembly a tela de inicialização de classes de tamanho, elementos simples da interface `UIImageView`do usuário (como) e imagens que você incluiu no pacote do aplicativo: 

    ![](launch-screens-images/image017.png "Assembly a tela de inicialização no designer do iOS")
6. Salve as alterações no storyboard.

-----

## <a name="related-links"></a>Links relacionados

- [Telas de inicialização dinâmica (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-dynamiclaunchscreen)
- [Storyboards unificadas](~/ios/user-interface/storyboards/unified-storyboards.md)
- [Noções básicas do Designer do iOS](~/ios/user-interface/designer/index.md)
- [Adicionando imagens a um conjunto de imagens do catálogo de ativos](~/ios/app-fundamentals/images-icons/displaying-an-image.md#adding-images-to-an-asset-catalog-image-set)
- [Layout automático com o Xamarin Designer para iOS](~/ios/user-interface/designer/designer-auto-layout.md)
- [Diretrizes de interface humana: Tela de inicialização](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/launch-screen/)
