---
title: Introdução ao toque 3D no Xamarin. iOS
description: Este artigo descreve como usar gestos de toque 3D introduzidos com o iPhone 6s e o iPhone 6s Plus. Esses gestos permitem sensibilidade à pressão, Peek e pop e ações rápidas.
ms.prod: xamarin
ms.assetid: 806D051E-3791-40F7-9776-4E4D3E56F7F3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 6e76821033276ed3bb756cecd10e801fa615fb1e
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431841"
---
# <a name="introduction-to-3d-touch-in-xamarinios"></a>Introdução ao toque 3D no Xamarin. iOS

_Este artigo aborda o uso dos novos gestos de toque do iPhone 6s e do iPhone 6s mais 3D em seu aplicativo._

[![Exemplos de aplicativos habilitados para toque 3D](3d-touch-images/info01.png)](3d-touch-images/info01.png#lightbox)

Este artigo fornecerá e apresentará uma introdução ao uso das novas APIs de toque 3D para adicionar gestos sensíveis à pressão aos seus aplicativos do Xamarin. iOS que estão em execução nos novos dispositivos iPhone 6s e iPhone 6s Plus.

Com o 3D Touch, um aplicativo do iPhone agora consegue não apenas dizer que o usuário está tocando na tela do dispositivo, mas é capaz de saber quanto pressão o usuário está exercendo e responder aos diferentes níveis de pressão.

o Touch 3D fornece os seguintes recursos para seu aplicativo:

- [Sensibilidade à pressão](#Pressure-Sensitivity) – os aplicativos agora podem medir o quão difícil ou claro o usuário está tocando na tela e tirar proveito dessas informações.
  Por exemplo, um aplicativo de pintura pode tornar uma linha mais espessa ou mais fina com base em quão difícil o usuário está tocando na tela.
- [Peek e pop](#Peek-and-Pop) -seu aplicativo agora pode permitir que o usuário interaja com seus dados sem precisar navegar fora do contexto atual. Ao pressionar o Hard na tela na tela, eles podem inspecionar o item em que estão interessados (como visualizar uma mensagem). Ao pressionar com mais dificuldade, eles podem se mostrar ao item.
- [Ações rápidas](#Quick-Actions) – imagine ações rápidas, como os menus contextuais que podem ser retirados quando um usuário clica com o botão direito do mouse em um item em um aplicativo de área de trabalho.
  Usando ações rápidas, você pode adicionar atalhos a funções em seu aplicativo diretamente do ícone do aplicativo na tela inicial.
- [Testando o toque 3D no simulador](#Testing-3D-Touch-in-the-Simulator) – com o hardware Mac correto, você pode testar aplicativos habilitados para toque 3D no simulador de Ios.

<a name="Pressure-Sensitivity"></a>

## <a name="pressure-sensitivity"></a>Sensibilidade à pressão

Conforme mencionado acima, usando as novas propriedades da classe [UITouch](xref:UIKit.UITouch) , você pode medir a quantidade de pressão que o usuário está aplicando à tela do dispositivo IOS e usar essas informações na sua interface do usuário. Por exemplo, tornando um pincel traçado mais translúcida ou opaco com base na quantidade de pressão.

[![Um traço de pincel renderizado como mais translúcida ou opaco com base na quantidade de pressão](3d-touch-images/pressure01.png)](3d-touch-images/pressure01.png#lightbox)

Como resultado do toque 3D, se seu aplicativo estiver em execução no iOS 9 (ou superior) e o dispositivo iOS for capaz de dar suporte ao toque 3D, as alterações na pressão farão com que o `TouchesMoved` evento seja gerado.

Por exemplo, ao monitorar o `TouchesMoved` evento de um [UIView](xref:UIKit.UIView), você pode usar o seguinte código para obter a pressão atual que o usuário está aplicando à tela:

```csharp
public override void TouchesMoved (NSSet touches, UIEvent evt)
{
    base.TouchesMoved (touches, evt);
    UITouch touch = touches.AnyObject as UITouch;
    if (touch != null)
    {
        // Get the pressure
        var force = touch.Force;
        var maxForce = touch.MaximumPossibleForce;

        // Do something with the touch and the pressure
        ...
    }
}
```

A `MaximumPossibleForce` propriedade retorna o valor mais alto possível para a `Force` Propriedade do [UITouch](xref:UIKit.UITouch) com base no dispositivo IOS em que o aplicativo está sendo executado.

> [!IMPORTANT]
> As alterações na pressão farão com que o `TouchesMoved` evento seja gerado, mesmo que as coordenadas X/Y não tenham sido alteradas. Devido a essa alteração no comportamento, seus aplicativos iOS devem estar preparados para que o `TouchesMoved` evento seja invocado com mais frequência e que as coordenadas X/Y sejam iguais às da última `TouchesMoved` chamada.

Para obter mais informações, consulte a TouchCanvas da Apple [: usando o UITouch de exemplo eficiente e eficiente e](https://developer.apple.com/library/prerelease/ios/samplecode/TouchCanvas/) a referência de classe de [UITouch](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITouch_Class/).

<a name="Peek-and-Pop"></a>

## <a name="peek-and-pop"></a>Peek e pop

o Touch 3D fornece novas maneiras para um usuário interagir com informações em seu aplicativo mais rápido do que nunca, sem precisar navegar do local atual.

Por exemplo, se seu aplicativo estiver exibindo uma tabela de mensagens, o usuário poderá pressionar Hard em um item para visualizar seu conteúdo em uma exibição de sobreposição (a qual a Apple se refere como uma *inspeção*).

[![Um exemplo de inspeção no conteúdo](3d-touch-images/peekandpop01.png)](3d-touch-images/peekandpop01.png#lightbox)

Se o usuário pressionar mais difícil, ele entrará no modo de exibição de mensagem regular (que é chamado de *pop*-ping na exibição).

### <a name="checking-for-3d-touch-availability"></a>Verificando a disponibilidade de toque 3D

Ao trabalhar com um `UIViewController` , você pode usar o código a seguir para ver se o dispositivo IOS em que o aplicativo está sendo executado dá suporte ao toque 3D:

```csharp
public override void TraitCollectionDidChange(UITraitCollection previousTraitCollection)
{
    //Important: call the base function
    base.TraitCollectionDidChange(previousTraitCollection);

    //See if the new TraitCollection value includes force touch
    if (TraitCollection.ForceTouchCapability == UIForceTouchCapability.Available) {
        //Do something with 3D touch, for instance...
        RegisterForPreviewingWithDelegate (this, View);
        ...
```

Esse método pode ser chamado antes *ou depois* `ViewDidLoad()` .

### <a name="handling-peek-and-pop"></a>Lidando com Peek e pop

Em um dispositivo iOS que pode lidar com o toque 3D, podemos usar uma instância da  `UIViewControllerPreviewingDelegate` classe para manipular a exibição dos detalhes do item de **Peek** e **pop** . Por exemplo, se tivéssemos um controlador de exibição de tabela chamado `MasterViewController` , poderíamos usar o seguinte código para dar suporte a **Peek** e **pop**:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;

namespace DTouch
{
    public class PreviewingDelegate : UIViewControllerPreviewingDelegate
    {
        #region Computed Properties
        public MasterViewController MasterController { get; set; }
        #endregion

        #region Constructors
        public PreviewingDelegate (MasterViewController masterController)
        {
            // Initialize
            this.MasterController = masterController;
        }

        public PreviewingDelegate (NSObjectFlag t) : base(t)
        {
        }

        public PreviewingDelegate (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        /// Present the view controller for the "Pop" action.
        public override void CommitViewController (IUIViewControllerPreviewing previewingContext, UIViewController viewControllerToCommit)
        {
            // Reuse Peek view controller for details presentation
            MasterController.ShowViewController(viewControllerToCommit,this);
        }

        /// Create a previewing view controller to be shown at "Peek".
        public override UIViewController GetViewControllerForPreview (IUIViewControllerPreviewing previewingContext, CGPoint location)
        {
            // Grab the item to preview
            var indexPath = MasterController.TableView.IndexPathForRowAtPoint (location);
            var cell = MasterController.TableView.CellAt (indexPath);
            var item = MasterController.dataSource.Objects [indexPath.Row];

            // Grab a controller and set it to the default sizes
            var detailViewController = MasterController.Storyboard.InstantiateViewController ("DetailViewController") as DetailViewController;
            detailViewController.PreferredContentSize = new CGSize (0, 0);

            // Set the data for the display
            detailViewController.SetDetailItem (item);
            detailViewController.NavigationItem.LeftBarButtonItem = MasterController.SplitViewController.DisplayModeButtonItem;
            detailViewController.NavigationItem.LeftItemsSupplementBackButton = true;

            // Set the source rect to the cell frame, so everything else is blurred.
            previewingContext.SourceRect = cell.Frame;

            return detailViewController;
        }
        #endregion
    }
}
```

O `GetViewControllerForPreview` método é usado para executar a operação de **inspeção** . Ele obtém acesso à célula da tabela e aos dados de backup e, em seguida, carrega o `DetailViewController` do storyboard atual. Ao definir o `PreferredContentSize` como (0, 0), estamos solicitando o tamanho padrão da exibição de **inspeção** . Finalmente, desfocamos tudo, exceto a célula que estamos exibindo `previewingContext.SourceRect = cell.Frame` e retornamos a nova exibição para exibição.

O `CommitViewController` reutiliza o modo de exibição que criamos no **Peek** para a exibição **pop** quando o usuário pressiona mais difícil.

### <a name="registering-for-peek-and-pop"></a>Registrando para Peek e pop

No controlador de exibição que queremos permitir que o usuário **Inspecione** e **exiba itens,** precisamos se registrar para esse serviço. No exemplo fornecido acima de um controlador de exibição de tabela ( `MasterViewController` ), usaremos o código a seguir:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Check to see if 3D Touch is available
    if (TraitCollection.ForceTouchCapability == UIForceTouchCapability.Available) {
        // Register for Peek and Pop
        RegisterForPreviewingWithDelegate(new PreviewingDelegate(this), View);
    }
    ...

}
```

Aqui, estamos chamando o `RegisterForPreviewingWithDelegate` método com uma instância do `PreviewingDelegate` que criamos acima. Em dispositivos iOS que dão suporte ao toque 3D, o usuário pode pressionar o disco rígido em um item para exibi-lo. Se eles pressionarem ainda mais, o item será exibido na exibição de exibição padrão.

Para obter mais informações, consulte nosso [exemplo de ApplicationShortcuts do IOS 9](/samples/xamarin/ios-samples/ios9-viewcontrollerpreview) e [ViewControllerPreviews da Apple: usando o aplicativo de exemplo UIViewController APIs de visualização](https://developer.apple.com/library/prerelease/ios/samplecode/ViewControllerPreviews/Introduction/Intro.html) , [referência de classe UIPreviewAction](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewAction_Class/), referência de classe [UIPreviewActionGroup](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionGroup_Class/) e [referência de protocolo UIPreviewActionItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionItem_Protocol/).

<a name="Quick-Actions"></a>

## <a name="quick-actions"></a>Ações Rápidas

Usando o Touch 3D e ações rápidas, você pode adicionar atalhos comuns, rápidos e fáceis de acessar para funções em seu aplicativo por meio do ícone de tela inicial no dispositivo iOS.

Conforme mencionado acima, você pode considerar ações rápidas, como os menus contextuais que podem ser retirados quando um usuário clica com o botão direito do mouse em um item em um aplicativo de área de trabalho. Você deve usar ações rápidas para fornecer atalhos para as funções ou recursos mais comuns de seu aplicativo.

[![Um exemplo de um menu de ações rápidas](3d-touch-images/quickactions01.png)](3d-touch-images/quickactions01.png#lightbox)

### <a name="defining-static-quick-actions"></a>Definindo ações rápidas estáticas

Se uma ou mais das ações rápidas exigidas pelo seu aplicativo forem estáticas e não precisarem ser alteradas, você poderá defini-las no `Info.plist` arquivo do aplicativo. Edite esse arquivo em um editor externo e adicione as seguintes chaves:

```xml
<key>UIApplicationShortcutItems</key>
<array>
    <dict>
        <key>UIApplicationShortcutItemIconType</key>
        <string>UIApplicationShortcutIconTypeSearch</string>
        <key>UIApplicationShortcutItemSubtitle</key>
        <string>Will search for an item</string>
        <key>UIApplicationShortcutItemTitle</key>
        <string>Search</string>
        <key>UIApplicationShortcutItemType</key>
        <string>com.company.appname.000</string>
    </dict>
    <dict>
        <key>UIApplicationShortcutItemIconType</key>
        <string>UIApplicationShortcutIconTypeShare</string>
        <key>UIApplicationShortcutItemSubtitle</key>
        <string>Will share an item</string>
        <key>UIApplicationShortcutItemTitle</key>
        <string>Share</string>
        <key>UIApplicationShortcutItemType</key>
        <string>com.company.appname.001</string>
    </dict>
</array>
```

Aqui estamos definindo dois itens de ação rápida estáticos com as seguintes chaves:

- `UIApplicationShortcutItemIconType` -Define o ícone que será exibido pelo item de ação rápida como um dos seguintes valores:
  - `UIApplicationShortcutIconTypeAdd`
  - `UIApplicationShortcutIconTypeAlarm`
  - `UIApplicationShortcutIconTypeAudio`
  - `UIApplicationShortcutIconTypeBookmark`
  - `UIApplicationShortcutIconTypeCapturePhoto`
  - `UIApplicationShortcutIconTypeCaptureVideo`
  - `UIApplicationShortcutIconTypeCloud`
  - `UIApplicationShortcutIconTypeCompose`
  - `UIApplicationShortcutIconTypeConfirmation`
  - `UIApplicationShortcutIconTypeContact`
  - `UIApplicationShortcutIconTypeDate`
  - `UIApplicationShortcutIconTypeFavorite`
  - `UIApplicationShortcutIconTypeHome`
  - `UIApplicationShortcutIconTypeInvitation`
  - `UIApplicationShortcutIconTypeLocation`
  - `UIApplicationShortcutIconTypeLove`
  - `UIApplicationShortcutIconTypeMail`
  - `UIApplicationShortcutIconTypeMarkLocation`
  - `UIApplicationShortcutIconTypeMessage`
  - `UIApplicationShortcutIconTypePause`
  - `UIApplicationShortcutIconTypePlay`
  - `UIApplicationShortcutIconTypeProhibit`
  - `UIApplicationShortcutIconTypeSearch`
  - `UIApplicationShortcutIconTypeShare`
  - `UIApplicationShortcutIconTypeShuffle`
  - `UIApplicationShortcutIconTypeTask`
  - `UIApplicationShortcutIconTypeTaskCompleted`
  - `UIApplicationShortcutIconTypeTime`
  - `UIApplicationShortcutIconTypeUpdate`

  ![Imagens de UIApplicationShortcutIconType](3d-touch-images/uiapplicationshortcuticontype.png)

- `UIApplicationShortcutItemSubtitle` -Define o subtítulo do item.
- `UIApplicationShortcutItemTitle` -Define o título do item.
- `UIApplicationShortcutItemType` -É um valor de cadeia de caracteres que usaremos para identificar o item em nosso aplicativo. Consulte a seguinte seção para obter mais informações.

> [!IMPORTANT]
> Itens de atalho de ação rápida que são definidos no `Info.plist` arquivo não podem ser acessados com a `Application.ShortcutItems` propriedade. Eles são passados apenas para o `HandleShortcutItem` manipulador de eventos.

### <a name="identifying-quick-action-items"></a>Identificando itens de ação rápida

Como vimos acima, quando você definiu seus itens de ação rápida em seu aplicativo `Info.plist` , você atribuiu um valor de cadeia de caracteres à `UIApplicationShortcutItemType` chave para identificá-los.

Para tornar esses identificadores mais fáceis de trabalhar no código, adicione uma classe chamada `ShortcutIdentifier` ao projeto do aplicativo e faça com que ele se pareça com o seguinte:

```csharp
using System;

namespace AppSearch
{
    public static class ShortcutIdentifier
    {
        public const string First = "com.company.appname.000";
        public const string Second = "com.company.appname.001";
        public const string Third = "com.company.appname.002";
        public const string Fourth = "com.company.appname.003";
    }
}
```

<a name="Handling-a-Quick-Action"></a>

### <a name="handling-a-quick-action"></a>Manipulando uma ação rápida

Em seguida, você precisa modificar o arquivo do aplicativo `AppDelegate.cs` para manipular o usuário selecionando um item de ação rápida no ícone do seu aplicativo na tela inicial.

Faça as seguintes edições:

```csharp
using System;
...

public UIApplicationShortcutItem LaunchedShortcutItem { get; set; }

public bool HandleShortcutItem(UIApplicationShortcutItem shortcutItem) {
    var handled = false;

    // Anything to process?
    if (shortcutItem == null) return false;

    // Take action based on the shortcut type
    switch (shortcutItem.Type) {
    case ShortcutIdentifier.First:
        Console.WriteLine ("First shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Second:
        Console.WriteLine ("Second shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Third:
        Console.WriteLine ("Third shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Fourth:
        Console.WriteLine ("Forth shortcut selected");
        handled = true;
        break;
    }

    // Return results
    return handled;
}

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    var shouldPerformAdditionalDelegateHandling = true;

    // Get possible shortcut item
    if (launchOptions != null) {
        LaunchedShortcutItem = launchOptions [UIApplication.LaunchOptionsShortcutItemKey] as UIApplicationShortcutItem;
        shouldPerformAdditionalDelegateHandling = (LaunchedShortcutItem == null);
    }

    return shouldPerformAdditionalDelegateHandling;
}

public override void OnActivated (UIApplication application)
{
    // Handle any shortcut item being selected
    HandleShortcutItem(LaunchedShortcutItem);

    // Clear shortcut after it's been handled
    LaunchedShortcutItem = null;
}

public override void PerformActionForShortcutItem (UIApplication application, UIApplicationShortcutItem shortcutItem, UIOperationHandler completionHandler)
{
    // Perform action
    completionHandler(HandleShortcutItem(shortcutItem));
}
```

Primeiro, definimos uma `LaunchedShortcutItem` propriedade pública para acompanhar o último item de ação rápida selecionado pelo usuário. Em seguida, substituimos o `FinishedLaunching` método e verificamos se `launchOptions` eles foram aprovados e se eles contêm um item de ação rápida. Se isso for feito, armazenaremos a ação rápida na `LaunchedShortcutItem` propriedade.

Em seguida, substituimos o `OnActivated` método e passamos qualquer item de início rápido selecionado para o `HandleShortcutItem` método a ser acionado. No momento, estamos apenas gravando uma mensagem no **console**. Em um aplicativo real, você trataria o que nunca era necessário. Depois que a ação for executada, a `LaunchedShortcutItem` propriedade será desmarcada.

Por fim, se seu aplicativo já estava em execução, o `PerformActionForShortcutItem` método seria chamado para lidar com o item de ação rápida, portanto, precisamos substituí-lo e chamar nosso `HandleShortcutItem` método aqui também.

### <a name="creating-dynamic-quick-action-items"></a>Criando itens dinâmicos de ação rápida

Além de definir itens de ação rápidos estáticos no arquivo do seu aplicativo `Info.plist` , você pode criar ações rápidas dinâmicas imediatas. Para definir duas novas ações rápidas dinâmicas, edite o `AppDelegate.cs` arquivo novamente e modifique o `FinishedLaunching` método para que fique semelhante ao seguinte:

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    var shouldPerformAdditionalDelegateHandling = true;

    // Get possible shortcut item
    if (launchOptions != null) {
        LaunchedShortcutItem = launchOptions [UIApplication.LaunchOptionsShortcutItemKey] as UIApplicationShortcutItem;
        shouldPerformAdditionalDelegateHandling = (LaunchedShortcutItem == null);
    }

    // Add dynamic shortcut items
    if (application.ShortcutItems.Length == 0) {
        var shortcut3 = new UIMutableApplicationShortcutItem (ShortcutIdentifier.Third, "Play") {
            LocalizedSubtitle = "Will play an item",
            Icon = UIApplicationShortcutIcon.FromType(UIApplicationShortcutIconType.Play)
        };

        var shortcut4 = new UIMutableApplicationShortcutItem (ShortcutIdentifier.Fourth, "Pause") {
            LocalizedSubtitle = "Will pause an item",
            Icon = UIApplicationShortcutIcon.FromType(UIApplicationShortcutIconType.Pause)
        };

        // Update the application providing the initial 'dynamic' shortcut items.
        application.ShortcutItems = new UIApplicationShortcutItem[]{shortcut3, shortcut4};
    }

    return shouldPerformAdditionalDelegateHandling;
}
```

Agora, estamos verificando se o `application` já contém um conjunto de criados dinamicamente `ShortcutItems` , se não criarei dois novos `UIMutableApplicationShortcutItem` objetos para definir os novos itens e adicioná-los à `ShortcutItems` matriz.

O código que já adicionamos na seção [manipulando uma ação rápida](#Handling-a-Quick-Action) acima tratará dessas ações rápidas dinâmicas, assim como as estáticas.

Deve-se observar que você pode criar uma mistura de itens de ação rápida estáticos e dinâmicos (como estamos fazendo aqui), você não está limitado a um ou outro.

Para obter mais informações, nosso [exemplo de ViewControllerPreview do IOS 9](/samples/xamarin/ios-samples/ios9-viewcontrollerpreview) e ver o ApplicationShortcuts da Apple [: usando UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/samplecode/ApplicationShortcuts/) aplicativo de exemplo, [referência de classe de UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutItem_class/), referência de classe [UIMutableApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIMutableApplicationShortcutItem_class/) e [referência de classe UIApplicationShortcutIcon](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutIcon_Class/).

<a name="Testing-3D-Touch-in-the-Simulator"></a>

## <a name="testing-3d-touch-in-the-simulator"></a>Testando o toque 3D no simulador

Ao usar a versão mais recente do Xcode e o simulador do iOS em um Mac compatível com um Force Touch habilitar trackpad, você pode testar a funcionalidade de toque 3D no simulador.

Para habilitar essa funcionalidade, execute qualquer aplicativo no hardware simulado do iPhone que dê suporte ao Touch 3D (iPhone 6s e superior). Em seguida, selecione o menu de **hardware** no simulador de Ios e habilite o item de menu **usar trackpad Force para 3D Touch** :

[![Selecione o menu de hardware no simulador de iOS e habilite o item de menu usar trackpad Force para 3D Touch](3d-touch-images/simulator01.png)](3d-touch-images/simulator01.png#lightbox)

Com esse recurso ativo, você pode pressionar com mais dificuldade o trackpad do Mac para habilitar o toque 3D, assim como faria com hardware real do iPhone.

## <a name="summary"></a>Resumo

Este artigo introduziu as novas APIs 3D Touch disponibilizadas no iOS 9 para iPhone 6s e iPhone 6s Plus. Ele abordou a adição de sensibilidade à pressão a um aplicativo; usando Peek e pop para exibir rapidamente informações no aplicativo a partir do contexto atual sem navegação; e usando ações rápidas para fornecer atalhos para os recursos mais usados do seu aplicativo.

## <a name="related-links"></a>Links Relacionados

- [Exemplo de ViewControllerPreview do iOS 9](/samples/xamarin/ios-samples/ios9-viewcontrollerpreview)
- [Exemplo de ApplicationShortcuts do iOS 9](/samples/xamarin/ios-samples/ios9-applicationshortcuts)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Prepare seus aplicativos para iPhone para 3D Touch](https://developer.apple.com/ios/3d-touch/)