---
title: "Alterações de estruturas adicionais iOS 9"
description: "Este artigo aborda as alterações adicionais, secundárias ou melhorias em estruturas existentes para iOS 9."
ms.topic: article
ms.prod: xamarin
ms.assetid: CFDE1FC4-9327-402B-95A0-581D4AA0E9D5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 5053d8d0cf9c1c3e6d3282ee8d6e42448e733c2c
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2018
---
# <a name="additional-ios-9-frameworks-changes"></a>Alterações de estruturas adicionais iOS 9

_Este artigo aborda as alterações adicionais, secundárias ou melhorias em estruturas existentes para iOS 9._

[![](additional-framework-changes-images/ios9-sml.png "iOS 9 Logo")](additional-framework-changes-images/ios9.png#lightbox)

Além das alterações principais para iOS, a Apple tem feitas modificações e melhorias para várias estruturas existentes no iOS 9.

## <a name="av-foundation-framework-additions"></a>Adições de Framework AV Foundation

Na estrutura de AV Foundation, o [AVSpeechSynthesisVoice](https://developer.xamarin.com/api/type/AVFoundation.AVSpeechSynthesisVoice/) classe agora permite que você especifique uma voz por identificador além de idioma.

Por exemplo, o código a seguir obtém uma lista de todas as vozes disponíveis:

```csharp
var voices = AVSpeechSynthesisVoice.GetSpeechVoices ();
```

Você pode usar uma das vozes da lista, em seguida, definindo-o como o `Voice` propriedade de uma instância do [AVSpeachUtterance](https://developer.xamarin.com/api/type/AVFoundation.AVSpeechUtterance/) classe.

O [AVQueuePlayer](https://developer.xamarin.com/api/type/AVFoundation.AVQueuePlayer/) classe agora dá suporte a uma mistura de mídia de streaming e baseada em arquivo internet na fila. Versões anteriores podem somente mídia de fila do mesmo tipo.

Para obter mais informações, consulte da Apple [AVSpeechSynthesisVoice referência](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVSpeechSynthesisVoice_Ref/index.html#//apple_ref/occ/cl/AVSpeechSynthesisVoice).

## <a name="avkit-framework-additions"></a>Adições de AVKit Framework

Para trabalhar com o novo recurso de imagem na imagem (PIP), a estrutura de AVKit inclui o novo `AVPictureInPictureController` e [AVPlayerViewController](https://developer.xamarin.com/api/type/AVKit.AVPlayerViewController/) classes:

- **AVPictureInPictureController** -esta classe permite que um aplicativo do iOS 9 responder ao usuário iniciar a reprodução de um vídeo em uma janela de pontos flutuante, redimensionável em um iPad.
- **AVPlayerViewController** -gerencia um `AVPlayer` controlador usado para apresentar um vídeo em uma janela de pontos flutuante, redimensionável em um iPad.

Para obter mais informações, consulte nosso [multitarefa para iPad](~/ios/platform/introduction-to-ios9/index.md#multitasking) documentação e da Apple [AVPictureInPictureController referência](https://developer.apple.com/library/prerelease/ios/documentation/AVKit/Reference/AVPictureInPictureController_Class/index.html#//apple_ref/occ/cl/AVPictureInPictureController) e [referência AVPlayerViewController](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVPlayerViewController_Class/index.html#//apple_ref/occ/cl/AVPlayerViewController).

## <a name="introducing-cloudkit-web-services"></a>Introdução ao CloudKit Web Services

A estrutura CloudKit simplifica o desenvolvimento de aplicativos que iCloud de acesso. Isso inclui a recuperação de dados de aplicativo e direitos de ativo e ser capaz de armazenar com segurança informações do aplicativo. Esse kit oferece aos usuários uma camada de anonimato permitindo o acesso a aplicativos com sua IDs de iCloud sem compartilhar informações pessoais.

O novo _CloudKit Web Services_ framework fornece uma biblioteca de JavaScript (JS CloudKit) que pode ser incorporada no seu site para fornecer acesso aos mesmos CloudKit com base em dados e conteúdo como seu aplicativo xamarin.

> [!IMPORTANT]
> Antes de poder acessar, apresentar ou atualizar o conteúdo de um banco de dados CloudKit usando CloudKit JS, você deve ter previamente definidas esquema do banco de dados.




Para obter mais informações, consulte os seguintes documentos:

- [Introdução ao CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) -nossa Introdução ao uso de CloudKit em um aplicativo xamarin.
- [Início rápido do CloudKit](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987) -introdução da Apple para CloudKit.
- [Referência de JS CloudKit](https://developer.apple.com/library/prerelease/ios/documentation/CloudKitJS/Reference/CloudKitJavaScriptReference/index.html#//apple_ref/doc/uid/TP40015359) -documentação de CloudKit JS da Apple.
- [Referência de serviços Web CloudKit](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloutKitWebServicesReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40015240) -referência da Apple que descreve a interface HTTP para CloudKit.
- [Catálogo de CloudKit: Uma introdução ao CloudKit (Cocoa e JavaScript)](https://developer.apple.com/library/prerelease/ios/samplecode/CloudAtlas/Introduction/Intro.html#//apple_ref/doc/uid/TP40014599) -aplicativo de exemplo da Apple usando CloudKit e CloudKit JS.

## <a name="foundation-framework-additions"></a>Adições de estrutura de base

Apple inclui as seguintes alterações para a estrutura de base no iOS 9:

### <a name="changes-to-nsbundle"></a>Alterações NSBundle

As seguintes alterações foram feitas para o [NSBundle](https://developer.xamarin.com/api/type/Foundation.NSBundle/) classe para iOS 9:

* `GetPreservationPriorityForTag (NSString tag)` -Obtém a prioridade de preservação atual para recursos com a marca determinado. Os valores válidos estão no intervalo `0.0` para `1.0`, recursos com a prioridade mais baixa sejam excluídos primeiro.
* `SetPreservationPriorityForTag (double priority, NSSet tags)` -Define a prioridade de preservação atual para recursos com as marcas de determinado. Os valores válidos estão no intervalo `0.0` para `1.0`, recursos com a prioridade mais baixa sejam excluídos primeiro.

Para obter mais informações, consulte da Apple [NSBundle referência](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSBundle_Class/index.html#//apple_ref/occ/cl/NSBundle).

### <a name="changes-to-nsprocessinfo"></a>Alterações NSProcessInfo

Cada processo em execução em um dispositivo iOS tem um único _processo informações agente_ (PIA). Use o [NSProcessInfo](https://developer.xamarin.com/api/type/Foundation.NSProcessInfo/) classe para fornecer informações sobre o atual PIA e controle e gerenciamento térmico para um determinado processo.

Por exemplo, para controlar a conclusão automática de um processo, você pode usar o código a seguir:

```csharp
// Disable automatic termination
var activity = NSProcessInfo.ProcessInfo.BeginActivity(NSActivityOptions.AutomaticTerminationDisabled, "Define reason for change here...");

// Perform the required task
...

// Return to normal operation
NSProcessInfo.ProcessInfo.EndActivity(activity);
```

Para obter mais informações, consulte da Apple [NSProcessInfo referência](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSProcessInfo_Class/index.html#//apple_ref/occ/cl/NSProcessInfo).

### <a name="reacting-to-low-power-mode"></a>Reagindo ao modo de baixa energia

Use o `LowPowerModeEnabled` propriedade do [NSProcessInfo](https://developer.xamarin.com/api/type/Foundation.NSProcessInfo/) classe para determinar se o modo de baixa energia foi habilitado no dispositivo iOS que o aplicativo está em execução. Por exemplo:

```csharp
// Is the device in low power mode?
if (NSProcessInfo.ProcessInfo.LowPowerModeEnabled) {
    // Reduce activity to conserve energy...
} else {
    // Return to normal activity...
}
```

## <a name="healthkit-framework-changes"></a>Alterações de estrutura HealthKit

Apple inclui as seguintes alterações para o [HealthKit](https://developer.xamarin.com/api/namespace/HealthKit/) framework no iOS 9:

- Suporte para controle de exclusão de entradas no banco de dados HealthKit e exclusão em massa. Consulte da Apple [HKDeletedObject](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKDeletedObject_ClassReference/index.html#//apple_ref/occ/cl/HKDeletedObject), [HKAnchoredObjectQuery](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKAnchoredObjectQuery_Class/index.html#//apple_ref/occ/cl/HKAnchoredObjectQuery) e [HKHealthStore Referência de classe](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKHealthStore_Class/index.html#//apple_ref/doc/uid/TP40014708) para obter mais informações.
- Foram adicionadas novas categorias de controle e características para o `HKQuantityTypeIdentifier` classe (como `UVExposure`) e o `HKCategoryTypeIdentifier` classe (como `OvulationTestResult`). Consulte da Apple [HealthKit constantes referência](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HealthKit_Constants/index.html#//apple_ref/doc/uid/TP40014710) para obter mais informações.

Confira nosso [Introdução ao HealthKit](~/ios/platform/healthkit.md) documentação para obter mais informações sobre como trabalhar com HealthKit em xamarin.

## <a name="local-authentication-framework-changes"></a>Alterações de estrutura de autenticação local

Apple inclui as seguintes alterações para o [autenticação Local](https://developer.xamarin.com/api/namespace/LocalAuthentication/) framework no iOS 9:

- Usando o `EvaluateAccessControl` e `EvaluatePolicy` métodos do [LAContext](https://developer.xamarin.com/api/type/LocalAuthentication.LAContext/) classe, você pode agora tentativas de reutilização corresponde a ID de toque de desbloquear bem-sucedida anterior.
- A capacidade de obter uma lista dos dedos registrados no momento.
- Suporte para rastreamento de quando um dedo é adicionado ou removido de autenticação.
- A capacidade de usar _contexto de autenticação_ em chamadas de conjunto de chaves e suporte para avaliar o controle de acesso do conjunto de chaves de lista.
- A capacidade para cancelar um prompt do usuário do código.

Confira nosso [Introdução à ID de toque](~/ios/platform/touchid.md) documentação para obter mais informações sobre como trabalhar com a ID de toque em xamarin.

### <a name="lacontext-changes"></a>Alterações de LAContext

As seguintes alterações foram feitas para o [LAContext](https://developer.xamarin.com/api/type/LocalAuthentication.LAContext/) classe para iOS 9:

- **TouchIdAuthenticationMaximumAllowableReuseDuration** -retorna a quantidade máxima de tempo que uma autenticação de ID de toque pode ser reutilizada.
- **EvaluatedPolicyDomainState** - obtém ou define o estado de uma diretiva avaliada.
- **MaxBiometryFailures** -tem foi depreciado no iOS 9.
- **TouchIdAuthenticationAllowableReuseDuration** obtém ou define a quantidade de tempo que uma autenticação de ID de toque pode ser reutilizada.
- **EvaluateAccessControl** - assincronamente avalia uma política de autenticação.
- **Invalidar** -invalida uma autenticação de ID de toque determinado.
- **IsCredentialSet** -retorna `true` se as credenciais são definidas no momento.
- **SetCredentialType** define o tipo de credencial especificado.

Consulte da Apple [LAContext referência](https://developer.apple.com/library/prerelease/ios/documentation/LocalAuthentication/Reference/LAContext_Class/index.html#//apple_ref/occ/instm/LAContext/evaluatePolicy:localizedReason:reply:) para obter mais detalhes.

## <a name="mapkit-framework-changes"></a>Alterações de estrutura MapKit

Apple inclui as seguintes alterações para o [MapKit](https://developer.xamarin.com/api/namespace/MapKit/) framework no iOS 9:

- MapKit agora oferece suporte para iniciar o aplicativo de mapa diretamente em instruções de trânsito e para consultar o trânsito tempo estimado de chegada (ETA) usando o [MKLaunchOptions](https://developer.xamarin.com/api/type/MapKit.MKLaunchOptions/) e [MKDirections](https://developer.xamarin.com/api/type/MapKit.MKLaunchOptions/) classes.
- Os resultados da pesquisa retornados por MapKit e [CLGeocoder](https://developer.xamarin.com/api/type/CoreLocation.CLGeocoder/) classe também pode fornecer o fuso horário do resultado.
- Você agora pode personalizar totalmente anotações de mapa apresentado pelo seu aplicativo iOS usando o `DetailCalloutAccessoryView` propriedade o [MKAnnotationView](https://developer.xamarin.com/api/type/MapKit.MKAnnotationView/) classe.

Consulte nossa [iOS mapas](~/ios/user-interface/controls/ios-maps/index.md) e [passo a passo - explorar as anotações e sobreposições em MapKit](~/ios/user-interface/controls/ios-maps/ios-maps-walkthrough.md) documentação para obter mais informações sobre como trabalhar com mapas e anotações em xamarin e Apple [CLGeocoder referência](https://developer.apple.com/library/prerelease/ios/documentation/CoreLocation/Reference/CLGeocoder_class/index.html#//apple_ref/occ/cl/CLGeocoder) para obter mais informações.

## <a name="passkit-framework-additions"></a>Adições de PassKit Framework

Apple inclui as seguintes alterações para o [PassKit](https://developer.xamarin.com/api/namespace/PassKit/) framework no iOS 9:

- Pagamento da Apple agora oferece suporte a armazenamento débito e cartões de crédito, juntamente com os cartões de identificação. Consulte o **pagamento redes** seção da Apple [PKPaymentRequest Referência de classe](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKPaymentRequest_Ref/index.html#//apple_ref/doc/uid/TP40014832) para obter mais informações.
- De dentro de um aplicativo xamarin, agora você pode adicionar redes de pagamento e emissores de cartão de pagamento da Apple. Consulte da Apple [PKAddPaymentPassViewController Referência de classe](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKAddPaymentPassViewController_Class/index.html#//apple_ref/doc/uid/TP40016116) para obter mais detalhes.

Confira nosso [Introdução ao PassKit](~/ios/platform/passkit.md) documentação para obter mais informações sobre como trabalhar com PassKit em xamarin.

## <a name="safari-services-framework-additions"></a>Adições de estrutura de serviços do Safari

Apple inclui as seguintes alterações para o [Safari serviços](https://developer.xamarin.com/api/namespace/SafariServices/) framework no iOS 9:

- Agora você pode usar o novo [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) classe para exibir o conteúdo da web em um aplicativo xamarin. Ele fornece a capacidade de compartilhar dados do site e os cookies com o aplicativo do Safari e inclui vários recursos do Safari (como leitor e preenchimento automático). [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) recursos um **feito** botão que retornará os usuários ao seu aplicativo quando ele tiverem terminados de visualizar o conteúdo da web.

Porque o [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) classe personalizada para exibir uma única página de conteúdo da web, você deve considerar a usá-lo para substituir qualquer [WKWebKit](https://developer.xamarin.com/api/type/WebKit.WKWebView/) ou [UIWebView](https://developer.xamarin.com/api/type/UIKit.UIWebView/)controles dentro de seus aplicativos existentes do xamarin.

### <a name="displaying-a-website"></a>Exibindo um site

O código a seguir é um exemplo de chamada uma [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) de dentro de outro controlador de exibição:

```csharp
// Create an instance of the Safari Services View Controller
var controller = new SFSafariViewController(new NSUrl("http://www.xamarin.com"));

// Display website
PresentViewController(controller, true, null);
```

## <a name="uikit-framework-changes"></a>Alterações de estrutura UIKit

Apple inclui vários aprimoramentos para vários elementos do [UIKit](https://developer.xamarin.com/api/namespace/UIKit/) framework para iOS 9. As seções a seguir detalha essas alterações.

### <a name="3d-touch-events"></a>Eventos de toque 3D

Novo para iOS 9 e o iPhone 6s e iPhone 6s Plus, 3D Touch adiciona gestos confidenciais pressão para seus aplicativos iOS. Como resultado, se seu aplicativo está em execução no iOS 9 (ou superior) e o dispositivo iOS é capaz de suporte 3D Touch, alterações na pressão fará com que o `TouchesMoved` evento ser gerado. 

Devido a essa alteração no comportamento, seus aplicativos iOS devem estar preparados para o `TouchesMoved` evento a ser invocado com mais frequência, mesmo se o X / coordenadas Y não foram alterados. 

Para obter mais informações, consulte nosso [Introdução ao 3D Touch](~/ios/platform/3d-touch.md) guia.

### <a name="document-open-in-place-functionality"></a>Funcionalidade de abrir no local do documento

Usando o `FinishedLaunching (application, launchOptions)` ou `WillFinishLaunching (Application, launchOptions)` métodos do [UIApplicationDelegate](https://developer.xamarin.com/api/type/UIKit.UIApplicationDelegate/) classe, você pode abrir um documento e modificá-lo no local (em vez de trabalhar em uma cópia).

Para dar suporte a nova funcionalidade de abrir no local, adicione o `LSSupportsOpeningDocumentsInPlace` chave para seu aplicativo xamarin **Info. plist** arquivo com um valor de `YES`.

Consulte da Apple [UIApplicationDelegate referência](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate) para obter mais detalhes.

### <a name="enhanced-touch-events"></a>Eventos de toque avançado

Apple fornece várias melhorias para eventos de toque no iOS 9. Isso inclui a capacidade de usar a previsão de toque e obter acesso a ajustes intermediárias entre as atualizações de exibição.

Consulte da Apple [guia tratamento de evento para iOS](https://developer.apple.com/library/ios/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/Introduction/Introduction.html) para obter mais detalhes.

### <a name="fetching-tailored-content"></a>Busca de conteúdo personalizado

O novo `NSDataAsset` classe permite que um aplicativo xamarin buscar o conteúdo adaptado para a memória e recursos gráficos do que está sendo executado no dispositivo iOS.

### <a name="new-layout-anchors"></a>Novas âncoras de Layout

O novo `NSLayoutAnchor` e `NSLayoutDimension` classes de âncora de layout trabalhar com as novas propriedades de âncora do [UIView](https://developer.xamarin.com/api/type/UIKit.UIView/) classe (como `LeadingAnchor` e `WidthAnchor`) para facilitar o layout no iOS 9.

Confira nosso [Introdução ao Unified Storyboards](~/ios/user-interface/storyboards/unified-storyboards.md) documentação para obter mais informações sobre como trabalhar com Classes de tamanho e AutoLayout em um aplicativo xamarin e da Apple [NSLayoutAnchor referência](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutAnchor_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutAnchor), [ Referência de NSLayoutDimension](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutDimension_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutDimension) e [UIView referência](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/cl/UIView) para obter mais informações.

### <a name="new-readable-content-margins"></a>Novas margens Conteúdas legíveis

O novo `UILayoutGuide` classe pode ser usada para fornecer as margens de conteúdo legíveis e definir as regiões de desenho para o conteúdo dentro de um modo de exibição. Consulte da Apple [UILayoutGuide referência](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UILayoutGuide_Class_Reference/index.html#//apple_ref/occ/cl/UILayoutGuide) para obter mais informações.

### <a name="text-input-in-notifications-modifications"></a>Entrada de texto em modificações de notificações

O [UIUserNotificationAction](https://developer.xamarin.com/api/type/UIKit.UIUserNotificationAction/) classe tem um novo `Behavior` propriedade que pode ser usada para dar suporte a entrada de texto de notificações.

### <a name="uiapplicationdelegate-changes"></a>Alterações de UIApplicationDelegate

Enquanto não formalmente substituído pela Apple, sugerir a substituição de todas as chamadas para o `FinishedLaunching (UIApplication application)` método o [UIApplicationDelegate](https://developer.xamarin.com/api/type/UIKit.UIApplicationDelegate/) classe com o o `FinishedLaunching (UIApplication application, NSDictionary launchOptions)` ou `WillFinishLaunching (UIApplication application, NSDictionary launchOptions)` métodos.

Consulte da Apple [UIApplicationDelegate referência](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate) para obter mais detalhes.

### <a name="uikit-dynamics-changes"></a>Alterações do Dynamics UIKit

Apple inclui as seguintes alterações para o UIKit Dynamics no iOS 9:

- Dynamics agora oferece suporte para limites de colisão não retangulares.
- O novo personalizável `UIFieldBehavior` classe é usada para oferecer suporte a vários tipos de campo.
- Tipos de anexo adicionais foram adicionados para o `UIAttachmentBehavior` classe.

Consulte da Apple [UIAttachment referência](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIAttachmentBehavior_Class/index.html#//apple_ref/occ/cl/UIAttachmentBehavior) para obter mais detalhes.

### <a name="uipickerview-and-uidatepicker-changes"></a>UIPickerView e UIDatePicker alterações

Antes do iOS 9, o [UIPickerView](https://developer.xamarin.com/api/type/UIKit.UIPickerView/) e [UIDatePicker](https://developer.xamarin.com/api/type/UIKit.UIDatePicker/) controles foram não redimensionável e será redimensionada automaticamente para preencher a largura do contêiner (geralmente a largura do dispositivo iOS, o aplicativo em execução).

No iOS 9, este redimensionamento automático não ocorrerá e os controles serão renderizados em uma largura de 320 ponto em todos os dispositivos iOS, independentemente do tamanho da tela e orientação.

Para corrigir essa situação, use Classes de tamanho e Layout automático para fixar a largura do controle para as bordas do contêiner pai (exibição) e especifica a altura necessária. Confira nosso [Introdução ao Unified Storyboards](~/ios/user-interface/storyboards/unified-storyboards.md) documentação para obter mais informações sobre como trabalhar com Classes de tamanho e Layout automático em um aplicativo xamarin.

### <a name="new-uitextinputassistantitem-class"></a>Nova classe UITextInputAssistantItem

Use a nova `UITextInputAssistantItem` classe para grupos de botões de barra de layout em um _atalho barra_. A barra de atalho é uma nova área que está disponível no teclado virtual para fornecer atalhos de digitação.



## <a name="related-links"></a>Links relacionados

- [Exemplos do iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Introdução ao iOS 9](~/ios/platform/introduction-to-ios9/index.md)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [O que há de novo no iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
