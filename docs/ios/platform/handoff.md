---
title: Entrega no Xamarin. iOS
description: Este artigo aborda como trabalhar com a entrega em um aplicativo Xamarin. iOS para transferir atividades do usuário entre aplicativos em execução nos outros dispositivos do usuário.
ms.prod: xamarin
ms.assetid: 405F966A-4085-4621-AA15-33D663AD15CD
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: 52ee92eca5fa0b3108b2ef96ef81bfb939e61a6c
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70752914"
---
# <a name="handoff-in-xamarinios"></a>Entrega no Xamarin. iOS

_Este artigo aborda como trabalhar com a entrega em um aplicativo Xamarin. iOS para transferir atividades do usuário entre aplicativos em execução nos outros dispositivos do usuário._

A Apple introduziu a entrega no iOS 8 e no OS X Yosemite (10,10) para fornecer um mecanismo comum para o usuário transferir atividades iniciadas em um de seus dispositivos para outro dispositivo que executa o mesmo aplicativo ou outro aplicativo que ofereça suporte à mesma atividade.

[![](handoff-images/handoff02.png "Um exemplo de execução de uma operação de entrega")](handoff-images/handoff02.png#lightbox)

Este artigo fará uma rápida visão geral de como habilitar o compartilhamento de atividades em um aplicativo Xamarin. iOS e abordar a estrutura de entrega em detalhes:

## <a name="about-handoff"></a>Sobre a entrega

A entrega (também conhecida como continuidade) foi introduzida pela Apple no iOS 8 e no OS X Yosemite (10,10) como uma maneira para o usuário iniciar uma atividade em um de seus dispositivos (iOS ou Mac) e continuar a mesma atividade em outro de seus dispositivos (conforme identificado pelo iClou do usuário Conta d).

A entrega foi expandida no iOS 9 para oferecer também suporte a novos recursos de pesquisa aprimorados. Para obter mais informações, consulte nossa documentação de [aprimoramentos de pesquisa](~/ios/platform/search/index.md) .

Por exemplo, o usuário pode iniciar um email em seu iPhone e continuar diretamente o email em seu Mac, com todas as mesmas informações de mensagem preenchidas e o cursor no mesmo local em que elas foram deixadas no iOS.

Qualquer um de seus aplicativos que compartilham a mesma _ID de equipe_ está qualificado para usar a entrega para continuar as atividades do usuário em aplicativos, desde que esses aplicativos sejam entregues por meio da iTunes App Store ou assinados por um desenvolvedor registrado (para aplicativos Mac, Enterprise ou ad hoc).

Os `NSDocument` aplicativos `UIDocument` ou baseados automaticamente têm suporte de entrega interno e exigem alterações mínimas para dar suporte à entrega.

### <a name="continuing-user-activities"></a>Continuando atividades do usuário

A `NSUserActivity` classe (juntamente com algumas pequenas alterações em `UIKit` e `AppKit`) fornece suporte para definir a atividade de um usuário que pode continuar a ser continuada em outro dos dispositivos do usuário.

Para que uma atividade seja passada para outro dos dispositivos do usuário, ela deve ser encapsulada em uma instância `NSUserActivity`, marcada como a _atividade atual_, ter sua carga definida (os dados usados para executar a continuação) e a atividade deve ser transmitido para esse dispositivo.

A entrega passa o mínimo de informações para definir a atividade a ser continuada, com pacotes de dados maiores sendo sincronizados por meio do iCloud.

No dispositivo receptor, o usuário receberá uma notificação informando que uma atividade está disponível para continuação. Se o usuário optar por continuar a atividade no novo dispositivo, o aplicativo especificado será iniciado (se ainda não estiver em execução) e a carga do `NSUserActivity` será usada para reiniciar a atividade.

[![](handoff-images/handoffinteractions.png "Uma visão geral de como continuar as atividades do usuário")](handoff-images/handoffinteractions.png#lightbox)

Somente os aplicativos que compartilham a mesma ID da equipe do desenvolvedor e respondem a um determinado _tipo de atividade_ são elegíveis para continuação. Um aplicativo define os tipos de atividade que ele suporta sob `NSUserActivityTypes` a chave de seu arquivo **info. plist** . Considerando isso, um dispositivo continuando escolhe o aplicativo para executar a continuação com base na ID da equipe, no tipo de atividade e, opcionalmente, no _título da atividade_.

O aplicativo de recebimento usa informações do `NSUserActivity`dicionário `UserInfo` do para configurar sua interface do usuário e restaurar o estado da atividade especificada para que a transição pareça direta para o usuário final.

Se a continuação exigir mais informações do que pode ser enviado com eficiência `NSUserActivity`por meio de um, o aplicativo de retomada poderá enviar uma chamada para o aplicativo de origem e estabelecer um ou mais fluxos para transmitir os dados necessários. Por exemplo, se a atividade estivesse editando um documento de texto grande com várias imagens, o streaming seria necessário para transferir as informações necessárias para continuar a atividade no dispositivo receptor. Para obter mais informações, consulte a seção de [fluxos de continuação de suporte](#supporting-continuation-streams) abaixo.

Conforme mencionado acima, `NSDocument` ou `UIDocument` aplicativos baseados automaticamente têm suporte de entrega interno. Para obter mais informações, consulte a seção [suporte à entrega em aplicativos baseados em documento](#supporting-handoff-in-document-based-apps) abaixo.

### <a name="the-nsuseractivity-class"></a>A classe NSUserActivity

A `NSUserActivity` classe é o objeto principal em uma troca de entrega e é usada para encapsular o estado de uma atividade de usuário que está disponível para continuação. Um aplicativo criará uma instância de `NSUserActivity` uma cópia do para qualquer atividade que ele suporta e deseja continuar em outro dispositivo. Por exemplo, o editor de documento criaria uma atividade para cada documento aberto no momento. No entanto, somente o documento na extremidade frontal (exibido na janela ou guia na extremidade frontal) é a _atividade atual_ e, portanto, disponível para continuação.

Uma instância do `NSUserActivity` é identificada por suas `ActivityType` propriedades `Title` e. A `UserInfo` Propriedade Dictionary é usada para transportar informações sobre o estado da atividade. Defina a `NeedsSave` Propriedade como `true` se você quiser carregar as informações de estado lentas por `NSUserActivity`meio do delegado do. Use o `AddUserInfoEntries` método para mesclar novos dados de outros clientes `UserInfo` no dicionário, conforme necessário, para preservar o estado da atividade.

### <a name="the-nsuseractivitydelegate-class"></a>A classe NSUserActivityDelegate

O `NSUserActivityDelegate` é usado para manter as informações em `NSUserActivity` `UserInfo` um dicionário atualizado e sincronizadas com o estado atual da atividade. Quando o sistema precisa que as informações na atividade sejam atualizadas (como antes da continuação em outro dispositivo), ele chama o `UserActivityWillSave` método do delegado.

`UserActivityWillSave` Você precisará implementar o método e fazer qualquer alteração `NSUserActivity` no ( `UserInfo`como, `Title`etc.) para garantir que ele ainda reflita o estado da atividade atual. Quando o sistema chamar o `UserActivityWillSave` método, o `NeedsSave` sinalizador será limpo. Se você modificar qualquer uma das propriedades de dados da atividade, precisará definir `NeedsSave` `true` novamente.

Em vez de usar `UserActivityWillSave` o método apresentado acima, você pode, opcionalmente `AppKit` , ter `UIKit` ou gerenciar a atividade do usuário automaticamente. Para fazer isso, defina a propriedade do `UserActivity` objeto Respondente e implemente o `UpdateUserActivityState` método. Consulte a seção [oferecendo suporte à entrega em respondentes](#supporting-handoff-in-responders) abaixo para obter mais informações.

### <a name="app-framework-support"></a>Suporte à estrutura do aplicativo

Ambos `UIKit` (Ios) e `AppKit` (os X) fornecem suporte interno para a `NSDocument`entrega nas classes, Respondente (`NSResponder``UIResponder`/) e `AppDelegate` . Embora cada sistema operacional implemente a entrega de forma ligeiramente diferente, o mecanismo básico e as APIs são os mesmos.

#### <a name="user-activities-in-document-based-apps"></a>Atividades do usuário em aplicativos baseados em documentos

Aplicativos iOS e OS X baseados em documentos automaticamente têm suporte de entrega interno. Para ativar esse suporte, você precisará adicionar uma chave `NSUbiquitousDocumentUserActivityType` e um valor para cada `CFBundleDocumentTypes` entrada no arquivo **info. plist** do aplicativo.

Se essa chave estiver presente, `NSDocument` e `UIDocument` criar `NSUserActivity` automaticamente instâncias para documentos baseados no iCloud do tipo especificado. Você precisará fornecer um tipo de atividade para cada tipo de documento que o aplicativo dá suporte e vários tipos de documento podem usar o mesmo tipo de atividade. `NSDocument` E `UIDocument` preencherautomaticamente`UserInfo` a propriedade do com`FileURL`ovalordapropriedade. `NSUserActivity`

No os X, o `NSUserActivity` gerenciado pelo `AppKit` e o associado aos respondentes se tornam automaticamente a atividade atual quando a janela do documento se torna a janela principal. No Ios, para `NSUserActivity` objetos gerenciados `UIKit`pelo, você deve chamar `BecomeCurrent` o método explicitamente ou ter a propriedade `UserActivity` do documento definida em `UIViewController` um quando o aplicativo chega ao primeiro plano.

`AppKit`o irá restaurar automaticamente `UserActivity` qualquer propriedade criada dessa maneira no os X. Isso ocorrerá se `ContinueUserActivity` o método `false` retornar ou se não for implementado. Nessa situação, o documento é aberto com o `OpenDocument` método `NSDocumentController` do e, em seguida, receberá uma `RestoreUserActivityState` chamada de método.

Consulte a seção [entrega de suporte em aplicativos baseados em documento](#supporting-handoff-in-document-based-apps) abaixo para obter mais informações.

#### <a name="user-activities-and-responders"></a>Atividades e respondentes do usuário

O `UIKit` e `AppKit` o podem gerenciar automaticamente uma atividade de usuário se você defini-la como uma propriedade `UserActivity` do objeto respondente. Se o estado tiver sido modificado, você precisará definir a `NeedsSave` Propriedade do Respondente `UserActivity` como `true`. O sistema salvará automaticamente o `UserActivity` quando necessário, depois de dar ao tempo de Respondente para atualizar o estado chamando `UpdateUserActivityState` seu método.

Se vários respondentes compartilharem uma `NSUserActivity` única instância, eles receberão um `UpdateUserActivityState` retorno de chamada quando o sistema atualizar o objeto de atividade do usuário. O respondente precisa chamar o `AddUserInfoEntries` método para atualizar o `NSUserActivity`dicionário do `UserInfo` para refletir o estado da atividade atual neste momento. O `UserInfo` dicionário é limpo antes de `UpdateUserActivityState` cada chamada.

Para desassociar-se de uma atividade, um respondente pode definir `UserActivity` sua propriedade `null`como. Quando uma instância gerenciada `NSUserActivity` da estrutura de aplicativo não tem mais respondentes ou documentos associados, ela é invalidada automaticamente.

Consulte a seção [oferecendo suporte à entrega em respondentes](#supporting-handoff-in-responders) abaixo para obter mais informações.

#### <a name="user-activities-and-the-appdelegate"></a>Atividades do usuário e o AppDelegate

Seu aplicativo `AppDelegate` é seu ponto de entrada principal ao lidar com uma continuação de entrega. Quando o usuário responde a uma notificação de entrega, o aplicativo apropriado é iniciado (se ainda não estiver em execução `WillContinueUserActivityWithType` ) e o `AppDelegate` método de será chamado. Neste ponto, o aplicativo deve informar ao usuário que a continuação está sendo iniciada.

A `NSUserActivity` instância é entregue quando `ContinueUserActivity` o `AppDelegate`método é chamado. Neste ponto, você deve configurar a interface do usuário do aplicativo e continuar a atividade especificada.

Consulte a seção [implementando a entrega](#implementing-handoff) abaixo para obter mais informações.

## <a name="enabling-handoff-in-a-xamarin-app"></a>Habilitando a entrega em um aplicativo Xamarin

Devido aos requisitos de segurança impostos pelo entrega, um aplicativo Xamarin. iOS que usa a estrutura de entrega deve ser configurado corretamente no portal do desenvolvedor da Apple e no arquivo de projeto Xamarin. iOS.

Faça o seguinte:

1. Faça logon no [portal do desenvolvedor da Apple](https://developer.apple.com).
2. Clique em **certificados, identificadores & perfis**.
3. Se você ainda não tiver feito isso, clique em **identificadores** e crie uma ID para seu aplicativo (por `com.company.appname`exemplo,), caso contrário, edite sua ID existente.
4. Verifique se o serviço **icloud** foi verificado para a ID fornecida:

    [![](handoff-images/provision01.png "Habilitar o serviço iCloud para a ID fornecida")](handoff-images/provision01.png#lightbox)
5. Salve as alterações.
6. Clique em**desenvolvimento** de **perfis** > de provisionamento e crie um novo perfil de provisionamento de desenvolvimento para seu aplicativo:

    [![](handoff-images/provision02.png "Criar um novo perfil de provisionamento de desenvolvimento para o aplicativo")](handoff-images/provision02.png#lightbox)
7. Baixe e instale o novo perfil de provisionamento ou use o Xcode para baixar e instalar o perfil.
8. Edite suas opções de projeto do Xamarin. iOS e verifique se você está usando o perfil de provisionamento que você acabou de criar:

    [![](handoff-images/provision03.png "Selecione o perfil de provisionamento que acabou de ser criado")](handoff-images/provision03.png#lightbox)
9. Em seguida, edite seu arquivo **info. plist** e verifique se você está usando a ID do aplicativo que foi usada para criar o perfil de provisionamento:

    [![](handoff-images/provision04.png "Definir ID do aplicativo")](handoff-images/provision04.png#lightbox)
10. Role até a seção **modos de segundo plano** e verifique os seguintes itens:

    [![](handoff-images/provision05.png "Habilitar os modos de segundo plano necessários")](handoff-images/provision05.png#lightbox)
11. Salve as alterações em todos os arquivos.

Com essas configurações em vigor, o aplicativo agora está pronto para acessar as APIs da estrutura de entrega. Para obter informações detalhadas sobre o provisionamento, consulte nosso [provisionamento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md) e [Provisionando seus guias de aplicativo](~/ios/get-started/installation/device-provisioning/index.md) .

## <a name="implementing-handoff"></a>Implementando a entrega

As atividades do usuário podem ser continuadas entre os aplicativos que são assinados com a mesma ID da equipe do desenvolvedor e dão suporte ao mesmo tipo de atividade. Implementar a entrega em um aplicativo Xamarin. Ios exige que você crie um objeto de atividade de usuário `UIKit` ( `AppKit`no ou), atualize o estado do objeto para acompanhar a atividade e continue a atividade em um dispositivo receptor.

### <a name="identifying-user-activities"></a>Identificando atividades do usuário

A primeira etapa na implementação da entrega é identificar os tipos de atividades de usuário que seu aplicativo dá suporte e ver quais dessas atividades são bons candidatos para a continuação em outro dispositivo. Por exemplo: um aplicativo de tarefas pode dar suporte à edição de itens como um _tipo de atividade de usuário_e dar suporte à navegação da lista de itens disponíveis como outro.

Um aplicativo pode criar quantos tipos de atividade de usuário forem necessários, um para qualquer função que o aplicativo fornecer. Para cada tipo de atividade de usuário, o aplicativo precisará controlar quando uma atividade do tipo começa e termina e precisa manter informações de estado atualizadas para continuar essa tarefa em outro dispositivo.

As atividades do usuário podem ser continuadas em qualquer aplicativo assinado com a mesma ID de equipe sem nenhum mapeamento de um para um entre os aplicativos de envio e recebimento. Por exemplo, um determinado aplicativo pode criar quatro tipos diferentes de atividades, que são consumidas por aplicativos individuais diferentes em outro dispositivo. Essa é uma ocorrência comum entre uma versão Mac do aplicativo (que pode ter muitos recursos e funções) e aplicativos iOS, em que cada aplicativo é menor e focalizado em uma tarefa específica.

### <a name="creating-activity-type-identifiers"></a>Criando identificadores de tipo de atividade

O _identificador de tipo de atividade_ é uma cadeia de caracteres `NSUserActivityTypes` curta adicionada à matriz do arquivo **info. plist** do aplicativo usado para identificar exclusivamente um determinado tipo de atividade de usuário. Haverá uma entrada na matriz para cada atividade que o aplicativo suporta. A Apple sugere o uso de uma notação de estilo DNS reverso para o identificador de tipo de atividade para evitar colisões. Por exemplo: `com.company-name.appname.activity` para atividades específicas baseadas em aplicativo `com.company-name.activity` ou para atividades que podem ser executadas em vários aplicativos.

O identificador de tipo de atividade é usado ao `NSUserActivity` criar uma instância para identificar o tipo de atividade. Quando uma atividade é continuada em outro dispositivo, o tipo de atividade (junto com a ID de equipe do aplicativo) determina qual aplicativo deve ser iniciado para continuar a atividade.

Como exemplo, vamos criar um aplicativo de exemplo chamado **MonkeyBrowser** ([download aqui](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-monkeybrowser)). Esse aplicativo apresentará quatro guias, cada uma com uma URL diferente aberta em um modo de exibição de navegador da Web. O usuário poderá continuar qualquer guia em um dispositivo iOS diferente que executa o aplicativo.

Para criar os identificadores de tipo de atividade necessários para dar suporte a esse comportamento, edite o arquivo **info. plist** e alterne para o modo de exibição de **origem** . Adicione uma `NSUserActivityTypes` chave e crie os seguintes identificadores:

[![](handoff-images/type01.png "A chave NSUserActivityTypes e os identificadores necessários no editor do plist")](handoff-images/type01.png#lightbox)

Criamos quatro novos identificadores de tipo de atividade, um para cada uma das guias no aplicativo de exemplo **MonkeyBrowser** . Ao criar seus próprios aplicativos, substitua o conteúdo da `NSUserActivityTypes` matriz pelos identificadores de tipo de atividade específicos das atividades às quais seu aplicativo dá suporte.

### <a name="tracking-user-activity-changes"></a>Acompanhamento de alterações de atividade do usuário

Quando criamos uma nova instância da `NSUserActivity` classe, especificaremos uma `NSUserActivityDelegate` instância para controlar as alterações no estado da atividade. Por exemplo, o código a seguir pode ser usado para rastrear alterações de Estado:

```csharp
using System;
using CoreGraphics;
using Foundation;
using UIKit;

namespace MonkeyBrowse
{
    public class UserActivityDelegate : NSUserActivityDelegate
    {
        #region Constructors
        public UserActivityDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void UserActivityReceivedData (NSUserActivity userActivity, NSInputStream inputStream, NSOutputStream outputStream)
        {
            // Log
            Console.WriteLine ("User Activity Received Data: {0}", userActivity.Title);
        }

        public override void UserActivityWasContinued (NSUserActivity userActivity)
        {
            Console.WriteLine ("User Activity Was Continued: {0}", userActivity.Title);
        }

        public override void UserActivityWillSave (NSUserActivity userActivity)
        {
            Console.WriteLine ("User Activity will be Saved: {0}", userActivity.Title);
        }
        #endregion
    }
}
```

O `UserActivityReceivedData` método é chamado quando um fluxo de continuação recebe dados de um dispositivo de envio. Para obter mais informações, consulte a seção de [fluxos de continuação de suporte](#supporting-continuation-streams) abaixo.

O `UserActivityWasContinued` método é chamado quando outro dispositivo toma uma atividade do dispositivo atual. Dependendo do tipo de atividade, como adicionar um novo item a uma lista de tarefas pendentes, o aplicativo pode precisar anular a atividade no dispositivo de envio.

O `UserActivityWillSave` método é chamado antes que qualquer alteração na atividade seja salva e sincronizada entre os dispositivos disponíveis localmente. Você pode usar esse método para fazer alterações no último minuto para a `UserInfo` propriedade `NSUserActivity` da instância antes que ela seja enviada.

### <a name="creating-a-nsuseractivity-instance"></a>Criando uma instância de NSUserActivity

Cada atividade que seu aplicativo deseja fornecer a possibilidade de continuar em outro dispositivo deve ser encapsulada em uma `NSUserActivity` instância. O aplicativo pode criar tantas atividades quantas forem necessárias e a natureza dessas atividades depende da funcionalidade e dos recursos do aplicativo em questão. Por exemplo, um aplicativo de email pode criar uma atividade para criar uma nova mensagem e outra para ler uma mensagem.

Para nosso aplicativo de exemplo, um `NSUserActivity` novo é criado toda vez que o usuário insere uma nova URL em um dos modos de exibição de navegador da Web com guias. O código a seguir armazena o estado de uma determinada guia:

```csharp
public NSString UserActivityTab1 = new NSString ("com.xamarin.monkeybrowser.tab1");
public NSUserActivity UserActivity { get; set; }
...

UserActivity = new NSUserActivity (UserActivityTab1);
UserActivity.Title = "Weather Tab";
UserActivity.Delegate = new UserActivityDelegate ();

// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);

// Inform Activity that it has been updated
UserActivity.BecomeCurrent ();
```

Ele cria um novo `NSUserActivity` usando um dos tipos de atividade de usuário criados acima e fornece um título legível para a atividade. Ele é anexado a uma instância do `NSUserActivityDelegate` criada acima para observar alterações de estado e informa ao Ios que essa atividade de usuário é a atividade atual.

### <a name="populating-the-userinfo-dictionary"></a>Populando o dicionário UserInfo

Como vimos acima, a `UserInfo` propriedade `NSUserActivity` da classe é um `NSDictionary` dos pares chave-valor usados para definir o estado de uma determinada atividade. Os valores armazenados em `UserInfo` devem ser um dos seguintes tipos: `NSData` `NSNull` `NSDate` `NSDictionary` `NSString` `NSSet` `NSURL`,,,,,,, ou. `NSNumber` `NSArray` `NSURL`os valores de dados que apontam para documentos do iCloud serão automaticamente ajustados para que apontem para os mesmos documentos em um dispositivo receptor.

No exemplo acima, criamos um `NSMutableDictionary` objeto e o populamos com uma única chave fornecendo uma URL que o usuário estava exibindo atualmente na guia fornecida. O `AddUserInfoEntries` método da atividade do usuário foi usado para atualizar a atividade com os dados que serão usados para restaurar a atividade no dispositivo receptor:

```csharp
// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);
```

A Apple sugere manter as informações enviadas ao mínimo mais simples para garantir que a atividade seja enviada em tempo hábil para o dispositivo receptor. Se forem necessárias informações maiores, como uma imagem anexada a um documento que deve ser editada, você deverá usar fluxos de continuação. Consulte a seção [fluxos de continuação de suporte](#supporting-continuation-streams) abaixo para obter mais detalhes.

### <a name="continuing-an-activity"></a>Continuando uma atividade

A entrega informará automaticamente dispositivos iOS e OS X locais que estão em proximidade física com o dispositivo de origem e que entraram na mesma conta do iCloud, da disponibilidade das atividades do usuário continuável. Se o usuário optar por continuar uma atividade em um novo dispositivo, o sistema iniciará o aplicativo apropriado (com base na ID da equipe e no tipo de atividade) `AppDelegate` e as informações que sua continuação precisará ocorrer.

Primeiro, o `WillContinueUserActivityWithType` método é chamado para que o aplicativo possa informar ao usuário que a continuação está prestes a começar. Usamos o seguinte código no arquivo **AppDelegate.cs** do nosso aplicativo de exemplo para lidar com uma continuação iniciando:

```csharp
public NSString UserActivityTab1 = new NSString ("com.xamarin.monkeybrowser.tab1");
public NSString UserActivityTab2 = new NSString ("com.xamarin.monkeybrowser.tab2");
public NSString UserActivityTab3 = new NSString ("com.xamarin.monkeybrowser.tab3");
public NSString UserActivityTab4 = new NSString ("com.xamarin.monkeybrowser.tab4");
...

public FirstViewController Tab1 { get; set; }
public SecondViewController Tab2 { get; set;}
public ThirdViewController Tab3 { get; set; }
public FourthViewController Tab4 { get; set; }
...

public override bool WillContinueUserActivity (UIApplication application, string userActivityType)
{
    // Report Activity
    Console.WriteLine ("Will Continue Activity: {0}", userActivityType);

    // Take action based on the user activity type
    switch (userActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Inform view that it's going to be modified
        Tab1.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Inform view that it's going to be modified
        Tab2.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Inform view that it's going to be modified
        Tab3.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Inform view that it's going to be modified
        Tab4.PreparingToHandoff ();
        break;
    }

    // Inform system we handled this
    return true;
}
```

No exemplo acima, cada controlador de exibição registra com o `AppDelegate` e tem um método `PreparingToHandoff` público que exibe um indicador de atividade e uma mensagem informando ao usuário que a atividade está prestes a ser enviada para o dispositivo atual. Exemplo:

```csharp
private void ShowBusy(string reason) {

    // Display reason
    BusyText.Text = reason;

    //Define Animation
    UIView.BeginAnimations("Show");
    UIView.SetAnimationDuration(1.0f);

    Handoff.Alpha = 0.5f;

    //Execute Animation
    UIView.CommitAnimations();
}
...

public void PreparingToHandoff() {
    // Inform caller
    ShowBusy ("Continuing Activity...");
}
```

`ContinueUserActivity` O`AppDelegate` de será chamado para realmente continuar a atividade especificada. Novamente, em nosso aplicativo de exemplo:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Report Activity
    Console.WriteLine ("Continuing User Activity: {0}", userActivity.ToString());

    // Get input and output streams from the Activity
    userActivity.GetContinuationStreams ((NSInputStream arg1, NSOutputStream arg2, NSError arg3) => {
        // Send required data via the streams
        // ...
    });

    // Take action based on the Activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Preform handoff
        Tab1.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab1});
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Preform handoff
        Tab2.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab2});
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Preform handoff
        Tab3.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab3});
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Preform handoff
        Tab4.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab4});
        break;
    }

    // Inform system we handled this
    return true;
}
```

O método `PerformHandoff` público de cada controlador de exibição, na verdade, forma a entrega e restaura a atividade no dispositivo atual. No caso do exemplo, ele exibe a mesma URL em uma determinada guia em que o usuário estava navegando em um dispositivo diferente. Exemplo:

```csharp
private void HideBusy() {

    //Define Animation
    UIView.BeginAnimations("Hide");
    UIView.SetAnimationDuration(1.0f);

    Handoff.Alpha = 0f;

    //Execute Animation
    UIView.CommitAnimations();
}
...

public void PerformHandoff(NSUserActivity activity) {

    // Hide busy indicator
    HideBusy ();

    // Extract URL from dictionary
    var url = activity.UserInfo ["Url"].ToString ();

    // Display value
    URL.Text = url;

    // Display the give webpage
    WebView.LoadRequest(new NSUrlRequest(NSUrl.FromString(url)));

    // Save activity
    UserActivity = activity;
    UserActivity.BecomeCurrent ();

}
```

O `ContinueUserActivity` método inclui um `UIApplicationRestorationHandler` que você pode chamar para retomar a atividade baseada em documento ou respondente. Você precisará passar um `NSArray` ou mais objetos restauráveis para o manipulador de restauração quando chamado. Por exemplo:

```csharp
completionHandler (new NSObject[]{Tab4});
```

Para cada objeto passado, seu `RestoreUserActivityState` método será chamado. Cada objeto pode usar os dados no `UserInfo` dicionário para restaurar seu próprio estado. Por exemplo:

```csharp
public override void RestoreUserActivityState (NSUserActivity activity)
{
    base.RestoreUserActivityState (activity);

    // Log activity
    Console.WriteLine ("Restoring Activity {0}", activity.Title);
}
```

Para aplicativos baseados em documento, se você não implementar `ContinueUserActivity` o método ou ele retornar `false`, `UIKit` ou `AppKit` pode retomar a atividade automaticamente. Consulte a seção [entrega de suporte em aplicativos baseados em documento](#supporting-handoff-in-document-based-apps) abaixo para obter mais informações.

### <a name="failing-handoff-gracefully"></a>Falha de entrega normalmente

Como a entrega se baseia na transmissão de informações entre uma coleção com dispositivos iOS e OS X conectados livremente, o processo de transferência pode, às vezes, falhar. Você deve projetar seu aplicativo para lidar com essas falhas normalmente e informar ao usuário todas as situações que surgirem.

Em caso de falha, o `DidFailToContinueUserActivitiy` método `AppDelegate` de será chamado. Por exemplo:

```csharp
public override void DidFailToContinueUserActivitiy (UIApplication application, string userActivityType, NSError error)
{
    // Log information about the failure
    Console.WriteLine ("User Activity {0} failed to continue. Error: {1}", userActivityType, error.LocalizedDescription);
}
```

Você deve usar o fornecido `NSError` para fornecer informações ao usuário sobre a falha.

## <a name="native-app-to-web-browser-handoff"></a>Aplicativo nativo para entrega do navegador da Web

Um usuário pode querer continuar uma atividade sem ter um aplicativo nativo apropriado instalado no dispositivo desejado. Em algumas situações, uma interface baseada na Web pode fornecer a funcionalidade necessária e a atividade ainda pode continuar. Por exemplo, a conta de email do usuário pode fornecer uma interface de usuário de base da Web para compor e ler mensagens.

Se a origem, o aplicativo nativo souber a URL da interface da Web (e a sintaxe necessária para identificar o item determinado que está sendo continuado), ele poderá codificar essas `WebpageURL` informações na propriedade `NSUserActivity` da instância. Se o dispositivo receptor não tiver um aplicativo nativo apropriado instalado para lidar com a continuação, a interface da Web fornecida poderá ser chamada.

## <a name="web-browser-to-native-app-handoff"></a>Navegador da Web para entrega de aplicativo nativo

Se o usuário estava usando uma interface baseada na Web no dispositivo de origem, e um aplicativo nativo no dispositivo receptor declara a parte do domínio da `WebpageURL` Propriedade, então o sistema usará esse aplicativo para lidar com a continuação. O novo dispositivo receberá uma `NSUserActivity` instância que marca o tipo de atividade `BrowsingWeb` como e `WebpageURL` o conterá a URL que o usuário estava visitando `UserInfo` , o dicionário estará vazio.

Para que um aplicativo participe desse tipo de entrega, ele deve reivindicar o domínio em um `com.apple.developer.associated-domains` direito com o formato `<service>:<fully qualified domain name>` (por exemplo: `activity continuation:company.com`).

Se o domínio especificado corresponder ao `WebpageURL` valor de uma propriedade, a entrega baixará uma lista de IDs de aplicativos aprovados do site nesse domínio. O site deve fornecer uma lista de IDs aprovadas em um arquivo JSON assinado chamado **Apple-App-site-Association** (por exemplo `https://company.com/apple-app-site-association`,).

Esse arquivo JSON contém um dicionário que especifica uma lista de IDs de aplicativo no formulário `<team identifier>.<bundle identifier>`. Por exemplo:

```csharp
{
    "activitycontinuation": {
        "apps": [    "YWBN8XTPBJ.com.company.FirstApp",
            "YWBN8XTPBJ.com.company.SecondApp" ]
    }
}
```

Para assinar o arquivo JSON (para que ele tenha o correto `Content-Type` `application/pkcs7-mime`), use o aplicativo de **terminal** e um `openssl` comando com um certificado e uma chave emitidos por uma autoridade de certificação confiável pelo [https://support.apple.com/kb/ht5012](https://support.apple.com/kb/ht5012) Ios (consulte para obter uma lista). Por exemplo:

```csharp
echo '{"activitycontinuation":{"apps":["YWBN8XTPBJ.com.company.FirstApp",
"YWBN8XTPBJ.com.company.SecondApp"]}}' > json.txt

cat json.txt | openssl smime -sign -inkey company.com.key
-signer company.com.pem
-certfile intermediate.pem
-noattr -nodetach
-outform DER > apple-app-site-association
```

O `openssl` comando gera um arquivo JSON assinado que você coloca em seu site na URL **Apple-App-site-Association** . Por exemplo:

```csharp
https://example.com/apple-app-site-association.
```

O aplicativo receberá qualquer atividade cujo `WebpageURL` domínio esteja em seu `com.apple.developer.associated-domains` direito. Somente os `http` protocolos `https` e são compatíveis, qualquer outro protocolo gerará uma exceção.

## <a name="supporting-handoff-in-document-based-apps"></a>Suporte à entrega em aplicativos baseados em documentos

Conforme mencionado acima, no Ios e no os X, os aplicativos baseados em documentos irão automaticamente dar suporte à entrega de documentos baseados no iCloud se o arquivo **info. plist** do aplicativo `NSUbiquitousDocumentUserActivityType`contiver uma `CFBundleDocumentTypes` chave de. Por exemplo:

```xml
<key>CFBundleDocumentTypes</key>
<array>
    <dict>
        <key>CFBundleTypeName</key>
        <string>NSRTFDPboardType</string>
        . . .
        <key>LSItemContentTypes</key>
        <array>
        <string>com.myCompany.rtfd</string>
        </array>
        . . .
        <key>NSUbiquitousDocumentUserActivityType</key>
        <string>com.myCompany.myEditor.editing</string>
    </dict>
</array>
```

Neste exemplo, a cadeia de caracteres é um designador de aplicativo DNS reverso com o nome da atividade acrescentada. Se inserido dessa forma, as entradas do tipo de atividade não precisam ser repetidas na `NSUserActivityTypes` matriz do arquivo **info. plist** .

O objeto de atividade de usuário criado automaticamente (disponível por meio da `UserActivity` Propriedade do documento) pode ser referenciado por outros objetos no aplicativo e usado para restaurar o estado na continuação. Por exemplo, para rastrear a seleção de itens e a posição do documento. Você precisa definir essa propriedade Activities `NeedsSave` como `true` sempre que o estado for alterado e `UpdateUserActivityState` atualizar `UserInfo` o dicionário no método.

A `UserActivity` propriedade pode ser usada de qualquer thread e está de acordo com o protocolo KVO (key-value Observation), portanto, ela pode ser usada para manter um documento em sincronia à medida que ele se move para dentro e para fora do icloud. A `UserActivity` propriedade será invalidada quando o documento for fechado.

Para obter mais informações, consulte o suporte de atividade do usuário da Apple na documentação de [aplicativos baseados em documentos](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/HandoffFundamentals/HandoffFundamentals.html#//apple_ref/doc/uid/TP40014338-CH3-SW5) .

## <a name="supporting-handoff-in-responders"></a>Dando suporte à entrega em respondentes

Você pode associar respondentes (herdados de `UIResponder` no Ios ou `NSResponder` no os X) às atividades definindo suas `UserActivity` Propriedades. O sistema salva automaticamente a `UserActivity` Propriedade nos horários apropriados, chamando o método do `UpdateUserActivityState` Respondente para adicionar dados atuais ao objeto de atividade do usuário usando o `AddUserInfoEntriesFromDictionary` método.

## <a name="supporting-continuation-streams"></a>Fluxos de continuação de suporte

O pode ser situações em que a quantidade de informações necessárias para continuar uma atividade não pode ser transferida com eficiência pela carga inicial de entrega. Nessas situações, o aplicativo de recebimento pode estabelecer um ou mais fluxos entre si mesmo e o aplicativo de origem para transferir os dados.

O aplicativo `SupportsContinuationStreams` `true`de origem definirá a propriedade da instânciacomo.`NSUserActivity` Por exemplo:

```csharp
// Create a new user Activity to support this tab
UserActivity = new NSUserActivity (ThisApp.UserActivityTab1){
    Title = "Weather Tab",
    SupportsContinuationStreams = true
};
UserActivity.Delegate = new UserActivityDelegate ();

// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);

// Inform Activity that it has been updated
UserActivity.BecomeCurrent ();
```

O aplicativo receptor pode então chamar o `GetContinuationStreams` método `NSUserActivity` do em seu `AppDelegate` para estabelecer o fluxo. Por exemplo:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Report Activity
    Console.WriteLine ("Continuing User Activity: {0}", userActivity.ToString());

    // Get input and output streams from the Activity
    userActivity.GetContinuationStreams ((NSInputStream arg1, NSOutputStream arg2, NSError arg3) => {
        // Send required data via the streams
        // ...
    });

    // Take action based on the Activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Preform handoff
        Tab1.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab1});
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Preform handoff
        Tab2.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab2});
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Preform handoff
        Tab3.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab3});
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Preform handoff
        Tab4.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab4});
        break;
    }

    // Inform system we handled this
    return true;
}
```

No dispositivo de origem, o representante de atividade do usuário recebe os fluxos chamando seu `DidReceiveInputStream` método para fornecer os dados solicitados para continuar a atividade do usuário no dispositivo retomado.

Você usará um `NSInputStream` para fornecer acesso somente leitura para dados de fluxo e um `NSOutputStream` fornecer acesso somente gravação. Os fluxos devem ser usados em um modo de solicitação e resposta, em que o aplicativo de recebimento solicita mais dados e o aplicativo de origem o fornece. Assim, os dados gravados no fluxo de saída no dispositivo de origem são lidos do fluxo de entrada no dispositivo em andamento e vice-versa.

Mesmo em situações em que o fluxo de continuação é necessário, deve haver uma comunicação mínima entre os dois aplicativos.

Para obter mais informações, consulte a documentação [usando os fluxos de continuação](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/AdoptingHandoff/AdoptingHandoff.html#//apple_ref/doc/uid/TP40014338-CH2-SW13) da Apple.

## <a name="handoff-best-practices"></a>Práticas recomendadas de entrega

A implementação bem-sucedida da continuação direta de uma atividade do usuário por meio da entrega requer um design cuidadoso devido a todos os vários componentes envolvidos. A Apple sugere a adoção das seguintes práticas recomendadas para seus aplicativos habilitados para entrega:

- Projete suas atividades de usuário para exigir a menor carga possível para relacionar o estado da atividade a ser continuada. Quanto maior a carga, mais tempo levará a continuação para começar.
- Se você precisar transferir grandes quantidades de dados para uma continuação bem-sucedida, leve em conta os custos envolvidos na configuração e na sobrecarga da rede.
- É comum que um aplicativo Mac grande crie atividades do usuário que são manipuladas por vários aplicativos menores e específicos da tarefa em dispositivos iOS. As diferentes versões do aplicativo e do sistema operacional devem ser projetadas para funcionar bem em conjunto ou falhar normalmente.
- Ao especificar os tipos de atividade, use a notação de DNS reverso para evitar colisões. Se uma atividade for específica para um determinado aplicativo, seu nome deverá ser incluído na definição de tipo (por exemplo `com.myCompany.myEditor.editing`,). Se a atividade puder funcionar em vários aplicativos, remova o nome do aplicativo da definição (por exemplo `com.myCompany.editing`).
- Se seu aplicativo precisar atualizar o estado de uma atividade de usuário (`NSUserActivity`), defina `NeedsSave` a propriedade `true`como. Nos momentos apropriados, a entrega chamará o método `UserActivityWillSave` do delegado para que você possa `UserInfo` atualizar o dicionário conforme necessário.
- Como o processo de entrega pode não ser inicializado instantaneamente no dispositivo receptor, você deve `AppDelegate`implementar `WillContinueUserActivity` o e informar ao usuário que uma continuação está prestes a ser iniciada.

## <a name="example-handoff-app"></a>Aplicativo de entrega de exemplo

Como exemplo de uso da entrega em um aplicativo Xamarin. iOS, incluímos o aplicativo de exemplo [**MonkeyBrowser**](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-monkeybrowser) com este guia. O aplicativo tem quatro guias que o usuário pode usar para navegar na Web, cada uma com um determinado tipo de atividade: Clima, favorito, intervalo de café e trabalho.

Em qualquer guia, quando o usuário insere uma nova URL e toca no botão **go** , um novo `NSUserActivity` é criado para essa guia que contém a URL que o usuário está procurando no momento:

[![](handoff-images/handoff01.png "Aplicativo de entrega de exemplo")](handoff-images/handoff01.png#lightbox)

Se outro dos dispositivos do usuário tiver o aplicativo **MonkeyBrowser** instalado, estiver conectado ao icloud usando a mesma conta de usuário, estiver na mesma rede e próximo à proximidade com o dispositivo acima, a atividade de entrega será exibida na tela inicial (na parte inferior canto esquerdo):

[![](handoff-images/handoff02.png "A atividade de entrega exibida na tela inicial no canto inferior esquerdo")](handoff-images/handoff02.png#lightbox)

Se o usuário arrastar para cima no ícone de entrega, o aplicativo será iniciado e a atividade de usuário especificada no `NSUserActivity` será continuada no novo dispositivo:

[![](handoff-images/handoff03.png "A atividade do usuário continuou no novo dispositivo")](handoff-images/handoff03.png#lightbox)

Quando a atividade do usuário for enviada com êxito para outro dispositivo da Apple, o dispositivo `NSUserActivity` de envio receberá uma chamada para o `UserActivityWasContinued` método em `NSUserActivityDelegate` seu para que ele saiba que a atividade do usuário foi transferida com êxito para outra Vice.

## <a name="summary"></a>Resumo

Este artigo deu uma introdução à estrutura de entrega usada para continuar uma atividade do usuário entre vários dispositivos Apple do usuário. Em seguida, ele mostrou como habilitar e implementar a entrega em um aplicativo Xamarin. iOS. Por fim, ele abordou os diferentes tipos de continuação de entrega disponíveis e as práticas recomendadas de entrega.

## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [Exemplo de MonkeyBrowser](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-monkeybrowser)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [O que há de novo no iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guia do HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [Diretrizes de interface do usuário do HomeKit](https://developer.apple.com/homekit/ui-guidelines/)
- [Referência da estrutura do HomeKit](https://developer.apple.com/library/ios/home_kit_framework_ref)
