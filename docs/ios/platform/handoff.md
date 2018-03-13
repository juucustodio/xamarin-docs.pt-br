---
title: Entrega
description: "Este artigo abrange trabalhando com entrega em um aplicativo xamarin para transferir as atividades do usuário entre os aplicativos em execução no usuário 's outros dispositivos."
ms.topic: article
ms.prod: xamarin
ms.assetid: 405F966A-4085-4621-AA15-33D663AD15CD
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 25220f37433037b55f13c4de5a07c0c09173a269
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="handoff"></a>Entrega

_Este artigo abrange trabalhando com entrega em um aplicativo xamarin para transferir as atividades do usuário entre os aplicativos em execução no usuário 's outros dispositivos._

Apple introduziu entrega no iOS 8 e OS X Yosemite (10.10) para fornecer um mecanismo comum para o usuário iniciadas em um dos seus dispositivos, de atividades de transferência para outro dispositivo executando o mesmo aplicativo ou outro aplicativo que ofereça suporte a mesma atividade.

[![](handoff-images/handoff02.png "Um exemplo de execução de uma operação de entrega")](handoff-images/handoff02.png#lightbox)

Este artigo dar uma olhada rápida permitindo que a atividade de compartilhamento em um aplicativo xamarin e aborda a estrutura de entrega em detalhes:

## <a name="about-handoff"></a>Sobre a entrega

Entrega (também conhecido como continuidade) foi introduzida pela Apple no iOS 8 e OS X Yosemite (10.10) como uma maneira para que o usuário iniciar uma atividade em um de seus dispositivos (iOS ou Mac) e continuar mesmo que a atividade em outro seus dispositivos (conforme identificado pelo iClou do usuário movidos conta).

Entrega foi expandida no iOS 9 para também dar suporte a novos e aprimorados recursos de pesquisa. Para obter mais informações, consulte nosso [aprimoramentos da pesquisa](~/ios/platform/search/index.md) documentação.

Por exemplo, o usuário pode iniciar um email em seu iPhone e perfeitamente continuar o email no seu Mac, com todas as mesmas informações de mensagem preenchido e o cursor no mesmo local que eles o deixou em iOS.

Qualquer um dos seus aplicativos que compartilham o mesmo _identificação de equipe_ são qualificados para usando a entrega continue as atividades do usuário em aplicativos como esses aplicativos são entregues por meio da iTunes Store ou assinado por um desenvolvedor registrado (para Mac, Enterprise ou aplicativos Ad-Hoc).

Qualquer `NSDocument` ou `UIDocument` aplicativos com base têm automaticamente entrega suporte interno e requerem alterações mínimas para dar suporte a entrega.

### <a name="continuing-user-activities"></a>Continuando atividades do usuário

O `NSUserActivity` classe (junto com algumas pequenas alterações em `UIKit` e `AppKit`) oferece suporte à definição de atividade do usuário que pode potencialmente ser em outro dispositivo do usuário.

Para uma atividade a ser transmitida para outro dispositivo do usuário, ele deve ser encapsulado em uma instância `NSUserActivity`, marcado como o _atividade atual_, ter sua carga definido (os dados usados para executar a continuação) e o atividade, em seguida, deve ser transmitida para esse dispositivo.

Entrega passa o mínimo de informações para definir a atividade para ser contínua, com pacotes de dados maiores que estão sendo sincronizados por meio do iCloud.

No dispositivo de recebimento, o usuário receberá uma notificação de que uma atividade está disponível para acompanhamento. Se o usuário optar por continuar a atividade no novo dispositivo, o aplicativo especificado é iniciado (se ainda não estiver em execução) e a carga da `NSUserActivity` é usada para reiniciar a atividade.

[![](handoff-images/handoffinteractions.png "Uma visão geral das atividades de usuário contínua")](handoff-images/handoffinteractions.png#lightbox)

Somente os aplicativos que compartilham o mesmo ID de equipe de desenvolvedor e responderam a um determinado _tipo de atividade_ são qualificados para a continuação. Um aplicativo define os tipos de atividade que ele suporta sob o `NSUserActivityTypes` chave do seu **Info. plist** arquivo. Dito isto, um dispositivo continuando escolhe o aplicativo para executar a continuação com base na ID da equipe, tipo de atividade e, opcionalmente, o _título da atividade_.

O aplicativo receptor usa as informações do `NSUserActivity`do `UserInfo` dicionário para configurar sua interface de usuário e restaurar o estado de cada atividade para que a transição será exibida transparente para o usuário final.

Se a continuação requer mais informações que podem ser enviados com eficiência a um `NSUserActivity`, a reiniciar o aplicativo pode enviar uma chamada para o aplicativo de origem e estabelecer um ou mais fluxos para transmitir os dados necessários. Por exemplo, se a atividade foi editando um documento de texto grandes com várias imagens, streaming seria necessário para transferir as informações necessárias para continuar a atividade no dispositivo receptor. Para obter mais informações, consulte o [suporte continuação fluxos](#Supporting-Continuation-Streams) seção abaixo.

Como mencionado acima, `NSDocument` ou `UIDocument` aplicativos com base têm automaticamente entrega suporte interno. Para obter mais informações, consulte o [entrega com suporte em aplicativos baseados em documentos](#Supporting-Handoff-in-Document-Based-Apps) seção abaixo.

### <a name="the-nsuseractivity-class"></a>A classe NSUserActivity

O `NSUserActivity` classe é o objeto principal em uma troca de entrega e é usada para encapsular o estado de uma atividade de usuário que está disponível para acompanhamento. Um aplicativo instanciará uma cópia de `NSUserActivity` para qualquer atividade oferece suporte e desejar continuar em outro dispositivo. Por exemplo, o editor de documento criaria uma atividade para cada documento aberto no momento. No entanto, apenas o documento mais à frente (exibido na dianteira janela ou guia) é o _atividade atual_ e, portanto, disponíveis para acompanhamento.

Uma instância de `NSUserActivity` é identificado pelo seu `ActivityType` e `Title` propriedades. O `UserInfo` propriedade de dicionário é usada para transmitir informações sobre o estado da atividade. Definir o `NeedsSave` propriedade `true` se você quiser lentas para carregar as informações de estado por meio a `NSUserActivity`do delegado. Use o `AddUserInfoEntries` método para mesclar os novos dados de outros clientes para o `UserInfo` dicionário conforme necessário para preservar o estado da atividade.

### <a name="the-nsuseractivitydelegate-class"></a>A classe NSUserActivityDelegate

O `NSUserActivityDelegate` é usada para manter informações em um `NSUserActivity`do `UserInfo` dicionário atualizado e sincronizado com o estado atual da atividade. Quando o sistema precisa das informações na atividade a ser atualizado (como antes da continuação em outro dispositivo), ele chama o `UserActivityWillSave` método do representante.

Você precisará implementar o `UserActivityWillSave` método e faz qualquer alteração de `NSUserActivity` (como `UserInfo`, `Title`, etc.) para garantir que ele ainda reflete o estado da atividade atual. Quando o sistema chama o `UserActivityWillSave` método, o `NeedsSave` sinalizador será limpo. Se você modificar qualquer uma das propriedades da atividade de dados, você precisará definir `NeedsSave` para `true` novamente.

Em vez de usar o `UserActivityWillSave` método apresentado acima, você pode ter opcionalmente `UIKit` ou `AppKit` gerenciar a atividade de usuário automaticamente. Para fazer isso, defina o objeto de Respondente `UserActivity` propriedade e implementar o `UpdateUserActivityState` método. Consulte o [entrega com suporte no respondentes](#Supporting-Handoff-in-Responders) seção abaixo para obter mais informações.

### <a name="app-framework-support"></a>Suporte à estrutura de aplicativo

Ambos `UIKit` (iOS) e `AppKit` (OS X) dão suporte interno para entrega no `NSDocument`, Respondente (`UIResponder`/`NSResponder`), e `AppDelegate` classes. Embora cada sistema operacional implementa entrega ligeiramente diferente, o mecanismo básico e as APIs são os mesmos.

#### <a name="user-activities-in-document-based-apps"></a>Atividades de usuário em aplicativos baseados no documento

Com base em documento aplicativos iOS e OS X automaticamente têm suporte de entrega interno. Para ativar esse suporte, você precisará adicionar um `NSUbiquitousDocumentUserActivityType` chave e valor para cada `CFBundleDocumentTypes` entrada no aplicativo do **Info. plist** arquivo.

Se essa chave estiver presente, ambos `NSDocument` e `UIDocument` criar automaticamente `NSUserActivity` instâncias para documentos com base no iCloud do tipo especificado. Você precisará fornecer um tipo de atividade para cada tipo de documento que suporte o aplicativo e vários tipos de documentos podem usar o mesmo tipo de atividade. Ambos `NSDocument` e `UIDocument` preencher automaticamente o `UserInfo` propriedade o `NSUserActivity` com seus `FileURL` valor da propriedade.

Nos X, o `NSUserActivity` gerenciados pelo `AppKit` e associado respondentes automaticamente se tornar a atividade atual quando a janela do documento se torna a janela principal. No iOS, para `NSUserActivity` objetos gerenciados pelo `UIKit`, você deve chamar `BecomeCurrent` método explicitamente ou ter o documento `UserActivity` propriedade definida em uma `UIViewController` quando o aplicativo vai para o primeiro plano.

`AppKit` automaticamente restaurar quaisquer `UserActivity` propriedade criada dessa maneira nos X. Isso ocorre se o `ContinueUserActivity` método retorna `false` ou se for não implementada. Nessa situação, o documento é aberto com o `OpenDocument` método o `NSDocumentController` e, em seguida, receberá um `RestoreUserActivityState` chamada de método.

Consulte o [entrega com suporte em aplicativos baseados em documentos](#Supporting-Handoff-in-Document-Based-Apps) seção abaixo para obter mais informações.

#### <a name="user-activities-and-responders"></a>Respondentes e as atividades do usuário

Ambos `UIKit` e `AppKit` pode gerenciar automaticamente uma atividade de usuário se você defini-lo como um objeto de Respondente `UserActivity` propriedade. Se o estado tiver sido modificado, você precisará definir o `NeedsSave` propriedade do Respondente `UserActivity` para `true`. O sistema salvará automaticamente o `UserActivity` quando necessário, após conceder, a hora do Respondente para atualizar o estado chamando seu `UpdateUserActivityState` método.

Se vários Respondentes compartilham um único `NSUserActivity` instância, eles recebem um `UpdateUserActivityState` retorno de chamada quando o sistema atualiza o objeto de atividade do usuário. O Respondente precisa chamar o `AddUserInfoEntries` para atualizar o `NSUserActivity`do `UserInfo` dicionário para refletir o estado atual da atividade neste momento. O `UserInfo` dicionário é limpo antes de cada `UpdateUserActivityState` chamar.

Para desassociar próprio de uma atividade, um Respondente pode definir seu `UserActivity` propriedade `null`. Quando uma estrutura de aplicativo gerenciado `NSUserActivity` instância não tem mais Respondentes associados ou documentos, ele é automaticamente invalidado.

Consulte o [entrega com suporte no respondentes](#Supporting-Handoff-in-Responders) seção abaixo para obter mais informações.

#### <a name="user-activities-and-the-appdelegate"></a>As atividades do usuário e o AppDelegate

Seu aplicativo `AppDelegate` é seu ponto de entrada principal ao tratar uma continuação de entrega. Quando o usuário responde a uma notificação de entrega, o aplicativo apropriado é iniciado (se não estiver em execução) e o `WillContinueUserActivityWithType` método o `AppDelegate` será chamado. Neste ponto, o aplicativo deve informar ao usuário que está iniciando a continuação.

O `NSUserActivity` instância é fornecida quando o `AppDelegate`do `ContinueUserActivity` método é chamado. Neste ponto, você deve configurar a interface do usuário do aplicativo e continuar a atividade especificada.

Consulte o [entrega implementando](#Implementing-Handoff) seção abaixo para obter mais informações.

## <a name="enabling-handoff-in-a-xamarin-app"></a>Habilitar a entrega em um aplicativo Xamarin

Por causa dos requisitos de segurança impostos pelo entrega, um aplicativo xamarin que usa a estrutura de entrega deve ser configurado corretamente no Portal do desenvolvedor Apple e no arquivo de projeto xamarin.

Faça o seguinte:

1. Faça logon na [Portal do desenvolvedor Apple](http://developer.apple.com).
2. Clique em **certificados, identificadores e perfis**.
3. Se você ainda não fez isso, clique em **identificadores** e crie uma ID para seu aplicativo (por exemplo, `com.company.appname`), ou editar sua ID existente.
4. Certifique-se de que o **iCloud** serviço foi verificado para a ID especificada: 

    [![](handoff-images/provision01.png "Habilitar o serviço iCloud da identificação fornecida")](handoff-images/provision01.png#lightbox)
5. Salve as alterações.
4. Clique em **perfis de provisionamento** > **desenvolvimento** e criar um novo perfil de provisionamento para você de desenvolvimento de aplicativo: 

    [![](handoff-images/provision02.png "Criar um novo perfil para o aplicativo de provisionamento de desenvolvimento")](handoff-images/provision02.png#lightbox)
5. Baixe e instale o novo perfil de provisionamento ou use Xcode para baixar e instalar o perfil.
6. Edite as opções de projeto xamarin e certifique-se de que você está usando o perfil de provisionamento que você acabou de criar: 

    [![](handoff-images/provision03.png "Selecione o perfil de provisionamento que acabou de criar")](handoff-images/provision03.png#lightbox)
7. Em seguida, edite o **Info. plist** de arquivo e certifique-se de que você está usando a ID do aplicativo que foi usado para criar o perfil de provisionamento: 

    [![](handoff-images/provision04.png "Definir a ID de aplicativo")](handoff-images/provision04.png#lightbox)
8. Role até a **modos de segundo plano** seção e verifique os seguintes itens: 

    [![](handoff-images/provision05.png "Habilitar os modos de plano de fundo necessária")](handoff-images/provision05.png#lightbox)
9. Salve as alterações para todos os arquivos.

Com essas configurações em vigor, o aplicativo está pronto para acessar as APIs de estrutura de entrega. Para obter informações detalhadas sobre o provisionamento, consulte nosso [aprovisionamento](~/ios/get-started/installation/device-provisioning/index.md) e [de provisionamento do seu aplicativo](~/ios/get-started/installation/device-provisioning/index.md) guias.

## <a name="implementing-handoff"></a>Implementação de entrega

Atividades de usuário podem ser continuadas entre aplicativos que são assinados com o mesmo ID de equipe de desenvolvedor e suportam o mesmo tipo de atividade. Implementação de entrega em um aplicativo xamarin exige que você criar um objeto de atividade do usuário (em `UIKit` ou `AppKit`), o estado do objeto para acompanhar a atividade de atualização e continuar a atividade em um dispositivo de recebimento.

### <a name="identifying-user-activities"></a>Identificar as atividades do usuário

A primeira etapa na implementação de entrega é identificar os tipos de atividades do usuário que seu aplicativo aceita e ver qual essas atividades é bons candidatos para continuação em outro dispositivo. Por exemplo: um aplicativo ToDo pode dar suporte a edição de itens como um _tipo de atividade de usuário_e oferece suporte a navegação a lista de itens disponíveis como outro.

Um aplicativo pode criar vários tipos de atividade de usuário porque são necessários, uma para qualquer função que fornece o aplicativo. Para cada tipo de atividade de usuário, o aplicativo precisa controlar quando uma atividade do tipo começa e termina e é necessário manter as informações atualizadas para continuar essa tarefa em outro dispositivo.

Atividades de usuário podem continuar em qualquer aplicativo assinado com a mesma ID de equipe sem qualquer mapeamento entre os aplicativos de envio e recebimento. Por exemplo, um determinado aplicativo pode criar quatro tipos diferentes de atividades, que são consumidos por aplicativos diferentes, individuais em outro dispositivo. Isso é uma ocorrência comum entre a versão do Mac do aplicativo (que pode ter muitos recursos e funções) e aplicativos do iOS, em que cada aplicativo é menor e se concentrem em uma tarefa específica.

### <a name="creating-activity-type-identifiers"></a>Criação de identificadores de tipo de atividade

O _identificador de tipo de atividade_ é uma cadeia de caracteres curta adicionada para o `NSUserActivityTypes` matriz do aplicativo **Info. plist** arquivo usado para identificar exclusivamente um determinado tipo de atividade do usuário. Haverá uma entrada na matriz para cada atividade que o aplicativo oferece suporte. Apple sugere usando uma notação inversa de DNS de estilo para o identificador de tipo de atividade para evitar colisões. Por exemplo: `com.company-name.appname.activity` para aplicativos específicos com base em atividades ou `com.company-name.activity` para atividades que podem ser executados em vários aplicativos.

O identificador de tipo de atividade é usado ao criar um `NSUserActivity` instância para identificar o tipo de atividade. Quando uma atividade é continuada em outro dispositivo, o tipo de atividade (junto com a ID do aplicativo Team) determina qual aplicativo para iniciá-lo para continuar a atividade.

Por exemplo, vamos criar um aplicativo de exemplo chamado **MonkeyBrowser** ([baixar aqui](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/)). Este aplicativo apresentará quatro guias, cada um com um aberto de URL diferente em uma exibição de navegador da web. O usuário será capaz de continuar a qualquer uma das guias em um dispositivo iOS diferente executando o aplicativo.

Para criar os identificadores de tipo de atividade necessário para dar suporte a esse comportamento, edite o **Info. plist** de arquivo e alterne para o **fonte** exibição. Adicionar um `NSUserActivityTypes` chave e crie os seguintes identificadores:

[![](handoff-images/type01.png "A chave de NSUserActivityTypes e identificadores necessários no editor plist")](handoff-images/type01.png#lightbox)

Criamos quatro novo tipo de identificadores de atividade, uma para cada uma das guias no exemplo **MonkeyBrowser** aplicativo. Ao criar seus próprios aplicativos, substitua o conteúdo da `NSUserActivityTypes` matriz com os identificadores de tipo de atividade específica para as atividades do seu aplicativo oferece suporte.

### <a name="tracking-user-activity-changes"></a>Controle de alterações de atividade de usuário

Quando criamos uma nova instância do `NSUserActivity` classe especificaremos um `NSUserActivityDelegate` instância para controlar alterações de estado da atividade. Por exemplo, o código a seguir pode ser usado para controlar alterações de estado:

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

O `UserActivityReceivedData` método é chamado quando um fluxo de continuação recebeu dados de um dispositivo de envio. Para obter mais informações, consulte o [suporte continuação fluxos](#Supporting-Continuation-Streams) seção abaixo.

O `UserActivityWasContinued` método é chamado quando outro dispositivo em uma atividade do dispositivo atual. Dependendo do tipo de atividade, como adicionar um novo item à lista de tarefas, o aplicativo pode precisar cancelar a atividade no dispositivo de envio.

O `UserActivityWillSave` método é chamado antes de quaisquer alterações para a atividade são salvos e sincronizadas em todos os dispositivos disponíveis localmente. Você pode usar esse método para fazer alterações de última hora para o `UserInfo` propriedade o `NSUserActivity` instância antes de serem enviado.

### <a name="creating-a-nsuseractivity-instance"></a>Criando uma instância de NSUserActivity

Cada atividade que seu aplicativo deseja fornecer a possibilidade de continuar em outro dispositivo deve ser encapsulada em um `NSUserActivity` instância. O aplicativo pode criar quantas atividades conforme necessário e a natureza dessas atividades é depende da funcionalidade e recursos do aplicativo em questão. Por exemplo, um aplicativo de email pode criar uma atividade para criar uma nova mensagem e outro para ler uma mensagem.

Para nosso aplicativo de exemplo, um novo `NSUserActivity` é criado sempre que o usuário insere uma nova URL em uma exibição de navegador da web com guias. O código a seguir armazena o estado de uma guia:

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

Cria um novo `NSUserActivity` usando um tipo de atividade de usuário criada acima e fornece um título legível para a atividade. Anexa a uma instância do `NSUserActivityDelegate` criado acima para inspecionar o estado muda e informa iOS que a atividade de usuário é a atividade atual.

### <a name="populating-the-userinfo-dictionary"></a>Preenchendo o dicionário UserInfo

Como vimos acima, o `UserInfo` propriedade o `NSUserActivity` classe é um `NSDictionary` de pares chave-valor usados para definir o estado de uma determinada atividade. Os valores armazenados em `UserInfo` deve ser um dos seguintes tipos: `NSArray`, `NSData`, `NSDate`, `NSDictionary`, `NSNull`, `NSNumber`, `NSSet`, `NSString`, ou `NSURL`. `NSURL` valores de dados que apontam para iCloud documentos serão ajustados automaticamente para que eles apontem para os mesmos documentos em um dispositivo de recebimento.

No exemplo acima, criamos um `NSMutableDictionary` de objeto e preenchido, com uma única chave fornecendo o URL que o usuário foi exibindo no momento na guia determinada. O `AddUserInfoEntries` método da atividade de usuário foi usado para atualizar a atividade com os dados que serão usados para restaurar a atividade no dispositivo receptor:

```csharp
// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);
```

Apple sugerir manter as informações enviadas para o mínimo absoluto para garantir que a atividade é enviada no tempo adequado para o dispositivo de recebimento. Se informações maior for necessárias, como uma imagem anexada a um documento ser editado precisa ser enviada, você deve usar fluxos de continuação. Consulte o [suporte continuação fluxos](#Supporting-Continuation-Streams) seção abaixo para obter mais detalhes.

### <a name="continuing-an-activity"></a>Continuar uma atividade

Entrega automaticamente informará local dispositivos iOS e OS X que estejam em proximidade física para o dispositivo de origem e assinado na mesma conta do iCloud, da disponibilidade de continuidade de atividades de usuário. Se o usuário optar por continuar uma atividade em um novo dispositivo, o sistema iniciará o aplicativo apropriado (com base na ID de equipe e tipo de atividade) e informações de seu `AppDelegate` continuação precisa ocorrer.

Primeiro, o `WillContinueUserActivityWithType` método é chamado para o aplicativo pode informar ao usuário que a continuação está prestes a começar. Podemos usar o seguinte código no **appdelegate. CS** arquivo do nosso aplicativo de exemplo para lidar com uma continuação iniciar:

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

No exemplo acima, cada controlador de exibição registra com o `AppDelegate` e tem um público `PreparingToHandoff` método que exibe um indicador de atividade e uma mensagem informando ao usuário que a atividade está prestes a ser entregue ao dispositivo atual. Exemplo:

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

O `ContinueUserActivity` do `AppDelegate` será chamado para continuar, na verdade, a atividade especificada. Novamente, do nosso aplicativo de exemplo:

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

O público `PerformHandoff` método de cada controlador de exibição, na verdade, valida ou não a entrega e restaura a atividade do dispositivo atual. No caso do exemplo, ele exibe a mesma URL em uma guia que o usuário foi navegando em um dispositivo diferente. Exemplo:

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

O `ContinueUserActivity` método inclui um `UIApplicationRestorationHandler` que você pode chamar para documento ou Respondente com base em retomada de atividade. Você precisará passar um `NSArray` ou objetos restauráveis ao manipulador de restauração, quando chamado. Por exemplo:

```csharp
completionHandler (new NSObject[]{Tab4});
```

Para cada objeto passado, seu `RestoreUserActivityState` método será chamado. Cada objeto, em seguida, pode usar os dados de `UserInfo` dicionário para restaurar seu próprio estado. Por exemplo:

```csharp
public override void RestoreUserActivityState (NSUserActivity activity)
{
    base.RestoreUserActivityState (activity);

    // Log activity
    Console.WriteLine ("Restoring Activity {0}", activity.Title);
}
```

Para aplicativos baseados em documentos, se você não implemente o `ContinueUserActivity` ou método retorna `false`, `UIKit` ou `AppKit` automaticamente pode retomar a atividade. Consulte o [entrega com suporte em aplicativos baseados em documentos](#Supporting-Handoff-in-Document-Based-Apps) seção abaixo para obter mais informações.

### <a name="failing-handoff-gracefully"></a>Falha na entrega normalmente

Uma vez que entrega depende a transmissão de informações entre um coleção vagamente conectada dispositivos iOS e OS X, o processo de transferência pode falhar. Você deve projetar seu aplicativo para tratar essas falhas normalmente e informar o usuário de qualquer situações que podem surgir.

Em caso de falha, o `DidFailToContinueUserActivitiy` método o `AppDelegate` será chamado. Por exemplo:

```csharp
public override void DidFailToContinueUserActivitiy (UIApplication application, string userActivityType, NSError error)
{
    // Log information about the failure
    Console.WriteLine ("User Activity {0} failed to continue. Error: {1}", userActivityType, error.LocalizedDescription);
}
```

Você deve usar o fornecido `NSError` para fornecer informações ao usuário sobre a falha.

## <a name="native-app-to-web-browser-handoff"></a>Aplicativo nativo para entrega de navegador da Web

Um usuário pode desejar continuar uma atividade sem a necessidade de um aplicativo nativo apropriado instalado no dispositivo desejado. Em algumas situações, uma interface baseada na web pode fornecer a funcionalidade necessária e a atividade ainda pode ser continuada. Por exemplo, conta de email do usuário pode fornecer uma interface de usuário baseada na web para compor e ler mensagens.

Se o aplicativo de origem, nativo souber a URL para a interface da web (e a sintaxe necessária para identificar o item fornecido sendo continuação), ele pode codificar essas informações no `WebpageURL` propriedade o `NSUserActivity` instância. Se o dispositivo de recebimento não tiver um aplicativo nativo apropriado instalado para lidar com a continuação, a interface da web fornecido pode ser chamada.

## <a name="web-browser-to-native-app-handoff"></a>Navegador da Web para entrega do aplicativo nativo

Se o usuário estava usando uma interface baseada na web no dispositivo de origem e a parte do domínio de declarações de um aplicativo nativo no dispositivo receptor de `WebpageURL` propriedade e, em seguida, o sistema usará o identificador a continuação do aplicativo. Receberão o novo dispositivo um `NSUserActivity` instância que marca o tipo de atividade como `BrowsingWeb` e `WebpageURL` conterá a URL foi visitando o usuário, o `UserInfo` dicionário está vazio.

Para um aplicativo participar desse tipo de entrega, ele deve declaração o domínio em um `com.apple.developer.associated-domains` autorização com o formato `<service>:<fully qualified domain name>` (por exemplo: `activity continuation:company.com`).

Se o domínio especificado corresponder um `WebpageURL` valor da propriedade, entrega baixa uma lista de IDs de aplicativo aprovado do site no domínio. O site deve fornecer uma lista de IDs aprovados em um arquivo JSON assinado chamado **apple-aplicativo-associação de site** (por exemplo, `https://company.com/apple-app-site-association`).

Esse arquivo JSON contém um dicionário que especifica uma lista de IDs de aplicativo no formato `<team identifier>.<bundle identifier>`. Por exemplo:

```csharp
{
    "activitycontinuation": {
        "apps": [    "YWBN8XTPBJ.com.company.FirstApp",
            "YWBN8XTPBJ.com.company.SecondApp" ]
    }
}
```

Para assinar o arquivo JSON (para que ele tenha o correto `Content-Type` de `application/pkcs7-mime`), use o **Terminal** aplicativo e um `openssl` com um certificado e chave emitido por uma autoridade de certificação confiada para iOS (consulte [ http://support.Apple.com/kb/ht5012](http://support.apple.com/kb/ht5012) para obter uma lista). Por exemplo:

```csharp
echo '{"activitycontinuation":{"apps":["YWBN8XTPBJ.com.company.FirstApp",
"YWBN8XTPBJ.com.company.SecondApp"]}}' > json.txt

cat json.txt | openssl smime -sign -inkey company.com.key
-signer company.com.pem
-certfile intermediate.pem
-noattr -nodetach
-outform DER > apple-app-site-association
```

O `openssl` comando gera um arquivo JSON assinado que você coloca no seu site no **apple-aplicativo-site-associação** URL. Por exemplo:

```csharp
https://example.com/apple-app-site-association.
```

O aplicativo receberá qualquer atividade cujo `WebpageURL` domínio está em seu `com.apple.developer.associated-domains` direito. Somente o `http` e `https` protocolos são suporte, qualquer outro protocolo gerará uma exceção.

## <a name="supporting-handoff-in-document-based-apps"></a>Entrega de suporte em aplicativos baseados no documento

Como mencionado anteriormente, no iOS e OS X, entrega de documentos com base no iCloud automaticamente oferecerá suporte a aplicativos baseados no documento se o aplicativo **Info. plist** arquivo contém um `CFBundleDocumentTypes` chave de `NSUbiquitousDocumentUserActivityType`. Por exemplo:

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

Neste exemplo, a cadeia de caracteres é um designador de aplicativo inversa de DNS com o nome da atividade anexado. Se inserido dessa maneira, as entradas de tipo de atividade não precisam ser repetido em de `NSUserActivityTypes` matriz do **Info. plist** arquivo.

O objeto de atividade de usuário criados automaticamente (disponíveis por meio do documento `UserActivity` propriedade) pode ser referenciado por outros objetos no aplicativo e usados para restaurar o estado de continuação. Por exemplo, para acompanhar item seleção e documento posição. Você precisará definir essa atividades `NeedsSave` propriedade `true` sempre que o estado de alterações e atualizar o `UserInfo` dicionário no `UpdateUserActivityState` método.

O `UserActivity` propriedade pode ser usada de qualquer thread e está em conformidade com o protocolo (KVO) observando chave-valor, para que possa ser usada para manter um documento em sincronização conforme ele move e sair do iCloud. O `UserActivity` propriedade será invalidada quando o documento é fechado.

Para obter mais informações, consulte da Apple [suporte de atividade do usuário em aplicativos baseados em documentos](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/HandoffFundamentals/HandoffFundamentals.html#//apple_ref/doc/uid/TP40014338-CH3-SW5) documentação.

## <a name="supporting-handoff-in-responders"></a>Dando suporte a entrega em respondentes

Você pode associar respondentes (herdada do `UIResponder` no iOS ou `NSResponder` nos X) para atividades, definindo suas `UserActivity` propriedades. O sistema salva automaticamente o `UserActivity` propriedade em apropriada vezes, chamando o Respondente `UpdateUserActivityState` método para adicionar dados atuais para o objeto de atividade de usuário usando o `AddUserInfoEntriesFromDictionary` método.

## <a name="supporting-continuation-streams"></a>Suporte a fluxos de continuação

O pode ser situações em que a quantidade de informações necessárias para continuar a uma atividade não pode ser transferida com eficiência, a carga inicial de entrega. Nessas situações, o aplicativo receptor pode estabelecer uma ou mais fluxo entre ele mesmo e o aplicativo de origem para transferir os dados.

O aplicativo de origem será definida a `SupportsContinuationStreams` propriedade o `NSUserActivity` de instância para `true`. Por exemplo:

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

O aplicativo receptor pode chamar o `GetContinuationStreams` método o `NSUserActivity` no seu `AppDelegate` para estabelecer o fluxo. Por exemplo:

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

No dispositivo de origem, o representante de atividade de usuário recebe os fluxos chamando seu `DidReceiveInputStream` método para fornecer os dados solicitados para continuar a atividade de usuário no dispositivo de retomada.

Você usará um `NSInputStream` para fornecer acesso somente leitura para transmitir dados e um `NSOutputStream` fornecer acesso somente gravação. Os fluxos de devem ser usados em um modo de solicitação e resposta, onde o aplicativo receptor solicita mais dados e fornece o aplicativo de origem. Para que os dados gravados no fluxo de saída no dispositivo de origem é lido do fluxo de entrada no dispositivo contínuo e vice-versa.

Mesmo em situações em que o fluxo de continuação são necessários, deve haver um mínimo de volta em ambas as direções a comunicação entre os dois aplicativos.

Para obter mais informações, consulte da Apple [usando fluxos de continuação](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/AdoptingHandoff/AdoptingHandoff.html#//apple_ref/doc/uid/TP40014338-CH2-SW13) documentação.

## <a name="handoff-best-practices"></a>Práticas recomendadas de entrega

A implementação bem-sucedida da continuação direta de uma atividade de usuário por meio de entrega exige design cuidadoso devido a todos os vários componentes envolvidos. Apple sugere adotando as práticas recomendadas de acompanhamento para seus aplicativos de entrega habilitado:

- Crie suas atividades de usuário para exigir a menor carga possível relacionar o estado da atividade para continuar. Quanto maior a carga, mais tempo levará a continuação para iniciar.
- Se você precisar transferir grandes quantidades de dados para continuação bem-sucedida, leve em consideração os custos envolvidos na sobrecarga de rede e configuração.
- É comum para um aplicativo do Mac grande criar atividades de usuário que são tratados por vários, menor, aplicativos específicos de tarefas em dispositivos iOS. O aplicativo diferente e versões do sistema operacional devem ser criadas para funcionar bem juntos ou falhar de maneira elegante.
- Ao especificar seus tipos de atividade, use a notação inversa de DNS para evitar colisões. Se uma atividade é específica para um determinado aplicativo, o nome deve ser incluído na definição do tipo (por exemplo `com.myCompany.myEditor.editing`). Se a atividade pode funcionar em vários aplicativos, remova o nome do aplicativo da definição de (por exemplo `com.myCompany.editing`).
- Se seu aplicativo precisa atualizar o estado de uma atividade de usuário (`NSUserActivity`) definir o `NeedsSave` propriedade `true`. Em momentos apropriados, entrega chamará o delegado `UserActivityWillSave` método para que você possa atualizar o `UserInfo` dicionário conforme necessário.
- Como o processo de entrega não pode inicializar instantaneamente no dispositivo receptor, você deve implementar o `AppDelegate`do `WillContinueUserActivity` e informar ao usuário que uma continuação está prestes a iniciar.

## <a name="example-handoff-app"></a>Exemplo de aplicativo de entrega

Como um exemplo do uso de entrega em um aplicativo xamarin, incluímos o [ **MonkeyBrowser** ](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/) aplicativo de exemplo com este guia. O aplicativo tem quatro guias que o usuário pode usar para navegar na web, cada um com um tipo de atividade determinado: clima, Favoritos, café e trabalho.

Em qualquer guia, quando o usuário insere uma nova URL e toques a **vá** botão, uma nova `NSUserActivity` é criado para a guia que contém a URL que o usuário estiver navegando no momento:

[![](handoff-images/handoff01.png "Exemplo de aplicativo de entrega")](handoff-images/handoff01.png#lightbox)

Se outro dispositivo do usuário tiver o **MonkeyBrowser** aplicativo instalado, está conectado a iCloud usando a mesma conta de usuário, está na mesma rede e perto o dispositivo acima, a atividade de entrega será exibida em página inicial tela (no canto inferior esquerdo):

[![](handoff-images/handoff02.png "A atividade de entrega exibida na tela inicial no canto inferior esquerdo")](handoff-images/handoff02.png#lightbox)

Se o usuário arrasta para cima no ícone de entrega, o aplicativo será iniciado e a atividade de usuário especificado no `NSUserActivity` continuará no novo dispositivo:

[![](handoff-images/handoff03.png "A atividade de usuário continua no novo dispositivo")](handoff-images/handoff03.png#lightbox)

Quando a atividade de usuário é enviada com êxito para outro Apple dispositivo, o dispositivo de envio `NSUserActivity` receberá uma chamada para o `UserActivityWasContinued` método no seu `NSUserActivityDelegate` para que ele saiba que a atividade de usuário foram transferida com êxito para outro dispositivo.

## <a name="summary"></a>Resumo

Este artigo foi dada uma introdução para a estrutura de entrega usada para continuar a uma atividade de usuário entre vários dos dispositivos da Apple do usuário. Em seguida, ele mostrou como habilitar e implementar entrega em um aplicativo xamarin. Finalmente, ele discutidos os diferentes tipos de continuação de entrega disponíveis e as práticas recomendadas de entrega.



## <a name="related-links"></a>Links relacionados

- [Exemplos do iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Exemplo de MonkeyBrowser](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [O que há de novo no iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guia HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [Diretrizes de Interface do usuário HomeKit](https://developer.apple.com/homekit/ui-guidelines/)
- [Referência HomeKit Framework](https://developer.apple.com/library/ios/home_kit_framework_ref)
