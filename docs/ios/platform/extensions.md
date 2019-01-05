---
title: Extensões no xamarin. IOS do iOS
description: Este documento descreve as extensões, que são apresentados pelo iOS no contexto padrão, como no Centro de notificações de widgets. Ele aborda como criar uma extensão e se comunicar com ele no aplicativo pai.
ms.prod: xamarin
ms.assetid: 3DEB3D43-3E4A-4099-8331-93C1E7A77095
ms.technology: xamarin-ios
ms.custom: xamu-video
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: f892774b4899fcbac46e8cc7bc2b0dd0336cc036
ms.sourcegitcommit: f5fce8308b2e7c39c5b0c904e5f38a4ce2b55c87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54012276"
---
# <a name="ios-extensions-in-xamarinios"></a>extensões do iOS no xamarin. IOS

> [!VIDEO https://youtube.com/embed/Sd0-ch9Udmk]

**Criando extensões no iOS, por [Xamarin University](https://university.xamarin.com/)**

Extensões, conforme apresentada no iOS 8, são especializadas `UIViewControllers` que são apresentados pelo iOS dentro do padrão de contextos, como dentro de **Central de notificações**, como especializada de tipos de teclado personalizado solicitados pelo usuário para executar entrada ou em outros contextos, como edição de uma foto em que a extensão pode fornecer filtros de efeito especial.

Todas as extensões são instaladas junto com um aplicativo de contêiner (com ambos os elementos escritos usando as APIs de Unificação de 64 bits) e são ativadas de um ponto de extensão específico em um aplicativo Host. E, desde que eles serão usados como suplementos para funções do sistema existentes, eles devem ser de alto desempenho, enxuto e robusto. 

## <a name="extension-points"></a>Pontos de extensão

|Tipo|Descrição|Ponto de extensão|Aplicativo de host|
|--- |--- |--- |--- |
|Ação|Editor especializado ou o visualizador para um determinado tipo de mídia|`com.apple.ui-services`|Qualquer|
|Provedor de documento|Permite que o aplicativo usar um armazenamento de documento remoto|`com.apple.fileprovider-ui`|Aplicativos que usam um [UIDocumentPickerViewController](https://developer.xamarin.com/api/type/UIKit.UIDocumentPickerViewController/)|
|Teclado|Teclados alternativos|`com.apple.keyboard-service`|Qualquer|
|Edição de fotos|Edição e manipulação de fotos|`com.apple.photo-editing`|Editor de Photos.App|
|Compartilhar|Compartilha dados com redes sociais, sistema de mensagens de serviços, etc.|`com.apple.share-services`|Qualquer|
|Hoje|"Widgets" que aparecem na tela hoje ou centro de notificações|`com.apple.widget-extensions`|Hoje e no Centro de notificações|

[Pontos de extensão adicionais](~/ios/platform/introduction-to-ios10/index.md#app-extensions) foram adicionados no iOS 10.

## <a name="limitations"></a>Limitações

Extensões têm várias limitações, algumas das quais são universais para todos os tipos (por instância, nenhum tipo de extensão pode acessar as câmeras ou microfones) enquanto outros tipos de extensão podem ter limitações específicas no uso (por exemplo, personalizados teclados não pode ser usado para campos de entrada de dados seguros, como senhas). 

As limitações de universais são:

- O [Kit de integridade](~/ios/platform/healthkit.md) e [interface do usuário do Kit de evento](~/ios/platform/eventkit.md) estruturas não estão disponíveis
- As extensões não é possível usar [estendido modos em segundo plano](http://developer.xamarin.com/guides/cross-platform/application_fundamentals/backgrounding/part_3_ios_backgrounding_techniques/registering_applications_to_run_in_background/)
- As extensões não podem acessar o dispositivo câmeras ou microfones (embora eles podem acessar os arquivos de mídia existente)
- As extensões não podem receber dados de descarte de ar (embora eles possam transmitir dados por meio de descarte de ar)
- [UIActionSheet](https://developer.xamarin.com/api/type/UIKit.UIActionSheet/) e [UIAlertView](https://developer.xamarin.com/api/type/UIKit.UIAlertView/) não estão disponíveis; as extensões devem usar [UIAlertController](https://developer.xamarin.com/api/type/UIKit.UIAlertController/)
- Vários membros da [UIApplication](https://developer.xamarin.com/api/type/UIKit.UIApplication/) não estão disponíveis: [UIApplication.SharedApplication](https://developer.xamarin.com/api/property/UIKit.UIApplication.SharedApplication/), `UIApplication.OpenURL`, `UIApplication.BeginIgnoringInteractionEvents` e `UIApplication.EndIgnoringInteractionEvents`
- o iOS impõe um limite de uso de memória de 16 MB de extensões de hoje.
- Por padrão, as extensões do teclado não tem acesso à rede. Isso afeta a depuração no dispositivo (não a restrição é imposta no simulador), uma vez que o xamarin. IOS requer acesso à rede para a depuração funcione. É possível solicitar o acesso de rede, definindo o `Requests Open Access` valor no Info. plist do projeto para `Yes`. Consulte da Apple [guia de teclado personalizados](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/CustomKeyboard.html) para obter mais informações sobre as limitações de extensão de teclado.

Para limitações individuais, consulte da Apple [guia de programação de extensão de aplicativo](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/).

## <a name="distributing-installing-and-running-extensions"></a>A distribuição, instalação e execução de extensões

As extensões são distribuídas de dentro de um aplicativo de contêiner, que, por sua vez é enviado e distribuídos por meio de Store de aplicativo. As extensões distribuídas com o aplicativo estão instaladas nesse ponto, mas o usuário deve habilitar explicitamente cada extensão. Os diferentes tipos de extensões são habilitados de diferentes maneiras; vários exigem que o usuário navegue para o **configurações** aplicativo e habilitá-los a partir daí. Enquanto outras são ativadas no ponto de uso, como a habilitação de uma extensão de compartilhamento ao enviar uma foto. 

O aplicativo no qual a extensão será usada (em que o usuário encontra o ponto de extensão) é conhecido como o **aplicativo de Host**, pois ele é o aplicativo que hospeda a extensão quando ela é executada. O aplicativo que instala a extensão é a **aplicativo de contêiner**, pois este é o aplicativo que continha a extensão quando ele foi instalado.  

Normalmente, o aplicativo de contêiner descreve a extensão e orienta o usuário durante o processo de habilitá-la.

## <a name="extension-lifecycle"></a>Ciclo de vida de extensão

Uma extensão pode ser tão simple quanto uma única [UIViewController](https://developer.xamarin.com/api/type/UIKit.UIViewController/) ou extensões mais complexas que apresentam várias telas da interface do usuário. Quando o usuário encontra um _pontos de extensão_ (por exemplo, quando uma imagem de compartilhamento), eles terão a oportunidade de escolher entre as extensões registradas para esse ponto de extensão. 

Se eles escolherem um aplicativo de extensões, seu `UIViewController` será instanciado e iniciar o ciclo de vida normal do controlador de exibição. No entanto, ao contrário de um aplicativo normal, que são suspensos, mas geralmente não encerrada quando o usuário termina de interagir com eles, extensões são carregadas, executadas e, em seguida, encerradas repetidamente.

As extensões podem se comunicar com seus aplicativos de Host por meio de um [NSExtensionContext](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/) objeto. Algumas extensões têm operações que recebem retornos de chamada assíncronos com os resultados. Esses retornos de chamada serão executados em threads em segundo plano e a extensão deve levar isso em consideração; Por exemplo, usando [NSObject.InvokeOnMainThread](https://developer.xamarin.com/api/member/Foundation.NSObject.InvokeOnMainThread/) se deseja atualizar a interface do usuário. Consulte a [comunicando-se com o aplicativo Host](#Communicating-with-the-Host-App) seção abaixo para obter mais detalhes.

Por padrão, seus aplicativos de contêiner e extensões podem não se comunicar, apesar de que estão sendo instalados juntos. Em alguns casos, o aplicativo de contêiner é essencialmente um contêiner "envio" vazio cujo objetivo é servido depois que a extensão está instalada. No entanto, se as circunstâncias ditarem, o aplicativo de contêiner e a extensão podem compartilhar recursos de uma área comum. Além disso, uma **hoje mesmo a extensão** pode solicitar o seu aplicativo de contêiner para abrir uma URL. Esse comportamento é mostrado na [evoluir Widget de contagem regressiva](http://github.com/xamarin/monotouch-samples/tree/master/ExtensionsDemo).

## <a name="creating-an-extension"></a>Criação de uma extensão

Extensões (e seus aplicativos de contêiner) deve ser a binários de 64 bits e criados usando o xamarin. IOS [APIs unificadas](http://developer.xamarin.com/guides/cross-platform/macios/unified). Ao desenvolver uma extensão, suas soluções conterá pelo menos dois projetos: o aplicativo de contêiner e um projeto para cada contêiner de extensão fornece. 

### <a name="container-app-project-requirements"></a>Requisitos de projeto de aplicativo de contêiner

O aplicativo de contêiner usado para instalar a extensão tem os seguintes requisitos:

- Ele deve manter uma referência ao projeto de extensão.   
- Ele deve ser um aplicativo completo (deve ser capaz de iniciar e executar com êxito), mesmo se ele não faz nada mais do que fornecem uma maneira de instalar uma extensão. 
- Ele deve ter um identificador de pacote que é a base para o identificador de pacote da extensão de projeto (consulte a seção abaixo para obter mais detalhes).

### <a name="extension-project-requirements"></a>Requisitos de projeto de extensão

Além disso, o projeto da extensão tem os seguintes requisitos:

- Ele deve ter um identificador de pacote que começa com o identificador de pacote do seu aplicativo de contêiner. Por exemplo, se o aplicativo de contêiner tem um identificador de pacote do `com.myCompany.ContainerApp`, identificador da extensão pode estar `com.myCompany.ContainerApp.MyExtension`: 

    ![](extensions-images/bundleidentifiers.png) 
- Ele deve definir a chave `NSExtensionPointIdentifier`, com um valor apropriado (como `com.apple.widget-extension` para um **hoje mesmo** widget do Centro de notificação), no seu `Info.plist` arquivo.
- Ele também deve definir *qualquer um dos* o `NSExtensionMainStoryboard` chave ou o `NSExtensionPrincipalClass` chave em seu `Info.plist` arquivo com um valor apropriado:
    - Use o `NSExtensionMainStoryboard` chave para especificar o nome do Storyboard que apresenta a principal interface do usuário para a extensão (menos `.storyboard`). Por exemplo, `Main` para o `Main.storyboard` arquivo.
    - Use o `NSExtensionPrincipalClass` chave para especificar a classe que será inicializada quando a extensão é iniciada. O valor deve corresponder a **registre** valor de seu `UIViewController`: 

    ![](extensions-images/registerandprincipalclass.png)

Tipos específicos de extensões podem ter requisitos adicionais. Por exemplo, uma **hoje** ou **Centro de notificação** deve implementar a classe de entidade de segurança da extensão [INCWidgetProviding](https://developer.xamarin.com/api/type/NotificationCenter.INCWidgetProviding/).

> [!IMPORTANT]
> Se você iniciar seu projeto usando um os modelos de extensões fornecidos pelo Visual Studio para Mac, maioria (se não todos) esses requisitos serão fornecidos e atendidos para você automaticamente pelo modelo.

## <a name="walkthrough"></a>Passo a passo 

Seguir instruções passo a passo, você criará um exemplo **hoje mesmo** widget que calcula o dia e o número de dias restantes no ano:

[![](extensions-images/carpediemscreenshot-sm.png "Um widget de hoje do exemplo que calcula o dia e o número de dias restantes no ano")](extensions-images/carpediemscreenshot.png#lightbox)

### <a name="creating-the-solution"></a>Criando a solução

Para criar a solução necessária, faça o seguinte:

1. Primeiro, crie um novo do iOS, **aplicativo de exibição única** do projeto e clique no **próxima** botão: 

    [![](extensions-images/today01.png "Primeiro, crie um novo do iOS, o projeto de aplicativo de exibição única e clique no botão Avançar")](extensions-images/today01.png#lightbox)
2. Chame o projeto `TodayContainer` e clique em de **próxima** botão: 

    [![](extensions-images/today02.png "Chame o projeto TodayContainer e clique no botão Avançar")](extensions-images/today02.png#lightbox)
3. Verifique se o **nome do projeto** e **SolutionName** e clique no **criar** botão para criar a solução: 

    [![](extensions-images/today03.png "Verifique se o nome do projeto e SolutionName e clique no botão Criar para criar a solução")](extensions-images/today03.png#lightbox)
4. Em seguida, na **Gerenciador de soluções**, clique com botão direito na solução e adicione uma nova **iOS extensão** do projeto do **extensão hoje** modelo: 

    [![](extensions-images/today04.png "Em seguida, no Gerenciador de soluções, clique com botão direito na solução e adicionar um novo projeto de extensão do iOS a partir do modelo de extensão hoje")](extensions-images/today04.png#lightbox)
5. Chame o projeto `DaysRemaining` e clique em de **próxima** botão: 

    [![](extensions-images/today05.png "Chame o projeto DaysRemaining e clique no botão Avançar")](extensions-images/today05.png#lightbox)
6. Examine o projeto e clique no **criar** botão para criá-lo: 

    [![](extensions-images/today06.png "Examine o projeto e clique no botão Criar para criá-la")](extensions-images/today06.png#lightbox)

A solução resultante agora deve ter dois projetos, conforme mostrado aqui:

[![](extensions-images/today07.png "A solução resultante agora deve ter dois projetos, conforme mostrado aqui")](extensions-images/today07.png#lightbox)

### <a name="creating-the-extension-user-interface"></a>Criando a interface do usuário de extensão

Em seguida, você precisará projetar a interface para seu **hoje mesmo** widget. Isso pode ser feito usando um Storyboard ou pela criação de interface do usuário no código. Ambos os métodos serão abordados abaixo em detalhes.

#### <a name="using-storyboards"></a>Uso de storyboards

Para criar a interface do usuário com um Storyboard, faça o seguinte:

1. No **Gerenciador de soluções**, clique duas vezes o projeto de extensão `Main.storyboard` arquivo para abri-lo para edição: 

    [![](extensions-images/today08.png "Clique duas vezes no arquivo de Main. Storyboard de projetos de extensão para abri-lo para edição")](extensions-images/today08.png#lightbox)
2. Selecione o rótulo que foi adicionado automaticamente à interface do usuário pelo modelo e dê a ele o **nome** `TodayMessage` no **Widget** guia do **Gerenciador de propriedades**: 

    [![](extensions-images/today09.png "Selecione o rótulo que foi adicionado automaticamente à interface do usuário pelo modelo e dê a ele o TodayMessage nome na guia do Widget do Gerenciador de propriedades")](extensions-images/today09.png#lightbox)
3. Salve as alterações para o Storyboard.

#### <a name="using-code"></a>Usando código

Para criar a interface do usuário no código, faça o seguinte: 

1. No **Gerenciador de soluções**, selecione o **DaysRemaining** do projeto, adicione uma nova classe e chamá-lo `CodeBasedViewController`: 

    [![](extensions-images/code01.png "Aelect projeto DaysRemaining, adicione uma nova classe e chamá-lo CodeBasedViewController")](extensions-images/code01.png#lightbox)
2. Novamente, na **Gerenciador de soluções**, clique duas vezes em da extensão `Info.plist` arquivo para abri-lo para edição: 

    [![](extensions-images/code02.png "Clique duas vezes em Info. plist de extensões de arquivo para abri-lo para edição")](extensions-images/code02.png#lightbox)
3. Selecione o **exibição da fonte** (na parte inferior da tela) e abra o `NSExtension` nó: 

    [![](extensions-images/code03.png "Selecione a exibição da fonte na parte inferior da tela e abra o nó NSExtension")](extensions-images/code03.png#lightbox)
4. Remover o `NSExtensionMainStoryboard` da chave e adicione uma `NSExtensionPrincipalClass` com o valor `CodeBasedViewController`: 

    [![](extensions-images/code04.png "Remova a chave NSExtensionMainStoryboard e adicione um NSExtensionPrincipalClass com o valor CodeBasedViewController")](extensions-images/code04.png#lightbox)
5. Salve as alterações.

Em seguida, edite o `CodeBasedViewController.cs` de arquivo e torná-lo semelhante ao seguinte:

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

Observe que o `[Register("CodeBasedViewController")]` corresponde ao valor especificado para o `NSExtensionPrincipalClass` acima.

### <a name="coding-the-extension"></a>Codificando a extensão

Com a Interface de usuário criada, abra o `TodayViewController.cs` ou o `CodeBasedViewController.cs` arquivo (com base do método usado para criar a Interface de usuário acima), altere o **ViewDidLoad** método e torná-lo semelhante ao seguinte:

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

Se usando o código com base em método de Interface do usuário, substitua o `// Insert code to power extension here...` comentário com o novo código acima. Após chamar a implementação base (e inserindo um rótulo para a versão do código com base), esse código faz um cálculo simples para obter o dia do ano e o número de dias restantes. Em seguida, ele exibe a mensagem no rótulo (`TodayMessage`) que você criou no design da interface do usuário.

Observe a semelhança esse processo é o processo normal de escrever um aplicativo. Uma extensão `UIViewController` tem o mesmo ciclo de vida como um controlador de exibição em um aplicativo, exceto que as extensões não têm modos de segundo plano e não são suspensas quando o usuário for concluído usá-los. Em vez disso, as extensões repetidamente são inicializadas e desalocadas conforme necessário.

### <a name="creating-the-container-app-user-interface"></a>Criando a interface de usuário do aplicativo de contêiner

Para este passo a passo, o aplicativo de contêiner simplesmente é usado como um método para enviar e instalar a extensão e não fornece nenhuma funcionalidade própria. Editar o TodayContainer `Main.storyboard` arquivo e adicione algum texto na definição de função da extensão e como instalá-lo:

[![](extensions-images/today10.png "Edite o arquivo main TodayContainers e adicione algum texto que define a função de extensões e como instalá-lo")](extensions-images/today10.png#lightbox)

Salve as alterações para o Storyboard.

### <a name="testing-the-extension"></a>A extensão de teste

Para testar sua extensão no simulador de iOS, execute as **TodayContainer** aplicativo. Modo de exibição principal do contêiner será exibido:

[![](extensions-images/run01.png "Os contêineres de modo de exibição principal será exibido")](extensions-images/run01.png#lightbox)

Em seguida, pressione a **Home** botão no simulador, passe o dedo para baixo da parte superior da tela para abrir o **Centro de notificação**, selecione o **hoje** guia e clique no **Editar** botão:

[![](extensions-images/run02.png "Pressione o botão Home no simulador, passe o dedo para baixo da parte superior da tela para abrir o Centro de notificação, selecione a guia de hoje e clique no botão Editar")](extensions-images/run02.png#lightbox)

Adicione a **DaysRemaining** extensão para o **hoje mesmo** exibir e clique no **feito** botão:

[![](extensions-images/run03.png "Adicionar a extensão de DaysRemaining para a exibição de hoje e clique no botão concluído")](extensions-images/run03.png#lightbox)

O novo widget será adicionado para o **hoje mesmo** modo de exibição e os resultados serão exibidos:

[![](extensions-images/run04.png "O novo widget será adicionado à exibição de hoje e os resultados serão exibidos")](extensions-images/run04.png#lightbox)

## <a name="communicating-with-the-host-app"></a>Comunicando-se com o aplicativo de host

O exemplo hoje extensão criado acima não se comunica com seu aplicativo de host (a **hoje mesmo** tela). Se tivesse, ele usa o [ExtensionContext](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/) propriedade da `TodayViewController` ou `CodeBasedViewController` classes. 

Para extensões que receberão os dados de seus aplicativos de host, os dados estão na forma de uma matriz de [NSExtensionItem](https://developer.xamarin.com/api/type/Foundation.NSExtensionItem/) objetos armazenados na [InputItems](https://developer.xamarin.com/api/property/Foundation.NSExtensionContext.InputItems/) propriedade do [ExtensionContext ](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/) da extensão do `UIViewController`.

Outras extensões, como extensões de edição de fotos podem distinguir entre o usuário concluir ou cancelar o uso. Isso será sinalizado volta para o aplicativo host por meio de [CompleteRequest](https://developer.xamarin.com/api/member/Foundation.NSExtensionContext.CompleteRequest/) e [CancelRequest](https://developer.xamarin.com/api/member/Foundation.NSExtensionContext.CancelRequest/) métodos da [ExtensionContext](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/) propriedade.

Para obter mais informações, consulte da Apple [guia de programação de extensão de aplicativo](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW1).

## <a name="communicating-with-the-parent-app"></a>Comunicando-se com o aplicativo pai

Um grupo de aplicativos permite que diferentes aplicativos (ou um aplicativo e suas extensões) acessem um local de armazenamento de arquivo compartilhado. Grupos de aplicativo podem ser usados para dados como:

- [Configurações do Apple Watch](~/ios/watchos/app-fundamentals/settings.md).
- [NSUserDefaults compartilhados](~/ios/app-fundamentals/user-defaults.md).
- [Arquivos compartilhados](~/ios/watchos/app-fundamentals/parent-app.md#files).

Para obter mais informações, consulte o [grupos de aplicativos](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) seção do nosso **trabalhando com capacidades** documentação.

## <a name="mobilecoreservices"></a>MobileCoreServices

Ao trabalhar com as extensões, use um identificador de tipo de uniforme (UTI) para criar e manipular os dados que são trocados entre o aplicativo, outros aplicativos e/ou serviços.

O `MobileCoreServices.UTType` classe estática define as seguintes propriedades de auxiliar que se relacionam com da Apple `kUTType...` definições:

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

Para obter mais informações, consulte o [grupos de aplicativos](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) seção do nosso **trabalhando com capacidades** documentação.

## <a name="precautions-and-considerations"></a>Precauções e considerações

Extensões têm muito menos memória disponível para eles que os aplicativos. Eles devem executar rapidamente e com intrusões mínimo para o usuário e o aplicativo que são hospedados em. No entanto, uma extensão também deve fornecer uma função distinta, útil para o aplicativo de consumo com uma interface do usuário com marca que permitem ao usuário identificar o desenvolvedor da extensão ou aplicativo de contêiner que pertencem.

Devido a esses requisitos apertados, você deve implantar apenas as extensões que foram minuciosamente testadas e otimizadas para desempenho e consumo de memória. 

## <a name="summary"></a>Resumo

Este documento abordou as extensões, o que são e o tipo de pontos de extensão e as limitações conhecidas impostas em uma extensão pelo iOS. Ele discutiu a criação, distribuição, instalando e executando o ciclo de vida de extensão e extensões. Ele forneceu um passo a passo da criação de um simples **hoje mesmo** widget que mostra duas maneiras de criar a interface de usuário do widget usando Storyboards ou código. Ele mostrou como uma extensão de teste no simulador de iOS. Por fim, ele discuti brevemente se comunicando com o aplicativo de Host e algumas precauções e considerações que devem ser realizadas durante o desenvolvimento de uma extensão. 

## <a name="related-links"></a>Links relacionados

- [ContainerApp (amostra)](https://developer.xamarin.com/samples/monotouch/intro-to-extensions)
- [Criando extensões no xamarin. IOS (vídeo)](https://university.xamarin.com/lightninglectures/creating-extensions-in-ios)
