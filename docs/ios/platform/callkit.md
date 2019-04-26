---
title: CallKit no xamarin. IOS
description: Este artigo aborda a nova API CallKit que a Apple lançada no iOS 10 e como implementá-lo em aplicativos xamarin. IOS VOIP.
ms.prod: xamarin
ms.assetid: 738A142D-FFD2-4738-B3ED-57C273179848
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: 6db9ff0085c17f07d07a7591f5d735793bfbc5f9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61389949"
---
# <a name="callkit-in-xamarinios"></a>CallKit no xamarin. IOS

A nova API de CallKit no iOS 10 fornece uma maneira para aplicativos VOIP integrar com o iPhone da interface do usuário e fornecer uma interface familiar e experiência para o usuário final. Com essa API os usuários podem exibir e interagir com chamadas VOIP de tela de bloqueio do dispositivo iOS e gerenciar contatos usando o aplicativo de telefone **Favoritos** e **recentes** modos de exibição.

## <a name="about-callkit"></a>Sobre CallKit

De acordo com a Apple, CallKit é uma nova estrutura que elevará 3ª aplicativos de voz sobre IP (VOIP) de terceiros para um 1º dia de terceiros experiência no iOS 10. A API CallKit permite que os aplicativos VOIP integrar com o iPhone da interface do usuário e fornecer uma interface familiar e experiência para o usuário final. Assim como o aplicativo de telefone interno, um usuário pode exibir e interagir com chamadas VOIP de tela de bloqueio do dispositivo iOS e gerenciar contatos usando o aplicativo de telefone **Favoritos** e **recentes** modos de exibição.

Além disso, a API de CallKit fornece a capacidade de criar extensões de aplicativo que pode associar um número de telefone com um nome (ID do chamador) ou informar ao sistema quando um número deve ser bloqueado (chamada de bloqueio).

### <a name="the-existing-voip-app-experience"></a>A experiência de aplicativo VOIP existente

Antes de discutir a nova API CallKit e suas capacidades, vejamos a experiência do usuário atual com uma 3ª de terceiros aplicativo VOIP no iOS 9 (e menor) usando um aplicativo VOIP fictício chamado MonkeyCall. MonkeyCall é um aplicativo simples que permite ao usuário enviar e receber chamadas VOIP usando APIs do iOS existentes.

Atualmente, se o usuário está recebendo uma chamada de entrada MonkeyCall e iPhone estiver bloqueado, a notificação recebida na tela de bloqueio é indistinguível de qualquer outro tipo de notificação (como aquelas das mensagens ou aplicativos de email por exemplo).

Se o usuário quisesse responde à chamada, precisaria deslize a notificação MonkeyCall para abrir o aplicativo e insira sua senha (ou ID de toque do usuário) para desbloquear o telefone antes de eles poderia aceitar a chamada e iniciar a conversa.

A experiência é igualmente complicada se o telefone está desbloqueado. Novamente, a chamada de entrada MonkeyCall é exibida como uma faixa de notificação padrão que desliza a partir da parte superior da tela. Uma vez que a notificação é temporária, podem ser perdida facilmente pelo usuário forçá-los para abrir o Centro de notificações e localizar a notificação específica para responder e em seguida, chamar ou localizar e iniciar o aplicativo de MonkeyCall manualmente.

### <a name="the-callkit-voip-app-experience"></a>A experiência de aplicativo CallKit VOIP

Ao implementar as novas APIs CallKit no aplicativo MonkeyCall, a experiência do usuário com uma chamada VOIP de entrada pode ser melhorada significativamente no iOS 10. Veja o exemplo do usuário que recebe uma chamada VOIP quando o telefone está bloqueado acima. Implementando CallKit, a chamada será exibida na tela de bloqueio do iPhone, exatamente como ele faria se a chamada que está sendo recebida do aplicativo de telefone interno, com a tela inteira, a interface do usuário nativa e a funcionalidade de passe o dedo para a resposta padrão.

Novamente, se o iPhone é desbloqueado quando uma chamada VOIP MonkeyCall é recebida, o mesmo em tela inteira, a interface do usuário nativa e a funcionalidade padrão de passar o dedo para resposta e toque para recusar o interna Phone app é apresentado e MonkeyCall tem a opção de reprodução de um toque personalizado .

