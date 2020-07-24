---
title: CallKit no Xamarin. iOS
description: Este artigo aborda a nova API do CallKit lançada pela Apple no iOS 10 e como implementá-la em aplicativos de VOIP do Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 738A142D-FFD2-4738-B3ED-57C273179848
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/15/2017
ms.openlocfilehash: 791ab82e0e5f47929eff561ac836ec87e6d6c134
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997313"
---
# <a name="callkit-in-xamarinios"></a>CallKit no Xamarin. iOS

A nova API do CallKit no iOS 10 fornece uma maneira para que os aplicativos de VOIP se integrem à interface do usuário do iPhone e forneçam uma interface familiar e uma experiência para os usuários finais. Com essa API, os usuários podem exibir e interagir com chamadas de VOIP da tela de bloqueio do dispositivo iOS e gerenciar contatos usando as exibições de **favoritos** e **recentes** do aplicativo de telefone.

## <a name="about-callkit"></a>Sobre o CallKit

De acordo com a Apple, o CallKit é uma nova estrutura que elevará aplicativos de VOIP (voz sobre IP) de terceiros para uma experiência de terceiros no iOS 10. A API do CallKit permite que os aplicativos de VOIP se integrem à interface do usuário do iPhone e forneçam uma interface familiar e uma experiência para os usuários finais. Assim como o aplicativo de telefone interno, um usuário pode exibir e interagir com chamadas de VOIP da tela de bloqueio do dispositivo iOS e gerenciar contatos usando as exibições de **favoritos** e **recentes** do aplicativo de telefone.

Além disso, a API do CallKit fornece a capacidade de criar extensões de aplicativo que podem associar um número de telefone com um nome (ID do chamador) ou informar ao sistema quando um número deve ser bloqueado (bloqueio de chamada).

### <a name="the-existing-voip-app-experience"></a>A experiência do aplicativo VOIP existente

Antes de discutir a nova API do CallKit e suas habilidades, dê uma olhada na experiência atual do usuário com um aplicativo VOIP de terceiros no iOS 9 (e menos) usando um aplicativo VOIP fictício chamado MonkeyCall. MonkeyCall é um aplicativo simples que permite ao usuário enviar e receber chamadas VOIP usando as APIs do iOS existentes.

Atualmente, se o usuário estiver recebendo uma chamada de entrada no MonkeyCall e seu iPhone estiver bloqueado, a notificação recebida na tela de bloqueio será indistinguíveis de qualquer outro tipo de notificação (como as das mensagens ou aplicativos de email, por exemplo).

Se o usuário quisesse responder à chamada, teria que deslizar a notificação MonkeyCall para abrir o aplicativo e inserir sua senha (ou ID de toque do usuário) para desbloquear o telefone antes que ele pudesse aceitar a chamada e iniciar a conversa.

A experiência é igualmente complicada se o telefone estiver desbloqueado. Novamente, a chamada MonkeyCall de entrada é exibida como uma faixa de notificação padrão que desliza na parte superior da tela. Como a notificação é temporária, ela pode ser facilmente perdida pelo usuário, forçando-a a abrir o centro de notificações e encontrar a notificação específica para responder, em seguida, chamar ou localizar e iniciar o aplicativo MonkeyCall manualmente.

### <a name="the-callkit-voip-app-experience"></a>A experiência do aplicativo CallKit VOIP

Ao implementar as novas APIs CallKit no aplicativo MonkeyCall, a experiência do usuário com uma chamada VOIP de entrada pode ser muito melhorada no iOS 10. Veja o exemplo do usuário que está recebendo uma chamada VOIP quando seu telefone está bloqueado acima. Ao implementar CallKit, a chamada será exibida na tela de bloqueio do iPhone, da mesma forma que faria se a chamada estivesse sendo recebida do aplicativo de telefone interno, com a interface do usuário de tela inteira e nativa e a funcionalidade padrão de toque para resposta.

Novamente, se o iPhone for desbloqueado quando uma chamada de VOIP MonkeyCall for recebida, será apresentada a mesma funcionalidade de tela inteira, interface do usuário nativa e padrão de toque para resposta e "tocar para recusar" do aplicativo de telefone interno e MonkeyCall tem a opção de reproduzir um toque personalizado.

O CallKit fornece funcionalidade adicional para o MonkeyCall, permitindo que suas chamadas de VOIP interajam com outros tipos de chamadas, apareçam nos recentes e nas listas favoritas, para usar os recursos internos não incomodar e bloquear, iniciar chamadas MonkeyCall de siri e oferece a capacidade para os usuários atribuirem chamadas de MonkeyCall a pessoas no aplicativo de contatos.

As seções a seguir abordarão a arquitetura do CallKit, os fluxos de chamada de entrada e saída e a API do CallKit em detalhes.

## <a name="the-callkit-architecture"></a>A arquitetura CallKit

No iOS 10, a Apple adotou o CallKit em todos os serviços do sistema, de modo que as chamadas feitas em CarPlay, por exemplo, são conhecidas pela interface do usuário do sistema via CallKit. No exemplo fornecido abaixo, como o MonkeyCall adota o CallKit, ele é conhecido pelo sistema da mesma forma que os serviços internos do sistema e obtém todos os mesmos recursos:

