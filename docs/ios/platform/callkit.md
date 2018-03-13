---
title: CallKit
description: "Este artigo aborda a nova API CallKit que Apple lançada em 10 de iOS e como implementá-la em aplicativos xamarin VOIP."
ms.topic: article
ms.prod: xamarin
ms.assetid: 738A142D-FFD2-4738-B3ED-57C273179848
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/15/2017
ms.openlocfilehash: cf519cb964bf852c74249c874b9a934d4a6cf5c3
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="callkit"></a>CallKit

_Este artigo aborda a nova API CallKit que Apple lançada em 10 de iOS e como implementá-la em aplicativos xamarin VOIP._


A nova API CallKit no iOS 10 fornece uma maneira para aplicativos VOIP integrar o iPhone da interface do usuário e fornecer uma interface familiar e experiência do usuário final. Com essa API os usuários podem exibir e interagir com chamadas VOIP de tela de bloqueio do dispositivo iOS e gerenciar contatos usando o aplicativo de telefone **Favoritos** e **recentes** modos de exibição.

## <a name="about-callkit"></a>Sobre CallKit

De acordo com a Apple, CallKit é uma nova estrutura que elevará 3º aplicativos de voz sobre IP (VOIP) de parte de um 1º participante experiência no iOS 10. A API CallKit permite que aplicativos VOIP integrar o iPhone da interface do usuário e fornecer uma interface familiar e experiência do usuário final. Assim como o aplicativo de telefone interno, um usuário pode exibir e interagir com chamadas VOIP de tela de bloqueio do dispositivo iOS e gerenciar contatos usando o aplicativo de telefone **Favoritos** e **recentes** modos de exibição.

Além disso, a API de CallKit fornece a capacidade de criar extensões de aplicativo que pode associar um número de telefone com um nome (ID de chamadas) ou diga o sistema quando um número deve ser bloqueado (chamada de bloqueio).

### <a name="the-existing-voip-app-experience"></a>A experiência de aplicativo VOIP existente

Antes de discutir sobre a nova API CallKit e suas capacidades, dar uma olhada na experiência do usuário atual, com um 3º de terceiros aplicativo VOIP no iOS 9 (e menor) usando um aplicativo VOIP fictício chamado MonkeyCall. MonkeyCall é um aplicativo simples que permite ao usuário enviar e receber chamadas VOIP usando APIs do iOS existente.

No momento, se o usuário recebe uma chamada de entrada MonkeyCall e seu iPhone está bloqueado, a notificação recebida na tela de bloqueio é indistinguível de qualquer outro tipo de notificação (como aqueles de mensagens ou aplicativos de email por exemplo).

Se o usuário deseja responder à chamada, precisaria deslize a notificação MonkeyCall para abrir o aplicativo e digite sua senha (ou o Touch ID de usuário) para desbloquear o telefone antes de serem podem aceitar a chamada e iniciar a conversa.

A experiência é igualmente complicada se o telefone está desbloqueado. Novamente, a chamada MonkeyCall é exibida como uma faixa de notificação standard slides de na parte superior da tela. Como a notificação é temporária, podem ser perdida facilmente pelo usuário forçando-para abrir o Centro de notificação e localizar a notificação específica para responder, em seguida, chamar ou localizar e iniciar o aplicativo MonkeyCall manualmente.

### <a name="the-callkit-voip-app-experience"></a>A experiência de aplicativo VOIP CallKit

Ao implementar as novas APIs CallKit no aplicativo MonkeyCall, a experiência do usuário com uma chamada VOIP pode ser significativamente melhor em iOS 10. Veja o exemplo do usuário receber uma chamada VOIP quando seu telefone está bloqueado acima. Implementando CallKit, a chamada será exibida na tela de bloqueio do iPhone, como faria se a chamada foi sendo recebida de aplicativo interno do telefone, com a tela inteira, interface do usuário nativa e funcionalidade passe o dedo para a resposta padrão.

