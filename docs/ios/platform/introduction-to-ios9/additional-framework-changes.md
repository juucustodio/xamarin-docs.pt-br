---
title: Alterações adicionais em estruturas do iOS 9
description: Este documento descreve as alterações de estrutura adicionais introduzidas no iOS 9. Ele discute AVFoundation, AVKit e CloudKit.
ms.prod: xamarin
ms.assetid: CFDE1FC4-9327-402B-95A0-581D4AA0E9D5
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: fd9bced0d2185fd9bd0d18932921c101b2ed207c
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292925"
---
# <a name="additional-ios-9-frameworks-changes"></a>Alterações adicionais em estruturas do iOS 9

_Este artigo aborda as alterações adicionais, secundárias ou aprimoramentos nas estruturas existentes do iOS 9._

[![](additional-framework-changes-images/ios9-sml.png "iOS 9 Logo")](additional-framework-changes-images/ios9.png#lightbox)

Além das principais alterações no iOS, a Apple fez modificações e melhorias em várias estruturas existentes no iOS 9.

## <a name="avfoundation-framework-additions"></a>Adições da estrutura AVFoundation

Na estrutura AVFoundation, a classe [AVSpeechSynthesisVoice](xref:AVFoundation.AVSpeechSynthesisVoice) agora permite que você especifique uma voz por identificador, além do idioma.

Por exemplo, o código a seguir obtém uma lista de todas as vozes disponíveis:

```csharp
var voices = AVSpeechSynthesisVoice.GetSpeechVoices ();
```

Em seguida, você pode usar uma das vozes da lista definindo-a como a propriedade `Voice` de uma instância da classe [AVSpeachUtterance](xref:AVFoundation.AVSpeechUtterance) .

A classe [AVQueuePlayer](xref:AVFoundation.AVQueuePlayer) agora dá suporte a uma combinação de streaming da Internet e mídia baseada em arquivo na fila. As versões anteriores só podiam enfileirar mídia do mesmo tipo.

Para obter mais informações, consulte a [referência do AVSpeechSynthesisVoice](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVSpeechSynthesisVoice_Ref/index.html#//apple_ref/occ/cl/AVSpeechSynthesisVoice)da Apple.

## <a name="avkit-framework-additions"></a>Adições da estrutura AVKit

Para trabalhar com o novo recurso PIP (Picture-in-Picture), a estrutura AVKit inclui as novas classes `AVPictureInPictureController` e [AVPlayerViewController](xref:AVKit.AVPlayerViewController) :

- **AVPictureInPictureController** -essa classe permite que um aplicativo IOS 9 responda ao usuário que está iniciando a reprodução de um vídeo em uma janela de Pip flutuante e redimensionável em um iPad.
- **AVPlayerViewController** -gerencia um controlador de `AVPlayer` usado para apresentar um vídeo em uma janela de Pip flutuante e redimensionável em um iPad.

Para obter mais informações, consulte nossa documentação [multitarefa para iPad](~/ios/platform/introduction-to-ios9/index.md#multitasking) e referência de [AVPictureInPictureController](https://developer.apple.com/library/prerelease/ios/documentation/AVKit/Reference/AVPictureInPictureController_Class/index.html#//apple_ref/occ/cl/AVPictureInPictureController) e referência de [AVPlayerViewController](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVPlayerViewController_Class/index.html#//apple_ref/occ/cl/AVPlayerViewController)da Apple.

## <a name="introducing-cloudkit-web-services"></a>Apresentando os serviços Web do CloudKit

A estrutura CloudKit simplifica o desenvolvimento de aplicativos que acessam o iCloud. Isso inclui a recuperação de dados de aplicativos e direitos de ativos, bem como a capacidade de armazenar informações de aplicativos com segurança. Esse kit oferece aos usuários uma camada de anonimato, permitindo o acesso a aplicativos com suas IDs do iCloud sem compartilhar informações pessoais.

A nova estrutura de _Serviços Web do CloudKit_ fornece uma biblioteca JavaScript (CloudKit js) que pode ser incorporada em seu site para fornecer acesso aos mesmos dados e conteúdo baseados em CloudKit como seu aplicativo Xamarin. Ios.

> [!IMPORTANT]
> Antes de poder acessar, apresentar ou atualizar o conteúdo de um banco de dados CloudKit usando o CloudKit JS, você deve ter definido anteriormente o esquema do banco de dados.

Para obter mais informações, consulte os seguintes documentos:

- [Introdução ao CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) -nossa introdução ao uso do CloudKit em um aplicativo Xamarin. Ios.
- [CloudKit início rápido](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987) – introdução da Apple ao CloudKit.
- [Referência do CLOUDKIT js](https://developer.apple.com/library/prerelease/ios/documentation/CloudKitJS/Reference/CloudKitJavaScriptReference/index.html#//apple_ref/doc/uid/TP40015359) – documentação do CloudKit js da Apple.
- [Catálogo CloudKit: uma introdução ao CloudKit (Cocoa e JavaScript)](https://developer.apple.com/library/prerelease/ios/samplecode/CloudAtlas/Introduction/Intro.html#//apple_ref/doc/uid/TP40014599) – aplicativo de exemplo da Apple usando o CloudKit e o CloudKit js.

> [!IMPORTANT]
> A Apple [fornece ferramentas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ajudar os desenvolvedores a lidar adequadamente com o GDPR (Regulamento Geral sobre a Proteção de Dados) da União Europeia.

## <a name="foundation-framework-additions"></a>Adições do Foundation Framework

A Apple incluiu as seguintes alterações na estrutura base do iOS 9:

### <a name="changes-to-nsbundle"></a>Alterações em NSBundle

As seguintes alterações foram feitas na classe [NSBundle](xref:Foundation.NSBundle) para IOS 9:

- `GetPreservationPriorityForTag (NSString tag)`-Obtém a prioridade de preservação atual para recursos com a marca fornecida. Os valores válidos estão no intervalo `0.0` para `1.0`, os recursos com a menor prioridade serão limpos primeiro.
- `SetPreservationPriorityForTag (double priority, NSSet tags)`-define a prioridade de preservação atual para recursos com as marcações fornecidas. Os valores válidos estão no intervalo `0.0` para `1.0`, os recursos com a menor prioridade serão limpos primeiro.

Para obter mais informações, consulte a [referência do NSBundle](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSBundle_Class/index.html#//apple_ref/occ/cl/NSBundle)da Apple.

### <a name="changes-to-nsprocessinfo"></a>Alterações em NSProcessInfo

Cada processo em execução em um dispositivo iOS tem um único pia ( _agente de informações de processo_ ). Use a classe [NSProcessInfo](xref:Foundation.NSProcessInfo) para fornecer informações sobre a potência de controle e o pia atual e o gerenciamento térmico para um determinado processo.

Por exemplo, para controlar o encerramento automático de um processo, você pode usar o seguinte código:

```csharp
// Disable automatic termination
var activity = NSProcessInfo.ProcessInfo.BeginActivity(NSActivityOptions.AutomaticTerminationDisabled, "Define reason for change here...");

// Perform the required task
...

// Return to normal operation
NSProcessInfo.ProcessInfo.EndActivity(activity);
```

Para obter mais informações, consulte a [referência do NSProcessInfo](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSProcessInfo_Class/index.html#//apple_ref/occ/cl/NSProcessInfo)da Apple.

### <a name="reacting-to-low-power-mode"></a>Reagindo ao modo de baixa energia

Use a propriedade `LowPowerModeEnabled` da classe [NSProcessInfo](xref:Foundation.NSProcessInfo) para determinar se o modo de baixa energia foi habilitado no dispositivo IOS em que o aplicativo está sendo executado. Por exemplo:

```csharp
// Is the device in low power mode?
if (NSProcessInfo.ProcessInfo.LowPowerModeEnabled) {
    // Reduce activity to conserve energy...
} else {
    // Return to normal activity...
}
```

## <a name="healthkit-framework-changes"></a>Alterações na estrutura do HealthKit

A Apple incluiu as seguintes alterações na estrutura [HealthKit](xref:HealthKit) no Ios 9:

- Suporte para exclusão em massa e controle de exclusão de entradas no banco de dados HealthKit. Consulte [referência da classe](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKHealthStore_Class/index.html#//apple_ref/doc/uid/TP40014708) [HKDeletedObject](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKDeletedObject_ClassReference/index.html#//apple_ref/occ/cl/HKDeletedObject), [HKAnchoredObjectQuery](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKAnchoredObjectQuery_Class/index.html#//apple_ref/occ/cl/HKAnchoredObjectQuery) e HKHealthStore da Apple para obter mais informações.
- Novas características e categorias de acompanhamento foram adicionadas à classe `HKQuantityTypeIdentifier` (como `UVExposure`) e à classe `HKCategoryTypeIdentifier` (como `OvulationTestResult`). 

Consulte nossa [introdução à](~/ios/platform/healthkit.md) documentação do HealthKit para saber mais sobre como trabalhar com o HealthKit no Xamarin. Ios.

## <a name="local-authentication-framework-changes"></a>Alterações da estrutura de autenticação local

A Apple incluiu as seguintes alterações na estrutura de [autenticação local](xref:LocalAuthentication) no Ios 9:

- Usando os métodos `EvaluateAccessControl` e `EvaluatePolicy` da classe [LAContext](xref:LocalAuthentication.LAContext) , agora você pode reutilizar as correspondências de ID de toque das tentativas anteriores de desbloqueio bem-sucedidas.
- A capacidade de obter uma lista de dedos atualmente registrados.
- Suporte para acompanhamento quando um dedo é adicionado ou removido da autenticação.
- A capacidade de usar o _contexto de autenticação_ em chamadas de conjunto de chaves e suporte para avaliar listas de controle de acesso de conjunto de chaves.
- A capacidade de cancelar um prompt de usuário do código.

Para obter mais informações, consulte [Touch ID e face ID com Xamarin. Ios](~/ios/platform/touch-id-face-id.md).

### <a name="lacontext-changes"></a>LAContext alterações

As seguintes alterações foram feitas na classe [LAContext](xref:LocalAuthentication.LAContext) para IOS 9:

- **TouchIdAuthenticationMaximumAllowableReuseDuration** -retorna a quantidade máxima de tempo que uma autenticação de ID de toque pode ser reutilizada.
- **EvaluatedPolicyDomainState** -Obtém ou define o estado de uma política avaliada.
- **MaxBiometryFailures** -foi depreciado no Ios 9.
- **TouchIdAuthenticationAllowableReuseDuration** Obtém ou define a quantidade de tempo que uma autenticação de ID de toque pode ser reutilizada.
- **EvaluateAccessControl** – avalia de forma assíncrona uma política de autenticação.
- Invalidar – invalida uma determinada **autenticação de ID** de toque.
- **Iscredentialset** – retorna `true` se as credenciais estiverem definidas no momento.
- **Setcredentialtype** Define o tipo de credencial fornecido.

Consulte a referência do [LAContext](https://developer.apple.com/library/prerelease/ios/documentation/LocalAuthentication/Reference/LAContext_Class/index.html#//apple_ref/occ/instm/LAContext/evaluatePolicy:localizedReason:reply:) da Apple para obter mais detalhes.

## <a name="mapkit-framework-changes"></a>Alterações na estrutura do MapKit

A Apple incluiu as seguintes alterações na estrutura [MapKit](xref:MapKit) no Ios 9:

- O MapKit agora oferece suporte para iniciar o aplicativo de mapa diretamente em direções de trânsito e para consultar o tempo estimado de chegada (ETA) de trânsito usando as classes [MKLaunchOptions](xref:MapKit.MKLaunchOptions) e [MKDirections](xref:MapKit.MKLaunchOptions) .
- Os resultados da pesquisa retornados por MapKit e a classe [CLGeocoder](xref:CoreLocation.CLGeocoder) também podem fornecer o fuso horário do resultado.
- Agora você pode personalizar totalmente as anotações de mapa apresentadas por seu aplicativo iOS usando a propriedade `DetailCalloutAccessoryView` da classe [MKAnnotationView](xref:MapKit.MKAnnotationView) .

Confira nossos [mapas do IOS](~/ios/user-interface/controls/ios-maps/index.md) e as [anotações e sobreposições que exploram a](~/ios/user-interface/controls/ios-maps/ios-maps-walkthrough.md) documentação do MapKit para obter mais informações sobre como trabalhar com mapas e anotações no Xamarin. Ios e na [referência do CLGeocoder](https://developer.apple.com/library/prerelease/ios/documentation/CoreLocation/Reference/CLGeocoder_class/index.html#//apple_ref/occ/cl/CLGeocoder) da Apple para obter mais informações.

## <a name="passkit-framework-additions"></a>Adições da estrutura PassKit

A Apple incluiu as seguintes alterações na estrutura [PassKit](xref:PassKit) no Ios 9:

- O Apple Pay agora dá suporte a débitos de loja e cartões de crédito juntamente com cartões de descoberta. Consulte a seção **redes de pagamento** da referência de [classe PKPaymentRequest](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKPaymentRequest_Ref/index.html#//apple_ref/doc/uid/TP40014832) da Apple para obter mais informações.
- Diretamente em um aplicativo Xamarin. iOS, agora você pode adicionar redes de pagamento e emissores de cartão a Apple Pay. Consulte a [referência de classe PKAddPaymentPassViewController](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKAddPaymentPassViewController_Class/index.html#//apple_ref/doc/uid/TP40016116) da Apple para obter mais detalhes.

Consulte nossa [introdução à](~/ios/platform/passkit.md) documentação do PassKit para saber mais sobre como trabalhar com o PassKit no Xamarin. Ios.

## <a name="safari-services-framework-additions"></a>Adições da estrutura de serviços do Safari

A Apple incluiu as seguintes alterações na estrutura de [Serviços do Safari](xref:SafariServices) no Ios 9:

- Agora você pode usar a nova classe [SFSafariViewController](xref:SafariServices.SFSafariViewController) para exibir o conteúdo da Web em um aplicativo Xamarin. Ios. Ele fornece a capacidade de compartilhar dados e cookies do site com o aplicativo Safari e inclui vários recursos do Safari (como leitor e preenchimento automático). [SFSafariViewController](xref:SafariServices.SFSafariViewController) apresenta um botão **concluído** que retornará os usuários ao seu aplicativo quando terminarem de exibir o conteúdo da Web.

Como a classe [SFSafariViewController](xref:SafariServices.SFSafariViewController) é adaptada para exibir uma única página de conteúdo da Web, você deve considerar usá-la para substituir os controles [WKWebKit](xref:WebKit.WKWebView) ou [UIWebView](xref:UIKit.UIWebView) em seus aplicativos Xamarin. Ios existentes.

### <a name="displaying-a-website"></a>Exibindo um site

O código a seguir é um exemplo de chamada de um [SFSafariViewController](xref:SafariServices.SFSafariViewController) de dentro de outro controlador de exibição:

```csharp
// Create an instance of the Safari Services View Controller
var controller = new SFSafariViewController(new NSUrl("http://www.xamarin.com"));

// Display website
PresentViewController(controller, true, null);
```

## <a name="uikit-framework-changes"></a>Alterações na estrutura do UIKit

A Apple incluiu muitos aprimoramentos em vários elementos da estrutura [UIKit](xref:UIKit) para IOS 9. As seções a seguir detalharão essas alterações.

### <a name="3d-touch-events"></a>Eventos de toque 3D

Novidade no iOS 9 e no iPhone 6s e iPhone 6s Plus, o 3D Touch adiciona gestos sensíveis à pressão aos seus aplicativos iOS. Como resultado, se seu aplicativo estiver em execução no iOS 9 (ou superior) e o dispositivo iOS for capaz de dar suporte ao toque 3D, as alterações na pressão farão com que o evento `TouchesMoved` seja gerado.

Devido a essa alteração no comportamento, seus aplicativos iOS devem estar preparados para que o evento `TouchesMoved` seja invocado com mais frequência, mesmo que as coordenadas X/Y não tenham sido alteradas.

Para obter mais informações, consulte nosso [introdução ao guia de toque 3D](~/ios/platform/3d-touch.md) .

### <a name="document-open-in-place-functionality"></a>Documentar a funcionalidade do Open-in-Place

Usando os métodos `FinishedLaunching (application, launchOptions)` ou `WillFinishLaunching (Application, launchOptions)` da classe [UIApplicationDelegate](xref:UIKit.UIApplicationDelegate) , agora você pode abrir um documento e modificá-lo no lugar (em vez de trabalhar em uma cópia).

Para dar suporte à nova funcionalidade do Open-in-Place, adicione a chave de `LSSupportsOpeningDocumentsInPlace` ao arquivo **info. plist** do aplicativo Xamarin. Ios com um valor de `YES`.

Consulte a referência do [UIApplicationDelegate](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate) da Apple para obter mais detalhes.

### <a name="enhanced-touch-events"></a>Eventos de toque avançados

A Apple forneceu vários aprimoramentos para eventos de toque no iOS 9. Isso inclui a capacidade de usar a previsão de toque e obter acesso a toques intermediários entre atualizações de exibição.

Consulte o [Guia de manipulação de eventos da Apple para IOS](https://developer.apple.com/library/ios/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/Introduction/Introduction.html) para obter mais detalhes.

### <a name="fetching-tailored-content"></a>Buscando conteúdo personalizado

A nova classe `NSDataAsset` permite que um aplicativo Xamarin. iOS busque o conteúdo sob medida para a memória e os recursos gráficos do dispositivo iOS em que ele está atualmente em execução.

### <a name="new-layout-anchors"></a>Novas âncoras de layout

As novas classes de âncora de layout `NSLayoutAnchor` e `NSLayoutDimension` funcionam com as novas propriedades de âncora da classe [UIView](xref:UIKit.UIView) (como `LeadingAnchor` e `WidthAnchor`) para facilitar o layout no Ios 9.

Consulte nossa [introdução à documentação de storyboards unificadas](~/ios/user-interface/storyboards/unified-storyboards.md) para obter mais informações sobre como trabalhar com classes de tamanho e AutoLayout em um aplicativo Xamarin. Ios e [referência de NSLayoutAnchor](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutAnchor_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutAnchor)da Apple, referência de [NSLayoutDimension](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutDimension_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutDimension) e [referência de UIView](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/cl/UIView) para obter mais informações.

### <a name="new-readable-content-margins"></a>Novas margens de conteúdo legíveis

A nova classe `UILayoutGuide` pode ser usada para fornecer margens de conteúdo legíveis e definir as regiões de desenho para o conteúdo dentro de uma exibição. Consulte a [referência do UILayoutGuide](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UILayoutGuide_Class_Reference/index.html#//apple_ref/occ/cl/UILayoutGuide) da Apple para obter mais informações.

### <a name="text-input-in-notifications-modifications"></a>Entrada de texto em modificações de notificações

A classe [UIUserNotificationAction](xref:UIKit.UIUserNotificationAction) tem uma nova propriedade `Behavior` que pode ser usada para dar suporte à entrada de texto de notificações.

### <a name="uiapplicationdelegate-changes"></a>UIApplicationDelegate alterações

Embora não seja formalmente preterido pela Apple, eles sugerem substituir todas as chamadas para o método `FinishedLaunching (UIApplication application)` da classe [UIApplicationDelegate](xref:UIKit.UIApplicationDelegate) com os métodos `FinishedLaunching (UIApplication application, NSDictionary launchOptions)` ou `WillFinishLaunching (UIApplication application, NSDictionary launchOptions)`.

Consulte a referência do [UIApplicationDelegate](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate) da Apple para obter mais detalhes.

### <a name="uikit-dynamics-changes"></a>Alterações do UIKit Dynamics

A Apple incluiu as seguintes alterações no UIKit Dynamics no iOS 9:

- O Dynamics agora oferece suporte a limites de colisão não retangulares.
- A nova classe de `UIFieldBehavior` personalizável é usada para oferecer suporte a vários tipos de campo.
- Tipos de anexo adicionais foram adicionados à classe `UIAttachmentBehavior`.

Consulte a referência do [UIAttachment](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIAttachmentBehavior_Class/index.html#//apple_ref/occ/cl/UIAttachmentBehavior) da Apple para obter mais detalhes.

### <a name="uipickerview-and-uidatepicker-changes"></a>Alterações de UIPickerView e UIDatePicker

Antes do iOS 9, os controles [UIPickerView](xref:UIKit.UIPickerView) e [UIDatePicker](xref:UIKit.UIDatePicker) não eram redimensionáveis e seriam redimensionados automaticamente para preencher a largura do contêiner (geralmente, a largura do dispositivo IOS em que o aplicativo estava sendo executado).

No iOS 9, esse redimensionamento automático não ocorrerá mais e os controles serão renderizados com uma largura de ponto de 320 em todos os dispositivos iOS, independentemente do tamanho e da orientação da tela.

Para corrigir essa situação, use o layout automático e as classes de tamanho para fixar a largura do controle nas bordas do contêiner pai (exibição) e especifique a altura necessária. Consulte nossa [introdução à documentação de storyboards unificadas](~/ios/user-interface/storyboards/unified-storyboards.md) para obter mais informações sobre como trabalhar com classes de layout e tamanho automático em um aplicativo Xamarin. Ios.

### <a name="new-uitextinputassistantitem-class"></a>Nova classe UITextInputAssistantItem

Use a nova classe `UITextInputAssistantItem` para grupos de botão da barra de layout em uma _barra de atalhos_. A barra de atalhos é uma nova área que está disponível no teclado flexível para fornecer atalhos de digitação.

## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [Introdução ao iOS 9](~/ios/platform/introduction-to-ios9/index.md)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [O que há de novo no iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
