---
title: Introdução ao 3D Touch no xamarin. IOS
description: Este artigo descreve como usar gestos de toque 3D introduzidos com o iPhone 6s e iPhone 6s Plus. Esses gestos habilitar sensibilidade à pressão, inspeção e pop-up e ações rápidas.
ms.prod: xamarin
ms.assetid: 806D051E-3791-40F7-9776-4E4D3E56F7F3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 6a12d157b3de7c3841f5d69d209c01fbc612f79b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61279049"
---
# <a name="introduction-to-3d-touch-in-xamarinios"></a>Introdução ao 3D Touch no xamarin. IOS

_Este artigo aborda usando o novo iPhone 6s e iPhone 6s Plus 3D Touch gestos em seu aplicativo._

[![](3d-touch-images/info01.png "Exemplos de toque 3D para aplicativos habilitados")](3d-touch-images/info01.png#lightbox)

Este artigo fornecerá e introdução ao uso as novas APIs de toque 3D para adicionar os gestos de pressão confidenciais aos seus aplicativos xamarin. IOS que estão em execução no novo iPhone 6s e iPhone 6s Plus dispositivos.

Com o 3D Touch, um aplicativo de iPhone agora é capaz não apenas informar que o usuário toca a tela do dispositivo, mas ele é capaz de detectar quanto o usuário é exercendo pressão de e responder aos níveis de pressão diferentes.

3D Touch oferece os seguintes recursos para seu aplicativo:

- [Sensibilidade à pressão](#Pressure-Sensitivity) – aplicativos agora podem medir como rígida ou luz o usuário está tocando a tela e aproveite essa informação.
  Por exemplo, um aplicativo de pintura pode fazer uma linha mais espessa ou mais finos com base em como o usuário está tocando a tela.
- [Inspecionar e Pop](#Peek-and-Pop) -seu aplicativo agora pode permitir ao usuário interagir com seus dados sem precisar navegar fora de seu contexto atual. Pressionando rígido na tela de tela, eles podem inspecionar o item que ele está interessado (como visualizar uma mensagem). Pressionando mais difícil, eles podem pop para o item.
- [Ações rápidas](#Quick-Actions) -pensar de ações rápidas, como os menus contextuais que podem ser removidos-up quando um usuário clica com o botão direito em um item em um aplicativo da área de trabalho.
  Usando ações rápidas, você pode adicionar atalhos às funções em seu aplicativo diretamente de no ícone do aplicativo na tela inicial.
- [Testando o 3D Touch no simulador](#Testing-3D-Touch-in-the-Simulator) -com o hardware correto do Mac, você pode testar aplicativos 3D de toque habilitado no simulador de iOS.

<a name="Pressure-Sensitivity" />

## <a name="pressure-sensitivity"></a>Sensibilidade de pressão

Conforme mencionado acima, usando as novas propriedades do [UITouch](xref:UIKit.UITouch) classe, você pode medir a quantidade de pressão que o usuário está sendo aplicada a tela do dispositivo iOS e usar essas informações na interface do usuário. Por exemplo, fazendo uma pincelada mais transparente ou opaca com base na quantidade de pressão.

[![](3d-touch-images/pressure01.png "Uma pincelada renderizada conforme mais transparente ou opaca com base na quantidade de pressão")](3d-touch-images/pressure01.png#lightbox)

Como resultado de 3D Touch, se o aplicativo é executado no iOS 9 (ou posterior) e o dispositivo iOS é capaz de suporte 3D Touch, alterações na pressão fará com que o `TouchesMoved` evento seja acionado.

Por exemplo, ao monitorar o `TouchesMoved` eventos de um [UIView](xref:UIKit.UIView), você pode usar o código a seguir para obter a pressão atual que o usuário está sendo aplicada na tela:

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

O `MaximumPossibleForce` propriedade retorna o maior valor possível para o `Force` propriedade da [UITouch](xref:UIKit.UITouch) com base no dispositivo iOS que o aplicativo está sendo executado.

> [!IMPORTANT]
> Alterações na pressão fará com que o `TouchesMoved` evento a ser gerado, mesmo se o X / coordenadas Y não foram alterados. Por causa dessa alteração no comportamento, seus aplicativos iOS devem estar preparados para o `TouchesMoved` evento a ser invocado com mais frequência e o x / coordenadas Y para ser o mesmo que o último `TouchesMoved` chamar.




Para obter mais informações, consulte da Apple [TouchCanvas: Usando UITouch com eficiência e eficácia](https://developer.apple.com/library/prerelease/ios/samplecode/TouchCanvas/) aplicativo de exemplo e [referência de classe UITouch](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITouch_Class/).

<a name="Peek-and-Pop" />

## <a name="peek-and-pop"></a>Espiada e Pop

3D Touch proporciona novas formas de um usuário para interagir com as informações dentro de seu aplicativo mais rapidamente do que nunca, sem precisar navegar de seu local atual.

Por exemplo, se seu aplicativo estiver exibindo uma tabela de mensagens, o usuário pode pressionar rígido em um item para visualizar seu conteúdo em uma exibição de sobreposição (que Apple se refere a como uma *inspecionar*).

[![](3d-touch-images/peekandpop01.png "Um exemplo de exibição no conteúdo")](3d-touch-images/peekandpop01.png#lightbox)

Se o usuário pressiona mais difícil, ele insere o modo de exibição de mensagem comum (que é conhecido como *Pop*-ping no modo de exibição).

### <a name="checking-for-3d-touch-availability"></a>Verificando a disponibilidade de toque 3D

Ao trabalhar com um `UIViewController` você pode usar o código a seguir para ver se o dispositivo iOS que o aplicativo está em execução no dá suporte a toque 3D:

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

Esse método pode ser chamado antes de *ou posterior* `ViewDidLoad()`.

### <a name="handling-peek-and-pop"></a>Pop e tratamento de espiada

Em um dispositivo iOS que pode manipular o 3D Touch, podemos usar uma instância das `UIViewControllerPreviewingDelegate` classe para manipular a exibição de **inspecionar** e **Pop** detalhes do item. Por exemplo, se tivéssemos um controlador de exibição de tabela chamada `MasterViewController ` poderíamos usar o código a seguir para dar suporte à **inspecionar** e **Pop**:

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

O `GetViewControllerForPreview` método é usado para executar o **inspecionar** operação. Ele obtém acesso aos dados de backup e a célula da tabela e, em seguida, carrega o `DetailViewController` usando o Storyboard atual. Definindo o `PreferredContentSize` como (0,0), pedimos para o padrão **inspecionar** o tamanho da exibição. Por fim, podemos desfoque tudo, exceto a célula, são exibidos com `previewingContext.SourceRect = cell.Frame` e retornamos o novo modo de exibição para exibição.

O `CommitViewController` reutiliza o modo de exibição que criamos na **inspecionar** para o **Pop** exibir quando o usuário pressiona mais difícil.

### <a name="registering-for-peek-and-pop"></a>Registrar-se para inspeção e Pop

Do controlador de exibição que desejamos permitir que o usuário **espiar** e **Pop** itens do, é preciso registrar para este serviço. No exemplo acima de um controlador de exibição de tabela (`MasterViewController`), usaríamos o código a seguir:

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

Aqui estamos chamando o `RegisterForPreviewingWithDelegate` método com uma instância das `PreviewingDelegate` criadas anteriormente. Em dispositivos iOS que dão suporte ao 3D Touch, o usuário pode pressionar rígido em um item para Peek-lo. Se eles ainda mais difícil pressionarem, o item será exibida para ele padrão de exibição.

Para obter mais informações, consulte nosso [iOS 9 exemplo ApplicationShortcuts](https://developer.xamarin.com/samples/monotouch/iOS9/ViewControllerPreview/) da Apple [ViewControllerPreviews: Usando a UIViewController visualizando as APIs](https://developer.apple.com/library/prerelease/ios/samplecode/ViewControllerPreviews/Introduction/Intro.html) aplicativo de exemplo, [referência de classe UIPreviewAction](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewAction_Class/), [referência de classe UIPreviewActionGroup](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionGroup_Class/) e [UIPreviewActionItem Referência de protocolo](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionItem_Protocol/).

<a name="Quick-Actions" />

## <a name="quick-actions"></a>Ações Rápidas

Usando o 3D Touch e ações rápidas, você pode adicionar comuns, rápido e fácil aos atalhos de acesso a funções em seu aplicativo do ícone de tela inicial no dispositivo iOS.

Como mencionado acima, você pode pensar ações rápidas, como os menus contextuais que podem ser removidos-up quando um usuário clica com o botão direito em um item em um aplicativo da área de trabalho. Você deve usar ações rápidas para fornecem atalhos para as funções ou recursos de seu aplicativo a mais comum.

[![](3d-touch-images/quickactions01.png "Um exemplo de um menu de ações rápidas")](3d-touch-images/quickactions01.png#lightbox)


### <a name="defining-static-quick-actions"></a>Definindo ações rápidas estáticas

Se uma ou mais das ações rápidas exigidos pelo seu aplicativo são estáticos e não precisa alterar, você pode defini-los no aplicativo do `Info.plist` arquivo. Edite esse arquivo em um editor externo e adicione as seguintes chaves:

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

Aqui definimos dois itens de ação rápida estáticos com as seguintes chaves:

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

        ![](3d-touch-images/uiapplicationshortcuticontype.png "UIApplicationShortcutIconType imagery")

* `UIApplicationShortcutItemSubtitle` -Define o subtítulo para o item.
* `UIApplicationShortcutItemTitle` -Define o título do item.
* `UIApplicationShortcutItemType` -É um valor de cadeia de caracteres que serão usadas para identificar o item em nosso aplicativo. Consulte a seguinte seção para obter mais informações.

> [!IMPORTANT]
> Itens de atalho ação rápidas que são definidas na `Info.plist` não puder ser acessado com o `Application.ShortcutItems` propriedade. Eles apenas são passados para o `HandleShortcutItem` manipulador de eventos.





### <a name="identifying-quick-action-items"></a>Identificação de itens de ação rápida

Como você viu anteriormente, quando você definiu seus itens de ação rápida em seu aplicativo `Info.plist`, você atribuiu um valor de cadeia de caracteres para o `UIApplicationShortcutItemType` chave para identificá-los.

Para tornar esses identificadores mais fáceis de trabalhar no código, adicione uma classe chamada `ShortcutIdentifier` ao seu aplicativo do projeto e que ela fique semelhante ao seguinte:

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

<a name="Handling-a-Quick-Action" />

### <a name="handling-a-quick-action"></a>Tratamento de uma ação rápida

Em seguida, você precisará modificar seu aplicativo `AppDelegate.cs` arquivo para lidar com o usuário selecionar um item de ação rápida do ícone do aplicativo na tela inicial.

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

Primeiro, definimos uma pública `LaunchedShortcutItem` propriedade para controlar o último item de ação rápida selecionado pelo usuário. Em seguida, substituímos o `FinishedLaunching` método e veja se `launchOptions` foram passados e se eles contém um item de ação rápida. Se Sim, podemos armazenar a ação rápida no `LaunchedShortcutItem` propriedade.

Em seguida, substituímos o `OnActivated` método e passar qualquer selecionada o item de início rápido para o `HandleShortcutItem` método ser acionados. No momento, estamos apenas escrevendo uma mensagem para o **Console**. Em um aplicativo real, você lidaria com a ação e queira era necessária. Depois que a ação foi executada, o `LaunchedShortcutItem` propriedade está desmarcada.

Por fim, se seu aplicativo já estava em execução, o `PerformActionForShortcutItem` método seria chamado para manipular o item de ação rápida, portanto, precisamos substituí-la e chamar nossa `HandleShortcutItem` método aqui também.


### <a name="creating-dynamic-quick-action-items"></a>Criando itens de ação rápida dinâmico

Além de definir a ação rápida estático itens em seu aplicativo `Info.plist` arquivo, você pode criar dinâmico em interrupções ações rápidas. Para definir duas novas ações rápidas dinâmico, edite sua `AppDelegate.cs` novamente e modificar o `FinishedLaunching` método procurar semelhante ao seguinte:

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

Agora está sendo verificado para ver se o `application` já contém um conjunto de criados dinamicamente `ShortcutItems`, caso contrário, vamos criar dois novos `UIMutableApplicationShortcutItem` objetos para definir os novos itens e adicioná-los para o `ShortcutItems` matriz.

O código já adicionamos o [tratamento de uma ação rápida](#Handling-a-Quick-Action) seção acima manipulará estas ações rápidas dinâmico assim como os estáticos.

Deve-se observar que você pode criar uma mistura de itens de ação rápida estáticas e dinâmicas (como estamos fazendo aqui), você não está limitado a um ou outro.

Para obter mais informações, veja nossa [iOS 9 exemplo ViewControllerPreview](https://developer.xamarin.com/samples/monotouch/iOS9/ViewControllerPreview/) e ver da Apple [ApplicationShortcuts: Usando UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/samplecode/ApplicationShortcuts/) aplicativo de exemplo [referência de classe UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutItem_class/), [referência de classe UIMutableApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIMutableApplicationShortcutItem_class/) e [ Referência de classe UIApplicationShortcutIcon](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutIcon_Class/).

<a name="Testing-3D-Touch-in-the-Simulator" />

## <a name="testing-3d-touch-in-the-simulator"></a>Testando o 3D Touch no simulador

Quando habilita o trackpad usando a versão mais recente do Xcode e o simulador de iOS em um Mac compatível com uma força de toque, você pode testar a funcionalidade de toque 3D no simulador.

Para habilitar essa funcionalidade, executar qualquer aplicativo no hardware simulado iPhone que dá suporte a toque 3D (iPhone 6s e posterior). Em seguida, selecione a **Hardware** menu no iOS Simulator e habilite a **uso Trackpad força para 3D touch** item de menu:

[![](3d-touch-images/simulator01.png "Selecione o menu de Hardware no simulador de iOS e habilitar a força de Trackpad de uso para o item de menu 3D touch")](3d-touch-images/simulator01.png#lightbox)

Com esse recurso ativado, você pode pressionar mais difícil no trackpad do Mac para habilitar o 3D Touch exatamente como você faria em um hardware real do iPhone.

## <a name="summary"></a>Resumo

Este artigo apresentou novos 3D Touch APIs disponibilizadas no iOS 9 para o iPhone 6s e iPhone 6s Plus. Ele abordou a sensibilidade de pressão de adição a um aplicativo; usando a inspeção e Pop para exibir rapidamente as informações no aplicativo do contexto atual sem navegação; e uso de ações rápidas para fornecer atalhos para seu aplicativo mais comumente usado do recursos.



## <a name="related-links"></a>Links relacionados

- [iOS 9 ViewControllerPreview exemplo](https://developer.xamarin.com/samples/monotouch/ios9/ViewControllerPreview/)
- [iOS 9 ApplicationShortcuts exemplo](https://developer.xamarin.com/samples/monotouch/ios9/ApplicationShortcuts/)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Preparar seus aplicativos do iPhone para 3D Touch](https://developer.apple.com/ios/3d-touch/)