Novamente, se o iPhone é desbloqueado após o recebimento de uma chamada de MonkeyCall VOIP, o mesmo, interface do usuário nativa e tela inteira funcionalidade passe o dedo para a resposta e toque para recusar padrão do telefone aplicativo é apresentado e MonkeyCall interna tem a opção de reproduzir um toque personalizado .

CallKit fornece funcionalidade adicional para MonkeyCall, permitindo que seus VOIP chamadas para interagir com outros tipos de chamadas, para constar a recentes interna e a lista de Favoritos, para usar os recursos internos não interfira forma e bloco, inicie MonkeyCall chamadas de Siri e oferece a capacidade dos usuários atribuir MonkeyCall chamadas para as pessoas no aplicativo de contatos.

As seções a seguir aborda a arquitetura CallKit, a entrada e saída chamar fluxos e a API CallKit em detalhes.


## <a name="the-callkit-architecture"></a>A arquitetura de CallKit

No iOS 10, Apple adotou CallKit em todos os serviços do sistema, de modo que as chamadas feitas em CarPlay, por exemplo, são conhecidas para a interface do usuário do sistema por meio de CallKit. No exemplo fornecido abaixo, pois MonkeyCall adota CallKit, ele é conhecido pelo sistema da mesma forma como esses serviços de sistema internas e obtém todos os recursos de:

