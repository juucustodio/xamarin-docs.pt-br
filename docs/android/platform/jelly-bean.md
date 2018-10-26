---
title: Jelly Bean recursos
description: 'Este documento fornecerá uma visão geral de alto nível dos novos recursos para desenvolvedores que foram introduzidos no Android 4.1. Esses recursos incluem: aprimorada notificações, as atualizações para o compartilhamento de arquivos grandes, as atualizações para a descoberta de rede de multimídia, peer-to-peer, animações, novas permissões Beam do Android.'
ms.prod: xamarin
ms.assetid: 23F57634-2EF9-5C15-C710-B3E19A5AF7E1
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 9e83c9a8c1e2740596a981598cafbbfb65e2caf2
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119235"
---
# <a name="jelly-bean-features"></a>Jelly Bean recursos

_Este documento fornecerá uma visão geral de alto nível dos novos recursos para desenvolvedores que foram introduzidos no Android 4.1. Esses recursos incluem: aprimorada notificações, as atualizações para o compartilhamento de arquivos grandes, as atualizações para a descoberta de rede de multimídia, peer-to-peer, animações, novas permissões Beam do Android._



## <a name="overview"></a>Visão geral

Android 4.1 (API nível 16), também conhecido como "Jelly Bean", foi a versão em 9 de julho de 2012. Este artigo fornecerá uma introdução de alto nível para alguns dos novos recursos do Android 4.1 para desenvolvedores que usam o xamarin. Android. Alguns desses novos recursos introduzidos são melhorias em animações para iniciar uma atividade, novos sons de uma câmera e suporte aprimorado para navegação de pilha do aplicativo. Agora é possível recortar e colar com as intenções.

A estabilidade dos aplicativos do Android é aprimorada com a capacidade de isolar a dependência de provedores de conteúdo instáveis. Os serviços também podem ser isolados para que fiquem acessíveis somente pela atividade que foi iniciado-los.

Foi adicionado suporte para descoberta de serviço de rede usando serviços baseados em Bonjour, UPnP ou um DNS de multicast. Agora é possível que as notificações mais ricas que formatou o texto, botões de ação e imagens grandes.

Por fim foram adicionadas várias novas permissões no Android 4.1.



## <a name="requirements"></a>Requisitos

Para desenvolver aplicativos xamarin. Android usar Jelly Bean requer o xamarin. Android 4.2.6 ou posterior e Android 4.1 (API nível 16) ser instalado por meio do Gerenciador de SDK do Android, conforme mostrado na seguinte captura de tela:

