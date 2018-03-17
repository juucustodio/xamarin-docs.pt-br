---
title: "Extensões do iOS"
description: "Introduzido no iOS 8, as extensões são widgets que são apresentados pelo iOS em contextos padrão, como no Centro de notificação, quando o usuário solicita um teclado personalizado, ou quando eles foto edição. Todas as extensões são instaladas em conjunto com um aplicativo de contêiner e são ativadas de um ponto de extensão específica em um aplicativo Host."
ms.topic: article
ms.prod: xamarin
ms.assetid: 3DEB3D43-3E4A-4099-8331-93C1E7A77095
ms.technology: xamarin-ios
ms.custom: xamu-video
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 6e0eebef2404ce3f117fe897d456f3ef78a8f585
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2018
---
# <a name="ios-extensions"></a>Extensões do iOS

_Introduzido no iOS 8, as extensões são widgets que são apresentados pelo iOS em contextos padrão, como no Centro de notificação, quando o usuário solicita um teclado personalizado, ou quando eles foto edição. Todas as extensões são instaladas em conjunto com um aplicativo de contêiner e são ativadas de um ponto de extensão específica em um aplicativo Host._

> [!VIDEO https://youtube.com/embed/Sd0-ch9Udmk]

**Criando extensões no iOS, pelo [University Xamarin](https://university.xamarin.com/)**

Extensões, conforme apresentada no iOS 8, são especializadas `UIViewControllers` que são apresentados pelo iOS dentro do padrão de contextos, como dentro de **Central de notificações**, como tipos personalizados de teclado solicitados pelo usuário para executar especializado entrada ou em outros contextos, como editar uma foto em que a extensão pode fornecer filtros de efeito especial.

Todas as extensões são instaladas em conjunto com um aplicativo de contêiner (com dois elementos escritos usando as APIs de unificado de 64 bits) e são ativadas de um ponto de extensão específica em um aplicativo Host. E como eles serão usados como suplementos para funções do sistema existentes, eles devem ser alto desempenho, lean e robusto. 

Este artigo aborda os seguintes tópicos:

- [Pontos de extensão](#Extension-Points) -lista o tipo de ponto de extensão está disponível e o tipo de extensão que pode ser criado para cada ponto.
- [Limitações](#Limitations) -extensões têm várias limitações, alguns dos quais são universais a todos os tipos, enquanto outros tipos de extensão podem ter limitações específicas no seu uso.
- [Distribuir instalando e executando extensões](#Distributing-Installing-and-Running-Extensions) -as extensões são distribuídas de dentro de um aplicativo de contêiner, que, por sua vez é enviado e distribuídos por meio da loja de aplicativos. As extensões distribuídas com o aplicativo estão instaladas nesse ponto, mas o usuário deve habilitar cada extensão explicitamente. Os diferentes tipos de extensões são habilitados de maneiras diferentes.
- [Ciclo de vida de extensão](#Extension-Lifecycle) - uma extensão `UIViewController` serão instanciados e iniciar o ciclo de vida do controlador do modo de exibição normal. No entanto, ao contrário de um aplicativo normal, extensões são carregadas, executadas e finalizadas repetidamente.
- [Criando uma extensão](#Creating-an-Extension) -ao desenvolver uma extensão, suas soluções irá conter pelo menos dois projetos: o aplicativo de contêiner e um projeto para cada extensão de contêiner fornece.
- [Instruções passo a passo](#Walkthrough) - abrange criando um simples **hoje** widget de extensão que fornece a Interface do usuário usando um Storyboard ou no código, instalar a extensão e testá-lo no simulador iOS.
- [Comunicação com o aplicativo de Host](#Communicating-with-the-Host-App) -aborda brevemente se comunicar com o aplicativo de Host de uma extensão.
- [Comunicação com o aplicativo-pai](#Communicating-with-the-Parent-App) -aborda brevemente se comunicar com o aplicativo-pai de uma extensão.
- [Considerações e precauções](#Precautions-and-Considerations) -lista alguns sabem precauções e as considerações que devem ser levadas em conta ao projetar e implementar uma extensão.
 

<a name="Extension-Points" />

## <a name="extension-points"></a>Pontos de extensão

|Tipo|Descrição|Ponto de extensão|Aplicativo de host|
|--- |--- |--- |--- |
|Ação|Editor especializados ou o visualizador para um determinado tipo de mídia|`com.apple.ui-services`|Qualquer|
|Provedor de documento|Permite que o aplicativo usar um armazenamento remoto de documento|`com.apple.fileprovider-ui`|Aplicativos que usam um [UIDocumentPickerViewController](https://developer.xamarin.com/api/type/UIKit.UIDocumentPickerViewController/)|
|Teclado|Teclados alternativos|`com.apple.keyboard-service`|Qualquer|
|Edição de fotos|Manipulação de fotos e edição|`com.apple.photo-editing`|Editor de Photos.App|
|Compartilhar|Compartilha dados com redes sociais, serviços de mensagens etc.|`com.apple.share-services`|Qualquer|
|Hoje|"Widgets" que aparecem na tela de hoje ou centro de notificação|`com.apple.widget-extensions`|Centro de notificação e hoje|

[Pontos de extensão adicional](~/ios/platform/introduction-to-ios10/index.md#app-extensions) foram adicionados no iOS 10.

<a name="Limitations" />

## <a name="limitations"></a>Limitações

Extensões têm várias limitações, alguns dos quais são universais a todos os tipos (por exemplo, nenhum tipo de extensão pode acessar a câmeras ou microfones) enquanto outros tipos de extensão podem ter limitações específicas no seu uso (por exemplo, personalizados teclados não pode ser usado para campos de entrada de dados seguros, como senhas). 

As limitações de universais são:

- O [integridade Kit](~/ios/platform/healthkit.md) e [interface do usuário do Kit de evento](~/ios/platform/eventkit.md) estruturas não estão disponíveis
- Não é possível usar extensões [estendidos modos de segundo plano](http://developer.xamarin.com/guides/cross-platform/application_fundamentals/backgrounding/part_3_ios_backgrounding_techniques/registering_applications_to_run_in_background/)
- Extensões não podem acessar o dispositivo câmeras ou microfones (embora eles possam acessar os arquivos de mídia existente)
- Extensões não podem receber dados de descarte de ar (embora eles podem transmitir dados via ar Drop)
- [UIActionSheet](https://developer.xamarin.com/api/type/UIKit.UIActionSheet/) e [UIAlertView](https://developer.xamarin.com/api/type/UIKit.UIAlertView/) não estão disponíveis; extensões devem usar [UIAlertController](https://developer.xamarin.com/api/type/UIKit.UIAlertController/)
- Vários membros de [UIApplication](https://developer.xamarin.com/api/type/UIKit.UIApplication/) não estão disponíveis: [UIApplication.SharedApplication](https://developer.xamarin.com/api/property/UIKit.UIApplication.SharedApplication/), `UIApplication.OpenURL`, `UIApplication.BeginIgnoringInteractionEvents` e `UIApplication.EndIgnoringInteractionEvents`
- iOS impõe um limite de uso de memória de 16 MB em extensões de hoje.
- Por padrão, as extensões de teclado não tem acesso à rede. Isso afeta a depuração no dispositivo (não a restrição é imposta no simulador), como o xamarin requer acesso à rede para a depuração de trabalho. É possível solicitar o acesso de rede definindo a `Requests Open Access` valor no Info. plist do projeto para `Yes`. Consulte da Apple [personalizado teclado guia](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/CustomKeyboard.html) para obter mais informações sobre as limitações da extensão de teclado.

Para limitações individuais, consulte da Apple [guia de programação de extensão de aplicativo](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/).

<a name="Distributing-Installing-and-Running-Extensions" />

## <a name="distributing-installing-and-running-extensions"></a>Distribuir, instalar e executar extensões

As extensões são distribuídas de dentro de um aplicativo de contêiner, que, por sua vez é enviado e distribuídos por meio da loja de aplicativos. As extensões distribuídas com o aplicativo estão instaladas nesse ponto, mas o usuário deve habilitar cada extensão explicitamente. Os diferentes tipos de extensões estão habilitados maneiras diferentes; vários exigem que o usuário navegue para o **configurações** aplicativo e habilitá-los a partir daí. Enquanto outros são habilitados no ponto de uso, como a habilitação de uma extensão de compartilhamento ao enviar uma foto. 

O aplicativo no qual a extensão será usada (em que o usuário encontra o ponto de extensão) é conhecido como o **aplicativo Host**, já que é o aplicativo que hospeda a extensão quando ela é executada. O aplicativo que instala a extensão é a **aplicativo de contêiner**, pois este é o aplicativo que continha a extensão quando ele foi instalado.  

Normalmente, o aplicativo de contêiner descreve a extensão e orienta o usuário durante o processo de habilitá-la.

<a name="Extension-Lifecycle" />

## <a name="extension-lifecycle"></a>Ciclo de vida de extensão

Uma extensão pode ser tão simple quanto um único [UIViewController](https://developer.xamarin.com/api/type/UIKit.UIViewController/) ou extensões mais complexas que apresentam várias telas da interface do usuário. Quando o usuário encontra um _pontos de extensão_ (como quando uma imagem de compartilhamento), eles terão a oportunidade de escolher as extensões registradas para esse ponto de extensão. 

Se eles escolhem um aplicativo de extensões, seu `UIViewController` serão instanciados e iniciar o ciclo de vida do controlador do modo de exibição normal. No entanto, ao contrário de um aplicativo normal, que são suspensas, mas geralmente não finalizada quando o usuário termina de interagir com eles, extensões são carregadas, executadas e finalizadas repetidamente.

As extensões podem se comunicar com seus aplicativos de Host por meio de um [NSExtensionContext](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/) objeto. Algumas extensões têm operações que recebem retornos de chamada assíncronos com os resultados. Esses retornos de chamada serão executados em threads em segundo plano e a extensão deve levar isso em consideração; Por exemplo, usando [NSObject.InvokeOnMainThread](https://developer.xamarin.com/api/member/Foundation.NSObject.InvokeOnMainThread/) se desejar atualizar a interface do usuário. Consulte o [se comunicar com o aplicativo de Host](#Communicating-with-the-Host-App) seção abaixo para obter mais detalhes.

Por padrão, as extensões e os aplicativos de contêiner podem não se comunicar, apesar de ser instalados juntos. Em alguns casos, o aplicativo de contêiner é essencialmente um contêiner "envio" vazio cujo objetivo é atendido quando a extensão está instalada. No entanto, se determinarem circunstâncias, o aplicativo de contêiner e a extensão podem compartilhar recursos de uma área comum. Além disso, um **hoje extensão** podem solicitar seu aplicativo de contêiner para abrir uma URL. Esse comportamento é mostrado no [evoluir Widget de contagem regressiva](http://github.com/xamarin/monotouch-samples/tree/master/ExtensionsDemo).

<a name="Creating-an-Extension" />

## <a name="creating-an-extension"></a>Criando uma extensão

Extensões (e seus aplicativos de contêiner) devem ser binários de 64 bits e compilados com o xamarin [Unified APIs](http://developer.xamarin.com/guides/cross-platform/macios/unified). Ao desenvolver uma extensão, suas soluções irá conter pelo menos dois projetos: o aplicativo de contêiner e um projeto para cada extensão de contêiner fornece. 

<a name="Container-App-Project-Requirements" />

### <a name="container-app-project-requirements"></a>Requisitos do contêiner do projeto de aplicativo

O aplicativo de contêiner usado para instalar a extensão tem os seguintes requisitos:

- Ele deve manter uma referência ao projeto de extensão.   
- Ele deve ser um aplicativo completo (deve ser capaz de iniciar e executar com êxito) mesmo se ele não faz nada mais do que fornecem uma maneira de instalar uma extensão. 
- Ele deve ter um identificador de pacote que é a base para o identificador de pacote da extensão de projeto (consulte a seção abaixo para obter mais detalhes).

<a name="Extension-Project-Requirements" />

### <a name="extension-project-requirements"></a>Requisitos de projeto de extensão

Além disso, o projeto de extensão tem os seguintes requisitos:

- Ele deve ter um identificador de pacote que começa com o identificador de pacote do aplicativo do seu contêiner. Por exemplo, se o aplicativo de contêiner tem um identificador de pacote de `com.myCompany.ContainerApp`, identificador da extensão pode estar `com.myCompany.ContainerApp.MyExtension`: 

    ![](extensions-images/bundleidentifiers.png) 
- Ele deve definir a chave `NSExtensionPointIdentifier`, com um valor apropriado (como `com.apple.widget-extension` para um **hoje** widget do Centro de notificação), no seu `Info.plist` arquivo.
- Também deve definir *ou* o `NSExtensionMainStoryboard` chave ou o `NSPrincipalClass` chave em seu `Info.plist` arquivo com um valor apropriado:
    - Use o `NSExtensionMainStoryboard` chave para especificar o nome do Storyboard que apresenta a interface do usuário principal para a extensão (menos `.storyboard`). Por exemplo, `Main` para o `Main.storyboard` arquivo.
    - Use o `NSPrincipalClass` chave para especificar a classe que será inicializada quando a extensão é iniciada. O valor deve corresponder a **registrar** valor de seu `UIViewController`: 

    ![](extensions-images/registerandprincipalclass.png)

Tipos específicos de extensões podem ter requisitos adicionais. Por exemplo, um **hoje** ou **Central de notificações** deve implementar a classe principal da extensão [INCWidgetProviding](https://developer.xamarin.com/api/type/NotificationCenter.INCWidgetProviding/).

> [!IMPORTANT]
> **Observação:** se você iniciar seu projeto usando um os modelos de extensões fornecidos pelo Visual Studio para Mac, mais (se não todos) esses requisitos serão fornecidos e atendidos para você automaticamente pelo modelo.

<a name="Walkthrough" />

## <a name="walkthrough"></a>Passo a passo 

Passo a passo, você criará um exemplo **hoje** widget que calcula o dia e o número de dias restantes no ano:

[![](extensions-images/carpediemscreenshot-sm.png "Um widget de hoje de exemplo que calcula o dia e o número de dias restantes no ano")](extensions-images/carpediemscreenshot.png#lightbox)

<a name="Creating-the-Solution" />

### <a name="creating-the-solution"></a>Criando a solução

Para criar a solução necessária, faça o seguinte:

1. Primeiro, crie um novo iOS, **único aplicativo de exibição** do projeto e clique no **próximo** botão: 

    [![](extensions-images/today01.png "Primeiro, crie um novo iOS, o projeto de aplicativo de modo único e clique no botão Avançar")](extensions-images/today01.png#lightbox)
2. Chame o projeto `TodayContainer` e clique no **próximo** botão: 

    [![](extensions-images/today02.png "Chamar o TodayContainer do projeto e clique no botão Avançar")](extensions-images/today02.png#lightbox)
3. Verifique se o **nome do projeto** e **SolutionName** e clique no **criar** botão para criar a solução: 

    [![](extensions-images/today03.png "Verifique o nome do projeto e SolutionName e clique no botão Criar para criar a solução")](extensions-images/today03.png#lightbox)
4. Em seguida, no **Solution Explorer**, com o botão direito na solução e adicione um novo **iOS extensão** de projeto do **hoje extensão** modelo: 

    [![](extensions-images/today04.png "Em seguida, no Gerenciador de soluções, clique com botão direito na solução e adicione um novo projeto de extensão do iOS do modelo de extensão hoje")](extensions-images/today04.png#lightbox)
5. Chame o projeto `DaysRemaining` e clique no **próximo** botão: 

    [![](extensions-images/today05.png "Chamar o DaysRemaining do projeto e clique no botão Avançar")](extensions-images/today05.png#lightbox)
6. Analise o projeto e clique no **criar** botão criá-la: 

    [![](extensions-images/today06.png "Analise o projeto e clique no botão Criar para criá-la")](extensions-images/today06.png#lightbox)

A solução resultante agora deve ter dois projetos, conforme mostrado aqui:

[![](extensions-images/today07.png "A solução resultante agora deve ter dois projetos, conforme mostrado aqui")](extensions-images/today07.png#lightbox)

<a name="Creating-the-Extension-User-Interface" />

### <a name="creating-the-extension-user-interface"></a>Criando a Interface do usuário de extensão

Em seguida, você precisará criar a interface para o **hoje** widget. Isso pode ser feito usando um Storyboard ou pela criação de interface do usuário no código. Ambos os métodos serão abordados abaixo em detalhes.

<a name="Using-Storyboards" />

#### <a name="using-storyboards"></a>Usando Storyboards

Para criar a interface do usuário com um Storyboard, faça o seguinte:

1. No **Solution Explorer**, clique duas vezes o projeto de extensão `Main.storyboard` arquivo para abri-lo para edição: 

    [![](extensions-images/today08.png "Clique duas vezes no arquivo de Main.storyboard de projetos de extensão para abri-lo para edição")](extensions-images/today08.png#lightbox)
2. Selecione o rótulo que foi adicionado automaticamente à interface do usuário por modelo e dê a ele o **nome** `TodayMessage` no **Widget** guia do **propriedades Explorer**: 

    [![](extensions-images/today09.png "Selecione o rótulo que foi adicionado automaticamente à interface do usuário pelo modelo e dê a ele o TodayMessage nome na guia do Widget do Gerenciador de propriedades")](extensions-images/today09.png#lightbox)
3. Salve as alterações para o Storyboard.

<a name="Using-Code" />

#### <a name="using-code"></a>Usando código

Para criar a interface do usuário no código, faça o seguinte: 

1. No **Solution Explorer**, selecione o **DaysRemaining** de projeto, adicione uma nova classe e chamá-lo `CodeBasedViewController`: 

    [![](extensions-images/code01.png "Aelect projeto DaysRemaining, adicione uma nova classe e chamá-lo CodeBasedViewController")](extensions-images/code01.png#lightbox)
2. Novamente, o **Solution Explorer**, clique duas vezes em da extensão `Info.plist` arquivo para abri-lo para edição: 

    [![](extensions-images/code02.png "Clique duas vezes no arquivo Info. plist de extensões para abri-lo para edição")](extensions-images/code02.png#lightbox)
3. Selecione o **exibição da fonte de** (da parte inferior da tela) e abra o `NSExtension` nó: 

    [![](extensions-images/code03.png "Selecione a exibição da fonte na parte inferior da tela e abra o nó NSExtension")](extensions-images/code03.png#lightbox)
4. Remover o `NSExtensionMainStoryboard` chave e adicione um `NSPrincipalClass` com o valor `CodeBasedViewController`: 

    [![](extensions-images/code04.png "Remova a chave NSExtensionMainStoryboard e adicionar um NSPrincipalClass com o valor CodeBasedViewController")](extensions-images/code04.png#lightbox)
5. Salve as alterações.

Em seguida, edite o `CodeBasedViewController.cs` de arquivo e torná-lo a aparência a seguir:

```csharp
using System;
using Foundation;
using UIKit;
using NotificationCenter;
using CoreGraphics;

namespace DaysRemaining
{
    [Register("CodeBasedViewContoller")]
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

Observe que o `[Register("CodeBasedViewContoller")]` corresponde ao valor especificado para o `NSPrincipalClass` acima.

<a name="Coding-the-Extension" />

### <a name="coding-the-extension"></a>A extensão de codificação

Com a Interface de usuário foi criado, abra a `TodayViewController.cs` ou `CodeBasedViewController.cs` arquivo (com base no método usado para criar a Interface de usuário acima), alterar o **ViewDidLoad** método e torná-lo a aparência a seguir:

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

Se usando o código com base em método de Interface do usuário, substitua o `// Insert code to power extension here...` comentário com o novo código acima. Após chamar a implementação base (e inserindo um rótulo para a versão do código com base em), este código faz um cálculo simples para obter o dia do ano e quantos dias são restantes. Em seguida, ele exibe a mensagem no rótulo (`TodayMessage`) que você criou no design da interface do usuário.

Observe como semelhante esse processo é o processo normal de escrever um aplicativo. Uma extensão `UIViewController` tem o mesmo ciclo de vida como um controlador de exibição em um aplicativo, exceto extensões não tem modos de segundo plano e não serão suspensas quando o usuário tenha terminado usá-los. Em vez disso, extensões repetidamente são inicializadas e desalocadas conforme necessário.

<a name="Creating-the-Container-App-User-Interface" />

### <a name="creating-the-container-app-user-interface"></a>Criando a Interface do usuário do aplicativo de contêiner

Para este passo a passo, o aplicativo de contêiner é usado apenas como um método para enviar e instalar a extensão e não fornece nenhuma funcionalidade de seu próprio. Editar o TodayContainer `Main.storyboard` e adicione um texto de definição de função de extensão e como instalá-lo:

[![](extensions-images/today10.png "Edite o arquivo TodayContainers Main.storyboard e adicione um texto que define a função de extensões e como instalá-lo")](extensions-images/today10.png#lightbox)

Salve as alterações para o Storyboard.

<a name="Testing-the-Extension" />

### <a name="testing-the-extension"></a>A extensão de teste

Para testar sua extensão no simulador iOS, execute o **TodayContainer** aplicativo. Modo de exibição principal do contêiner será exibido:

[![](extensions-images/run01.png "Os contêineres de modo de exibição principal será exibido")](extensions-images/run01.png#lightbox)

Ocorrências em seguida, o **início** botão no simulador, passe o dedo para baixo da parte superior da tela para abrir o **Central de notificações**, selecione o **hoje** guia e clique no **Editar** botão:

[![](extensions-images/run02.png "Clique no botão página inicial no simulador, passe o dedo para baixo da parte superior da tela para abrir a Central de notificação, selecione a guia de hoje e clique no botão Editar")](extensions-images/run02.png#lightbox)

Adicionar o **DaysRemaining** extensão para o **hoje** exibir e clique no **feito** botão:

[![](extensions-images/run03.png "Adicionar a extensão de DaysRemaining para o modo de exibição de hoje e clique no botão concluído")](extensions-images/run03.png#lightbox)

Novo widget será adicionado para o **hoje** exibição e os resultados serão exibidos:

[![](extensions-images/run04.png "O novo widget será adicionado à exibição de hoje e os resultados serão exibidos")](extensions-images/run04.png#lightbox)

<a name="Communicating-with-the-Host-App" />

## <a name="communicating-with-the-host-app"></a>Comunicação com o aplicativo de Host

O exemplo hoje extensão criado acima não se comunicar com seu aplicativo de host (a **hoje** tela). Se tivesse, ele usaria a [ExtensionContext](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/) propriedade o `TodayViewController` ou `CodeBasedViewController` classes. 

Para as extensões que receberão os dados de seus aplicativos de host, os dados estão na forma de uma matriz de [NSExtensionItem](https://developer.xamarin.com/api/type/Foundation.NSExtensionItem/) objetos armazenados na [InputItems](https://developer.xamarin.com/api/property/Foundation.NSExtensionContext.InputItems/) propriedade o [ExtensionContext ](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/) da extensão de `UIViewController`.

Outra extensão, como extensões de edição de foto pode distinguir entre o usuário concluir ou cancelar o uso. Isso sinalizará para o aplicativo de host por meio de [CompleteRequest](https://developer.xamarin.com/api/member/Foundation.NSExtensionContext.CompleteRequest/) e [CancelRequest](https://developer.xamarin.com/api/member/Foundation.NSExtensionContext.CancelRequest/) métodos de [ExtensionContext](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/) propriedade.

Para obter mais informações, consulte da Apple [guia de programação de extensão de aplicativo](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW1).

<a name="Communicating-with-the-Parent-App" />

## <a name="communicating-with-the-parent-app"></a>Comunicação com o aplicativo-pai

Um grupo de aplicativos permite que diferentes aplicativos (ou um aplicativo e suas extensões) acessem um local de armazenamento de arquivo compartilhado. Grupos de aplicativo podem ser usados para dados como:

- [Configurações do Apple Watch](~/ios/watchos/app-fundamentals/settings.md).
- [NSUserDefaults compartilhados](~/ios/app-fundamentals/user-defaults.md).
- [Arquivos compartilhados](~/ios/watchos/app-fundamentals/parent-app.md#files).

Para obter mais informações, consulte o [grupos de aplicativos](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) seção do nosso **trabalhando com recursos** documentação.

<a name="MobileCoreServices" />

## <a name="mobilecoreservices"></a>MobileCoreServices

Ao trabalhar com extensões, use um identificador de tipo uniforme (utilitário) para criar e manipular os dados que são trocados entre o aplicativo, outros aplicativos e/ou serviços.

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

Para obter mais informações, consulte o [grupos de aplicativos](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) seção do nosso **trabalhando com recursos** documentação.


<a name="Precautions-and-Considerations" />

## <a name="precautions-and-considerations"></a>Precauções e considerações

Extensões têm muito menos memória disponível para eles que os aplicativos. Eles devem executar rapidamente e com invasão mínimo para o usuário e o aplicativo que são hospedados em. No entanto, uma extensão também deve fornecer uma função diferente, útil para o aplicativo do consumidor com uma interface de usuário com marca que permitem ao usuário identificar o desenvolvedor de extensão ou aplicativo de contêiner pertencerem a.

Devido a esses requisitos rígidos, você deverá implantar somente as extensões que foram cuidadosamente testadas e otimizadas para desempenho e o consumo de memória. 

<a name="Summary" />

## <a name="summary"></a>Resumo

Este documento abordou extensões, o que são, o tipo de pontos de extensão e as limitações conhecidas impostas pelo iOS uma extensão. Ele discutidos criando, distribuir, instalar e executar extensões e o ciclo de vida de extensão. Ele fornecido um passo a passo de criação de um simples **hoje** widget mostra duas maneiras de criar a interface de usuário do widget usando Storyboards ou código. Ele mostrou como testar uma extensão no simulador iOS. Finalmente, ele brevemente discutidos se comunicar com o aplicativo de Host e algumas precauções e considerações que devem ser tomadas ao desenvolver uma extensão. 


## <a name="related-links"></a>Links relacionados

- [ContainerApp (exemplo)](https://developer.xamarin.com/samples/monotouch/intro-to-extensions)
- [Criando extensões no xamarin (vídeo)](https://university.xamarin.com/lightninglectures/creating-extensions-in-ios)