[![](callkit-images/callkit01.png "A pilha do serviço CallKit")](callkit-images/callkit01.png#lightbox)

Examine perto o App MonkeyCall do diagrama acima. O aplicativo contém todos os seus códigos para se comunicar com sua própria rede e contém suas próprias Interfaces do usuário. Vincula em CallKit para se comunicar com o sistema:

[![](callkit-images/callkit02.png "Arquitetura de aplicativo MonkeyCall")](callkit-images/callkit02.png#lightbox)

Há duas interfaces principais CallKit que usa o aplicativo:

- `CXProvider` -Isso permite que o aplicativo MonkeyCall informar o sistema de notificações fora de banda que podem ocorrer.
- `CXCallController` -Permite que o aplicativo MonkeyCall informar o sistema de ações de usuário local.

### <a name="the-cxprovider"></a>O CXProvider

Como mencionado acima, `CXProvider` permite que um aplicativo informar o sistema de notificações fora de banda que podem ocorrer. Esses são notificação não ocorrem devido a ações de usuário local, mas ocorrer devido a eventos externos, como chamadas de entrada.

Um aplicativo deve usar o `CXProvider` para o seguinte:

- Uma chamada para o sistema de relatórios.
- Relatório uma chamada de saída foi conectado ao sistema.
- O usuário remoto encerrar a chamada para o sistema de relatórios.

Quando o aplicativo deseja se comunicar com o sistema, ele usa o `CXCallUpdate` classe e quando o sistema precisa se comunicar com o aplicativo, ele usa o `CXAction` classe:

[![](callkit-images/callkit03.png "Comunicação com o sistema por meio de um CXProvider")](callkit-images/callkit03.png#lightbox)

### <a name="the-cxcallcontroller"></a>O CXCallController

O `CXCallController` permite que um aplicativo informar o sistema de ações de usuário local como o usuário iniciar uma chamada VOIP. Implementando um `CXCallController` o aplicativo obtém a interação com outros tipos de chamadas do sistema. Por exemplo, se já houver uma chamada de telefonia ativa em andamento, `CXCallController` pode permitir que o aplicativo VOIP fazer essa chamada em espera e iniciar ou responder a uma chamada VOIP.

Um aplicativo deve usar o `CXCallController` para o seguinte:

- Relatório quando o usuário iniciou uma chamada de saída para o sistema.
- Relatório quando o usuário responde a uma chamada de entrada para o sistema.
- Relatório quando o usuário termina uma chamada para o sistema.

Quando o aplicativo deseja se comunicar ações de usuário local para o sistema, ele usa o `CXTransaction` classe:

[![](callkit-images/callkit04.png "Emissão de relatórios para o sistema usando uma CXCallController")](callkit-images/callkit04.png#lightbox)

## <a name="implementing-callkit"></a>Implementando CallKit

As seções a seguir mostrará como implementar CallKit em um aplicativo xamarin VOIP. Por exemplo, este documento usará o código do aplicativo MonkeyCall VOIP fictício. O código apresentado aqui representa várias classes de suporte, o CallKit partes específicas serão abordado em detalhes nas seções a seguir.

### <a name="the-activecall-class"></a>A classe ActiveCall

O `ActiveCall` classe é usada pelo aplicativo MonkeyCall para manter todas as informações sobre uma chamada VOIP está ativa no momento da seguinte maneira:

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

`ActiveCall` contém várias propriedades que definem o estado de chamada e dois eventos que podem ser gerados quando o estado de chamada é alterado. Como esta é apenas um exemplo, há três métodos usados para simulados iniciando, responder e encerrar uma chamada.

### <a name="the-startcallrequest-class"></a>A classe StartCallRequest

O `StartCallRequest` classe estática, fornece alguns métodos auxiliares que serão usados quando chama de trabalhar com saída:

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

O `CallHandleFromURL` e `CallHandleFromActivity` classes são usadas no AppDelegate para obter o identificador de contato da pessoa que está sendo chamado em uma chamada de saída. Para obter mais informações, consulte o [chamadas tratamento](#Handling-Outgoing-Calls) seção abaixo.

### <a name="the-activecallmanager-class"></a>A classe ActiveCallManager

O `ActiveCallManager` classe manipula todas as chamadas no aplicativo MonkeyCall.

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
                if (call.UUID == uuid) return call;
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

Novamente, uma vez que isso é apenas uma simulação de `ActiveCallManager` só mantém uma coleção de `ActiveCall` objetos e tem uma rotina para localizar uma chamada fornecida pelo seu `UUID` propriedade. Ele também inclui métodos para iniciar e terminar o estado em espera de uma chamada de saída. Para obter mais informações, consulte o [chamadas tratamento](#Handling-Outgoing-Calls) seção abaixo.

### <a name="the-providerdelegate-class"></a>A classe ProviderDelegate

Como discutido acima, uma `CXProvider` fornece comunicação bidirecional entre o aplicativo e o sistema para que as notificações de fora da banda. O desenvolvedor precisa fornecer um personalizado `CXProviderDelegate` e anexá-lo para o `CXProvider` para o aplicativo tratar eventos de CallKit fora de banda. MonkeyCall usa os seguintes `CXProviderDelegate`:

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

            // Get Image Mask
            var maskImage = UIImage.FromFile ("telephone_receiver.png");

            // Setup the initial configurations
            Configuration = new CXProviderConfiguration ("MonkeyCall") {
                MaximumCallsPerCallGroup = 1,
                SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
                IconMaskImageData = maskImage.AsPNG(),
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
                    Provider.ReportConnectingOutgoingCall (call.UUID, call.StartedConnectingOn.ToNsDate());
                }
            };

            activeCall.ConnectedChanged += (call) => {
                if (call.isConnected) {
                    // Inform system that the call has connected
                    provider.ReportConnectedOutgoingCall (call.UUID, call.ConnectedOn.ToNsDate ());
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

Quando uma instância deste delegado é criada, ele é passado a `ActiveCallManager` que ele usará para lidar com qualquer atividade de chamada. Em seguida, ele define os tipos de identificador (`CXHandleType`) que o `CXProvider` responderá:

```csharp
// Define handle types
var handleTypes = new [] { (NSNumber)(int)CXHandleType.PhoneNumber };
```

E obtém a máscara que será aplicada para o ícone do aplicativo quando uma chamada está em andamento:

```csharp
// Get Image Mask
var maskImage = UIImage.FromFile ("telephone_receiver.png");
```

Esses valores obtenham empacotados em um `CXProviderConfiguration` que será usado para configurar o `CXProvider`:

```csharp
// Setup the initial configurations
Configuration = new CXProviderConfiguration ("MonkeyCall") {
    MaximumCallsPerCallGroup = 1,
    SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
    IconMaskImageData = maskImage.AsPNG(),
    RingtoneSound = "musicloop01.wav"
};
```

O representante, em seguida, cria um novo `CXProvider` com essas configurações e se anexa a ele:

```csharp
// Create a new provider
Provider = new CXProvider (Configuration);

// Attach this delegate
Provider.SetDelegate (this, null);
```

Ao usar CallKit, o aplicativo não será criar e manipular seus próprio sessões de áudio, em vez disso, ele será necessário configurar e usar uma sessão de áudio que o sistema irá criar e manipular para ele. 

Se esse fosse um aplicativo real, o `DidActivateAudioSession` método seria usado para iniciar a chamada com um pré-configurado `AVAudioSession` que forneceu o sistema:

```csharp
public override void DidActivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // Start the call's audio session here...
}
```

Também deve usar o `DidDeactivateAudioSession` método para finalizar e liberar sua conexão ao sistema fornecido áudio sessão:

```csharp
public override void DidDeactivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // End the calls audio session and restart any non-call
    // releated audio
}
```

O restante do código será abordado em detalhes nas seções a seguir.

### <a name="the-appdelegate-class"></a>A classe AppDelegate

MonkeyCall usa o AppDelegate para manter instâncias de `ActiveCallManager` e `CXProviderDelegate` que será usado em todo o aplicativo:

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

O `OpenUrl` e `ContinueUserActivity` substituir métodos são usados quando o aplicativo está processando uma chamada de saída. Para obter mais informações, consulte o [chamadas tratamento](#Handling-Outgoing-Calls) seção abaixo.

## <a name="handling-incoming-calls"></a>Tratamento de chamadas

Há vários estados e processos que uma chamada VOIP pode passar por durante um típico fluxo de trabalho de chamada de entrada, como:

- Informar o usuário (e o sistema) que existe uma chamada de entrada.
- Receber notificação quando o usuário deseja responder à chamada e inicializando a chamada com outro usuário.
- Informe o sistema e a rede de comunicação, quando o usuário deseja encerrar a chamada atual.

As seções a seguir terão uma visão detalhada de como um aplicativo pode usar CallKit para lidar com o fluxo de trabalho o chamada de entrada, novamente usando o aplicativo MonkeyCall VOIP como um exemplo.

### <a name="informing-user-of-incoming-call"></a>Informar o usuário de chamada

Quando um usuário remoto iniciou uma conversa de VOIP do usuário local, ocorre o seguinte:

[![](callkit-images/callkit05.png "Um usuário remoto iniciou uma conversa de VOIP")](callkit-images/callkit05.png#lightbox)

1. O aplicativo recebe uma notificação da sua rede de comunicação que há uma chamada VOIP.
2. O aplicativo usa o `CXProvider` para enviar um `CXCallUpdate` para o sistema informando-lo da chamada.
3. O sistema publica a chamada para a interface do usuário do sistema, serviços do sistema e quaisquer outros aplicativos VOIP usando CallKit.

Por exemplo, o `CXProviderDelegate`:

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

Esse código cria um novo `CXCallUpdate` instância e anexa um identificador que identifica o chamador. Em seguida, ele usa o `ReportNewIncomingCall` método o `CXProvider` classe para informar o sistema da chamada. Se for bem-sucedida, a chamada será adicionada à coleção do aplicativo de chamadas ativas, se não estiver, o erro precisa ser informada ao usuário.

### <a name="user-answering-incoming-call"></a>Chamada de entrada de resposta do usuário

Se o usuário deseja atender a chamada VOIP, ocorre o seguinte:

[![](callkit-images/callkit06.png "O usuário responde à chamada VOIP de entrada")](callkit-images/callkit06.png#lightbox)

1. A interface do usuário do sistema informa o sistema que o usuário deseja responder à chamada VOIP.
2. O sistema envia um `CXAnswerCallAction` para o aplicativo `CXProvider` informando-a intenção de resposta.
3. O aplicativo informa a sua rede de comunicação que o usuário está respondendo a chamada e a chamada VOIP continua normalmente.

Por exemplo, o `CXProviderDelegate`:

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

Esse código primeiro procura a determinada chamada em sua lista de chamadas ativas. Se a chamada não for encontrada, o sistema será notificado e o método será encerrado. Se ele for encontrado, o `AnswerCall` método o `ActiveCall` classe é chamada para iniciar a chamada e o sistema de informações se ele for bem-sucedida ou falha.

### <a name="user-ending-incoming-call"></a>Terminando de chamada de entrada do usuário

Se o usuário deseja encerrar a chamada de dentro da interface do usuário do aplicativo, ocorre o seguinte:

[![](callkit-images/callkit07.png "O usuário encerra a chamada de dentro da interface do usuário do aplicativo")](callkit-images/callkit07.png#lightbox)

1. O aplicativo cria `CXEndCallAction` que obtém empacotada em um `CXTransaction` que é enviada para o sistema para informá-lo que a chamada está terminando.
2. O sistema verifica a tentativa de chamar final e envia o `CXEndCallAction` volta para o aplicativo por meio de `CXProvider`.
3. O aplicativo então informa sua rede de comunicação que a chamada está terminando.

Por exemplo, o `CXProviderDelegate`:

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

Esse código primeiro procura a determinada chamada em sua lista de chamadas ativas. Se a chamada não for encontrada, o sistema será notificado e o método será encerrado. Se ele for encontrado, o `EndCall` método o `ActiveCall` classe é chamada para encerrar a chamada e o sistema de informações se ele for bem-sucedida ou falha. Se for bem-sucedido, a chamada será removida da coleção de chamadas ativas.

## <a name="managing-multiple-calls"></a>Gerenciando várias chamadas

A maioria dos aplicativos VOIP pode manipular várias chamadas de uma vez. Por exemplo, se há uma chamada VOIP ativa no momento e a notificação do aplicativo obtém que há uma nova chamada de entrada, o usuário pode pausar ou desligamento na primeira chamada para responder a segunda.

Em conceder a situação acima, o sistema enviará um `CXTransaction` para o aplicativo que inclui uma lista de várias ações (como o `CXEndCallAction` e `CXAnswerCallAction`). Todas essas ações precisará ser atendida individualmente, para que o sistema pode atualizar a interface do usuário adequadamente.

## <a name="handling-outgoing-calls"></a>Tratamento de chamadas de saída

Se o usuário toca uma entrada da lista recentes (no aplicativo do telefone), por exemplo, que é de uma chamada que pertencem ao aplicativo, ele será enviado um _iniciar intenção chamar_ pelo sistema:

[![](callkit-images/callkit08.png "Recebimento de uma tentativa de chamada de início")](callkit-images/callkit08.png#lightbox)

1. O aplicativo criará um _iniciar ação chamar_ com base em Iniciar chamar intenção que ele recebeu do sistema. 
2. O aplicativo usará o `CXCallController` para solicitar a ação de chamar Iniciar do sistema.
3. Se o sistema aceitar a ação, ele será retornado para o aplicativo por meio de `XCProvider` delegate.
4. O aplicativo inicia a chamada de saída com a sua rede de comunicação.

Para obter mais informações sobre tentativas, consulte nosso [propósitos de extensões de interface do usuário e tentativas](~/ios/platform/sirikit/understanding-sirikit.md) documentação. 

### <a name="the-outgoing-call-lifecycle"></a>O ciclo de vida de chamada de saída

Ao trabalhar com CallKit e uma chamada de saída, o aplicativo precisará informar ao sistema dos eventos de ciclo de vida a seguir:

1. **Iniciando** -informar o sistema que uma chamada de saída está prestes a iniciar.
2. **Iniciado** -informar o sistema que iniciou uma chamada de saída.
3. **Conectar-se** -informar ao sistema que a chamada de saída está se conectando.
4. **Conectado** -informar a saída chamada se conectou e ambas as partes podem se comunicar agora.

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

Ele cria um `CXHandle` e o utiliza para configurar um `CXStartCallAction` que é fornecido em um `CXTransaction` que é enviado para o sistema usando o `RequestTransaction` método do `CXCallController` classe. Chamando o `RequestTransaction` método, o sistema pode colocar qualquer existente chamadas em espera, não importa a origem (aplicativo de telefone, FaceTime, VOIP, etc.), antes do início da chamada de novo.

A solicitação para iniciar uma chamada de VOIP saída pode vir de várias fontes diferentes, como o Siri, uma entrada em um cartão de visita (no aplicativo contatos) ou da lista de recentes (no aplicativo do telefone). Nessas situações, o aplicativo será enviado uma tentativa de chamar iniciar dentro de um `NSUserActivity` e o AppDelegate precisará de tratá-la:

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

Aqui o `CallHandleFromActivity` método da classe auxiliar `StartCallRequest` está sendo usado para obter o identificador para a pessoa que está sendo chamada (consulte [a classe StartCallRequest](#The-StartCallRequest-Class) acima). 

O `PerformStartCallAction` método o [ProviderDelegate classe](#The-ProviderDelegate-Class) é usada para iniciar a chamada de saída real finalmente e informar o sistema de seu ciclo de vida:

```csharp
public override void PerformStartCallAction (CXProvider provider, CXStartCallAction action)
{
    // Create new call record
    var activeCall = new ActiveCall (action.CallUuid, action.CallHandle.Value, true);

    // Monitor state changes
    activeCall.StartingConnectionChanged += (call) => {
        if (call.IsConnecting) {
            // Inform system that the call is starting
            Provider.ReportConnectingOutgoingCall (call.UUID, call.StartedConnectingOn.ToNsDate());
        }
    };

    activeCall.ConnectedChanged += (call) => {
        if (call.IsConnected) {
            // Inform system that the call has connected
            Provider.ReportConnectedOutgoingCall (call.UUID, call.ConnectedOn.ToNsDate ());
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

Ele cria uma instância do `ActiveCall` classe (para armazenar informações sobre a chamada em andamento) e o preenche com a pessoa que está sendo chamada. O `StartingConnectionChanged` e `ConnectedChanged` eventos são usados para monitorar e relatar o ciclo de vida de chamada de saída. A chamada é iniciada e o sistema informado de que a ação foi atendida.

### <a name="ending-an-outgoing-call"></a>Encerrar uma chamada de saída

Quando o usuário termina com uma chamada de saída e deseja encerrá-lo, o código a seguir pode ser usado:

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

Se cria um `CXEndCallAction` com o UUID da chamada ao fim, inclui-lo em uma `CXTransaction` que é enviado para o sistema usando o `RequestTransaction` método do `CXCallController` classe. 

## <a name="additional-callkit-details"></a>Detalhes adicionais CallKit

Esta seção aborda alguns detalhes adicionais que o desenvolvedor precisa levar em consideração ao trabalhar com CallKit como:

- Configuração do provedor
- Erros de ação
- Restrições do sistema
- Áudio VOIP

### <a name="provider-configuration"></a>Configuração do provedor

A configuração do provedor permite que um aplicativo VOIP do iOS 10 personalizar a experiência do usuário (dentro a interface do usuário na chamada nativa) ao trabalhar com CallKit.

Um aplicativo pode fazer os seguintes tipos de personalizações:

- Exiba um nome localizado.
- Habilite suporte a chamada de vídeo.
- Personalize os botões da interface do usuário na chamada apresentando seu próprio ícone de imagem mascarada. Interação do usuário com botões personalizados é enviada diretamente para o aplicativo a ser processado. 

### <a name="action-errors"></a>Erros de ação

aplicativos do iOS 10 VOIP usando CallKit precisam lidar com ações falhando normalmente e manter o usuário informado sobre o estado de ação em todos os momentos. 

Consideram o exemplo a seguir:

1. O aplicativo recebeu uma ação de chamar iniciar e iniciou o processo de inicialização de uma nova chamada VOIP com sua rede de comunicação.
2. Devido a acesso limitado ou nenhum recurso de comunicação de rede, essa conexão falha.
3. O aplicativo *deve* enviar o **falha** mensagem de volta para a ação de chamar iniciar (`Action.Fail()`) para informar o sistema da falha.
4. Isso permite que o sistema informar ao usuário sobre o status da chamada. Por exemplo, para exibir a interface do usuário chamar de falha.

Além disso, um aplicativo do iOS 10 VOIP precisará responder para _erros de tempo limite_ que pode ocorrer quando uma ação esperada não pode ser processada dentro de um determinado período de tempo. Cada tipo de ação fornecido pelo CallKit tem um valor de tempo limite máximo associado a ele. Esses valores de tempo limite Certifique-se de que qualquer ação CallKit solicitado pelo usuário é tratada de maneira responsiva, mantendo assim o sistema operacional flexível e responsivo também.

Há vários métodos sobre o delegado de provedor (`CXProviderDelegate`) que deve ser substituído para manipular normalmente também este situações de tempo limite.

### <a name="system-restrictions"></a>Restrições do sistema

Com base no estado atual do dispositivo iOS que está executando o aplicativo do iOS 10 VOIP, certas restrições de sistema podem ser impostas.

Por exemplo, uma chamada VOIP pode ser restringida pelo sistema se:

1. A pessoa chamada estiver na lista de chamador de bloqueados do usuário.
2. O dispositivo iOS do usuário está no modo interfira forma não.

Se uma chamada VOIP é restrito por qualquer uma dessas situações, use o seguinte código para tratá-la:

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

CallKit oferece vários benefícios para lidar com os recursos de áudio que um aplicativo do iOS 10 VOIP exigirá durante uma chamada VOIP ao vivo. Uma das maiores vantagens é que o aplicativo áudio sessão será aumentaram prioridades durante a execução no iOS 10. Este é o mesmo nível de prioridade que o telefone interna e FaceTime aplicativos e esse nível de prioridade avançado impedirá outros aplicativos em execução de interromper a sessão de áudio de VOIP do aplicativo.

Além disso, CallKit tem acesso a outras dicas de roteamentos de áudio que pode melhorar o desempenho e rotear inteligente áudio VOIP para dispositivos de saída específicos durante uma chamada dinâmica com base nas preferências do usuário e estados de dispositivo. Por exemplo, com base em dispositivos anexados como fones de ouvido bluetooth, uma conexão CarPlay ao vivo ou configurações de acessibilidade.

Durante o ciclo de vida de um típico VOIP chamar usando CallKit, o aplicativo será necessário configurar o fluxo de áudio CallKit fornecerá. Veja o exemplo a seguir:

[![](callkit-images/callkit09.png "A sequência de ação de chamada inicial")](callkit-images/callkit09.png#lightbox)

1. Uma ação de chamar Iniciar é recebida pelo aplicativo para responder a uma chamada de entrada.
2. Antes que essa ação é atendida pelo aplicativo, ele fornece exigirá a configuração para seu `AVAudioSession`.
3. O aplicativo informa o sistema que a ação foi atendida.
4. Antes da chamada se conecta, CallKit fornece uma alta prioridade `AVAudioSession` correspondendo à configuração do que o aplicativo solicitado. O aplicativo será notificado por meio de `DidActivateAudioSession` método de sua `CXProviderDelegate`.

## <a name="working-with-call-directory-extensions"></a>Trabalhando com extensões de diretório de chamada

Ao trabalhar com CallKit, _chamar extensões de diretório_ fornecem uma maneira de adicionar números de chamada bloqueados e identificar os números que são específicos para um determinado aplicativo de VOIP contatos no aplicativo do contato no dispositivo iOS.

### <a name="implementing-a-call-directory-extension"></a>Implementando uma extensão de diretório de chamada

Para implementar uma extensão de diretório chamada em um aplicativo xamarin, faça o seguinte:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Abra a solução do aplicativo no Visual Studio para Mac.
2. Clique com botão direito no nome da solução no **Solution Explorer** e selecione **adicionar** > **adicionar novo projeto**.
3. Selecione **iOS** > **extensões** > **chamar extensões de diretório** e clique no **próximo** botão: 

    [![](callkit-images/calldir01.png "Criando uma nova extensão de diretório chamar")](callkit-images/calldir01.png#lightbox)
4. Insira um **nome** para a extensão e clique no **próximo** botão: 

    [![](callkit-images/calldir02.png "Inserir um nome para a extensão")](callkit-images/calldir02.png#lightbox)
5. Ajustar o **nome do projeto** e/ou **nome da solução** se necessário e clique no **criar** botão: 

    [![](callkit-images/calldir03.png "Criando o projeto")](callkit-images/calldir03.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Abra a solução do aplicativo no Visual Studio.
2. Clique com botão direito no nome da solução no **Solution Explorer** e selecione **adicionar** > **adicionar novo projeto**.
3. Selecione **iOS** > **extensões** > **chamar extensões de diretório** e clique no **próximo** botão: 

    [![](callkit-images/calldir01w.png "Criando uma nova extensão de diretório chamar")](callkit-images/calldir01.png#lightbox)
4. Insira um **nome** para a extensão e clique no **Okey** botão

-----


Isso adicionará uma `CallDirectoryHandler.cs` classe ao projeto que é semelhante ao seguinte:

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

O `BeginRequest` método no manipulador de diretório de chamada precisará ser modificada para fornecer a funcionalidade necessária. No caso do exemplo acima, ele tenta definir a lista de números bloqueados e está disponíveis no banco de dados do aplicativo VOIP contatos. Se o solicita falhar por algum motivo, crie um `NSError` para descrever a falha e passá-lo a `CancelRequest` método do `CXCallDirectoryExtensionContext` classe.

Para definir o uso de números bloqueado o `AddBlockingEntry` método o `CXCallDirectoryExtensionContext` classe. Os números fornecidos para o método _deve_ estar em ordem numérica crescente. Para melhor desempenho e uso de memória quando há que muitos números de telefone, considere apenas carregar um subconjunto de números em um determinado momento e usando pools de autorelease para liberar objetos alocados durante cada lote de números que são carregados.

Para informar ao aplicativo de contato dos números de contato conhecidos para o aplicativo VOIP, use o `AddIdentificationEntry` método o `CXCallDirectoryExtensionContext` classe e forneça o número e um rótulo de identificação. Novamente, os números fornecidos para o método _deve_ estar em ordem numérica crescente. Para melhor desempenho e uso de memória quando há que muitos números de telefone, considere apenas carregar um subconjunto de números em um determinado momento e usando pools de autorelease para liberar objetos alocados durante cada lote de números que são carregados.


## <a name="summary"></a>Resumo

Este artigo abordou a nova API CallKit que Apple lançada em 10 de iOS e como implementá-la em aplicativos xamarin VOIP. Mostrou como CallKit permite que um aplicativo integrar o sistema do iOS, como ele fornece paridade de recursos com aplicativos internos (por exemplo, um telefone) e como aumenta a visibilidade de um aplicativo em iOS em locais, como o bloqueio e telas de início, através de interações Siri e os aplicativos de contatos.



## <a name="related-links"></a>Links relacionados

- [Exemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