CallKit fornece funcionalidade adicional a MonkeyCall, permitindo que seu VOIP chama para interagir com outros tipos de chamadas, apareça no internos em recentes e a lista de Favoritos, para usar os recursos internos de bloco e não incomodar, iniciar chamadas MonkeyCall do Siri e oferece a capacidade dos usuários atribuir MonkeyCall chamadas para as pessoas no aplicativo de contatos.

As seções a seguir abordarão a arquitetura CallKit, entrada e saída chamam fluxos e a API CallKit em detalhes.

## <a name="the-callkit-architecture"></a>A arquitetura CallKit

No iOS 10, a Apple adotou CallKit em todos os serviços do sistema, de modo que as chamadas feitas em CarPlay, por exemplo, são conhecidas para a interface do usuário do sistema por meio de CallKit. No exemplo fornecido abaixo, uma vez que MonkeyCall adota CallKit, ele é conhecido pelo sistema da mesma forma como esses serviços internos do sistema e obtém todos os mesmos recursos:

[![](callkit-images/callkit01.png "A pilha do serviço CallKit")](callkit-images/callkit01.png#lightbox)

Dar uma olhada mais próxima a App MonkeyCall no diagrama acima. O aplicativo contém todos os seus códigos para se comunicar com sua própria rede e contém seu próprio Interfaces do usuário. Ele vincula na CallKit para se comunicar com o sistema:

[![](callkit-images/callkit02.png "Arquitetura de aplicativo MonkeyCall")](callkit-images/callkit02.png#lightbox)

Há duas interfaces principais CallKit que o aplicativo usa:

- `CXProvider` -Isso permite que o aplicativo MonkeyCall informar o sistema de qualquer notificação de fora de banda que podem ocorrer.
- `CXCallController` -Permite que o aplicativo MonkeyCall informar o sistema de ações de usuário local.

### <a name="the-cxprovider"></a>O CXProvider

Conforme mencionado acima, `CXProvider` permite que um aplicativo informar o sistema de qualquer notificação de fora de banda que podem ocorrer. Esses são a notificação de que não ocorrem devido a ações de usuário local, mas ocorrer devido a eventos externos, como as chamadas de entrada.

Um aplicativo deve usar o `CXProvider` para os seguintes itens:

- Uma chamada recebida para o sistema de relatórios.
- Relatar um que chamadas de saída tem conectados ao sistema.
- O usuário remoto terminando a chamada para o sistema de relatórios.

Quando o aplicativo deseja se comunicar com o sistema, ele usa o `CXCallUpdate` classe, e quando o sistema precisa se comunicar com o aplicativo, ele usa o `CXAction` classe:

[![](callkit-images/callkit03.png "Comunicando-se com o sistema por meio de um CXProvider")](callkit-images/callkit03.png#lightbox)

### <a name="the-cxcallcontroller"></a>O CXCallController

O `CXCallController` permite que um aplicativo informar o sistema de ações de usuário local como o usuário iniciar uma chamada VOIP. Implementando um `CXCallController` o aplicativo obtém a interagem com outros tipos de chamadas no sistema. Por exemplo, se já houver uma chamada de telefonia do Active Directory em andamento, `CXCallController` pode permitir que o aplicativo VOIP colocar essa chamada em espera e iniciar ou responder a uma chamada VOIP.

Um aplicativo deve usar o `CXCallController` para os seguintes itens:

- Relatório quando o usuário iniciou uma chamada de saída para o sistema.
- Relatório quando o usuário responde a uma chamada recebida para o sistema.
- Relatório quando o usuário termina uma chamada para o sistema.

Quando o aplicativo deseja comunicar ações do usuário local no sistema, ele usa o `CXTransaction` classe:

[![](callkit-images/callkit04.png "Emissão de relatórios para o sistema usando um CXCallController")](callkit-images/callkit04.png#lightbox)

## <a name="implementing-callkit"></a>Implementando CallKit

As seções a seguir mostrará como implementar CallKit em um aplicativo xamarin. IOS VOIP. Para fins de exemplo, este documento usará o código do aplicativo de VOIP MonkeyCall fictício. O código apresentado aqui representa várias classes de suporte, o CallKit partes específicas serão abordados em detalhes nas seções a seguir.

### <a name="the-activecall-class"></a>A classe ActiveCall

O `ActiveCall` classe é usada pelo aplicativo MonkeyCall para manter todas as informações sobre uma chamada VOIP que está ativa no momento da seguinte maneira:

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

`ActiveCall` contém várias propriedades que definem o estado da chamada e dois eventos que podem ser gerados quando o estado de chamada é alterado. Como essa é apenas um exemplo, há três métodos usados para simulada iniciando, responder e encerrar uma chamada.

### <a name="the-startcallrequest-class"></a>A classe StartCallRequest

O `StartCallRequest` classe estática, fornece alguns métodos auxiliares que serão usados ao trabalhar com saída de chamadas:

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

O `CallHandleFromURL` e `CallHandleFromActivity` classes são usadas no AppDelegate para obter o identificador de contato da pessoa que está sendo chamado em uma chamada de saída. Para obter mais informações, consulte o [tratamento de chamadas de saída](#handling-outgoing-calls) seção abaixo.

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

Novamente, uma vez que isso é uma simulação só, o `ActiveCallManager` só mantém uma coleção de `ActiveCall` objetos e tem uma rotina para localizar uma determinada chamada pelo seu `UUID` propriedade. Ele também inclui métodos para iniciar, encerrar e alterar o estado em espera de uma chamada de saída. Para obter mais informações, consulte o [tratamento de chamadas de saída](#handling-outgoing-calls) seção abaixo.

### <a name="the-providerdelegate-class"></a>A classe ProviderDelegate

Como discutido acima, um `CXProvider` fornece comunicação bidirecional entre o aplicativo e o sistema para out-of-band notificações. O desenvolvedor precisa fornecer um personalizado `CXProviderDelegate` e anexá-lo para o `CXProvider` para o aplicativo tratar eventos de CallKit out-of-band. MonkeyCall usa os seguintes `CXProviderDelegate`:

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

Quando uma instância desse delegado é criada, ele é passado a `ActiveCallManager` que usará para lidar com qualquer atividade de chamada. Em seguida, ele define os tipos de identificador (`CXHandleType`) que o `CXProvider` responderá a:

```csharp
// Define handle types
var handleTypes = new [] { (NSNumber)(int)CXHandleType.PhoneNumber };
```

E ele obtém a imagem de modelo que será aplicada ao ícone do aplicativo quando uma chamada está em andamento:

```csharp
// Get Image Template
var templateImage = UIImage.FromFile ("telephone_receiver.png");
```

Esses valores obterem empacotados em um `CXProviderConfiguration` que será usado para configurar o `CXProvider`:

```csharp
// Setup the initial configurations
Configuration = new CXProviderConfiguration ("MonkeyCall") {
    MaximumCallsPerCallGroup = 1,
    SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
    IconTemplateImageData = templateImage.AsPNG(),
    RingtoneSound = "musicloop01.wav"
};
```

O delegado, em seguida, cria um novo `CXProvider` com essas configurações e se anexa a ele:

```csharp
// Create a new provider
Provider = new CXProvider (Configuration);

// Attach this delegate
Provider.SetDelegate (this, null);
```

Ao usar CallKit, o aplicativo não será criar e lidar com suas próprias sessões de áudio, em vez disso, ele será necessário configurar e usar uma sessão de áudio que o sistema irá criar e manipular para ele. 

Se esse fosse um aplicativo real, o `DidActivateAudioSession` método seria usado para iniciar a chamada com um pré-configurados `AVAudioSession` que o sistema fornecidos:

```csharp
public override void DidActivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // Start the call's audio session here...
}
```

Ele também usa o `DidDeactivateAudioSession` método para finalizar e liberar sua conexão para o sistema fornecido a sessão de áudio:

```csharp
public override void DidDeactivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // End the calls audio session and restart any non-call
    // releated audio
}
```

O restante do código será abordado em detalhes nas seções a seguir.

### <a name="the-appdelegate-class"></a>A classe AppDelegate

MonkeyCall usa AppDelegate para armazenar instâncias de `ActiveCallManager` e `CXProviderDelegate` que será usado em todo o aplicativo:

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

O `OpenUrl` e `ContinueUserActivity` substituição métodos são usados quando o aplicativo está processando uma chamada de saída. Para obter mais informações, consulte o [tratamento de chamadas de saída](#handling-outgoing-calls) seção abaixo.

## <a name="handling-incoming-calls"></a>Tratamento de chamadas de entrada

Há vários estados e processos que uma chamada VOIP pode passar por durante um típico fluxo de trabalho de chamada de entrada, como:

- Informar o usuário (e o sistema) que uma chamada de entrada existe.
- Receber notificação quando o usuário deseja responder à chamada e inicializando a chamada com o outro usuário.
- Informe o sistema e a rede de comunicação, quando o usuário deseja encerrar a chamada atual.

As seções a seguir terão uma visão detalhada de como um aplicativo pode usar CallKit para lidar com entrada chamada fluxo de trabalho, usando novamente o aplicativo de VOIP MonkeyCall como exemplo.

### <a name="informing-user-of-incoming-call"></a>Informar o usuário de chamada

Quando um usuário remoto iniciou uma conversa de VOIP com o usuário local, ocorre o seguinte:

[![](callkit-images/callkit05.png "Um usuário remoto iniciou uma conversa de VOIP")](callkit-images/callkit05.png#lightbox)

1. O aplicativo obtém uma notificação da sua rede de comunicação que há uma chamada VOIP.
2. O aplicativo usa o `CXProvider` para enviar um `CXCallUpdate` para o sistema informando-o da chamada.
3. O sistema publica a chamada para a interface do usuário do sistema, serviços do sistema e quaisquer outros aplicativos VOIP usando CallKit.

Por exemplo, no `CXProviderDelegate`:

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

Esse código cria um novo `CXCallUpdate` da instância e anexa um identificador que identificará o chamador. Em seguida, ele usa o `ReportNewIncomingCall` método da `CXProvider` classe para informar o sistema da chamada. Se for bem-sucedida, a chamada é adicionada à coleção do aplicativo de chamadas do Active Directory, caso contrário, o erro precisa ser relatada ao usuário.

### <a name="user-answering-incoming-call"></a>Chamada de entrada de resposta do usuário

Se o usuário deseja atender a chamada VOIP, ocorre o seguinte:

[![](callkit-images/callkit06.png "O usuário responde à chamada VOIP de entrada")](callkit-images/callkit06.png#lightbox)

1. A interface do usuário do sistema informa o sistema que o usuário deseja responde à chamada VOIP.
2. O sistema envia uma `CXAnswerCallAction` para o aplicativo `CXProvider` informando-o da intenção de resposta.
3. O aplicativo informa à sua rede de comunicação que o usuário está atendendo a chamada e a chamada VOIP continua como de costume.

Por exemplo, no `CXProviderDelegate`:

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

Esse código primeiro procura em determinada chamada em sua lista de chamadas ativas. Se a chamada não for encontrada, o sistema será notificado e o método sai. Se ela for encontrada, o `AnswerCall` método da `ActiveCall` classe é chamado para iniciar a chamada e o sistema de informações se ela for bem-sucedida ou falha.

### <a name="user-ending-incoming-call"></a>Terminando a chamada de entrada do usuário

Se o usuário deseja encerrar a chamada de dentro da interface do usuário do aplicativo, ocorre o seguinte:

[![](callkit-images/callkit07.png "O usuário encerra a chamada de dentro da interface do usuário do aplicativo")](callkit-images/callkit07.png#lightbox)

1. O aplicativo cria `CXEndCallAction` que obtém empacotada em um `CXTransaction` que é enviada para o sistema para informar a ele que a chamada está terminando.
2. O sistema verifica a intenção de chamar End e envia o `CXEndCallAction` volta para o aplicativo por meio de `CXProvider`.
3. O aplicativo, em seguida, informa à sua rede de comunicação que a chamada está terminando.

Por exemplo, no `CXProviderDelegate`:

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

Esse código primeiro procura em determinada chamada em sua lista de chamadas ativas. Se a chamada não for encontrada, o sistema será notificado e o método sai. Se ela for encontrada, o `EndCall` método da `ActiveCall` classe é chamado para encerrar a chamada e o sistema de informações se ela for bem-sucedida ou falha. Se for bem-sucedido, a chamada será removida da coleção de chamadas ativas.

## <a name="managing-multiple-calls"></a>Gerenciando várias chamadas

A maioria dos aplicativos VOIP pode manipular várias chamadas ao mesmo tempo. Por exemplo, se, atualmente, há uma chamada VOIP ativa e as notificações de obtém do aplicativo que não há uma nova chamada de entrada, o usuário podem fazer uma pausa ou desligamento na primeira chamada para responder a um segundo.

Dê a situação acima, o sistema enviará um `CXTransaction` até o aplicativo que inclui uma lista de várias ações (como o `CXEndCallAction` e o `CXAnswerCallAction`). Todas essas ações precisará ser atendida individualmente, para que o sistema possa atualizar a interface do usuário adequadamente.

## <a name="handling-outgoing-calls"></a>Manipulação de saída chama

Se o usuário toca uma entrada na lista recentes (no aplicativo do telefone), por exemplo, que é de uma chamada que pertencem ao aplicativo, ele será enviado um _começar a intenção de chamar_ pelo sistema:

[![](callkit-images/callkit08.png "Recebendo uma intenção da chamada de início")](callkit-images/callkit08.png#lightbox)

1. O aplicativo criará um _iniciar ação chamar_ com base na intenção de chamar Iniciar que ele recebeu do sistema. 
2. O aplicativo usará o `CXCallController` para solicitar a ação de chamar Iniciar do sistema.
3. Se o sistema aceitar a ação, ele será retornado para o aplicativo por meio de `XCProvider` delegar.
4. O aplicativo inicia a chamada de saída com a sua rede de comunicação.

Para obter mais informações sobre as intenções, consulte nosso [intenções e propósitos de extensões de interface do usuário](~/ios/platform/sirikit/understanding-sirikit.md) documentação. 

### <a name="the-outgoing-call-lifecycle"></a>O ciclo de vida de chamada de saída

Ao trabalhar com CallKit e uma chamada de saída, o aplicativo precisa informar o sistema de eventos de ciclo de vida a seguir:

1. **Iniciando** -informam ao sistema que uma chamada de saída está prestes a iniciar.
2. **Iniciado** -informam ao sistema que uma chamada de saída foi iniciada.
3. **Conectar-se** -informam ao sistema que a chamada de saída está se conectando.
4. **Conectado** -informar a saída chamada se conectou e que ambas as partes podem conversar agora.

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

Ele cria uma `CXHandle` e o utiliza para configurar uma `CXStartCallAction` que é fornecido em um `CXTransaction` que é enviado para o sistema usando o `RequestTransaction` método da `CXCallController` classe. Chamando o `RequestTransaction` método, o sistema pode colocar qualquer existente chamadas em espera, não importa a origem (aplicativo de telefone, FaceTime, VOIP, etc.), antes de inicia a chamada de novo.

A solicitação para iniciar uma chamada VOIP saída pode vir de várias fontes diferentes, como o Siri, uma entrada em um cartão de contato (no aplicativo contatos) ou na lista recentes (no aplicativo do telefone). Nessas situações, o aplicativo será enviado uma tentativa de chamar iniciar dentro de um `NSUserActivity` e AppDelegate precisará tratá-la:

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

Veja a `CallHandleFromActivity` método da classe auxiliar `StartCallRequest` está sendo usado para obter o identificador para a pessoa que está sendo chamada (consulte [a classe StartCallRequest](#the-startcallrequest-class) acima).

O `PerformStartCallAction` método da [ProviderDelegate classe](#the-providerdelegate-class) é usado para iniciar a chamada de saída real, por fim e informar o sistema de seu ciclo de vida:

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

Ele cria uma instância da `ActiveCall` classe (para armazenar informações sobre a chamada em andamento) e a preenche com a pessoa que está sendo chamada. O `StartingConnectionChanged` e `ConnectedChanged` eventos são usados para monitorar e relatar o ciclo de vida de chamada de saída. A chamada é iniciada e o sistema informado de que a ação foi atendida.

### <a name="ending-an-outgoing-call"></a>Termina uma chamada de saída

Quando o usuário termina com uma chamada de saída e desejos para encerrá-lo, o código a seguir pode ser usado:

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

Se cria um `CXEndCallAction` com o UUID da chamada para encerrar, empacota-o em um `CXTransaction` que é enviado para o sistema usando o `RequestTransaction` método da `CXCallController` classe. 

## <a name="additional-callkit-details"></a>Detalhes adicionais de CallKit

Esta seção aborda alguns detalhes adicionais que o desenvolvedor precisa levar em consideração ao trabalhar com CallKit, como:

- Configuração do provedor
- Erros de ação
- Restrições do sistema
- Áudio VOIP

### <a name="provider-configuration"></a>Configuração do provedor

A configuração do provedor permite que um aplicativo VOIP do iOS 10 personalizar a experiência do usuário (dentro a interface do usuário na chamada nativa) quando trabalhar com CallKit.

Um aplicativo pode fazer os seguintes tipos de personalizações:

- Exiba um nome localizado.
- Habilite o suporte de chamada de vídeo.
- Personalize os botões da interface do usuário na chamada ao apresentar seu próprio ícone de imagem de modelo. Interação do usuário com botões personalizados é enviada diretamente para o aplicativo a ser processado. 

### <a name="action-errors"></a>Erros de ação

aplicativos do iOS 10 VOIP usando CallKit precisam lidar com ações caindo em desgraça e manter o usuário informado sobre o estado de ação em todos os momentos. 

Veja o exemplo a seguir em consideração:

1. O aplicativo recebeu uma ação de chamar iniciar e iniciou o processo de inicializar uma nova chamada VOIP com sua rede de comunicação.
2. Devido ao suporte limitado ou nenhum recurso de comunicação de rede, essa conexão falha.
3. O aplicativo *devem* enviar as **falhar** mensagem de volta para a ação de chamar iniciar (`Action.Fail()`) para informar o sistema da falha.
4. Isso permite que o sistema informar ao usuário sobre o status da chamada. Por exemplo, para exibir a interface do usuário chamar de falha.

Além disso, um aplicativo VOIP do iOS 10 precisará responder às _erros de tempo limite_ que pode ocorrer quando uma ação esperada não pode ser processada dentro de um determinado período de tempo. Cada tipo de ação fornecido pelo CallKit tem um valor máximo de tempo limite associado a ele. Esses valores de tempo limite Certifique-se de que qualquer ação CallKit solicitado pelo usuário é tratada de maneira responsiva, mantendo assim o sistema operacional fluidos e dinâmicos também.

Há vários métodos no provedor delegado (`CXProviderDelegate`) que deve ser substituído para manipular normalmente também esse situações de tempo limite.

### <a name="system-restrictions"></a>Restrições do sistema

Com base no estado atual do dispositivo iOS executando o aplicativo do iOS 10 VOIP, determinadas restrições do sistema podem ser impostas.

Por exemplo, uma chamada VOIP de entrada pode ser restringida pelo sistema se:

1. A pessoa que chama está na lista de chamador bloqueados do usuário.
2. Dispositivo iOS do usuário está no modo-não incomodar.

Se uma chamada VOIP é restrito por qualquer uma dessas situações, use o seguinte código para manipulá-lo:

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

CallKit oferece vários benefícios para lidar com os recursos de áudio que um aplicativo VOIP do iOS 10 exigirá durante uma chamada VOIP em tempo real. Uma das maiores vantagens é que o aplicativo sessão áudio será aumentaram prioridades durante a execução no iOS 10. Isso é o mesmo nível de prioridade que internos no telefone e FaceTime aplicativos e esse nível de prioridade aprimorada impedirá outros aplicativos em execução de interromper a sessão de áudio do aplicativo VOIP.

Além disso, CallKit tem acesso a outras dicas de roteamentos de áudio que pode melhorar o desempenho e rotear o áudio VOIP inteligentemente dispositivos de saída específico durante uma chamada ao vivo com base nas preferências do usuário e estados de dispositivo. Por exemplo, com base em dispositivos conectados, como fones de ouvido bluetooth, uma conexão ao vivo de CarPlay ou configurações de acessibilidade.

Durante o ciclo de vida de um típico VOIP chamar usando CallKit, o aplicativo será necessário configurar o Stream de áudio CallKit fornecerá. Vejamos o exemplo a seguir:

[![](callkit-images/callkit09.png "A sequência de ação de chamadas de início")](callkit-images/callkit09.png#lightbox)

1. Uma ação de chamar Iniciar é recebida pelo aplicativo para responder a uma chamada de entrada.
2. Antes que essa ação é atendida pelo aplicativo, ele fornece a configuração será exigido para seu `AVAudioSession`.
3. O aplicativo informa o sistema que a ação foi atendida.
4. Antes da chamada se conecta, CallKit fornece uma alta prioridade `AVAudioSession` correspondendo à configuração do que o aplicativo solicitou. O aplicativo será notificado por meio de `DidActivateAudioSession` método de seu `CXProviderDelegate`.

## <a name="working-with-call-directory-extensions"></a>Trabalhando com extensões de diretório de chamada

Ao trabalhar com CallKit, _extensões de diretório chamar_ fornecem uma maneira para adicionar números de chamadas bloqueados e identificar números que são específicos para um determinado aplicativo VOIP aos contatos no aplicativo entre em contato com o dispositivo iOS.

### <a name="implementing-a-call-directory-extension"></a>Implementando uma extensão de diretório de chamada

Para implementar uma extensão de diretório chamar em um aplicativo xamarin. IOS, faça o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Abra a solução do aplicativo no Visual Studio para Mac.
2. Clique com botão direito no nome da solução nos **Gerenciador de soluções** e selecione **Add** > **adicionar novo projeto**.
3. Selecione **iOS** > **extensões** > **chamar extensões de diretório** e clique no **próxima** botão: 

    [![](callkit-images/calldir01.png "Criando uma nova extensão de diretório de chamada")](callkit-images/calldir01.png#lightbox)
4. Insira um **nome** para a extensão e clique no **próxima** botão: 

    [![](callkit-images/calldir02.png "Inserir um nome para a extensão")](callkit-images/calldir02.png#lightbox)
5. Ajustar a **nome do projeto** e/ou **nome da solução** se necessário e clique no **criar** botão: 

    [![](callkit-images/calldir03.png "Criando o projeto")](callkit-images/calldir03.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Abra a solução do aplicativo no Visual Studio.
2. Clique com botão direito no nome da solução nos **Gerenciador de soluções** e selecione **Add** > **adicionar novo projeto**.
3. Selecione **iOS** > **extensões** > **chamar extensões de diretório** e clique no **próxima** botão: 

    [![](callkit-images/calldir01w.png "Criando uma nova extensão de diretório de chamada")](callkit-images/calldir01.png#lightbox)
4. Insira um **nome** para a extensão e clique no **Okey** botão

-----

Isso adicionará um `CallDirectoryHandler.cs` classe ao projeto que é semelhante ao seguinte:

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

O `BeginRequest` método no manipulador de diretório de chamada precisará ser modificado para fornecer a funcionalidade necessária. No caso do exemplo acima, ele tenta definir a lista de números bloqueados e está disponíveis no banco de dados do aplicativo VOIP contatos. Se qualquer um solicita falhar por algum motivo, crie uma `NSError` Descreva a falha e passá-lo a `CancelRequest` método da `CXCallDirectoryExtensionContext` classe.

Para definir o uso de números bloqueado a `AddBlockingEntry` método da `CXCallDirectoryExtensionContext` classe. Os números fornecidos para o método _deve_ estar em ordem crescente numericamente. Para melhor desempenho e uso de memória quando houver que muitos números de telefone, considere somente carregar um subconjunto de números em um determinado momento e usar pools autorelease liberar objetos alocados durante cada lote de números que são carregados.

Para informar ao aplicativo entre em contato com os números de contato conhecido para o aplicativo VOIP, use o `AddIdentificationEntry` método da `CXCallDirectoryExtensionContext` de classe e forneça o número e um rótulo de identificação. Novamente, os números fornecidos para o método _deve_ estar em ordem crescente numericamente. Para melhor desempenho e uso de memória quando houver que muitos números de telefone, considere somente carregar um subconjunto de números em um determinado momento e usar pools autorelease liberar objetos alocados durante cada lote de números que são carregados.

## <a name="summary"></a>Resumo

Este artigo abordou a nova API CallKit que a Apple lançada no iOS 10 e como implementá-lo em aplicativos xamarin. IOS VOIP. Ele mostrou como CallKit permite que um aplicativo integrar o sistema do iOS, como ele fornece paridade de recursos com os aplicativos internos (como telefone) e como ele aumenta a visibilidade de um aplicativo ao longo de iOS em locais como o bloqueio e telas de página inicial, por meio de interações de Siri e por meio de os aplicativos de contatos.

## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
