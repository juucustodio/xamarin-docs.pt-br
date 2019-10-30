---
title: Launch Screens for Xamarin.iOS Apps
description: This article explains how to create an app Launch Screen for all iOS devices, at any resolution and orientation, using a single Unified Storyboard.
ms.prod: xamarin
ms.assetid: 31A489CA-756B-4B9B-B386-4BADF18EDD33
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/02/2018
ms.openlocfilehash: 3adabbac3f56747093aa8a3def85869a92d33600
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73010494"
---
# <a name="launch-screens-for-xamarinios-apps"></a>Launch Screens for Xamarin.iOS Apps

_This article explains how to create an app Launch Screen for all iOS devices, at any resolution and orientation, using a single Unified Storyboard._

Before iOS 8, creating a Launch Screen for an iOS app required the developer to provide an image asset for each of the various device form factors and resolutions in which the app could run. Since the release of iOS 8, however, it has been possible to use a single Unified Storyboard to create a Launch Screen that looks correct in all cases.

This brief walkthrough describes how to create a Launch Screen with either a Storyboard provided by default in a new Project or with a Storyboard added manually to an existing Project. It then demonstrates how to use the iOS Designer to add an Image View and a Label to the Storyboard, to set constraints on those views, and to verify that the Storyboard looks correct for various devices and orientations.

<a name="storyboard" />

## <a name="managing-launch-screens-with-storyboards"></a>Managing Launch Screens with Storyboards

In iOS 8 (and later), the developer can create a special Unified Storyboard to provide the Launch Screen instead of using one or more static launch images. When creating a launch Storyboard in the iOS Designer, use Size Classes and Auto Layout to define different layouts for different display environments. By using Size Classes and Auto Layout, the developer can create a single launch screen that looks good on all devices and display environments.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. In Visual Studio for Mac, create a new project by selecting **File > New Solution** and then choosing **Single View App**: 

    ![The New Project window, with Single View App selected](launch-screens-images/launch01.png)

    - By default, a new Project includes a **LaunchScreen.storyboard** file that defines the Launch Screen interface. 
    - To instead add a Launch Screen Storyboard to an existing project, right-click on the project name in the **Solution Pad** and choose **Add > New File...** and then select **Launch Screen**:

    ![The New File window, with iOS Launch Screen selected](launch-screens-images/launch01b.png)

    - Name the file **LaunchScreen** or another name of your choosing.

2. Configure the Project to use the appropriate Storyboard for its Launch Screen:

    - Double-click the **Info.plist** file in the **Solution Pad** to open it for editing.
    - In the **Launch Images** section, make sure that **Launch Screen** is set to the name of the appropriate Storyboard:

    ![O seletor de tela de inicialização no info. plist](launch-screens-images/launch02.png)

    - By default, a new Project is configured to use **LaunchScreen.storyboard** as its Launch Screen.

3. Add an image to the **Assets.xcassets** Asset Catalog so that it is available for use on the Launch Screen. For more information, see the [Adding Images to an Asset Catalog Image Set](~/ios/app-fundamentals/images-icons/displaying-an-image.md) section of the [Displaying an Image](~/ios/app-fundamentals/images-icons/displaying-an-image.md) guide.

4. Open **LaunchScreen.storyboard** for editing by double-clicking it in the **Solution Pad**.

5. Escolha um dispositivo e uma orientação para visualizar o storyboard da tela de inicialização no designer do iOS. Open the device selection panel on the bottom toolbar and select **iPhone 4S** and **Portrait**.

    ![A barra de ferramentas de seleção de dispositivo](launch-screens-images/launch05.png)

    - Observe que a seleção de um dispositivo e orientação altera apenas a forma como o designer do iOS visualiza o design. Independentemente da seleção feita aqui, as restrições adicionadas recentemente são aplicadas em todos os dispositivos e orientações, a menos que o botão **Editar características** tenha sido usado para especificar o contrário. 

6. Defina a cor da tela de **fundo** da exibição principal do controlador de exibição. Select the View by clicking in the middle of the View Controller and adjust the background color using the **Properties Pad**:

    ![Uma única exibição com uma cor de fundo roxa](launch-screens-images/launch06.png)

7. Adicione uma **exibição de imagem** à tela de inicialização e defina sua **imagem**de origem:

    - Drag an **Image View** from the **Toolbox Pad** to the center of the View.
    - With the **Image View** selected, in the **Widget** section of the **Properties Pad** set the **Image** property to the Image Set already added to the **Assets.xcassets** Asset Catalog. Reposicione e dimensione a **exibição de imagem** conforme necessário:
    
    ![An Image View with its Image property set](launch-screens-images/launch07.png)

8. Add a **Label** below the **Image View** and use the **Properties Pad** to set its attributes: 

    ![A Label with its text and color set](launch-screens-images/launch08.png)

9. Switch to Constraint Editing Mode by using the right-hand button in the **Constraints Toolbar**:
    
    ![The Constraint Editing Mode button](launch-screens-images/launch09.png)

10. Add constraints to the **Image View**, setting its height and width and centering it horizontally and vertically:

    ![An Image View with layout constraints](launch-screens-images/launch10.png)

    - Para obter mais detalhes sobre como adicionar restrições, consulte [layout automático com o Xamarin designer para IOS](~/ios/user-interface/designer/designer-auto-layout.md).

