---
title: Extensões do iOS no Xamarin. iOS
description: Este documento descreve as extensões do, que são widgets apresentados pelo iOS no contexto padrão, como no centro de notificações. Ele aborda como criar uma extensão e se comunicar com ela do aplicativo pai.
ms.prod: xamarin
ms.assetid: 3DEB3D43-3E4A-4099-8331-93C1E7A77095
ms.technology: xamarin-ios
ms.custom: xamu-video
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 4137ce7542a213a0a4c27b6a66b38828e4646520
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653464"
---
# <a name="ios-extensions-in-xamarinios"></a>extensões do iOS no Xamarin. iOS

> [!VIDEO https://youtube.com/embed/Sd0-ch9Udmk]

**Criando extensões no vídeo do iOS**

As extensões, como introduzidas no Ios 8, `UIViewControllers` são especializadas que são apresentadas por Ios dentro de contextos padrão, como no **centro de notificações**, como tipos de teclado personalizados solicitados pelo usuário para executar entradas especializadas ou outros contextos como editar uma foto onde a extensão pode fornecer filtros de efeito especiais.

Todas as extensões são instaladas em conjunto com um aplicativo de contêiner (com os dois elementos escritos usando as APIs unificadas de 64 bits) e são ativadas de um ponto de extensão específico em um aplicativo host. E, como eles serão usados como suplementos para funções de sistema existentes, eles devem ser de alto desempenho, de Lean e robustos. 

## <a name="extension-points"></a>Pontos de extensão

|Tipo|Descrição|Ponto de extensão|Aplicativo host|
|--- |--- |--- |--- |
|Ação|Editor ou Visualizador especializado para um tipo de mídia específico|`com.apple.ui-services`|Qualquer|
|Provedor de documentos|Permite que o aplicativo use um repositório de documentos remoto|`com.apple.fileprovider-ui`|Aplicativos usando um [UIDocumentPickerViewController](xref:UIKit.UIDocumentPickerViewController)|
|Teclado|Teclados alternativos|`com.apple.keyboard-service`|Qualquer|
|Edição de fotos|Manipulação e edição de fotos|`com.apple.photo-editing`|Editor de photos. app|
|Compartilhar|Compartilha dados com redes sociais, serviços de mensagens, etc.|`com.apple.share-services`|Qualquer|
|Hoje|"Widgets" que aparecem na tela atual ou no centro de notificações|`com.apple.widget-extensions`|Hoje e centro de notificações|

[Pontos de extensão adicionais](~/ios/platform/introduction-to-ios10/index.md#app-extensions) foram adicionados no Ios 10.

## <a name="limitations"></a>Limitações

As extensões têm várias limitações, algumas das quais são universais para todos os tipos (por exemplo, nenhum tipo de extensão pode acessar câmeras ou microfones), enquanto outros tipos de extensão podem ter limitações específicas sobre seu uso (por exemplo, teclados personalizados Não pode ser usado para proteger campos de entrada de dados, como para senhas). 

As limitações universais são:

- O [Kit de integridade](~/ios/platform/healthkit.md) e as estruturas de [interface do usuário do kit de eventos](~/ios/platform/eventkit.md) não estão disponíveis
- As extensões não podem usar [modos de segundo plano estendidos](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/registering-applications-to-run-in-background.md)
- As extensões não podem acessar câmeras ou microfones do dispositivo (embora possam acessar os arquivos de mídia existentes)
- As extensões não podem receber dados de descarte de ar (embora possam transmitir dados por meio do ar)
- [UIActionSheet](xref:UIKit.UIActionSheet) e [UIAlertView](xref:UIKit.UIAlertView) não estão disponíveis; as extensões devem usar [UIAlertController](xref:UIKit.UIAlertController)
- Vários membros de [UIApplication](xref:UIKit.UIApplication) não estão disponíveis: [UIApplication. SharedApplication](xref:UIKit.UIApplication.SharedApplication), [UIApplication. OpenURL](xref:UIKit.UIApplication.OpenUrl(Foundation.NSUrl)), [UIApplication. BeginIgnoringInteractionEvents](xref:UIKit.UIApplication.BeginIgnoringInteractionEvents) e [UIApplication. EndIgnoringInteractionEvents](xref:UIKit.UIApplication.EndIgnoringInteractionEvents)
- o iOS impõe um limite de uso de memória de 16 MB nas extensões de hoje.
- Por padrão, as extensões de teclado não têm acesso à rede. Isso afeta a depuração no dispositivo (a restrição não é imposta no simulador), pois o Xamarin. iOS requer acesso à rede para que a depuração funcione. É possível solicitar o acesso à rede definindo o `Requests Open Access` valor no info. plist do projeto como. `Yes` Consulte o guia de [teclado personalizado](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/CustomKeyboard.html) da Apple para obter mais informações sobre as limitações de extensão do teclado.

Para limitações individuais, consulte o guia de [programação de extensões de aplicativo](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/)da Apple.

## <a name="distributing-installing-and-running-extensions"></a>Distribuindo, instalando e executando extensões

As extensões são distribuídas de dentro de um aplicativo de contêiner, que, por sua vez, é enviado e distribuído por meio da loja de aplicativos. As extensões distribuídas com o aplicativo são instaladas nesse ponto, mas o usuário deve habilitar cada extensão explicitamente. Os diferentes tipos de extensões são habilitados de maneiras diferentes; vários exigem que o usuário navegue até o aplicativo **configurações** e habilite-os a partir daí. Enquanto outros estão habilitados no ponto de uso, como habilitar uma extensão de compartilhamento ao enviar uma foto. 

O aplicativo no qual a extensão é usada (onde o usuário encontra o ponto de extensão) é conhecido como o **aplicativo host**, pois é o aplicativo que hospeda a extensão quando ela é executada. O aplicativo que instala a extensão é o **aplicativo de contêiner**, porque ele é o aplicativo que continha a extensão quando ela foi instalada.  

Normalmente, o aplicativo de contêiner descreve a extensão e orienta o usuário pelo processo de habilitá-lo.

## <a name="extension-lifecycle"></a>Ciclo de vida da extensão

Uma extensão pode ser tão simples quanto uma única [UIViewController](xref:UIKit.UIViewController) ou extensões mais complexas que apresentam várias telas de interface do usuário. Quando o usuário encontra um _ponto de extensão_ (como ao compartilhar uma imagem), ele terá a oportunidade de escolher entre as extensões registradas para esse ponto de extensão. 

Se eles escolherem uma das extensões do seu aplicativo, `UIViewController` ele será instanciado e iniciará o ciclo de vida do controlador de exibição normal. No entanto, ao contrário de um aplicativo normal, que são suspensos, mas que não são geralmente encerrados quando o usuário termina de interagir com eles, as extensões são carregadas, executadas e encerradas repetidamente.

As extensões podem se comunicar com seus aplicativos host por meio de um objeto [NSExtensionContext](xref:Foundation.NSExtensionContext) . Algumas extensões têm operações que recebem retornos de chamada assíncronos com os resultados. Esses retornos de chamada serão executados em threads em segundo plano e a extensão deverá levar isso em consideração; por exemplo, usando [NSObject. InvokeOnMainThread](xref:Foundation.NSObject.InvokeOnMainThread*) se desejar atualizar a interface do usuário. Consulte a seção comunicando- [se com o aplicativo host](#communicating-with-the-host-app) abaixo para obter mais detalhes.

Por padrão, as extensões e seus aplicativos de contêiner não podem se comunicar, apesar de serem instalados juntos. Em alguns casos, o aplicativo de contêiner é essencialmente um contêiner de "envio" vazio cuja finalidade é servida quando a extensão é instalada. No entanto, se as circunstâncias ditarem, o aplicativo de contêiner e a extensão poderão compartilhar recursos de uma área comum. Além disso, uma **extensão de hoje** pode solicitar que seu aplicativo de contêiner abra uma URL. Esse comportamento é mostrado no [widget contagem](https://github.com/xamarin/ios-samples/tree/master/intro-to-extensions)regressiva de eventos.

## <a name="creating-an-extension"></a>Criando uma extensão

As extensões (e seus aplicativos de contêiner) devem ser binários de 64 bits e criados usando as [APIs](~/cross-platform/macios/unified/index.md)unificadas do Xamarin. Ios. Ao desenvolver uma extensão, suas soluções conterão pelo menos dois projetos: o aplicativo de contêiner e um projeto para cada extensão que o contêiner fornece.

### <a name="container-app-project-requirements"></a>Requisitos de projeto de aplicativo de contêiner

O aplicativo de contêiner usado para instalar a extensão tem os seguintes requisitos:

- Ele deve manter uma referência ao projeto de extensão.   
- Ele deve ser um aplicativo completo (deve ser capaz de iniciar e executar com êxito) mesmo que ele não faça nada além de fornecer uma maneira de instalar uma extensão. 
- Ele deve ter um identificador de pacote que seja a base para o identificador de pacote do projeto de extensão (consulte a seção abaixo para obter mais detalhes).

### <a name="extension-project-requirements"></a>Requisitos do projeto de extensão

Além disso, o projeto da extensão tem os seguintes requisitos:

- Ele deve ter um identificador de pacote que começa com o identificador de pacote do seu aplicativo de contêiner. Por exemplo, se o aplicativo de contêiner tiver um identificador de pacote `com.myCompany.ContainerApp`de, o identificador da extensão poderá `com.myCompany.ContainerApp.MyExtension`ser: 

    ![](extensions-images/bundleidentifiers.png) 
- Ele deve definir a chave `NSExtensionPointIdentifier`, com um valor apropriado (por `com.apple.widget-extension` exemplo, para um widget da central de notificações **atual** ) `Info.plist` , em seu arquivo.
- Ele também *deve definir a* `NSExtensionMainStoryboard` chave ou a `NSExtensionPrincipalClass` chave em seu `Info.plist` arquivo com um valor apropriado:
    - Use a `NSExtensionMainStoryboard` chave para especificar o nome do Storyboard que apresenta a interface do usuário principal para a extensão ( `.storyboard`menos). Por exemplo, `Main` para o `Main.storyboard` arquivo.
    - Use a `NSExtensionPrincipalClass` chave para especificar a classe que será inicializada quando a extensão for iniciada. O valor deve corresponder ao valor de **registro** de `UIViewController`seu: 

    ![](extensions-images/registerandprincipalclass.png)

Tipos específicos de extensões podem ter requisitos adicionais. Por exemplo, uma classe principal da extensão do **centro de notificação** ou de **hoje** deve implementar [INCWidgetProviding](xref:NotificationCenter.INCWidgetProviding).

> [!IMPORTANT]
> Se você iniciar seu projeto usando um dos modelos de extensões fornecidos pelo Visual Studio para Mac, a maioria (se não todos) esses requisitos serão fornecidos e atendidos automaticamente pelo modelo.

## <a name="walkthrough"></a>Passo a passo 

Nas instruções a seguir, você criará um widget de exemplo **hoje** que calcula o dia e o número de dias restantes no ano:

[![](extensions-images/carpediemscreenshot-sm.png "Um widget de exemplo hoje que calcula o dia e o número de dias restantes no ano")](extensions-images/carpediemscreenshot.png#lightbox)

### <a name="creating-the-solution"></a>Criando a solução

Para criar a solução necessária, faça o seguinte:

1. Primeiro, crie um novo projeto de aplicativo iOS, de **exibição única** e clique no botão **Avançar** : 

    [![](extensions-images/today01.png "Primeiro, crie um novo projeto de aplicativo iOS, de exibição única e clique no botão Avançar")](extensions-images/today01.png#lightbox)
2. Chame o projeto `TodayContainer` e clique no botão **Avançar** : 

    [![](extensions-images/today02.png "Chame o projeto TodayContainer e clique no botão Avançar")](extensions-images/today02.png#lightbox)
3. Verifique o **nome do projeto** e **SolutionName** e clique no botão **criar** para criar a solução: 

    [![](extensions-images/today03.png "Verifique o nome do projeto e SolutionName e clique no botão criar para criar a solução")](extensions-images/today03.png#lightbox)
4. Em seguida, na **Gerenciador de soluções**, clique com o botão direito do mouse na solução e adicione um novo projeto de **extensão do IOS** do modelo de **extensão atual** : 

    [![](extensions-images/today04.png "Em seguida, na Gerenciador de Soluções, clique com o botão direito do mouse na solução e adicione um novo projeto de extensão do iOS do modelo de extensão atual")](extensions-images/today04.png#lightbox)
5. Chame o projeto `DaysRemaining` e clique no botão **Avançar** : 

    [![](extensions-images/today05.png "Chame o projeto DaysRemaining e clique no botão Avançar")](extensions-images/today05.png#lightbox)
6. Examine o projeto e clique no botão **criar** para criá-lo: 

    [![](extensions-images/today06.png "Examine o projeto e clique no botão criar para criá-lo")](extensions-images/today06.png#lightbox)

A solução resultante agora deve ter dois projetos, como mostrado aqui:

[![](extensions-images/today07.png "A solução resultante agora deve ter dois projetos, como mostrado aqui")](extensions-images/today07.png#lightbox)

### <a name="creating-the-extension-user-interface"></a>Criando a interface do usuário da extensão

Em seguida, você precisará criar a interface para o widget **atual** . Isso pode ser feito usando um storyboard ou criando a interface do usuário no código. Os dois métodos serão abordados abaixo em detalhes.

#### <a name="using-storyboards"></a>Usando storyboards

Para criar a interface do usuário com um storyboard, faça o seguinte:

1. No **Gerenciador de soluções**, clique duas vezes no arquivo do `Main.storyboard` projeto de extensão para abri-lo para edição: 

    [![](extensions-images/today08.png "Clique duas vezes no arquivo de extensão projetos Main. Storyboard para abri-lo para edição")](extensions-images/today08.png#lightbox)
2. Selecione o rótulo que foi adicionado automaticamente à interface do usuário por modelo e dê a ele o **nome** `TodayMessage` na guia **widget** do **Gerenciador de propriedades**: 

    [![](extensions-images/today09.png "Selecione o rótulo que foi adicionado automaticamente à interface do usuário por modelo e dê a ele o nome TodayMessage na guia widget do Gerenciador de propriedades")](extensions-images/today09.png#lightbox)
3. Salve as alterações no storyboard.

#### <a name="using-code"></a>Usando código

Para criar a interface do usuário no código, faça o seguinte: 

1. No **Gerenciador de soluções**, selecione o projeto **DaysRemaining** , adicione uma nova classe e chame- `CodeBasedViewController`o: 

    [![](extensions-images/code01.png "Aelect o projeto DaysRemaining, adicione uma nova classe e chame-a de CodeBasedViewController")](extensions-images/code01.png#lightbox)
2. Novamente, no **Gerenciador de soluções**, clique duas vezes no arquivo da `Info.plist` extensão para abri-lo para edição: 

    [![](extensions-images/code02.png "Clique duas vezes em extensões do arquivo info. plist para abri-lo para edição")](extensions-images/code02.png#lightbox)
3. Selecione o **modo de exibição de origem** (na parte inferior da tela) e `NSExtension` Abra o nó: 

    [![](extensions-images/code03.png "Selecione o modo de exibição de origem na parte inferior da tela e abra o nó NSExtension")](extensions-images/code03.png#lightbox)
4. Remova a `NSExtensionMainStoryboard` chave e adicione uma `NSExtensionPrincipalClass` com o valor `CodeBasedViewController`: 

    [![](extensions-images/code04.png "Remova a chave NSExtensionMainStoryboard e adicione um NSExtensionPrincipalClass com o valor CodeBasedViewController")](extensions-images/code04.png#lightbox)
5. Salve as alterações.

Em seguida, edite o `CodeBasedViewController.cs` arquivo e faça com que ele se pareça com o seguinte:

```csharp
using System;
using Foundation;
using UIKit;
using NotificationCenter;
using CoreGraphics;

namespace DaysRemaining
{
    [Register("CodeBasedViewController")]
    public class CodeBasedViewController : UIViewController, INCWidgetProviding
    {
        public CodeBasedViewController ()
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Add label to view
            var TodayMessage = new UILabel (new CGRect (0, 0, View.Frame.Width, View.Frame.Height)) {
                TextAlignment = UITextAlignment.Center
            };

            View.AddSubview (TodayMessage);
            
            // Insert code to power extension here...

        }
    }
}
```

Observe que o `[Register("CodeBasedViewController")]` corresponde ao valor que você especificou para `NSExtensionPrincipalClass` o acima.

### <a name="coding-the-extension"></a>Codificando a extensão

Com a interface do usuário criada, abra o `TodayViewController.cs` ou o `CodeBasedViewController.cs` arquivo (com base no método usado para criar a interface do usuário acima), altere o método **ViewDidLoad** e faça com que ele se pareça com o seguinte:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Calculate the values
    var dayOfYear = DateTime.Now.DayOfYear;
    var leapYearExtra = DateTime.IsLeapYear (DateTime.Now.Year) ? 1 : 0;
    var daysRemaining = 365 + leapYearExtra - dayOfYear;

    // Display the message
    if (daysRemaining == 1) {
        TodayMessage.Text = String.Format ("Today is day {0}. There is one day remaining in the year.", dayOfYear);
    } else {
        TodayMessage.Text = String.Format ("Today is day {0}. There are {1} days remaining in the year.", dayOfYear, daysRemaining);
    }
}
```

Se estiver usando o método de interface do usuário baseado em `// Insert code to power extension here...` código, substitua o comentário pelo novo código acima. Depois de chamar a implementação base (e inserir um rótulo para a versão baseada em código), esse código faz um cálculo simples para obter o dia do ano e quantos dias restam. Em seguida, ele exibe a mensagem no rótulo`TodayMessage`() que você criou no design da interface do usuário.

Observe como esse processo é semelhante ao processo normal de escrever um aplicativo. Uma extensão `UIViewController` tem o mesmo ciclo de vida de um controlador de exibição em um aplicativo, exceto que as extensões não têm modos de segundo plano e não são suspensas quando o usuário termina de usá-las. Em vez disso, as extensões são inicializadas e desalocadas repetidamente conforme necessário.

### <a name="creating-the-container-app-user-interface"></a>Criando a interface do usuário do aplicativo de contêiner

Para este passo a passos, o aplicativo de contêiner é simplesmente usado como um método para enviar e instalar a extensão e não fornece nenhuma funcionalidade própria. Edite o arquivo `Main.storyboard` do TodayContainer e adicione algum texto definindo a função da extensão e como instalá-lo:

[![](extensions-images/today10.png "Edite o arquivo TodayContainers Main. Storyboard e adicione algum texto definindo a função Extensions e como instalá-lo")](extensions-images/today10.png#lightbox)

Salve as alterações no storyboard.

### <a name="testing-the-extension"></a>Testando a extensão

Para testar sua extensão no simulador de iOS, execute o aplicativo **TodayContainer** . O modo de exibição principal do contêiner será exibido:

[![](extensions-images/run01.png "O modo de exibição principal dos contêineres será exibido")](extensions-images/run01.png#lightbox)

Em seguida, pressione o botão **início** no simulador, passe o dedo para baixo na parte superior da tela para abrir a **central de notificações**, selecione a guia **hoje** e clique no botão **Editar** :

[![](extensions-images/run02.png "Pressione o botão Início no simulador, passe o dedo para baixo na parte superior da tela para abrir a central de notificações, selecione a guia hoje e clique no botão Editar")](extensions-images/run02.png#lightbox)

Adicione a extensão **DaysRemaining** à exibição **atual** e clique no botão **concluído** :

[![](extensions-images/run03.png "Adicione a extensão DaysRemaining à exibição atual e clique no botão concluído")](extensions-images/run03.png#lightbox)

O novo widget será adicionado à exibição **atual** e os resultados serão exibidos:

[![](extensions-images/run04.png "O novo widget será adicionado à exibição atual e os resultados serão exibidos")](extensions-images/run04.png#lightbox)

## <a name="communicating-with-the-host-app"></a>Comunicando-se com o aplicativo host

A extensão de exemplo hoje que você criou acima não se comunica com seu aplicativo host (a tela **hoje** ). Se tiver feito isso, ele usaria a propriedade [ExtensionContext](xref:Foundation.NSExtensionContext) das `TodayViewController` classes ou `CodeBasedViewController` . 

Para extensões que receberão dados de seus aplicativos host, os dados estarão na forma de uma matriz de objetos [NSExtensionItem](xref:Foundation.NSExtensionItem) armazenados na propriedade [InputItems](xref:Foundation.NSExtensionContext.InputItems) do [ExtensionContext](xref:Foundation.NSExtensionContext) da extensão `UIViewController`.

Outra extensão, como as extensões de edição de fotos, pode distinguir entre o usuário concluindo ou cancelando o uso. Isso será sinalizado de volta para o aplicativo host por meio dos métodos [CompleteRequest](xref:Foundation.NSExtensionContext.CompleteRequest*) e [CancelRequest](xref:Foundation.NSExtensionContext.CancelRequest*) da propriedade [ExtensionContext](xref:Foundation.NSExtensionContext) .

Para obter mais informações, consulte o [Guia de programação de extensões de aplicativo](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW1)da Apple.

## <a name="communicating-with-the-parent-app"></a>Comunicando-se com o aplicativo pai

Um grupo de aplicativos permite que diferentes aplicativos (ou um aplicativo e suas extensões) acessem um local de armazenamento de arquivo compartilhado. Grupos de aplicativo podem ser usados para dados como:

- [Configurações do Apple Watch](~/ios/watchos/app-fundamentals/settings.md).
- [NSUserDefaults compartilhados](~/ios/app-fundamentals/user-defaults.md).
- [Arquivos compartilhados](~/ios/watchos/app-fundamentals/parent-app.md#files).

Para obter mais informações, consulte a seção [grupos de aplicativos](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) da documentação **trabalhando com recursos** .

## <a name="mobilecoreservices"></a>MobileCoreServices

Ao trabalhar com extensões, use um identificador de tipo uniforme (UTI) para criar e manipular dados trocados entre o aplicativo, outros aplicativos e/ou serviços.

A `MobileCoreServices.UTType` classe estática define as seguintes propriedades auxiliares relacionadas às definições da `kUTType...` Apple:

- `kUTTypeAlembic` - `Alembic`
- `kUTTypeAliasFile` - `AliasFile`
- `kUTTypeAliasRecord` - `AliasRecord`
- `kUTTypeAppleICNS` - `AppleICNS`
- `kUTTypeAppleProtectedMPEG4Audio` - `AppleProtectedMPEG4Audio`
- `kUTTypeAppleProtectedMPEG4Video` - `AppleProtectedMPEG4Video`
- `kUTTypeAppleScript` - `AppleScript`
- `kUTTypeApplication` - `Application`
- `kUTTypeApplicationBundle` - `ApplicationBundle`
- `kUTTypeApplicationFile` - `ApplicationFile`
- `kUTTypeArchive` - `Archive`
- `kUTTypeAssemblyLanguageSource` - `AssemblyLanguageSource`
- `kUTTypeAudio` - `Audio`
- `kUTTypeAudioInterchangeFileFormat` - `AudioInterchangeFileFormat`
- `kUTTypeAudiovisualContent` - `AudiovisualContent`
- `kUTTypeAVIMovie` - `AVIMovie`
- `kUTTypeBinaryPropertyList` - `BinaryPropertyList`
- `kUTTypeBMP` - `BMP`
- `kUTTypeBookmark` - `Bookmark`
- `kUTTypeBundle` - `Bundle`
- `kUTTypeBzip2Archive` - `Bzip2Archive`
- `kUTTypeCalendarEvent` - `CalendarEvent`
- `kUTTypeCHeader` - `CHeader`
- `kUTTypeCommaSeparatedText` - `CommaSeparatedText`
- `kUTTypeCompositeContent` - `CompositeContent`
- `kUTTypeConformsToKey` - `ConformsToKey`
- `kUTTypeContact` - `Contact`
- `kUTTypeContent` - `Content`
- `kUTTypeCPlusPlusHeader` - `CPlusPlusHeader`
- `kUTTypeCPlusPlusSource` - `CPlusPlusSource`
- `kUTTypeCSource` - `CSource`
- `kUTTypeData` - `Database`
- `kUTTypeDelimitedText` - `DelimitedText`
- `kUTTypeDescriptionKey` - `DescriptionKey`
- `kUTTypeDirectory` - `Directory`
- `kUTTypeDiskImage` - `DiskImage`
- `kUTTypeElectronicPublication` - `ElectronicPublication`
- `kUTTypeEmailMessage` - `EmailMessage`
- `kUTTypeExecutable` - `Executable`
- `kUTExportedTypeDeclarationsKey` - `ExportedTypeDeclarationsKey`
- `kUTTypeFileURL` - `FileURL`
- `kUTTypeFlatRTFD` - `FlatRTFD`
- `kUTTypeFolder` - `Folder`
- `kUTTypeFont` - `Font`
- `kUTTypeFramework` - `Framework`
- `kUTTypeGIF` - `GIF`
- `kUTTypeGNUZipArchive` - `GNUZipArchive` 
- `kUTTypeHTML` - `HTML`
- `kUTTypeICO` - `ICO`
- `kUTTypeIconFileKey` - `IconFileKey`
- `kUTTypeIdentifierKey` - `IdentifierKey`
- `kUTTypeImage` - `Image`
- `kUTImportedTypeDeclarationsKey` - `ImportedTypeDeclarationsKey`
- `kUTTypeInkText` - `InkText`
- `kUTTypeInternetLocation` - `InternetLocation`
- `kUTTypeItem` - `Item`
- `kUTTypeJavaArchive` - `JavaArchive`
- `kUTTypeJavaClass` - `JavaClass`
- `kUTTypeJavaScript` - `JavaScript`
- `kUTTypeJavaSource` - `JavaSource`
- `kUTTypeJPEG` - `JPEG`
- `kUTTypeJPEG2000` - `JPEG2000`
- `kUTTypeJSON` - `JSON`
- `kUTType3dObject` - `k3dObject`
- `kUTTypeLivePhoto` - `LivePhoto`
- `kUTTypeLog` - `Log` 
- `kUTTypeM3UPlaylist` - `M3UPlaylist`
- `kUTTypeMessage` - `Message`
- `kUTTypeMIDIAudio` - `MIDIAudio`
- `kUTTypeMountPoint` - `MountPoint`
- `kUTTypeMovie` - `Movie`
- `kUTTypeMP3` - `MP3`
- `kUTTypeMPEG` - `MPEG`
- `kUTTypeMPEG2TransportStream` - `MPEG2TransportStream`
- `kUTTypeMPEG2Video` - `MPEG2Video`
- `kUTTypeMPEG4` - `MPEG4`
- `kUTTypeMPEG4Audio` - `MPEG4Audio`
- `kUTTypeObjectiveCPlusPlusSource` - `ObjectiveCPlusPlusSource`
- `kUTTypeObjectiveCSource` - `ObjectiveCSource`
- `kUTTypeOSAScript` - `OSAScript`
- `kUTTypeOSAScriptBundle` - `OSAScriptBundle`
- `kUTTypePackage` - `Package`
- `kUTTypePDF` - `PDF`
- `kUTTypePerlScript` - `PerlScript`
- `kUTTypePHPScript` - `PHPScript`
- `kUTTypePICT` - `PICT`
- `kUTTypePKCS12` - `PKCS12`
- `kUTTypePlainText` - `PlainText`
- `kUTTypePlaylist` - `Playlist`
- `kUTTypePluginBundle` - `PluginBundle`
- `kUTTypePNG` - `PNG`
- `kUTTypePolygon` - `Polygon`
- `kUTTypePresentation` - `Presentation`
- `kUTTypePropertyList` - `PropertyList`
- `kUTTypePythonScript` - `PythonScript`
- `kUTTypeQuickLookGenerator` - `QuickLookGenerator`
- `kUTTypeQuickTimeImage` - `QuickTimeImage`
- `kUTTypeQuickTimeMovie` - `QuickTimeMovie` 
- `kUTTypeRawImage` - `RawImage`
- `kUTTypeReferenceURLKey` - `ReferenceURLKey`
- `kUTTypeResolvable` - `Resolvable`
- `kUTTypeRTF` - `RTF`
- `kUTTypeRTFD` - `RTFD`
- `kUTTypeRubyScript` - `RubyScript`
- `kUTTypeScalableVectorGraphics` - `ScalableVectorGraphics`
- `kUTTypeScript` - `Script`
- `kUTTypeShellScript` - `ShellScript`
- `kUTTypeSourceCode` - `SourceCode`
- `kUTTypeSpotlightImporter` - `SpotlightImporter`
- `kUTTypeSpreadsheet` - `Spreadsheet`
- `kUTTypeStereolithography` - `Stereolithography`
- `kUTTypeSwiftSource` - `SwiftSource`
- `kUTTypeSymLink` - `SymLink`
- `kUTTypeSystemPreferencesPane` - `SystemPreferencesPane`
- `kUTTypeTabSeparatedText` - `TabSeparatedText`
- `kUTTagClassFilenameExtension` - `TagClassFilenameExtension`
- `kUTTagClassMIMEType` - `TagClassMIMEType`
- `kUTTypeTagSpecificationKey` - `TagSpecificationKey`
- `kUTTypeText` - `Text`
- `kUTType3DContent` - `ThreeDContent`
- `kUTTypeTIFF` - `TIFF`
- `kUTTypeToDoItem` - `ToDoItem`
- `kUTTypeTXNTextAndMultimediaData` - `TXNTextAndMultimediaData`
- `kUTTypeUniversalSceneDescription` - `UniversalSceneDescription`
- `kUTTypeUnixExecutable` - `UnixExecutable`
- `kUTTypeURL` - `URL` 
- `kUTTypeURLBookmarkData` - `URLBookmarkData`
- `kUTTypeUTF16ExternalPlainText` - `UTF16ExternalPlainText`
- `kUTTypeUTF16PlainText` - `UTF16PlainText`
- `kUTTypeUTF8PlainText` - `UTF8PlainText`
- `kUTTypeUTF8TabSeparatedText` - `UTF8TabSeparatedText`
- `kUTTypeVCard` - `VCard`
- `kUTTypeVersionKey` - `VersionKey` 
- `kUTTypeVideo` - `Video` 
- `kUTTypeVolume` - `Volume` 
- `kUTTypeWaveformAudio` - `WaveformAudio`
- `kUTTypeWebArchive` - `WebArchive`
- `kUTTypeWindowsExecutable` - `WindowsExecutable`
- `kUTTypeX509Certificate` - `X509Certificate`
- `kUTTypeXML` - `XML`
- `kUTTypeXMLPropertyList` - `XMLPropertyList`
- `kUTTypeXPCService` - `XPCService`
- `kUTTypeZipArchive` - `ZipArchive`

Consulte o exemplo a seguir:

```csharp
using MobileCoreServices;
...

NSItemProvider itemProvider = new NSItemProvider ();
itemProvider.LoadItem(UTType.PropertyList ,null, (item, err) => {
    if (err == null) {
        NSDictionary results = (NSDictionary )item;
        NSString baseURI =
results.ObjectForKey("NSExtensionJavaScriptPreprocessingResultsKey");
    }
});
```

Para obter mais informações, consulte a seção [grupos de aplicativos](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) da documentação **trabalhando com recursos** .

## <a name="precautions-and-considerations"></a>Precauções e considerações

As extensões têm significativamente menos memória disponível para eles do que os aplicativos. Eles devem ser executados rapidamente e com intrusão mínima para o usuário e o aplicativo no qual estão hospedados. No entanto, uma extensão também deve fornecer uma função distinta e útil para o aplicativo de consumo com uma interface do usuário com marca que permite ao usuário identificar o aplicativo de contêiner ou desenvolvedor da extensão ao qual eles pertencem.

Devido a esses requisitos rigorosos, você só deve implantar extensões que foram totalmente testadas e otimizadas para o consumo de desempenho e de memória. 

## <a name="summary"></a>Resumo

Este documento abordou as extensões, o que são, o tipo de pontos de extensão e as limitações conhecidas impostas em uma extensão pelo iOS. Ele abordou a criação, a distribuição, a instalação e a execução de extensões e o ciclo de vida da extensão. Ele forneceu um passo a passos para criar um widget de **hoje** simples, mostrando duas maneiras de criar a interface do usuário do widget usando storyboards ou código. Ele mostrou como testar uma extensão no simulador de iOS. Por fim, discutiu brevemente a comunicação com o aplicativo host e algumas precauções e considerações que devem ser tomadas durante o desenvolvimento de uma extensão. 

## <a name="related-links"></a>Links relacionados

- [ContainerApp (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/intro-to-extensions)
- [Criando extensões no Xamarin. iOS (vídeo)](https://university.xamarin.com/lightninglectures/creating-extensions-in-ios)
