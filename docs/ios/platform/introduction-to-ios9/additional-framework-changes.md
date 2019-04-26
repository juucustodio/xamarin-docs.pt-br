---
title: Alterações de estruturas adicionais do iOS 9
description: Este documento descreve as alterações de estrutura adicionais introduzidas no iOS 9. Ele aborda AVFoundation, AVKit e CloudKit.
ms.prod: xamarin
ms.assetid: CFDE1FC4-9327-402B-95A0-581D4AA0E9D5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 5156259f8178da69595464f75a10cd8f41965519
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293790"
---
# <a name="additional-ios-9-frameworks-changes"></a>Alterações de estruturas adicionais do iOS 9

_Este artigo aborda as alterações adicionais, secundárias ou aprimoramentos para as estruturas existentes para o iOS 9._

[![](additional-framework-changes-images/ios9-sml.png "iOS 9 Logo")](additional-framework-changes-images/ios9.png#lightbox)

Além das principais alterações para o iOS, o Apple fez modificações e melhorias nas várias estruturas existentes no iOS 9.

## <a name="avfoundation-framework-additions"></a>Adições de Framework AVFoundation

No framework AVFoundation, o [AVSpeechSynthesisVoice](xref:AVFoundation.AVSpeechSynthesisVoice) classe agora permite que você especifique uma voz por identificador além do idioma.

Por exemplo, o código a seguir obtém uma lista de todas as vozes disponíveis:

```csharp
var voices = AVSpeechSynthesisVoice.GetSpeechVoices ();
```

Você pode usar uma das vozes da lista, em seguida, definindo-o como o `Voice` propriedade de uma instância das [AVSpeachUtterance](xref:AVFoundation.AVSpeechUtterance) classe.

O [AVQueuePlayer](xref:AVFoundation.AVQueuePlayer) classe agora dá suporte a uma mistura de mídia de streaming e baseados em arquivo da internet na fila. As versões anteriores podiam apenas mídia de fila do mesmo tipo.

Para obter mais informações, consulte da Apple [AVSpeechSynthesisVoice referência](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVSpeechSynthesisVoice_Ref/index.html#//apple_ref/occ/cl/AVSpeechSynthesisVoice).

## <a name="avkit-framework-additions"></a>Adições de Framework AVKit

Para trabalhar com o novo recurso de imagem na imagem (PIP), a estrutura de AVKit inclui o novo `AVPictureInPictureController` e [AVPlayerViewController](xref:AVKit.AVPlayerViewController) classes:

- **AVPictureInPictureController** -essa classe permite que um aplicativo do iOS 9 responder ao usuário iniciar a reprodução de um vídeo em uma janela flutuante, redimensionável de PIP em um iPad.
- **AVPlayerViewController** -gerencia um `AVPlayer` controlador usado para apresentar um vídeo em uma janela flutuante, redimensionável de PIP em um iPad.

Para obter mais informações, consulte nosso [multitarefa para iPad](~/ios/platform/introduction-to-ios9/index.md#multitasking) documentação e da Apple [AVPictureInPictureController referência](https://developer.apple.com/library/prerelease/ios/documentation/AVKit/Reference/AVPictureInPictureController_Class/index.html#//apple_ref/occ/cl/AVPictureInPictureController) e [AVPlayerViewController referência](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVPlayerViewController_Class/index.html#//apple_ref/occ/cl/AVPlayerViewController).

## <a name="introducing-cloudkit-web-services"></a>Introdução ao CloudKit Web Services

A estrutura de CloudKit simplifica o desenvolvimento de aplicativos do iCloud esse acesso. Isso inclui a recuperação de dados de aplicativo e direitos de ativo, bem como sendo capaz de armazenar com segurança informações do aplicativo. Esse kit oferece aos usuários uma camada de anonimato, permitindo o acesso a aplicativos com sua IDs do iCloud sem compartilhar informações pessoais.

O novo _CloudKit Web Services_ framework fornece uma biblioteca de JavaScript (JS CloudKit) que pode ser incorporada em seu site para fornecer acesso aos mesmos CloudKit com base em dados e conteúdo como um aplicativo xamarin. IOS.

> [!IMPORTANT]
> Antes de poder acessar, apresentar ou atualizar o conteúdo de um banco de dados do CloudKit usando o CloudKit JS, você deve ter definido anteriormente esquema do banco de dados.




Para obter mais informações, consulte os seguintes documentos:

- [Introdução ao CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) -nossa Introdução ao uso do CloudKit em um aplicativo xamarin. IOS.
- [Início rápido do CloudKit](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987) -introdução da Apple ao CloudKit.
- [Referência do CloudKit JS](https://developer.apple.com/library/prerelease/ios/documentation/CloudKitJS/Reference/CloudKitJavaScriptReference/index.html#//apple_ref/doc/uid/TP40015359) -documentação do CloudKit JS da Apple.
- [Referência de serviços Web CloudKit](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloutKitWebServicesReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40015240) -referência da Apple que descreve a interface HTTP ao CloudKit.
- [Catálogo do CloudKit: Uma introdução ao CloudKit (Cocoa e JavaScript)](https://developer.apple.com/library/prerelease/ios/samplecode/CloudAtlas/Introduction/Intro.html#//apple_ref/doc/uid/TP40014599) -aplicativo de exemplo da Apple usando o CloudKit e CloudKit JS.

> [!IMPORTANT]
> A Apple [fornece ferramentas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ajudar os desenvolvedores a lidar adequadamente com o GDPR (Regulamento Geral sobre a Proteção de Dados) da União Europeia.

## <a name="foundation-framework-additions"></a>Adições de estrutura de base

Apple incluiu as seguintes alterações à estrutura do Foundation no iOS 9:

### <a name="changes-to-nsbundle"></a>Alterações NSBundle

As seguintes alterações foram feitas para o [NSBundle](xref:Foundation.NSBundle) classe para iOS 9:

* `GetPreservationPriorityForTag (NSString tag)` -Obtém a prioridade de preservação atual para os recursos com a marca de determinado. Os valores válidos estão no intervalo `0.0` para `1.0`, recursos com a prioridade mais baixa sejam excluídos primeiro.
* `SetPreservationPriorityForTag (double priority, NSSet tags)` -Define a prioridade de preservação atual para os recursos com as marcas de determinado. Os valores válidos estão no intervalo `0.0` para `1.0`, recursos com a prioridade mais baixa sejam excluídos primeiro.

Para obter mais informações, consulte da Apple [NSBundle referência](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSBundle_Class/index.html#//apple_ref/occ/cl/NSBundle).

### <a name="changes-to-nsprocessinfo"></a>Alterações NSProcessInfo

Cada processo em execução em um dispositivo iOS tem um único _agente de informações do processo_ (PIA). Use o [NSProcessInfo](xref:Foundation.NSProcessInfo) classe para fornecer informações sobre a potência PIA e o controle atual e o gerenciamento térmico para um determinado processo.

Por exemplo, para controlar o encerramento automático de um processo, você pode usar o código a seguir:

```csharp
// Disable automatic termination
var activity = NSProcessInfo.ProcessInfo.BeginActivity(NSActivityOptions.AutomaticTerminationDisabled, "Define reason for change here...");

// Perform the required task
...

// Return to normal operation
NSProcessInfo.ProcessInfo.EndActivity(activity);
```

Para obter mais informações, consulte da Apple [NSProcessInfo referência](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSProcessInfo_Class/index.html#//apple_ref/occ/cl/NSProcessInfo).

### <a name="reacting-to-low-power-mode"></a>Reagindo a modo de baixa energia

Use o `LowPowerModeEnabled` propriedade do [NSProcessInfo](xref:Foundation.NSProcessInfo) classe para determinar se o modo de baixa energia foi habilitado no dispositivo iOS que o aplicativo está sendo executado. Por exemplo:

```csharp
// Is the device in low power mode?
if (NSProcessInfo.ProcessInfo.LowPowerModeEnabled) {
    // Reduce activity to conserve energy...
} else {
    // Return to normal activity...
}
```

## <a name="healthkit-framework-changes"></a>Alterações de estrutura HealthKit

Apple incluiu as seguintes alterações para o [HealthKit](xref:HealthKit) framework no iOS 9:

- Suporte para exclusão em massa e controle de exclusão de entradas no banco de dados HealthKit. Consulte da Apple [HKDeletedObject](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKDeletedObject_ClassReference/index.html#//apple_ref/occ/cl/HKDeletedObject), [HKAnchoredObjectQuery](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKAnchoredObjectQuery_Class/index.html#//apple_ref/occ/cl/HKAnchoredObjectQuery) e [referência de classe HKHealthStore](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKHealthStore_Class/index.html#//apple_ref/doc/uid/TP40014708) para obter mais informações.
- Novas categorias de rastreamento e características foram adicionadas à `HKQuantityTypeIdentifier` classe (como `UVExposure`) e o `HKCategoryTypeIdentifier` classe (como `OvulationTestResult`). Consulte da Apple [HealthKit constantes referência](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HealthKit_Constants/index.html#//apple_ref/doc/uid/TP40014710) para obter mais informações.

Consulte nosso [Introdução ao HealthKit](~/ios/platform/healthkit.md) documentação para obter mais informações sobre como trabalhar com o HealthKit no xamarin. IOS.

## <a name="local-authentication-framework-changes"></a>Alterações de estrutura de autenticação local

Apple incluiu as seguintes alterações para o [autenticação Local](xref:LocalAuthentication) framework no iOS 9:

- Usando o `EvaluateAccessControl` e `EvaluatePolicy` métodos das [LAContext](xref:LocalAuthentication.LAContext) classe, agora você pode tentativas de reutilização corresponde à ID de toque de desbloquear bem-sucedida anterior.
- A capacidade de obter uma lista dos dedos registrados no momento.
- Suporte para rastreamento de quando um dedo é adicionado ou removido da autenticação.
- A capacidade de usar _contexto de autenticação_ nas chamadas de conjunto de chaves e suporte para avaliar o controle de acesso do conjunto de chaves de listas.
- A capacidade de cancelar um prompt do usuário do código.

Consulte nosso [Introdução ao Touch ID](~/ios/platform/touchid.md) documentação para obter mais informações sobre como trabalhar com a ID de toque no xamarin. IOS.

### <a name="lacontext-changes"></a>Alterações de LAContext

As seguintes alterações foram feitas para o [LAContext](xref:LocalAuthentication.LAContext) classe para iOS 9:

- **TouchIdAuthenticationMaximumAllowableReuseDuration** -retorna a quantidade máxima de tempo que uma autenticação de ID de toque pode ser reutilizada.
- **EvaluatedPolicyDomainState** – obtém ou define o estado de uma política avaliada.
- **MaxBiometryFailures** -tem foi depreciado no iOS 9.
- **TouchIdAuthenticationAllowableReuseDuration** obtém ou define a quantidade de tempo que uma autenticação de ID de toque pode ser reutilizada.
- **EvaluateAccessControl** - assincronamente avalia uma política de autenticação.
- **Invalidar** -invalida uma autenticação de ID de toque determinado.
- **IsCredentialSet** -retorna `true` se as credenciais são definidas no momento.
- **SetCredentialType** define o tipo de credencial fornecida.

Consulte da Apple [LAContext referência](https://developer.apple.com/library/prerelease/ios/documentation/LocalAuthentication/Reference/LAContext_Class/index.html#//apple_ref/occ/instm/LAContext/evaluatePolicy:localizedReason:reply:) para obter mais detalhes.

## <a name="mapkit-framework-changes"></a>Alterações de estrutura MapKit

Apple incluiu as seguintes alterações para o [MapKit](xref:MapKit) framework no iOS 9:

- MapKit agora fornece suporte para iniciar o aplicativo de mapa diretamente em instruções de trânsito e para consultar o trânsito tempo estimado de chegada (ETA) usando o [MKLaunchOptions](xref:MapKit.MKLaunchOptions) e [MKDirections](xref:MapKit.MKLaunchOptions) classes.
- Os resultados da pesquisa retornados por MapKit e o [CLGeocoder](xref:CoreLocation.CLGeocoder) classe também pode fornecer a zona de tempo do resultado.
- Você agora pode personalizar totalmente anotações de mapa apresentado pelo seu aplicativo iOS usando o `DetailCalloutAccessoryView` propriedade do [MKAnnotationView](xref:MapKit.MKAnnotationView) classe.

Consulte nosso [mapas do iOS](~/ios/user-interface/controls/ios-maps/index.md) e [passo a passo: explorar as anotações e sobreposições no MapKit](~/ios/user-interface/controls/ios-maps/ios-maps-walkthrough.md) documentação para obter mais informações sobre como trabalhar com mapas e anotações no xamarin. IOS e Apple [CLGeocoder referência](https://developer.apple.com/library/prerelease/ios/documentation/CoreLocation/Reference/CLGeocoder_class/index.html#//apple_ref/occ/cl/CLGeocoder) para obter mais informações.

## <a name="passkit-framework-additions"></a>Adições do PassKit Framework

Apple incluiu as seguintes alterações para o [PassKit](xref:PassKit) framework no iOS 9:

- Apple Pay agora oferece suporte a armazenamento de débito e cartões de crédito, juntamente com os cartões Discover. Consulte a **redes de pagamento** seção da Apple [referência de classe PKPaymentRequest](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKPaymentRequest_Ref/index.html#//apple_ref/doc/uid/TP40014832) para obter mais informações.
- De dentro de um aplicativo xamarin. IOS, agora você pode adicionar as redes de pagamento e emissores de cartão para o Apple Pay. Consulte da Apple [referência de classe PKAddPaymentPassViewController](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKAddPaymentPassViewController_Class/index.html#//apple_ref/doc/uid/TP40016116) para obter mais detalhes.

Consulte nosso [Introdução ao PassKit](~/ios/platform/passkit.md) documentação para obter mais informações sobre como trabalhar com PassKit no xamarin. IOS.

## <a name="safari-services-framework-additions"></a>Adições de estrutura de serviços do Safari

Apple incluiu as seguintes alterações para o [Safari serviços](xref:SafariServices) framework no iOS 9:

- Agora você pode usar o novo [ter o SFSafariViewController](xref:SafariServices.SFSafariViewController) classe para exibir o conteúdo da web dentro de um aplicativo xamarin. IOS. Ele fornece a capacidade de compartilhar dados do site e os cookies com o aplicativo do Safari e inclui vários recursos do Safari (por exemplo, leitor e preenchimento automático). [Ter o SFSafariViewController](xref:SafariServices.SFSafariViewController) recursos de um **feito** botão que retornará os usuários ao seu aplicativo quando ele tiverem terminados de exibir o conteúdo da web.

Porque o [ter o SFSafariViewController](xref:SafariServices.SFSafariViewController) classe personalizada para exibir uma única página de conteúdo da web, considere usá-lo para substituir qualquer [WKWebKit](xref:WebKit.WKWebView) ou [UIWebView](xref:UIKit.UIWebView)controles dentro de seus aplicativos existentes do xamarin. IOS.

### <a name="displaying-a-website"></a>Exibindo um site da Web

O código a seguir está um exemplo de como chamar um [ter o SFSafariViewController](xref:SafariServices.SFSafariViewController) de dentro de outro controlador de exibição:

```csharp
// Create an instance of the Safari Services View Controller
var controller = new SFSafariViewController(new NSUrl("http://www.xamarin.com"));

// Display website
PresentViewController(controller, true, null);
```

## <a name="uikit-framework-changes"></a>Alterações de estrutura UIKit

Apple incluiu muitos aprimoramentos para vários elementos do [UIKit](xref:UIKit) framework para iOS 9. As seções a seguir detalha essas alterações.

### <a name="3d-touch-events"></a>Eventos de toque 3D

Novo no iOS 9 e o iPhone 6s e iPhone 6s Plus, 3D Touch adiciona gestos confidenciais de pressão para seus aplicativos iOS. Como resultado, se o aplicativo é executado no iOS 9 (ou posterior) e o dispositivo iOS é capaz de suporte 3D Touch, alterações na pressão fará com que o `TouchesMoved` evento seja acionado.

Por causa dessa alteração no comportamento, seus aplicativos iOS devem estar preparados para o `TouchesMoved` evento a ser invocado com mais frequência, mesmo que o X / coordenadas Y não foram alterados.

Para obter mais informações, consulte nosso [Introdução ao 3D Touch](~/ios/platform/3d-touch.md) guia.

### <a name="document-open-in-place-functionality"></a>Funcionalidade do documento aberto no local

Usando o `FinishedLaunching (application, launchOptions)` ou `WillFinishLaunching (Application, launchOptions)` métodos da [UIApplicationDelegate](xref:UIKit.UIApplicationDelegate) classe, agora você pode abrir um documento e modificá-lo no local (em vez de trabalhar em uma cópia).

Para dar suporte a nova funcionalidade de open in loco, adicione a `LSSupportsOpeningDocumentsInPlace` chave do seu aplicativo xamarin. IOS **Info. plist** arquivo com um valor de `YES`.

Consulte da Apple [UIApplicationDelegate referência](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate) para obter mais detalhes.

### <a name="enhanced-touch-events"></a>Eventos de toque aprimorado

Apple forneceu vários aprimoramentos para eventos de toque no iOS 9. Isso inclui a capacidade de usar a previsão de toque e obter acesso aos toques intermediários entre as atualizações de exibição.

Consulte da Apple [guia de tratamento de eventos para iOS](https://developer.apple.com/library/ios/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/Introduction/Introduction.html) para obter mais detalhes.

### <a name="fetching-tailored-content"></a>Buscar conteúdo sob medido

O novo `NSDataAsset` classe permite que um aplicativo xamarin. IOS Buscar conteúdo desenvolvido sob medida para a memória e recursos gráficos do que está sendo executado no dispositivo iOS.

### <a name="new-layout-anchors"></a>Novas âncoras de Layout

O novo `NSLayoutAnchor` e `NSLayoutDimension` classes de âncora de layout funcionam com as novas propriedades de âncora da [UIView](xref:UIKit.UIView) classe (como `LeadingAnchor` e `WidthAnchor`) para facilitar o layout no iOS 9.

Consulte nosso [Introdução ao Storyboards unificados](~/ios/user-interface/storyboards/unified-storyboards.md) documentação para obter mais informações sobre como trabalhar com layout automático e Classes de tamanho em um aplicativo xamarin. IOS e da Apple [NSLayoutAnchor referência](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutAnchor_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutAnchor), [ Referência de NSLayoutDimension](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutDimension_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutDimension) e [UIView referência](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/cl/UIView) para obter mais informações.

### <a name="new-readable-content-margins"></a>Novo margens de conteúdo legíveis

O novo `UILayoutGuide` classe pode ser usada para fornecer as margens de conteúdo legíveis e definir as regiões de desenho para o conteúdo dentro de um modo de exibição. Consulte da Apple [UILayoutGuide referência](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UILayoutGuide_Class_Reference/index.html#//apple_ref/occ/cl/UILayoutGuide) para obter mais informações.

### <a name="text-input-in-notifications-modifications"></a>Entrada de texto em modificações de notificações

O [UIUserNotificationAction](xref:UIKit.UIUserNotificationAction) classe tem um novo `Behavior` propriedade que pode ser usada para dar suporte à entrada de texto de notificações.

### <a name="uiapplicationdelegate-changes"></a>Alterações de UIApplicationDelegate

Enquanto não formalmente preterido pela Apple, sugerir a substituição de todas as chamadas para o `FinishedLaunching (UIApplication application)` método da [UIApplicationDelegate](xref:UIKit.UIApplicationDelegate) classe com qualquer um os `FinishedLaunching (UIApplication application, NSDictionary launchOptions)` ou `WillFinishLaunching (UIApplication application, NSDictionary launchOptions)` métodos.

Consulte da Apple [UIApplicationDelegate referência](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate) para obter mais detalhes.

### <a name="uikit-dynamics-changes"></a>Alterações do Dynamics UIKit

Apple incluiu as seguintes alterações ao UIKit Dynamics no iOS 9:

- Agora, o Dynamics fornece suporte para limites de colisão não retangulares.
- O novo, personalizável `UIFieldBehavior` classe é usada para dar suporte a vários tipos de campo.
- Tipos de anexo adicionais foram adicionados para o `UIAttachmentBehavior` classe.

Consulte da Apple [UIAttachment referência](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIAttachmentBehavior_Class/index.html#//apple_ref/occ/cl/UIAttachmentBehavior) para obter mais detalhes.

### <a name="uipickerview-and-uidatepicker-changes"></a>Alterações de UIDatePicker e UIPickerView

Antes do iOS 9, o [UIPickerView](xref:UIKit.UIPickerView) e o [UIDatePicker](xref:UIKit.UIDatePicker) controles foram não redimensionável e seria redimensionada automaticamente para preencher a largura de seu contêiner (normalmente, a largura do dispositivo iOS foi o aplicativo sendo executado).

No iOS 9, o redimensionamento automático não ocorre mais e os controles serão renderizados com uma largura de 320 ponto em todos os dispositivos iOS, independentemente do tamanho da tela e a orientação.

Para corrigir essa situação, use o Layout automático e Classes de tamanho para fixar a largura do controle às bordas do contêiner pai (exibição) e especifique a altura necessária. Consulte nosso [Introdução ao Storyboards unificados](~/ios/user-interface/storyboards/unified-storyboards.md) documentação para obter mais informações sobre como trabalhar com Layout automático e Classes de tamanho em um aplicativo xamarin. IOS.

### <a name="new-uitextinputassistantitem-class"></a>Nova classe UITextInputAssistantItem

Use a nova `UITextInputAssistantItem` classe para grupos de botões de barra de layout em um _barra de atalhos_. A barra de atalho é uma nova área que está disponível no teclado virtual para fornecer os atalhos de digitação.



## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Introdução ao iOS 9](~/ios/platform/introduction-to-ios9/index.md)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [O que há de novo no iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
