---
title: Introdução ao 3D Touch em xamarin
description: Este artigo descreve como usar gestos de toque 3D introduzidos com o iPhone 6s e iPhone 6s Plus. Esses gestos habilitar sensibilidade à pressão, inspeção e pop e ações rápidas.
ms.prod: xamarin
ms.assetid: 806D051E-3791-40F7-9776-4E4D3E56F7F3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: f6eb71409317661cdd571c708db062e06e63ff55
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786577"
---
# <a name="introduction-to-3d-touch-in-xamarinios"></a>Introdução ao 3D Touch em xamarin

_Este artigo aborda o uso do novo iPhone 6s e iPhone 6s Plus 3D Touch gestos em seu aplicativo._

[![](3d-touch-images/info01.png "Exemplos de toque 3D para aplicativos habilitados")](3d-touch-images/info01.png#lightbox)

Este artigo fornece e introdução ao usar as novas APIs 3D Touch adicionar gestos confidenciais pressão aos seus aplicativos xamarin que estão em execução no novo iPhone 6s e iPhone 6s Plus dispositivos.

Com 3D Touch, um aplicativo de iPhone agora é capaz de não apenas dizer que o usuário estiver tocando a tela do dispositivo, mas é capaz de detectar pressão que o usuário é exercendo e responder aos níveis diferentes de pressão.

Toque 3D fornece os seguintes recursos para seu aplicativo:

- [Sensibilidade à pressão](#Pressure-Sensitivity) - aplicativos agora podem medir como rígida ou leve o usuário estiver tocando a vantagem de tela e execute essas informações.
  Por exemplo, um aplicativo de pintura pode fazer uma linha mais espessa ou mais fino com base em como o usuário é tocar na tela.
- [Inspecionar e Pop](#Peek-and-Pop) -seu aplicativo pode agora permitem que o usuário interaja com seus dados sem precisar navegar fora de seu contexto atual. Pressionando rígido na tela de tela, eles podem inspecionar o item que estão interessados (como visualizar uma mensagem). Pressionando mais difícil, eles podem pop para o item.
- [Ações rápidas](#Quick-Actions) -pensar de ações rápidas como os menus contextuais que podem ser exibido o quando um usuário clica com o botão direito em um item em um aplicativo de área de trabalho.
  Usando ações rápidas, você pode adicionar atalhos às funções em seu aplicativo diretamente pelo ícone do aplicativo na tela inicial.
- [Teste 3D Touch no simulador](#Testing-3D-Touch-in-the-Simulator) -com o hardware correto do Mac, você pode testar aplicativos 3D de toque habilitado no simulador iOS.

<a name="Pressure-Sensitivity" />

## <a name="pressure-sensitivity"></a>Sensibilidade de pressão

Como mencionado acima, usando as propriedades de novo o [UITouch](https://developer.xamarin.com/api/type/UIKit.UITouch/) classe pode medir a quantidade de pressão o usuário está aplicando a tela do dispositivo iOS e usar essas informações em sua interface de usuário. Por exemplo, fazendo uma pincelada mais transparente ou opaco com base na quantidade de pressão.

[![](3d-touch-images/pressure01.png "Uma pincelada renderizada como mais transparente ou opaco com base na quantidade de pressão")](3d-touch-images/pressure01.png#lightbox)

Como resultado de 3D Touch, se seu aplicativo é executado no iOS 9 (ou superior) e o dispositivo iOS é capaz de suporte 3D Touch, alterações na pressão fará com que o `TouchesMoved` evento ser gerado.

Por exemplo, ao monitorar o `TouchesMoved` eventos de um [UIView](https://developer.xamarin.com/api/type/UIKit.UIView/), você pode usar o código a seguir para obter a pressão atual que o usuário está aplicando a tela:

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

O `MaximumPossibleForce` propriedade retorna o maior valor possível para o `Force` propriedade do [UITouch](https://developer.xamarin.com/api/type/UIKit.UITouch/) com base no que o aplicativo está em execução no dispositivo iOS.

> [!IMPORTANT]
> Alterações na pressão fará com que o `TouchesMoved` evento ser gerado, mesmo se o X / coordenadas Y não foram alterados. Devido a essa alteração no comportamento, seus aplicativos iOS devem estar preparados para a `TouchesMoved` evento a ser invocado com mais frequência e o x / coordenadas Y para ser o mesmo que o último `TouchesMoved` chamar.




Para obter mais informações, consulte da Apple [TouchCanvas: usando UITouch com eficiência e eficácia](https://developer.apple.com/library/prerelease/ios/samplecode/TouchCanvas/) aplicativo de exemplo e [referência de classe UITouch](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITouch_Class/).

<a name="Peek-and-Pop" />

## <a name="peek-and-pop"></a>Espiada e Pop

Toque 3D fornece novas maneiras de um usuário interagir com informações dentro de seu aplicativo mais rápido do que nunca, sem precisar navegar de seu local atual.

Por exemplo, se seu aplicativo estiver exibindo uma tabela de mensagens, o usuário pode pressionar rígido em um item para visualizar seu conteúdo em uma exibição de sobreposição (que Apple se refere a como uma *inspecionar*).

[![](3d-touch-images/peekandpop01.png "Um exemplo de exibição de conteúdo")](3d-touch-images/peekandpop01.png#lightbox)

Se o usuário pressionar mais difícil, eles deverão inserir o modo de exibição de mensagem normal (que é conhecido como *Pop*-ping no modo de exibição).

### <a name="checking-for-3d-touch-availability"></a>Verificando a disponibilidade de toque 3D

Ao trabalhar com um [UIViewController]() você pode usar o código a seguir para ver se o dispositivo iOS que o aplicativo está em execução no dá suporte ao 3D Touch:

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

Esse método pode ser chamado antes de *ou depois* `ViewDidLoad()`. 

### <a name="handling-peek-and-pop"></a>Pop e tratamento de pico

Em um dispositivo iOS que pode lidar com 3D Touch, podemos usar uma instância do `UIViewControllerPreviewingDelegate` classe para lidar com a exibição de **inspecionar** e **Pop** detalhes do item. Por exemplo, se um controlador de exibição de tabela chamada `MasterViewController ` podemos usar o código a seguir para dar suporte a **inspecionar** e **Pop**:

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

O `GetViewControllerForPreview` método é usado para executar o **inspecionar** operação. Ele obtém acesso aos dados de backup e a célula da tabela e, em seguida, carrega o `DetailViewController` do Storyboard atual. Definindo o `PreferredContentSize` de (0,0), pedimos que para o padrão **inspecionar** o tamanho de exibição. Por fim, podemos desfoque tudo, exceto a célula que está exibindo com `previewingContext.SourceRect = cell.Frame` e retornamos o novo modo de exibição.

O `CommitViewController` reutiliza o modo de exibição que criamos no **inspecionar** para o **Pop** exibir quando o usuário pressiona mais difícil.

### <a name="registering-for-peek-and-pop"></a>Registrar-se para inspecionar e Pop

Do controlador de exibição que você deseja permitir que o usuário **inspecionar** e **Pop** itens do, é preciso registrar para este serviço. No exemplo acima de um controlador de exibição de tabela (`MasterViewController`), utilizamos o código a seguir:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Check to see if 3D Touch is available
    if (TraitCollection.ForceTouchCapability == UIForceTouchCapability.Available) {
        // Regiser for Peek and Pop
        RegisterForPreviewingWithDelegate(new PreviewingDelegate(this), View);
    }
    ...

}
```

Aqui estamos ligando para o `RegisterForPreviewingWithDelegate` método com uma instância do `PreviewingDelegate` criado anteriormente. Em dispositivos iOS que dão suporte ao 3D Touch, o usuário pode pressionar rígido em um item para inspecionar a ele. Se eles ainda mais difícil pressionarem, o item será exibida para ele padrão de exibição.

Para obter mais informações, consulte nosso [iOS 9 exemplo ApplicationShortcuts](https://developer.xamarin.com/samples/monotouch/iOS9/ViewControllerPreview/) da Apple [ViewControllerPreviews: usando a APIs de visualização de UIViewController](https://developer.apple.com/library/prerelease/ios/samplecode/ViewControllerPreviews/Introduction/Intro.html) aplicativo de exemplo, [ Referência de classe UIPreviewAction](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewAction_Class/), [referência de classe UIPreviewActionGroup](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionGroup_Class/) e [referência de protocolo UIPreviewActionItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionItem_Protocol/).

<a name="Quick-Actions" />

## <a name="quick-actions"></a>Ações Rápidas

Usando 3D Touch e ações rápidas, você pode adicionar comuns, rápida e fácil para acesso atalhos para funções em seu aplicativo do ícone de tela inicial no dispositivo iOS.

Como mencionado acima, você pode pensar ações rápidas como os menus contextuais que podem ser exibido o quando um usuário clica com o botão direito em um item em um aplicativo de área de trabalho. Você deve usar ações rápidas para fornecer os atalhos para as funções ou os recursos do seu aplicativo mais comuns.

[![](3d-touch-images/quickactions01.png "Um exemplo de um menu de ações rápidas")](3d-touch-images/quickactions01.png#lightbox)


### <a name="defining-static-quick-actions"></a>Definindo ações rápidas estáticas

Se uma ou mais das ações rápidas exigida pelo seu aplicativo são estáticos e não precisa alterar, você pode defini-los no aplicativo do `Info.plist` arquivo. Editar esse arquivo em um editor externo e adicione as seguintes chaves:

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

Aqui nós está definindo dois itens de ação rápida estáticos com as seguintes chaves:

- `UIApplicationShortcutItemIconType` -Define o ícone que será exibido pelo item de ação rápida como um dos valores a seguir:
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

* `UIApplicationShortcutItemSubtitle` -Define a legenda para o item.
* `UIApplicationShortcutItemTitle` -Define o título do item.
* `UIApplicationShortcutItemType` -É um valor de cadeia de caracteres que serão usadas para identificar o item em nosso aplicativo. Consulte a seguinte seção para obter mais informações.

> [!IMPORTANT]
> Itens de atalho ação rápidas que são definidas no `Info.plist` arquivo não pode ser acessado com o `Application.ShortcutItems` propriedade. Eles apenas são passados para o `HandleShortcutItem` manipulador de eventos. 





### <a name="identifying-quick-action-items"></a>Identificação de itens de ação rápida

Como você viu anteriormente, quando você definiu os itens de ação rápida no seu aplicativo `Info.plist`, atribuído um valor de cadeia de caracteres para o `UIApplicationShortcutItemType` chave para identificá-los.

Para facilitar os identificadores trabalhar com código, adicione uma classe chamada `ShortcutIdentifier` para seu aplicativo do projeto e torná-lo a aparência a seguir:

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

Em seguida, você precisa modificar seu aplicativo `AppDelegate.cs` arquivo para lidar com o usuário selecionar um item de ação rápida de ícone do aplicativo na tela inicial.

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

Primeiro, definimos um público `LaunchedShortcutItem` propriedade para controlar o último item de ação rápida selecionado pelo usuário. Em seguida, podemos substituir o `FinishedLaunching` método e veja se `launchOptions` foram passados e se ele contém um item de ação rápida. Se isso ocorrer, armazenamos a ação rápida no `LaunchedShortcutItem` propriedade.

Em seguida, podemos substituir o `OnActivated` método e passe Selecionar item de início rápido para o `HandleShortcutItem` método a ser acionado. Atualmente, apenas escrevemos uma mensagem para o **Console**. Um aplicativo real, você usaria e qualquer ação era necessária. Depois que a ação foi executada, o `LaunchedShortcutItem` propriedade está desmarcada.

Finalmente, se seu aplicativo já estava em execução, o `PerformActionForShortcutItem` método será chamado para manipular o item de ação rápida, portanto, precisamos substituí-la e chamar nosso `HandleShortcutItem` método bem aqui.


### <a name="creating-dynamic-quick-action-items"></a>Criando itens de ação rápida dinâmico

Além de definir a ação rápida estático itens em seu aplicativo `Info.plist` arquivo, você pode criar dinâmico em rapidamente ações rápidas. Para definir duas novas ações rápidas dinâmico, edite seu `AppDelegate.cs` novamente e modificar o `FinishedLaunching` método para pesquisar, como o seguinte:

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

Agora está sendo verificado para ver se o `application` já contém um conjunto de criados dinamicamente `ShortcutItems`, se ele não vamos criar dois novos `UIMutableApplicationShortcutItem` objetos para definir os novos itens e adicioná-los para o `ShortcutItems` matriz.

O código já adicionamos a [tratamento uma ação rápida](#Handling-a-Quick-Action) seção acima tratará essas ações rápidas dinâmico assim como as estáticas.

Deve-se observar que você pode criar uma combinação de itens de ação rápida estáticos e dinâmicos (como estamos fazendo aqui), você não está limitado a um ou outro.

Para obter mais informações, veja nosso [iOS 9 exemplo ViewControllerPreview](https://developer.xamarin.com/samples/monotouch/iOS9/ViewControllerPreview/) e consulte da Apple [ApplicationShortcuts: usando UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/samplecode/ApplicationShortcuts/) aplicativo de exemplo, [ Referência de classe UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutItem_class/), [referência de classe UIMutableApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIMutableApplicationShortcutItem_class/) e [referência de classe UIApplicationShortcutIcon](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutIcon_Class/).

<a name="Testing-3D-Touch-in-the-Simulator" />

## <a name="testing-3d-touch-in-the-simulator"></a>Teste 3D Touch no simulador

Quando usando a versão mais recente do Xcode e o simulador de iOS em um Mac compatível com um toque de força habilitar trackpad, você pode testar a funcionalidade toque 3D no simulador.

Para habilitar essa funcionalidade, execute qualquer aplicativo em hardware de iPhone simulada que dá suporte ao 3D Touch (iPhone 6s e maior). Em seguida, selecione o **Hardware** menu no iOS simulador e habilitar o **Use Trackpad Force para 3D touch** item de menu:

[![](3d-touch-images/simulator01.png "Selecione o menu de Hardware no simulador iOS e habilitar a força de Trackpad de uso de item de menu sensível ao toque 3D")](3d-touch-images/simulator01.png#lightbox)

Com esse recurso ativado, você pode pressionar mais difícil no trackpad do Mac para habilitar 3D Touch, exatamente como você faria em um hardware real iPhone.

## <a name="summary"></a>Resumo

Este artigo apresenta as novas 3D Touch APIs disponível no iOS 9 para o iPhone 6s e iPhone 6s Plus. Ele coberto sensibilidade à pressão da adição a um aplicativo; usando o pico e Pop para exibir rapidamente as informações no aplicativo do contexto atual sem navegação; e usando as ações rápido fornece atalhos para seu aplicativo do mais comumente usados recursos.



## <a name="related-links"></a>Links relacionados

- [iOS 9 ViewControllerPreview exemplo](https://developer.xamarin.com/samples/monotouch/ios9/ViewControllerPreview/)
- [iOS 9 ApplicationShortcuts exemplo](https://developer.xamarin.com/samples/monotouch/ios9/ApplicationShortcuts/)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Preparar seus aplicativos iPhone para toque 3D](https://developer.apple.com/ios/3d-touch/)