11. Add constraints to the **Label**, centering it horizontally, giving it a height and width, and positioning it a fixed distance vertically from the **Image View**:

    ![A Label with layout constraints](launch-screens-images/launch11.png)

12. Test other devices and orientations to verify that the design looks as intended in all scenarios. In cases where adjustments need to be made for a specific device or orientation, use the **Edit Traits** button to add constaints for specific size classes:

    ![The Launch Screen rendered as an iPhone X using Landscape orientation](launch-screens-images/launch12.png)

13. Save the changes to the Storyboard. Run the app on a simulator or device, and the Launch Screen will be visible as the app is launching.

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
    
    ![An Image View with its Image property set](launch-screens-images/launch10-vs.png)

11. Add a **Label** below the **Image View**:

    - Drag a **Label** from the **Toolbox** onto the design surface, placing it below the **Image View**.
    - Set attributes for the **Label** using the **Properties Window**:

    ![A Label with its text and color set](launch-screens-images/launch11-vs.png) 

12. Switch to Constraint Editing Mode by using the right-hand button in the **Constraints Toolbar**:
    
    ![The Constraint Editing Mode button](launch-screens-images/launch12-vs.png) 

13. Add constraints to the **Image View**, setting its height and width and centering it horizontally and vertically:

    ![An Image View with layout constraints](launch-screens-images/launch13-vs.png) 

    - For information about adding constraints, see [Auto Layout with the Xamarin Designer for iOS](~/ios/user-interface/designer/designer-auto-layout.md).

14. Add constraints to the **Label**, centering it horizontally, giving it a height and width, and positioning it a fixed distance vertically from the **Image View**:
    
    ![A Label with layout constraints](launch-screens-images/launch14-vs.png) 

15. Test other devices and orientations to verify that the design looks as intended in all scenarios. In cases where adjustments need to be made for a specific device or orientation, use the **Edit Traits** button to add constaints for specific size classes:

    ![The Launch Screen rendered as an iPhone X using Landscape orientation](launch-screens-images/launch15-vs.png) 

16. Save the changes to the Storyboard. Run the app on a simulator or device, and the Launch Screen will be visible as the app is launching.

-----

> [!NOTE]
> A Storyboard used as a Launch Screen _must_ include only simple, built-in UI elements and **cannot** do any calculations or derive from a custom class.

For more information about creating a Launch Screen with a Unified Storyboard, please see the [Dynamic Launch Screens](~/ios/user-interface/storyboards/unified-storyboards.md#dynamic-launch-screens) section of the [Unified Storyboards](~/ios/user-interface/storyboards/unified-storyboards.md) guide.

## <a name="migrating-to-launch-screen-storyboards"></a>Migrating to Launch Screen Storyboards

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

When updating an existing app to use Storyboards for its Launch Screens, right click the **Project Name** in the **Solution Explorer** and select **Add** > **New File...** . Select **iOS** > **Launch Screen** and click the **New** button:

![](launch-screens-images/storyboard02.png "Select an iOS Launch Screen")

Next, double-click the `Info.plist` file in the **Solution Explorer** to open it for editing. Under **Launch Screen**, select the new Storyboard file created above.

![](launch-screens-images/storyboard09.png "Select the new Storyboard file created above")

To use the new Storyboard as a launch screen, do the following:

1. Double-click the `Info.plist` file in the **Solution Explorer** to open it for editing.
2. Scroll to the **Universal Launch Images** section of the editor, open the **Launch Screen** dropdown and select the name of the storyboard created above: 

    ![](launch-screens-images/storyboard08.png "Setting the launch screen to the storyboard")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Right-click on the project name in the **Solution Explorer** and select **Add** > **New File...** : 

    ![](launch-screens-images/image012.png "Add new file")
2. Enter a name for the launch screen and click the **Add** button: 

    ![](launch-screens-images/image013.png "Enter a name for the launch screen")
3. In the **Solution Explorer**, double-click the newly created storyboard file to open it for editing.
4. Ensure that the **Size Class** is set to **any:any** and the **View As** is **Generic**: 

    ![](launch-screens-images/image016.png "Ensure that the Size Class is set to any:any and the View As is Generic")
5. Assembly the launch screen from Size Classes, simple UI elements (such as `UIImageView`) and images that you have included in the application's bundle: 

    ![](launch-screens-images/image017.png "Assembly the launch screen in the iOS Designer")
6. Save the changes to the Storyboard.

-----

## <a name="related-links"></a>Links relacionados

- [Dynamic Launch Screens (sample)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-dynamiclaunchscreen)
- [Storyboards unificadas](~/ios/user-interface/storyboards/unified-storyboards.md)
- [Noções básicas do Designer do iOS](~/ios/user-interface/designer/index.md)
- [Adding Images to an Asset Catalog Image Set](~/ios/app-fundamentals/images-icons/displaying-an-image.md#adding-images-to-an-asset-catalog-image-set)
- [Auto Layout with the Xamarin Designer for iOS](~/ios/user-interface/designer/designer-auto-layout.md)
- [Human Interface Guidelines: Launch Screen](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/launch-screen/)