[![A pilha de serviço CallKit](callkit-images/callkit01.png)](callkit-images/callkit01.png#lightbox)

Examine com mais detalhes o aplicativo MonkeyCall do diagrama acima. O aplicativo contém todo o seu código para se comunicar com sua própria rede e contém suas próprias interfaces de usuário. Ele vincula no CallKit para se comunicar com o sistema:

[![Arquitetura de aplicativo MonkeyCall](callkit-images/callkit02.png)](callkit-images/callkit02.png#lightbox)

Há duas interfaces principais no CallKit que o aplicativo usa:

- `CXProvider`-Isso permite que o aplicativo MonkeyCall informe o sistema de quaisquer notificações fora de banda que possam ocorrer.
- `CXCallController`– Permite que o aplicativo MonkeyCall informe o sistema de ações de usuário local.

### <a name="the-cxprovider"></a>O CXProvider

Conforme mencionado acima, `CXProvider` permite que um aplicativo informe o sistema de quaisquer notificações fora de banda que possam ocorrer. Essas são notificações que não ocorrem devido a ações de usuário local, mas ocorrem devido a eventos externos, como chamadas de entrada.

Um aplicativo deve usar o `CXProvider` para o seguinte:

- Relatar uma chamada de entrada para o sistema.
- Relatar que uma chamada de saída foi conectada ao sistema.
- Relate o usuário remoto que está encerrando a chamada para o sistema.

Quando o aplicativo quer se comunicar com o sistema, ele usa a `CXCallUpdate` classe e quando o sistema precisa se comunicar com o aplicativo, ele usa a `CXAction` classe:

[![Comunicando-se com o sistema por meio de um CXProvider](callkit-images/callkit03.png)](callkit-images/callkit03.png#lightbox)

### <a name="the-cxcallcontroller"></a>O CXCallController

O `CXCallController` permite que um aplicativo informe o sistema de ações de usuário local, como o usuário que inicia uma chamada VoIP. A implementação de um `CXCallController` aplicativo é para Interplay com outros tipos de chamadas no sistema. Por exemplo, se já houver uma chamada de telefonia ativa em andamento, `CXCallController` o poderá permitir que o aplicativo VoIP Coloque essa chamada em espera e inicie ou responda a uma chamada VoIP.

Um aplicativo deve usar o `CXCallController` para o seguinte:

- Relatório quando o usuário iniciou uma chamada de saída para o sistema.
- Relatar quando o usuário responder a uma chamada de entrada para o sistema.
- Relatar quando o usuário terminar uma chamada para o sistema.

Quando o aplicativo deseja comunicar ações de usuário local para o sistema, ele usa a `CXTransaction` classe:

[![Relatórios para o sistema usando um CXCallController](callkit-images/callkit04.png)](callkit-images/callkit04.png#lightbox)

## <a name="implementing-callkit"></a>Implementando CallKit

As seções a seguir mostrarão como implementar o CallKit em um aplicativo de VOIP do Xamarin. iOS. Por exemplo, este documento usará o código do aplicativo MonkeyCall de VOIP fictício. O código apresentado aqui representa várias classes de suporte, as partes específicas do CallKit serão abordadas detalhadamente nas seções a seguir.

### <a name="the-activecall-class"></a>A classe ActiveCall

A `ActiveCall` classe é usada pelo aplicativo MonkeyCall para armazenar todas as informações sobre uma chamada VoIP que está atualmente ativa da seguinte maneira:

```csharp
using System;
using CoreFoundation;
using Foundation;

namespace MonkeyCall
{
    public class ActiveCall
    {
        #region Private Variables
        private bool isConnecting;
        private bool isConnected;
        private bool isOnhold;
        #endregion

        #region Computed Properties
        public NSUuid UUID { get; set; }
        public bool isOutgoing { get; set; }
        public string Handle { get; set; }
        public DateTime StartedConnectingOn { get; set;}
        public DateTime ConnectedOn { get; set;}
        public DateTime EndedOn { get; set; }

        public bool IsConnecting {
            get { return isConnecting; }
            set {
                isConnecting = value;
                if (isConnecting) StartedConnectingOn = DateTime.Now;
                RaiseStartingConnectionChanged ();
            }
        }

        public bool IsConnected {
            get { return isConnected; }
            set {
                isConnected = value;
                if (isConnected) {
                    ConnectedOn = DateTime.Now;
                } else {
                    EndedOn = DateTime.Now;
                }
                RaiseConnectedChanged ();
            }
        }

        public bool IsOnHold {
            get { return isOnhold; }
            set {
                isOnhold = value;
            }
        }
        #endregion

        #region Constructors
        public ActiveCall ()
        {
        }

        public ActiveCall (NSUuid uuid, string handle, bool outgoing)
        {
            // Initialize
            this.UUID = uuid;
            this.Handle = handle;
            this.isOutgoing = outgoing;
        }
        #endregion

        #region Public Methods
        public void StartCall (ActiveCallbackDelegate completionHandler)
        {
            // Simulate the call starting successfully
            completionHandler (true);

            // Simulate making a starting and completing a connection
            DispatchQueue.MainQueue.DispatchAfter (new DispatchTime(DispatchTime.Now, 3000), () => {
                // Note that the call is starting
                IsConnecting = true;

                // Simulate pause before connecting
                DispatchQueue.MainQueue.DispatchAfter (new DispatchTime (DispatchTime.Now, 1500), () => {
                    // Note that the call has connected
                    IsConnecting = false;
                    IsConnected = true;
                });
            });
        }

        public void AnswerCall (ActiveCallbackDelegate completionHandler)
        {
            // Simulate the call being answered
            IsConnected = true;
            completionHandler (true);
        }

        public void EndCall (ActiveCallbackDelegate completionHandler)
        {
            // Simulate the call ending
            IsConnected = false;
            completionHandler (true);
        }
        #endregion

        #region Events
        public delegate void ActiveCallbackDelegate (bool successful);
        public delegate void ActiveCallStateChangedDelegate (ActiveCall call);

        public event ActiveCallStateChangedDelegate StartingConnectionChanged;
        internal void RaiseStartingConnectionChanged ()
        {
            if (this.StartingConnectionChanged != null) this.StartingConnectionChanged (this);
        }

        public event ActiveCallStateChangedDelegate ConnectedChanged;
        internal void RaiseConnectedChanged ()
        {
            if (this.ConnectedChanged != null) this.ConnectedChanged (this);
        }
        #endregion
    }
}
```

`ActiveCall`mantém várias propriedades que definem o estado da chamada e dois eventos que podem ser gerados quando o estado da chamada é alterado. Como este é apenas um exemplo, há três métodos usados para iniciar, responder e encerrar uma chamada simulada.

### <a name="the-startcallrequest-class"></a>A classe StartCallRequest

A `StartCallRequest` classe estática fornece alguns métodos auxiliares que serão usados ao trabalhar com chamadas de saída:

```csharp
using System;
using Foundation;
using Intents;

namespace MonkeyCall
{
    public static class StartCallRequest
    {
        public static string URLScheme {
            get { return "monkeycall"; }
        }

        public static string ActivityType {
            get { return INIntentIdentifier.StartAudioCall.GetConstant ().ToString (); }
        }

        public static string CallHandleFromURL (NSUrl url)
        {
            // Is this a MonkeyCall handle?
            if (url.Scheme == URLScheme) {
                // Yes, return host
                return url.Host;
            } else {
                // Not handled
                return null;
            }
        }

        public static string CallHandleFromActivity (NSUserActivity activity)
        {
            // Is this a start call activity?
            if (activity.ActivityType == ActivityType) {
                // Yes, trap any errors
                try {
                    // Get first contact
                    var interaction = activity.GetInteraction ();
                    var startAudioCallIntent = interaction.Intent as INStartAudioCallIntent;
                    var contact = startAudioCallIntent.Contacts [0];

                    // Get the person handle
                    return contact.PersonHandle.Value;
                } catch {
                    // Error, report null
                    return null;
                }
            } else {
                // Not handled
                return null;
            }
        }
    }
}
```

As `CallHandleFromURL` `CallHandleFromActivity` classes e são usadas no AppDelegate para obter o identificador de contato da pessoa que está sendo chamada em uma chamada de saída. Para obter mais informações, consulte a seção [manipulando chamadas de saída](#handling-outgoing-calls) abaixo.

### <a name="the-activecallmanager-class"></a>A classe ActiveCallManager

A `ActiveCallManager` classe manipula todas as chamadas abertas no aplicativo MonkeyCall.

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using CallKit;

namespace MonkeyCall
{
    public class ActiveCallManager
    {
        #region Private Variables
        private CXCallController CallController = new CXCallController ();
        #endregion

        #region Computed Properties
        public List<ActiveCall> Calls { get; set; }
        #endregion

        #region Constructors
        public ActiveCallManager ()
        {
            // Initialize
            this.Calls = new List<ActiveCall> ();
        }
        #endregion

        #region Private Methods
        private void SendTransactionRequest (CXTransaction transaction)
        {
            // Send request to call controller
            CallController.RequestTransaction (transaction, (error) => {
                // Was there an error?
                if (error == null) {
                    // No, report success
                    Console.WriteLine ("Transaction request sent successfully.");
                } else {
                    // Yes, report error
                    Console.WriteLine ("Error requesting transaction: {0}", error);
                }
            });
        }
        #endregion

        #region Public Methods
        public ActiveCall FindCall (NSUuid uuid)
        {
            // Scan for requested call
            foreach (ActiveCall call in Calls) {
                if (call.UUID.Equals(uuid)) return call;
            }

            // Not found
            return null;
        }

        public void StartCall (string contact)
        {
            // Build call action
            var handle = new CXHandle (CXHandleType.Generic, contact);
            var startCallAction = new CXStartCallAction (new NSUuid (), handle);

            // Create transaction
            var transaction = new CXTransaction (startCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }

        public void EndCall (ActiveCall call)
        {
            // Build action
            var endCallAction = new CXEndCallAction (call.UUID);

            // Create transaction
            var transaction = new CXTransaction (endCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }

        public void PlaceCallOnHold (ActiveCall call)
        {
            // Build action
            var holdCallAction = new CXSetHeldCallAction (call.UUID, true);

            // Create transaction
            var transaction = new CXTransaction (holdCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }

        public void RemoveCallFromOnHold (ActiveCall call)
        {
            // Build action
            var holdCallAction = new CXSetHeldCallAction (call.UUID, false);

            // Create transaction
            var transaction = new CXTransaction (holdCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }
        #endregion
    }
}
```

Novamente, como essa é apenas uma simulação, o `ActiveCallManager` só mantém uma coleção de `ActiveCall` objetos e tem uma rotina para localizar uma determinada chamada por sua `UUID` propriedade. Ele também inclui métodos para iniciar, encerrar e alterar o estado de manutenção de uma chamada de saída. Para obter mais informações, consulte a seção [manipulando chamadas de saída](#handling-outgoing-calls) abaixo.

### <a name="the-providerdelegate-class"></a>A classe ProviderDelegate

Conforme discutido acima, um `CXProvider` fornece comunicação bidirecional entre o aplicativo e o sistema para notificações fora de banda. O desenvolvedor precisa fornecer um personalizado `CXProviderDelegate` e anexá-lo ao `CXProvider` para que o aplicativo manipule eventos CallKit fora de banda. MonkeyCall usa o seguinte `CXProviderDelegate` :

```csharp
using System;
using Foundation;
using CallKit;
using UIKit;

namespace MonkeyCall
{
    public class ProviderDelegate : CXProviderDelegate
    {
        #region Computed Properties
        public ActiveCallManager CallManager { get; set;}
        public CXProviderConfiguration Configuration { get; set; }
        public CXProvider Provider { get; set; }
        #endregion

        #region Constructors
        public ProviderDelegate (ActiveCallManager callManager)
        {
            // Save connection to call manager
            CallManager = callManager;

            // Define handle types
            var handleTypes = new [] { (NSNumber)(int)CXHandleType.PhoneNumber };

            // Get Image Template
            var templateImage = UIImage.FromFile ("telephone_receiver.png");

            // Setup the initial configurations
            Configuration = new CXProviderConfiguration ("MonkeyCall") {
                MaximumCallsPerCallGroup = 1,
                SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
                IconTemplateImageData = templateImage.AsPNG(),
                RingtoneSound = "musicloop01.wav"
            };

            // Create a new provider
            Provider = new CXProvider (Configuration);

            // Attach this delegate
            Provider.SetDelegate (this, null);

        }
        #endregion

        #region Override Methods
        public override void DidReset (CXProvider provider)
        {
            // Remove all calls
            CallManager.Calls.Clear ();
        }

        public override void PerformStartCallAction (CXProvider provider, CXStartCallAction action)
        {
            // Create new call record
            var activeCall = new ActiveCall (action.CallUuid, action.CallHandle.Value, true);

            // Monitor state changes
            activeCall.StartingConnectionChanged += (call) => {
                if (call.isConnecting) {
                    // Inform system that the call is starting
                    Provider.ReportConnectingOutgoingCall (call.UUID, call.StartedConnectingOn.ToNSDate());
                }
            };

            activeCall.ConnectedChanged += (call) => {
                if (call.isConnected) {
                    // Inform system that the call has connected
                    provider.ReportConnectedOutgoingCall (call.UUID, call.ConnectedOn.ToNSDate ());
                }
            };

            // Start call
            activeCall.StartCall ((successful) => {
                // Was the call able to be started?
                if (successful) {
                    // Yes, inform the system
                    action.Fulfill ();

                    // Add call to manager
                    CallManager.Calls.Add (activeCall);
                } else {
                    // No, inform system
                    action.Fail ();
                }
            });
        }

        public override void PerformAnswerCallAction (CXProvider provider, CXAnswerCallAction action)
        {
            // Find requested call
            var call = CallManager.FindCall (action.CallUuid);

            // Found?
            if (call == null) {
                // No, inform system and exit
                action.Fail ();
                return;
            }

            // Attempt to answer call
            call.AnswerCall ((successful) => {
                // Was the call successfully answered?
                if (successful) {
                    // Yes, inform system
                    action.Fulfill ();
                } else {
                    // No, inform system
                    action.Fail ();
                }
            });
        }

        public override void PerformEndCallAction (CXProvider provider, CXEndCallAction action)
        {
            // Find requested call
            var call = CallManager.FindCall (action.CallUuid);

            // Found?
            if (call == null) {
                // No, inform system and exit
                action.Fail ();
                return;
            }

            // Attempt to answer call
            call.EndCall ((successful) => {
                // Was the call successfully answered?
                if (successful) {
                    // Remove call from manager's queue
                    CallManager.Calls.Remove (call);

                    // Yes, inform system
                    action.Fulfill ();
                } else {
                    // No, inform system
                    action.Fail ();
                }
            });
        }

        public override void PerformSetHeldCallAction (CXProvider provider, CXSetHeldCallAction action)
        {
            // Find requested call
            var call = CallManager.FindCall (action.CallUuid);

            // Found?
            if (call == null) {
                // No, inform system and exit
                action.Fail ();
                return;
            }

            // Update hold status
            call.isOnHold = action.OnHold;

            // Inform system of success
            action.Fulfill ();
        }

        public override void TimedOutPerformingAction (CXProvider provider, CXAction action)
        {
            // Inform user that the action has timed out
        }

        public override void DidActivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
        {
            // Start the calls audio session here
        }

        public override void DidDeactivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
        {
            // End the calls audio session and restart any non-call
            // related audio
        }
        #endregion

        #region Public Methods
        public void ReportIncomingCall (NSUuid uuid, string handle)
        {
            // Create update to describe the incoming call and caller
            var update = new CXCallUpdate ();
            update.RemoteHandle = new CXHandle (CXHandleType.Generic, handle);

            // Report incoming call to system
            Provider.ReportNewIncomingCall (uuid, update, (error) => {
                // Was the call accepted
                if (error == null) {
                    // Yes, report to call manager
                    CallManager.Calls.Add (new ActiveCall (uuid, handle, false));
                } else {
                    // Report error to user here
                    Console.WriteLine ("Error: {0}", error);
                }
            });
        }
        #endregion
    }
}
```

Quando uma instância desse delegado é criada, ela é passada ao `ActiveCallManager` que será usada para lidar com qualquer atividade de chamada. Em seguida, ele define os tipos de identificadores ( `CXHandleType` ) que o `CXProvider` responderá a:

```csharp
// Define handle types
var handleTypes = new [] { (NSNumber)(int)CXHandleType.PhoneNumber };
```

E obtém a imagem de modelo que será aplicada ao ícone do aplicativo quando uma chamada estiver em andamento:

```csharp
// Get Image Template
var templateImage = UIImage.FromFile ("telephone_receiver.png");
```

Esses valores são agrupados em um `CXProviderConfiguration` que será usado para configurar `CXProvider` :

```csharp
// Setup the initial configurations
Configuration = new CXProviderConfiguration ("MonkeyCall") {
    MaximumCallsPerCallGroup = 1,
    SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
    IconTemplateImageData = templateImage.AsPNG(),
    RingtoneSound = "musicloop01.wav"
};
```

Em seguida, o delegado cria um novo `CXProvider` com essas configurações e se anexa a ela:

```csharp
// Create a new provider
Provider = new CXProvider (Configuration);

// Attach this delegate
Provider.SetDelegate (this, null);
```

Ao usar CallKit, o aplicativo não criará mais e tratará suas próprias sessões de áudio, em vez disso, ele precisará configurar e usar uma sessão de áudio que o sistema criará e tratará para ela.

Se esse fosse um aplicativo real, o `DidActivateAudioSession` método seria usado para iniciar a chamada com um pré-configurado `AVAudioSession` que o sistema forneceu:

```csharp
public override void DidActivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // Start the call's audio session here...
}
```

Ele também usaria o `DidDeactivateAudioSession` método para finalizar e liberar sua conexão com a sessão de áudio fornecida pelo sistema:

```csharp
public override void DidDeactivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // End the calls audio session and restart any non-call
    // releated audio
}
```

O restante do código será abordado em detalhes nas seções a seguir.

### <a name="the-appdelegate-class"></a>A classe AppDelegate

MonkeyCall usa o AppDelegate para manter instâncias do `ActiveCallManager` e `CXProviderDelegate` que serão usadas em todo o aplicativo:

```csharp
using Foundation;
using UIKit;
using Intents;
using System;

namespace MonkeyCall
{
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate
    {
        #region Constructors
        public override UIWindow Window { get; set; }
        public ActiveCallManager CallManager { get; set; }
        public ProviderDelegate CallProviderDelegate { get; set; }
        #endregion

        #region Override Methods
        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            // Initialize the call handlers
            CallManager = new ActiveCallManager ();
            CallProviderDelegate = new ProviderDelegate (CallManager);

            return true;
        }

        public override bool OpenUrl (UIApplication app, NSUrl url, NSDictionary options)
        {
            // Get handle from url
            var handle = StartCallRequest.CallHandleFromURL (url);

            // Found?
            if (handle == null) {
                // No, report to system
                Console.WriteLine ("Unable to get call handle from URL: {0}", url);
                return false;
            } else {
                // Yes, start call and inform system
                CallManager.StartCall (handle);
                return true;
            }
        }

        public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
        {
            var handle = StartCallRequest.CallHandleFromActivity (userActivity);

            // Found?
            if (handle == null) {
                // No, report to system
                Console.WriteLine ("Unable to get call handle from User Activity: {0}", userActivity);
                return false;
            } else {
                // Yes, start call and inform system
                CallManager.StartCall (handle);
                return true;
            }
        }

        ...
        #endregion
    }
}
```

Os `OpenUrl` `ContinueUserActivity` métodos e substituem são usados quando o aplicativo está processando uma chamada de saída. Para obter mais informações, consulte a seção [manipulando chamadas de saída](#handling-outgoing-calls) abaixo.

## <a name="handling-incoming-calls"></a>Manipulando chamadas de entrada

Há vários Estados e processos que uma chamada VOIP de entrada pode passar durante um fluxo de trabalho de chamada de entrada típico, como:

- Informando o usuário (e o sistema) de que existe uma chamada de entrada.
- Receber uma notificação quando o usuário quiser responder à chamada e inicializar a chamada com o outro usuário.
- Informe o sistema e a rede de comunicação quando o usuário quiser encerrar a chamada atual.

As seções a seguir terão uma visão detalhada de como um aplicativo pode usar o CallKit para lidar com o fluxo de trabalho de chamada de entrada, novamente usando o aplicativo VOIP MonkeyCall como um exemplo.

### <a name="informing-user-of-incoming-call"></a>Informando o usuário da chamada de entrada

Quando um usuário remoto inicia uma conversa VOIP com o usuário local, ocorre o seguinte:

[![Um usuário remoto iniciou uma conversa VOIP](callkit-images/callkit05.png)](callkit-images/callkit05.png#lightbox)

1. O aplicativo obtém uma notificação de sua rede de comunicações de que há uma chamada VOIP de entrada.
2. O aplicativo usa o `CXProvider` para enviar um `CXCallUpdate` ao sistema informando-o sobre a chamada.
3. O sistema publica a chamada à interface do usuário do sistema, serviços do sistema e qualquer outro aplicativo VOIP usando o CallKit.

Por exemplo, no `CXProviderDelegate` :

```csharp
public void ReportIncomingCall (NSUuid uuid, string handle)
{
    // Create update to describe the incoming call and caller
    var update = new CXCallUpdate ();
    update.RemoteHandle = new CXHandle (CXHandleType.Generic, handle);

    // Report incoming call to system
    Provider.ReportNewIncomingCall (uuid, update, (error) => {
        // Was the call accepted
        if (error == null) {
            // Yes, report to call manager
            CallManager.Calls.Add (new ActiveCall (uuid, handle, false));
        } else {
            // Report error to user here
            Console.WriteLine ("Error: {0}", error);
        }
    });
}
```

Esse código cria uma nova `CXCallUpdate` instância e anexa um identificador a ela que identificará o chamador. Em seguida, ele usa o `ReportNewIncomingCall` método da `CXProvider` classe para informar o sistema da chamada. Se for bem-sucedida, a chamada será adicionada à coleção de chamadas ativas do aplicativo, se não for, o erro precisará ser relatado ao usuário.

### <a name="user-answering-incoming-call"></a>Usuário respondendo chamada de entrada

Se o usuário quiser responder à chamada VOIP de entrada, ocorrerá o seguinte:

[![O usuário responde à chamada VOIP de entrada](callkit-images/callkit06.png)](callkit-images/callkit06.png#lightbox)

1. A interface de usuário do sistema informa ao sistema que o usuário deseja responder à chamada VOIP.
2. O sistema envia um `CXAnswerCallAction` para o aplicativo `CXProvider` informando-o sobre a tentativa de resposta.
3. O aplicativo informa sua rede de comunicação que o usuário está respondendo à chamada e a chamada VOIP continua como de costume.

Por exemplo, no `CXProviderDelegate` :

```csharp
public override void PerformAnswerCallAction (CXProvider provider, CXAnswerCallAction action)
{
    // Find requested call
    var call = CallManager.FindCall (action.CallUuid);

    // Found?
    if (call == null) {
        // No, inform system and exit
        action.Fail ();
        return;
    }

    // Attempt to answer call
    call.AnswerCall ((successful) => {
        // Was the call successfully answered?
        if (successful) {
            // Yes, inform system
            action.Fulfill ();
        } else {
            // No, inform system
            action.Fail ();
        }
    });
}
```

Esse código procura pela chamada fornecida em sua lista de chamadas ativas. Se a chamada não puder ser encontrada, o sistema é notificado e o método é encerrado. Se for encontrado, o `AnswerCall` método da `ActiveCall` classe será chamado para iniciar a chamada e o sistema será informações se for bem-sucedida ou falhar.

### <a name="user-ending-incoming-call"></a>Usuário encerrando chamada recebida

Se o usuário quiser encerrar a chamada de dentro da interface do usuário do aplicativo, ocorrerá o seguinte:

[![O usuário encerra a chamada de dentro da interface do usuário do aplicativo](callkit-images/callkit07.png)](callkit-images/callkit07.png#lightbox)

1. O aplicativo cria `CXEndCallAction` um pacote em um `CXTransaction` que é enviado ao sistema para informá-lo de que a chamada está terminando.
2. O sistema verifica a intenção de chamada final e envia o de `CXEndCallAction` volta para o aplicativo por meio do `CXProvider` .
3. Em seguida, o aplicativo informa sua rede de comunicação de que a chamada está terminando.

Por exemplo, no `CXProviderDelegate` :

```csharp
public override void PerformEndCallAction (CXProvider provider, CXEndCallAction action)
{
    // Find requested call
    var call = CallManager.FindCall (action.CallUuid);

    // Found?
    if (call == null) {
        // No, inform system and exit
        action.Fail ();
        return;
    }

    // Attempt to answer call
    call.EndCall ((successful) => {
        // Was the call successfully answered?
        if (successful) {
            // Remove call from manager's queue
            CallManager.Calls.Remove (call);

            // Yes, inform system
            action.Fulfill ();
        } else {
            // No, inform system
            action.Fail ();
        }
    });
}
```

Esse código procura pela chamada fornecida em sua lista de chamadas ativas. Se a chamada não puder ser encontrada, o sistema é notificado e o método é encerrado. Se for encontrado, o `EndCall` método da `ActiveCall` classe será chamado para encerrar a chamada e o sistema será informações se for bem-sucedida ou falhar. Se for bem-sucedido, a chamada será removida da coleção de chamadas ativas.

## <a name="managing-multiple-calls"></a>Gerenciando várias chamadas

A maioria dos aplicativos VOIP pode lidar com várias chamadas de uma só vez. Por exemplo, se atualmente houver uma chamada VOIP ativa e o aplicativo receber uma notificação de que há uma nova chamada de entrada, o usuário poderá pausar ou desligar na primeira chamada para responder à segunda.

Na situação em que for mostrado acima, o sistema enviará um `CXTransaction` para o aplicativo que incluirá uma lista de várias ações (como o `CXEndCallAction` e o `CXAnswerCallAction` ). Todas essas ações precisarão ser atendidas individualmente, para que o sistema possa atualizar a interface do usuário adequadamente.

## <a name="handling-outgoing-calls"></a>Manipulando chamadas de saída

Se o usuário tocar em uma entrada da lista recentes (no aplicativo de telefone), por exemplo, que é de uma chamada pertencente ao aplicativo, ele receberá uma tentativa de _chamada inicial_ pelo sistema:

[![Recebendo uma tentativa de chamada inicial](callkit-images/callkit08.png)](callkit-images/callkit08.png#lightbox)

1. O aplicativo criará uma _ação iniciar chamada_ com base na intenção iniciar chamada recebida do sistema.
2. O aplicativo usará o `CXCallController` para solicitar a ação iniciar chamada do sistema.
3. Se o sistema aceitar a ação, ele será retornado para o aplicativo por meio do `XCProvider` delegado.
4. O aplicativo inicia a chamada de saída com sua rede de comunicação.

Para obter mais informações sobre tentativas, consulte nossa documentação [de extensões de interface do usuário de tentativas e](~/ios/platform/sirikit/understanding-sirikit.md) intenções.

### <a name="the-outgoing-call-lifecycle"></a>O ciclo de vida da chamada de saída

Ao trabalhar com CallKit e uma chamada de saída, o aplicativo precisará informar o sistema dos seguintes eventos de ciclo de vida:

1. **Iniciando** -informar o sistema de que uma chamada de saída está prestes a começar.
2. **Iniciado** -informar ao sistema que uma chamada de saída foi iniciada.
3. **Conectando** – informe ao sistema que a chamada de saída está se conectando.
4. **Conectado** -informe a chamada de saída conectada e que ambas as partes podem conversar agora.

Por exemplo, o código a seguir iniciará uma chamada de saída:

```csharp
private CXCallController CallController = new CXCallController ();
...

private void SendTransactionRequest (CXTransaction transaction)
{
    // Send request to call controller
    CallController.RequestTransaction (transaction, (error) => {
        // Was there an error?
        if (error == null) {
            // No, report success
            Console.WriteLine ("Transaction request sent successfully.");
        } else {
            // Yes, report error
            Console.WriteLine ("Error requesting transaction: {0}", error);
        }
    });
}

public void StartCall (string contact)
{
    // Build call action
    var handle = new CXHandle (CXHandleType.Generic, contact);
    var startCallAction = new CXStartCallAction (new NSUuid (), handle);

    // Create transaction
    var transaction = new CXTransaction (startCallAction);

    // Inform system of call request
    SendTransactionRequest (transaction);
}
```

Ele cria um `CXHandle` e o usa para configurar um `CXStartCallAction` que é agrupado em um `CXTransaction` que é enviado para o sistema usando o `RequestTransaction` método da `CXCallController` classe. Ao chamar o `RequestTransaction` método, o sistema pode fazer qualquer chamada existente em espera, independentemente da origem (aplicativo de telefone, FaceTime, VoIP, etc.), antes de iniciar a nova chamada.

A solicitação para iniciar uma chamada VOIP de saída pode vir de várias fontes diferentes, como Siri, uma entrada em um cartão de visita (no aplicativo de contatos) ou na lista recentes (no aplicativo de telefone). Nessas situações, o aplicativo será enviado uma intenção iniciar chamada dentro de um `NSUserActivity` e o AppDelegate precisará tratá-lo:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    var handle = StartCallRequest.CallHandleFromActivity (userActivity);

    // Found?
    if (handle == null) {
        // No, report to system
        Console.WriteLine ("Unable to get call handle from User Activity: {0}", userActivity);
        return false;
    } else {
        // Yes, start call and inform system
        CallManager.StartCall (handle);
        return true;
    }
}
```

Aqui, o `CallHandleFromActivity` método da classe auxiliar `StartCallRequest` está sendo usado para obter o identificador para a pessoa que está sendo chamada (consulte [a classe StartCallRequest](#the-startcallrequest-class) acima).

O `PerformStartCallAction` método da [classe ProviderDelegate](#the-providerdelegate-class) é usado para, por fim, iniciar a chamada real de saída e informar o sistema de seu ciclo de vida:

```csharp
public override void PerformStartCallAction (CXProvider provider, CXStartCallAction action)
{
    // Create new call record
    var activeCall = new ActiveCall (action.CallUuid, action.CallHandle.Value, true);

    // Monitor state changes
    activeCall.StartingConnectionChanged += (call) => {
        if (call.IsConnecting) {
            // Inform system that the call is starting
            Provider.ReportConnectingOutgoingCall (call.UUID, call.StartedConnectingOn.ToNSDate());
        }
    };

    activeCall.ConnectedChanged += (call) => {
        if (call.IsConnected) {
            // Inform system that the call has connected
            Provider.ReportConnectedOutgoingCall (call.UUID, call.ConnectedOn.ToNSDate ());
        }
    };

    // Start call
    activeCall.StartCall ((successful) => {
        // Was the call able to be started?
        if (successful) {
            // Yes, inform the system
            action.Fulfill ();

            // Add call to manager
            CallManager.Calls.Add (activeCall);
        } else {
            // No, inform system
            action.Fail ();
        }
    });
}
```

Ele cria uma instância da `ActiveCall` classe (para armazenar informações sobre a chamada em andamento) e preenche com a pessoa que está sendo chamada. Os `StartingConnectionChanged` `ConnectedChanged` eventos e são usados para monitorar e relatar o ciclo de vida da chamada de saída. A chamada é iniciada e o sistema informava que a ação foi atendida.

### <a name="ending-an-outgoing-call"></a>Finalizando uma chamada de saída

Quando o usuário termina com uma chamada de saída e deseja encerrar, o código a seguir pode ser usado:

```csharp
private CXCallController CallController = new CXCallController ();
...

private void SendTransactionRequest (CXTransaction transaction)
{
    // Send request to call controller
    CallController.RequestTransaction (transaction, (error) => {
        // Was there an error?
        if (error == null) {
            // No, report success
            Console.WriteLine ("Transaction request sent successfully.");
        } else {
            // Yes, report error
            Console.WriteLine ("Error requesting transaction: {0}", error);
        }
    });
}

public void EndCall (ActiveCall call)
{
    // Build action
    var endCallAction = new CXEndCallAction (call.UUID);

    // Create transaction
    var transaction = new CXTransaction (endCallAction);

    // Inform system of call request
    SendTransactionRequest (transaction);
}
```

Se o criar um `CXEndCallAction` com o UUID da chamada para End, o agrupará em um `CXTransaction` que é enviado ao sistema usando o `RequestTransaction` método da `CXCallController` classe.

## <a name="additional-callkit-details"></a>Detalhes adicionais do CallKit

Esta seção abordará alguns detalhes adicionais que o desenvolvedor precisará levar em consideração ao trabalhar com CallKit como:

- Configuração do Provedor
- Erros de ação
- Restrições do sistema
- Áudio VOIP

### <a name="provider-configuration"></a>Configuração do provedor

A configuração do provedor permite que um aplicativo do iOS 10 VOIP Personalize a experiência do usuário (dentro da interface do usuário nativa em chamada) ao trabalhar com CallKit.

Um aplicativo pode fazer os seguintes tipos de personalizações:

- Exibir um nome localizado.
- Habilite o suporte à chamada de vídeo.
- Personalize os botões na interface do usuário em chamada apresentando seu próprio ícone de imagem de modelo. A interação do usuário com botões personalizados é enviada diretamente para o aplicativo a ser processado.

### <a name="action-errors"></a>Erros de ação

os aplicativos VOIP 10 do iOS usando CallKit precisam manipular ações que falham normalmente e manter o usuário informado do estado de ação em todos os momentos.

Considere o seguinte exemplo:

1. O aplicativo recebeu uma ação iniciar chamada e começou o processo de inicializar uma nova chamada VOIP com sua rede de comunicação.
2. Devido à capacidade de comunicação limitada ou sem rede, essa conexão falha.
3. O aplicativo *deve* enviar a mensagem de **falha** de volta para a ação iniciar chamada ( `Action.Fail()` ) para informar o sistema da falha.
4. Isso permite que o sistema informe o usuário sobre o status da chamada. Por exemplo, para exibir a interface do usuário de falha de chamada.

Além disso, um aplicativo VOIP 10 do iOS precisa responder a _erros de tempo limite_ que podem ocorrer quando uma ação esperada não pode ser processada dentro de um determinado período de tempo. Cada tipo de ação fornecido pelo CallKit tem um valor de tempo limite máximo associado a ele. Esses valores de tempo limite garantem que qualquer ação CallKit solicitada pelo usuário seja tratada de maneira responsiva, mantendo o sistema operacional fluido e responsivo também.

Há vários métodos no delegado do provedor ( `CXProviderDelegate` ) que devem ser substituídos para lidar normalmente com essas situações de tempo limite também.

### <a name="system-restrictions"></a>Restrições do sistema

Com base no estado atual do dispositivo iOS que executa o aplicativo de VOIP 10 do iOS, determinadas restrições do sistema podem ser impostas.

Por exemplo, uma chamada VOIP de entrada pode ser restringida pelo sistema se:

1. A pessoa que está chamando está na lista de chamadores bloqueados do usuário.
2. O dispositivo iOS do usuário está no modo do-não-incomodar.

Se uma chamada VOIP for restrita por qualquer uma dessas situações, use o seguinte código para tratá-la:

```csharp
public class ProviderDelegate : CXProviderDelegate
{
...

    public void ReportIncomingCall (NSUuid uuid, string handle)
    {
        // Create update to describe the incoming call and caller
        var update = new CXCallUpdate ();
        update.RemoteHandle = new CXHandle (CXHandleType.Generic, handle);

        // Report incoming call to system
        Provider.ReportNewIncomingCall (uuid, update, (error) => {
            // Was the call accepted
            if (error == null) {
                // Yes, report to call manager
                CallManager.Calls.Add (new ActiveCall (uuid, handle, false));
            } else {
                // Report error to user here
                if (error.Code == (int)CXErrorCodeIncomingCallError.CallUuidAlreadyExists) {
                    // Handle duplicate call ID
                } else if (error.Code == (int)CXErrorCodeIncomingCallError.FilteredByBlockList) {
                    // Handle call from blocked user
                } else if (error.Code == (int)CXErrorCodeIncomingCallError.FilteredByDoNotDisturb) {
                    // Handle call while in do-not-disturb mode
                } else {
                    // Handle unknown error
                }
            }
        });
    }

}
```

### <a name="voip-audio"></a>Áudio VOIP

O CallKit fornece vários benefícios para lidar com os recursos de áudio que um aplicativo VOIP de iOS 10 exigirá durante uma chamada de VOIP ao vivo. Um dos maiores benefícios é que a sessão de áudio do aplicativo terá prioridades elevadas quando executado no iOS 10. Esse é o mesmo nível de prioridade que o telefone interno e os aplicativos FaceTime e esse nível de prioridade avançado impedirá que outros aplicativos em execução interrompam a sessão de áudio do aplicativo VOIP.

Além disso, o CallKit tem acesso a outras dicas de roteamento de áudio que podem aprimorar o desempenho e rotear de forma inteligente o áudio VOIP para dispositivos de saída específicos durante uma chamada dinâmica com base nas preferências do usuário e nos Estados do dispositivo. Por exemplo, com base em dispositivos anexados, como fones de ouvido Bluetooth, uma conexão CarPlay ao vivo ou configurações de acessibilidade.

Durante o ciclo de vida de uma chamada VOIP típica usando CallKit, o aplicativo precisará configurar o fluxo de áudio que o CallKit fornecerá. Veja o exemplo a seguir:

[![A sequência de ação iniciar chamada](callkit-images/callkit09.png)](callkit-images/callkit09.png#lightbox)

1. Uma ação iniciar chamada é recebida pelo aplicativo para responder a uma chamada recebida.
2. Antes que essa ação seja atendida pelo aplicativo, ela fornece a configuração que será necessária para seu `AVAudioSession` .
3. O aplicativo informa ao sistema que a ação foi atendida.
4. Antes que a chamada se conecte, o CallKit fornece uma `AVAudioSession` correspondência de alta prioridade com a configuração solicitada pelo aplicativo. O aplicativo será notificado por meio do `DidActivateAudioSession` método de seu `CXProviderDelegate` .

## <a name="working-with-call-directory-extensions"></a>Trabalhando com extensões de diretório de chamada

Ao trabalhar com CallKit, _as extensões de diretório de chamada_ fornecem uma maneira de adicionar números de chamada bloqueados e identificar números específicos a um determinado aplicativo VoIP para contatos no aplicativo de contato no dispositivo IOS.

### <a name="implementing-a-call-directory-extension"></a>Implementando uma extensão de diretório de chamada

Para implementar uma extensão de diretório de chamada em um aplicativo Xamarin. iOS, faça o seguinte:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Abra a solução do aplicativo no Visual Studio para Mac.
2. Clique com o botão direito do mouse no nome da solução na **Gerenciador de soluções** e selecione **Adicionar**  >  **Adicionar novo projeto**.
3. Selecione **iOS**  >  **extensões**  >  **do IOS extensões de diretório** e clique no botão **Avançar** :

    [![Criando uma nova extensão de diretório de chamada](callkit-images/calldir01.png)](callkit-images/calldir01.png#lightbox)
4. Insira um **nome** para a extensão e clique no botão **Avançar** :

    [![Inserindo um nome para a extensão](callkit-images/calldir02.png)](callkit-images/calldir02.png#lightbox)
5. Ajuste o **nome do projeto** e/ou o **nome da solução** , se necessário, e clique no botão **criar** :

    [![Criação do projeto](callkit-images/calldir03.png)](callkit-images/calldir03.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Abra a solução do aplicativo no Visual Studio.
2. Clique com o botão direito do mouse no nome da solução na **Gerenciador de soluções** e selecione **Adicionar**  >  **Adicionar novo projeto**.
3. Selecione **iOS**  >  **extensões**  >  **do IOS extensões de diretório** e clique no botão **Avançar** :

    [![Criando uma nova extensão de diretório de chamada](callkit-images/calldir01w.png)](callkit-images/calldir01.png#lightbox)
4. Insira um **nome** para a extensão e clique no botão **OK**

-----

Isso adicionará uma `CallDirectoryHandler.cs` classe ao projeto semelhante ao seguinte:

```csharp
using System;

using Foundation;
using CallKit;

namespace MonkeyCallDirExtension
{
    [Register ("CallDirectoryHandler")]
    public class CallDirectoryHandler : CXCallDirectoryProvider, ICXCallDirectoryExtensionContextDelegate
    {
        #region Constructors
        protected CallDirectoryHandler (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void BeginRequest (CXCallDirectoryExtensionContext context)
        {
            context.Delegate = this;

            if (!AddBlockingPhoneNumbers (context)) {
                Console.WriteLine ("Unable to add blocking phone numbers");
                var error = new NSError (new NSString ("CallDirectoryHandler"), 1, null);
                context.CancelRequest (error);
                return;
            }

            if (!AddIdentificationPhoneNumbers (context)) {
                Console.WriteLine ("Unable to add identification phone numbers");
                var error = new NSError (new NSString ("CallDirectoryHandler"), 2, null);
                context.CancelRequest (error);
                return;
            }

            context.CompleteRequest (null);
        }
        #endregion

        #region Private Methods
        private bool AddBlockingPhoneNumbers (CXCallDirectoryExtensionContext context)
        {
            // Retrieve phone numbers to block from data store. For optimal performance and memory usage when there are many phone numbers,
            // consider only loading a subset of numbers at a given time and using autorelease pool(s) to release objects allocated during each batch of numbers which are loaded.
            //
            // Numbers must be provided in numerically ascending order.

            long [] phoneNumbers = { 14085555555, 18005555555 };

            foreach (var phoneNumber in phoneNumbers)
                context.AddBlockingEntry (phoneNumber);

            return true;
        }

        private bool AddIdentificationPhoneNumbers (CXCallDirectoryExtensionContext context)
        {
            // Retrieve phone numbers to identify and their identification labels from data store. For optimal performance and memory usage when there are many phone numbers,
            // consider only loading a subset of numbers at a given time and using autorelease pool(s) to release objects allocated during each batch of numbers which are loaded.
            //
            // Numbers must be provided in numerically ascending order.

            long [] phoneNumbers = { 18775555555, 18885555555 };
            string [] labels = { "Telemarketer", "Local business" };

            for (var i = 0; i < phoneNumbers.Length; i++) {
                long phoneNumber = phoneNumbers [i];
                string label = labels [i];
                context.AddIdentificationEntry (phoneNumber, label);
            }

            return true;
        }
        #endregion

        #region Public Methods
        public void RequestFailed (CXCallDirectoryExtensionContext extensionContext, NSError error)
        {
            // An error occurred while adding blocking or identification entries, check the NSError for details.
            // For Call Directory error codes, see the CXErrorCodeCallDirectoryManagerError enum.
            //
            // This may be used to store the error details in a location accessible by the extension's containing app, so that the
            // app may be notified about errors which occurred while loading data even if the request to load data was initiated by
            // the user in Settings instead of via the app itself.
        }
        #endregion
    }
}
```

O `BeginRequest` método no manipulador de diretório de chamada precisará ser modificado para fornecer a funcionalidade necessária. No caso do exemplo acima, ele tenta definir a lista de números bloqueados e disponíveis no banco de dados de contatos do aplicativo VOIP. Se qualquer uma das solicitações falhar por qualquer motivo, crie um `NSError` para descrever a falha e passá-la para o `CancelRequest` método da `CXCallDirectoryExtensionContext` classe.

Para definir os números bloqueados, use o `AddBlockingEntry` método da `CXCallDirectoryExtensionContext` classe. Os números fornecidos para o método _devem_ estar em ordem numérica crescente. Para obter o melhor desempenho e uso de memória quando há muitos números de telefone, considere carregar apenas um subconjunto de números em um determinado momento e usar pools de lançamentos para liberar objetos alocados durante cada lote de números que são carregados.

Para informar para contatar o aplicativo dos números de contato conhecidos pelo aplicativo VOIP, use o `AddIdentificationEntry` método da `CXCallDirectoryExtensionContext` classe e forneça o número e um rótulo de identificação. Novamente, os números fornecidos para o método _devem_ estar em ordem numérica crescente. Para obter o melhor desempenho e uso de memória quando há muitos números de telefone, considere carregar apenas um subconjunto de números em um determinado momento e usar pools de lançamentos para liberar objetos alocados durante cada lote de números que são carregados.

## <a name="summary"></a>Resumo

Este artigo abordou a nova API do CallKit lançada pela Apple no iOS 10 e como implementá-la em aplicativos de VOIP do Xamarin. iOS. Ele mostrou como o CallKit permite que um aplicativo se integre ao sistema iOS, como ele fornece a paridade de recursos com aplicativos internos (como telefone) e como ele aumenta a visibilidade de um aplicativo durante o iOS em locais como as telas de bloqueio e de página inicial, por meio de interações de siri e por meio dos aplicativos de contatos.

## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