[![Selecionando o Android 4.1 no Gerenciador de SDK do Android](jelly-bean-images/image1.png)](jelly-bean-images/image1.png#lightbox)



## <a name="whats-new"></a>O Que Há de Novo



### <a name="animations"></a>Animations

As atividades podem ser iniciadas usando animações de zoom ou animações personalizadas usando o `ActivityOptions` classe. Os novos métodos a seguir são fornecidos para oferecer suporte a essas animações:

-   `MakeScaleUpAnimation` – Isso criará uma animação que é dimensionado uma janela de atividade de uma posição de início e o tamanho na tela.
-   `MakeThumbnailScaleUpAnimation` – Isso criará uma animação que é dimensionado de uma imagem em miniatura de uma posição especificada na tela.
-   `MakeCustomAnimation` – Isso cria uma animação de recursos no aplicativo. Há uma animação para quando a atividade é aberto e outro para quando a atividade for interrompido.


O novo `TimeAnimator` classe fornece uma interface `TimeAnimator.ITimeListener` que pode notificar um aplicativo sempre que um quadro é alterada em uma animação. Por exemplo, considere a seguinte implementação do `TimeAnimator.ITimeListener`:

```csharp
class MyTimeListener : Java.Lang.Object,  TimeAnimator.ITimeListener
{
    public void OnTimeUpdate(TimeAnimator animation, long totalTime, long deltaTime)
    {
        Log.Debug("Activity1", "totalTime={0}, deltaTime={1}", totalTime, deltaTime);
    }
}
```

E agora para usar a classe, uma instância de `TimeAnimator` é criado, e o ouvinte é definido:

```csharp
var animator = new TimeAnimator();
animator.SetTimeListener(new MyTimeListener());
animator.Start();
```

Como o `TimeAnimator` instância está em execução, ele invocará `ITimeAnimator.ITimeListener`, que, em seguida, faça logon como tempo o animador permaneceu em execução e quanto tempo ele conforme foi desde a última vez que o método foi invocado.



### <a name="application-stack-navigation"></a>Navegação de pilha do aplicativo

Android 4.1 aprimora a navegação de pilha do aplicativo que foi introduzida no Android 3.0. Especificando o `ParentName` propriedade do `ActivityAttribute`, Android pode abrir a atividades pai apropriado quando o usuário pressiona o [botão](http://developer.android.com/design/patterns/navigation.html#up-vs-back) na barra de ação - Android instanciará a atividade especificada, o `ParentName`propriedade. Isso permite que os aplicativos preservar a hierarquia de atividades que fazem uma determinada tarefa.

Para a maioria dos aplicativos definindo o `ParentName` na atividade são informações suficientes para Android fornecer o comportamento correto para navegar a pilha de aplicativos; Android será sintetizar a pilha voltar necessário com a criação de uma série de tentativas para cada atividade pai. No entanto, como esta é uma pilha de aplicativos artificial, cada atividade sintética não terá o estado salvo que teria uma atividade natural. Para fornecer o estado salvo para uma atividade pai sintética, uma atividade pode substituir o `OnPrepareNavigationUpTaskStack` método. Esse método recebe um `TaskStackBuilder` objetos de instância que terá uma coleção de intenção de que o Android usará para criar a pilha voltar. A atividade pode modificar essas tentativas para que, como a atividade sintética é criada, ele receberá as informações de estado apropriado.

Para cenários mais complexos, há novos métodos na classe de atividade que pode ser usado para controlar o comportamento de navegação e construir a pilha voltar:

-   `OnNavigateUp` – Substituindo esse método, é possível realizar uma ação personalizada quando o <span class="ui">backup</span> é pressionado.
-   `NavigateUpTo` – Chamar esse método fará com que o aplicativo navegar de atividade atual para a atividade especificada por um determinado objetivo.
-   `ParentActivityIntent` – Isso é usado para obter uma intenção que iniciará a atividade pai da atividade atual.
-   `ShouldUpRecreateTask` – Esse método é usado para consultar se a pilha voltar sintética deve ser criada para navegar até uma atividade pai. Retorna `true` se a pilha sintética deve ser criada. 
-   `FinishAffinity` – Chamar esse método concluirá a atividade atual e todas as atividades abaixo na tarefa atual que têm a mesma tarefa de afinidade.
-   `OnCreateNavigateUpTaskStack` – Esse método é substituído quando ele é necessário ter controle completo sobre como sintética pilha é criada.




### <a name="camera"></a>Câmera

Há uma nova interface, `Camera.IAutoFocusMoveCallback`, que pode ser usado para detectar quando o foco automática tiver sido iniciada ou interrompida movendo. Um exemplo dessa nova interface pode ser visto no trecho a seguir:

```csharp
public class AutoFocusCallbackActivity : Activity, Camera.IAutoFocusCallback
{
    public void OnAutoFocus(bool success, Camera camera)
    {
        // camera is an instance of the camera service object.

        if (success)
        {
            // Auto focus was successful - do something here.
        }
        else
        {
            // Auto focus didn't happen for some reason - react to that here.
        }
    }
}
```

A nova classe `MediaActionSound` fornece um conjunto de APIs para a produção de sons para várias ações de mídia. Existem várias ações que podem ocorrer com uma câmera, eles são definidos pela enumeração `Android.Media.MediaActionSoundType`:

-   `MediaActionSoundType.FocusComplete` – Esse som que é reproduzida quando se concentrando foi concluída.
-   `MediaActionSoundType.ShutterClick` – O som será reproduzido quando uma imagem fixa a imagem é tomada.
-   `MediaActionSoundType.StartVideoRecording` – Esse som é usada indicar o início de gravação de vídeo.
-   `MediaActionSoundType.StopVideoRecording` – O som será reproduzido para indicar o final de gravação de vídeo.


Um exemplo de como usar o `MediaActionSound` classe pode ser visto no trecho a seguir:

```csharp
var mediaActionPlayer = new MediaActionSound();

// Preload the sound for a shutter click.
mediaActionPlayer.Load(MediaActionSoundType.ShutterClick);
var button = FindViewById<Button>(Resource.Id.MyButton);

// Play the sound on a button click.
button.Click += (sender, args) => mediaActionPlayer.Play(MediaActionSoundType.ShutterClick);

// This releases the preloaded resources. Don’t make any calls on
// mediaActionPlayer after this.
mediaActionPlayer.Release();
```



### <a name="connectivity"></a>conectividade



#### <a name="android-beam"></a>Beam do Android

Beam do Android é uma tecnologia de NFC com base que permite que os dois dispositivos Android para se comunicar entre si. Android 4.1 fornece melhor suporte para a transferência de arquivos grandes. Ao usar o novo método `NfcAdapter.SetBeamPushUris()` Android alternará entre os mecanismos de transporte alternativo (como Bluetooth) para alcançar uma velocidade de transferência rápida.



#### <a name="network-services-discovery"></a>Descoberta de serviços de rede

Android 4.1 contém novos da API para a descoberta de serviço multicast com base em DNS.
Isso permite que um aplicativo para detectar e conecte-se por Wi-Fi para outros dispositivos como impressoras, câmeras e dispositivos de mídia. Esse nova API está no `Android.Net.Nsd` pacote.

Para criar um serviço que pode ser consumido por outros serviços, o `NsdServiceInfo` classe é usada para criar um objeto que define as propriedades de um serviço. Esse objeto é fornecido ao `NsdManager.RegisterService()` juntamente com uma implementação de `NsdManager.ResolveListener`. Implementações de `NsdManager.ResolveListener` são usados para notificar um registro foi bem-sucedido e cancelar o registro de serviço.

Para descobrir serviços na rede e implementação de `Nsd.DiscoveryListener` passado para `NsdManager.discoverServices()`.



#### <a name="network-usage"></a>Uso de rede

Um novo método, `ConnectivityManager.IsActiveNetworkMetered` permite que um dispositivo verificar se ele está conectado a uma rede limitada. Esse método pode ser usado para ajudar a gerenciar o uso de dados com precisão informando os usuários que pode ser caros encargos para operações de dados.



#### <a name="wifi-direct-service-discovery"></a>Descoberta de serviço de Wi-Fi Direct

O `WifiP2pManager` classe foi introduzida no Android 4.0 para dar suporte à *zeroconf*. Zeroconf (zero rede de configuração) é um conjunto de técnicas que permite que os dispositivos (computadores, impressoras, telefones) para se conectar a redes, automaticamente, sem a intervenção de operadores humanos de rede ou servidores de configuração especial.

No Jelly Bean `WifiP2pManager` pode descobrir dispositivos usando um próximos *Bonjour* ou *Upnp*. Bonjour é a implementação da Apple de zeroconf. UPnP é um conjunto de protocolos de rede que também dá suporte a zeroconf. Os seguintes métodos adicionados para o `WiFiP2pManager` para dar suporte à descoberta de serviço de Wi-Fi:

-   `AddLocalService()` – Esse método é usado anunciar um aplicativo como um serviço por Wi-Fi para a descoberta por pares.
-   `AddServiceRequest(` ) – Esse método é para enviar uma solicitação de descoberta de serviço para a estrutura. Ele é usado para inicializar a descoberta de serviço de Wi-Fi.
-   `SetDnsSdResponseListeners()` – Esse método é usado para registrar retornos de chamada a ser invocado durante o recebimento de uma resposta a solicitações de descoberta do Bonjour.
-   `SetUpnpServiceResponseListener()` – Esse método é usado para registrar retornos de chamada a ser invocado durante o recebimento de uma resposta a solicitações de descoberta de Upnp.




### <a name="content-providers"></a>Provedores de conteúdo

O `ContentResolver` classe recebeu um novo método, `AcquireUnstableContentProvider`. Esse método permite que um aplicativo adquirir um provedor de conteúdo "instável". Normalmente, quando um aplicativo adquire um provedor de conteúdo, e esse provedor falha, então, será o aplicativo. Com essa chamada de método, um aplicativo não falhará se o provedor de conteúdo falhar. Em vez disso, `Android.OS.DeadObjectionException` será gerado por chamadas no provedor de conteúdo para informar a um aplicativo que o provedor de conteúdo tiver desaparecido. Um provedor de conteúdo "instável" é útil ao interagir com provedores de conteúdo de outros aplicativos – é menos provável que o código com bugs de outro aplicativo afetará a outro aplicativo.



### <a name="copy-and-paste-with-intents"></a>Copiar e colar com as intenções

O `Intent` classe agora pode ter um `ClipData` objeto associado a ele por meio de `Intent.ClipData` propriedade. Esse método permite que dados extras da área de transferência ser transmitido com a intenção. Uma instância do `ClipData` pode conter um ou mais `ClipData.Item`. `ClipData.Item`de itens dos seguintes tipos:

-   **Texto** – isso é qualquer cadeia de caracteres de texto, qualquer HTML ou qualquer cadeia de caracteres cujo formato é compatível com o estilo de Android interno abrange.
-  **Intenção** – qualquer `Intent` objeto.
-   **URI** – isso pode ser qualquer URI, como um indicador HTTP ou o URI para um provedor de conteúdo.




### <a name="isolated-services"></a>Serviços isolados

Um serviço isolado é um serviço que é executado em seu próprio processo especial e não tem permissões de seu próprio. A única comunicação com o serviço é quando iniciar o serviço e associar a ele por meio da API de serviço. É possível declarar um serviço como isolado, definindo a propriedade `IsolatedProcess="true"` no `ServiceAttribute` que adorna uma classe de serviço.


### <a name="media"></a>Mídia

O novo `Android.Media.MediaCodec` classe fornece uma API para codecs de mídia de baixo nível. Aplicativos podem consultar o sistema para descobrir quais codecs de nível inferior estão disponíveis no dispositivo.

O novo `Android.Media.Audiofx.AudioEffect` subclasses foram adicionadas para dar suporte a pré-processando capturados de áudio de áudio adicional:

-   `Android.Media.Audiofx.AcousticEchoCanceler` – Essa classe é usada para pré-processamento de áudio para remover o sinal de uma parte remota de um sinal de áudio capturada. Por exemplo, removendo o eco de um aplicativo de comunicação de voz.
-   `Android.Media.Audiofx.AutomaticGainControl` – Essa classe é usada para normalizar o sinal capturado aumentando ou reduzindo um sinal de entrada para que o sinal de saída é constante.
-   `Android.Media.Audiofx.NoiseSuppressor` – Essa classe removerá o ruído de fundo de sinal capturado.


Nem todos os dispositivos oferecerá suporte a esses efeitos. O método `AudioEffect.IsAvailable` deve ser chamado por um aplicativo para ver se o efeito de áudio em questão tem suporte no dispositivo que executa o aplicativo.

O `MediaPlayer` classe agora dá suporte a reprodução sem sobreposição com o `SetNextMediaPlayer()` método. Esse novo método Especifica o MediaPlayer Avançar para iniciar quando o player de mídia atual termina a reprodução.

As novas classes a seguir fornecem mecanismos padrão e a interface do usuário para selecionar onde mídia será reproduzida:

-   `MediaRouter` – Essa classe permite que os aplicativos controlar o roteamento de canais de mídia de um dispositivo de alto-falantes externos ou outros dispositivos.
-   `MediaRouterActionProvider` e `MediaRouteButton` – essas classes ajudam a fornecer uma interface do usuário consistente para selecionar e reprodução de mídia.




### <a name="notifications"></a>Notificações

Android 4.1 permite que os aplicativos mais flexibilidade e controle de exibição de notificações. Aplicativos agora podem mostrar notificações maiores e melhores para os usuários. Um novo método, `NotificationBuilder.SetStyle()` permite para uma das três novos novo estilo a ser definido em notificações:

-   `Notification.BigPictureStyle` -Esta é uma classe auxiliar que irá gerar notificações que terão uma imagem no-los. A imagem a seguir mostra um exemplo de uma notificação com uma imagem grande:


 [![Captura de tela de exemplo de uma notificação BigPictureStyle](jelly-bean-images/image2.png)](jelly-bean-images/image2.png#lightbox)

-   `Notification.BigTextStyle` -Esta é uma classe auxiliar que irá gerar notificações que terão várias linhas de texto, como email. Um exemplo de como esse novo estilo de notificação pode ser visto na captura de tela a seguir:


 [![Captura de tela de exemplo de uma notificação BigTextStyle](jelly-bean-images/image3.png)](jelly-bean-images/image3.png#lightbox)

-   `Notification.InboxStyle` -Esta é uma classe auxiliar que irá gerar notificações que contêm uma lista de cadeias de caracteres, como trechos de código de uma mensagem de email, conforme mostrado nesta captura de tela:


 [![Captura de tela de exemplo de uma notificação Notification.InboxStyle](jelly-bean-images/image4.png)](jelly-bean-images/image4.png#lightbox)

É possível adicionar até dois botões de ação na parte inferior de uma mensagem de notificação quando a notificação é usando o estilo normal ou maior.
Um exemplo disso pode ser visto na seguinte captura de tela, em que os botões de ação são visíveis na parte inferior da notificação:

 [![Captura de tela de exemplo de botões de ação exibidas abaixo de uma mensagem de notificação](jelly-bean-images/image5.png)](jelly-bean-images/image5.png#lightbox)

O `Notification` classe tiver recebido novas constantes que permitem que um desenvolvedor especificar um dos cinco níveis de prioridade para uma notificação. Elas podem ser definidas em uma notificação usando o `Priority` propriedade.



### <a name="permissions"></a>Permissões

As novas permissões a seguir foram adicionadas:

-   `READ_EXTERNAL_STORAGE` -O aplicativo requer acesso somente leitura para o armazenamento externo. Atualmente, todos os aplicativos têm acesso de leitura por padrão, mas as versões futuras do Android exigirá aplicativos solicitarem explicitamente o acesso de leitura.
-   `READ_USER_DICTIONARY` -Permite um acesso de leitura ao dicionário de palavras do usuário.
-   `READ_CALL_LOG` -Permite que um aplicativo obter informações sobre chamadas de entrada e saídas, lendo o log de chamada.
-   `WRITE_CALL_LOG` -Permite que um aplicativo para gravar no log de chamada no telefone.
-   `WRITE_USER_DICTIONARY` -Permite que um aplicativo gravar no dicionário de palavras do usuário.


Uma alteração importante a observar `READ_EXTERNAL_STORAGE` – atualmente, essa permissão é concedida automaticamente pelo Android. Futuras versões do Android exigirá um aplicativo para solicitar essa permissão antes de receber a permissão.



## <a name="summary"></a>Resumo

Este artigo apresentou algumas do novas APIs que estão disponíveis no Android 4.1 (API nível 16). Ele realçados algumas das alterações de animações e animação o lançamento de uma atividade e introduziu a API nova descoberta de rede de outros dispositivos usando protocolos como Bonjour ou UPnP. Outras alterações para a API foram realçadas Além disso, como a capacidade de recortar e colar dados por meio de tentativas, a capacidade de usar serviços isolados ou provedores de conteúdo "instáveis".

Esse artigo, em seguida, entrou em apresentar as atualizações para enviar notificações e discute algumas das novas permissões que foram introduzidas com o Android 4.1


## <a name="related-links"></a>Links relacionados

- [Exemplo de animação de tempo (amostra)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/TimeAnimatorExample/)
- [Android 4.1 APIs](http://developer.android.com/about/versions/android-4.1.html)
- [Tarefas e pilhas de back-](http://developer.android.com/guide/components/tasks-and-back-stack.html)
- [Navegação com Up e voltar](http://developer.android.com/design/patterns/navigation.html)
