---
title: Recursos de Bean Jelly
description: "Este documento fornece uma visão geral de alto nível dos novos recursos para desenvolvedores que foram introduzidos no Android 4.1. Esses recursos incluem: aprimorada notificações, atualizações de emissão Android para compartilhar arquivos grandes, as atualizações para a descoberta de rede de multimídia, ponto a ponto, animações, novas permissões."
ms.topic: article
ms.prod: xamarin
ms.assetid: 23F57634-2EF9-5C15-C710-B3E19A5AF7E1
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 136484644779ac40e661f50ff19cf15884c864c2
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="jelly-bean-features"></a>Recursos de Bean Jelly

_Este documento fornece uma visão geral de alto nível dos novos recursos para desenvolvedores que foram introduzidos no Android 4.1. Esses recursos incluem: aprimorada notificações, atualizações de emissão Android para compartilhar arquivos grandes, as atualizações para a descoberta de rede de multimídia, ponto a ponto, animações, novas permissões._



## <a name="overview"></a>Visão geral

Android 4.1 (API nível 16), também conhecido como "Jelly Bean", foi a versão em 9 de julho de 2012. Este artigo fornecem uma introdução de alto nível para alguns dos novos recursos no Android 4.1 para desenvolvedores que usam o xamarin. Alguns desses novos recursos introduzidos são aprimoramentos animações para iniciar uma atividade, novos sons de uma câmera e suporte aprimorado para navegação de pilha do aplicativo. Agora é possível recortar e colar com propósitos.

A estabilidade dos aplicativos do Android foi aprimorada com a capacidade de isolar a dependência de provedores de conteúdo instáveis. Os serviços também podem ser isolados para que eles são acessíveis somente pela atividade que foi iniciado-los.

Foi adicionado suporte para descoberta de serviço de rede usando serviços baseados em Bon jour, UPnP ou DNS de multicast. Agora é possível para notificações mais sofisticadas que formatou texto, botões de ação e imagens grandes.

Por fim várias novas permissões foram adicionadas no Android 4.1.



## <a name="requirements"></a>Requisitos

Para desenvolver aplicativos xamarin usar Jelly Bean requer xamarin 4.2.6 ou superior e Android 4.1 (API nível 16) ser instalado por meio do Gerenciador de SDK do Android, conforme mostrado na captura de tela a seguir:

[![Selecionando o Android 4.1 no Gerenciador de SDK do Android](jelly-bean-images/image1.png)](jelly-bean-images/image1.png#lightbox)



## <a name="whats-new"></a>O Que Há de Novo



### <a name="animations"></a>Animations

Atividades podem ser iniciadas usando animações de zoom ou animações personalizadas usando o `ActivityOptions` classe. Os novos métodos a seguir são fornecidos para oferecer suporte a essas animações:

-   `MakeScaleUpAnimation` – Isso criará uma animação que dimensiona uma janela de atividade de uma posição inicial e o tamanho na tela.
-   `MakeThumbnailScaleUpAnimation` – Isso criará uma animação que é dimensionado a partir de uma imagem em miniatura de uma posição especificada na tela.
-   `MakeCustomAnimation` – Isso cria uma animação de recursos no aplicativo. Há uma animação para quando a atividade é aberto e outro para quando a atividade for interrompido.


O novo `TimeAnimator` classe fornece uma interface `TimeAnimator.ITimeListener` que pode notificar um aplicativo sempre que um quadro é alterado em uma animação. Por exemplo, considere a seguinte implementação do `TimeAnimator.ITimeListener`:

```csharp
class MyTimeListener : Java.Lang.Object,  TimeAnimator.ITimeListener
{
    public void OnTimeUpdate(TimeAnimator animation, long totalTime, long deltaTime)
    {
        Log.Debug("Activity1", "totalTime={0}, deltaTime={1}", totalTime, deltaTime);
    }
}
```

E agora para usar a classe, uma instância de `TimeAnimator` é criado, e o ouvinte está definido:

```csharp
var animator = new TimeAnimator();
animator.SetTimeListener(new MyTimeListener());
animator.Start();
```

Como o `TimeAnimator` instância está em execução, ele chamará `ITimeAnimator.ITimeListener`, que, em seguida, faça logon como tempo de animator permaneceu em execução e quanto tempo ele como foi desde a última vez que o método foi chamado.



### <a name="application-stack-navigation"></a>Navegação de pilha do aplicativo

A navegação de pilha do aplicativo que foi introduzida no Android 3.0 melhora o Android 4.1. Especificando o `ParentName` propriedade o `ActivityAttribute`, Android pode abrir a atividade pai apropriada quando o usuário pressiona o [botão](http://developer.android.com/design/patterns/navigation.html#up-vs-back) na barra de ação - Android instanciará a atividade especificada pelo `ParentName`propriedade. Isso permite que aplicativos preservar a hierarquia de atividades que fazem uma determinada tarefa.

Para a maioria dos aplicativos definindo o `ParentName` na atividade é informações suficientes para o Android fornecer o comportamento correto para navegar a pilha do aplicativo; Android será sintetizar a pilha de volta necessária por meio da criação de uma série de tentativas para cada atividade pai. No entanto, como esta é uma pilha de aplicativo artificial, cada atividade sintética não terão o estado salvo teria uma atividade natural. Para fornecer um estado salvo para uma atividade pai sintética, uma atividade pode substituir o `OnPrepareNavigationUpTaskStack` método. Este método recebe um `TaskStackBuilder` objetos de instância que terão uma coleção de intenção que Android usará para criar a pilha de volta. A atividade pode modificar essas tentativas de forma que, como a atividade sintética é criada, ela receberá as informações de estado apropriado.

Para cenários mais complexos, há novos métodos na classe de atividade que pode ser usada para controlar o comportamento da navegação e construir a pilha de volta:

-   `OnNavigateUp` – Ao substituir esse método, é possível executar uma ação personalizada quando o <span class="ui">backup</span> botão é pressionado.
-   `NavigateUpTo` – Chamar esse método fará com que o aplicativo navegar da atividade atual para a atividade especificada por uma determinada tentativa.
-   `ParentActivityIntent` – Isso é usado para obter uma intenção que abrirá a atividade pai da atividade atual.
-   `ShouldUpRecreateTask` – Esse método é usado para consultar se a pilha voltar sintética deve ser criada para navegar até uma atividade pai. Retorna `true` se a pilha sintética deve ser criada. 
-   `FinishAffinity` – Chamar esse método terminará a atividade atual e todas as atividades abaixo na tarefa atual que têm a mesma tarefa de afinidade.
-   `OnCreateNavigateUpTaskStack` – Esse método é substituído quando é necessário ter controle completo sobre como a pilha sintética é criada.




### <a name="camera"></a>Câmera

Há uma nova interface, `Camera.IAutoFocusMoveCallback`, que pode ser usado para detectar quando o foco automático foi iniciado ou interrompido mover. Um exemplo dessa nova interface pode ser visto no trecho a seguir:

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

A nova classe `MediaActionSound` fornece um conjunto de APIs para produzir sons para várias ações de mídia. Existem várias ações que podem ocorrer com uma câmera, eles são definidos pela enumeração `Android.Media.MediaActionSoundType`:

-   `MediaActionSoundType.FocusComplete` – Esse som é reproduzido quando o foco for concluída.
-   `MediaActionSoundType.ShutterClick` – Esse som será executado quando uma imagem de imagem ainda é executada.
-   `MediaActionSoundType.StartVideoRecording` – Esse som é usado indicam o início da gravação de vídeo.
-   `MediaActionSoundType.StopVideoRecording` – Esse som será reproduzida para indicar o fim de gravação de vídeo.


Um exemplo de como usar o `MediaActionSound` classe pode ser vista no trecho a seguir:

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



### <a name="connectivity"></a>Conectividade



#### <a name="android-beam"></a>Emitir Android

Emitir Android é uma tecnologia de NFC com base que permite que os dois dispositivos Android para se comunicar entre si. Android 4.1 fornece melhor suporte para a transferência de arquivos grandes. Ao usar o novo método `NfcAdapter.SetBeamPushUris()` Android alternará entre mecanismos de transporte alternativo (como Bluetooth) para obter uma velocidade de transferência rápida.



#### <a name="network-services-discovery"></a>Descoberta de serviços de rede

Android 4.1 contém a nova API para descoberta de serviço multicast baseadas em DNS.
Isso permite que um aplicativo para detectar e conecte-se por Wi-Fi para outros dispositivos, como impressoras, câmeras e dispositivos de mídia. Esse nova API está no `Android.Net.Nsd` pacote.

Para criar um serviço que pode ser consumido por outros serviços, o `NsdServiceInfo` classe é usada para criar um objeto que define as propriedades de um serviço. Esse objeto é então fornecido ao `NsdManager.RegisterService()` juntamente com uma implementação de `NsdManager.ResolveListener`. Implementações de `NsdManager.ResolveListener` são usados para notificar um registro foi bem-sucedido e cancelar o registro do serviço.

Para descobrir os serviços na rede e a implementação de `Nsd.DiscoveryListener` passado para `NsdManager.discoverServices()`.



#### <a name="network-usage"></a>Uso de rede

Um novo método, `ConnectivityManager.IsActiveNetworkMetered` permite que um dispositivo verificar se ele está conectado a uma rede limitada. Esse método pode ser usado para ajudar a gerenciar o uso de dados com precisão informando os usuários que pode ser caros encargos para operações de dados.



#### <a name="wifi-direct-service-discovery"></a>Descoberta de serviço de Wi-Fi Direct

O `WifiP2pManager` classe foi introduzida no Android 4.0 para oferecer suporte a *zeroconf*. Zeroconf (nenhuma rede de configuração) é um conjunto de técnicas que permite que os dispositivos (computadores, impressoras, telefones) para se conectar a redes automaticamente, sem a intervenção de operadores de rede humano ou servidores de configuração especial.

No Bean Jelly, `WifiP2pManager` pode descobrir dispositivos usando próximos *Bon jour* ou *Upnp*. Bon jour é a implementação da Apple de zeroconf. UPnP é um conjunto de protocolos de rede que também oferece suporte a zeroconf. Os seguintes métodos adicionados para o `WiFiP2pManager` para dar suporte à descoberta de serviço de Wi-Fi:

-   `AddLocalService()` – Esse método é usado anunciar um aplicativo como um serviço por Wi-Fi para descoberta por pares.
-   `AddServiceRequest(` ) – Esse método é para enviar uma solicitação de descoberta de serviço para a estrutura. Ele é usado para inicializar a descoberta de serviço de Wi-Fi.
-   `SetDnsSdResponseListeners()` – Esse método é usado para registrar retornos de chamada a ser invocado durante o recebimento de uma resposta a solicitações de descoberta de Bon jour.
-   `SetUpnpServiceResponseListener()` – Esse método é usado para registrar retornos de chamada a ser invocado durante o recebimento de uma resposta a solicitações de descoberta Upnp.




### <a name="content-providers"></a>Provedores de conteúdo

O `ContentResolver` classe recebeu um novo método, `AcquireUnstableContentProvider`. Esse método permite que um aplicativo adquirir um provedor de conteúdo "instável". Normalmente, quando um aplicativo obtém um provedor de conteúdo, e esse provedor falha, será o aplicativo. Com essa chamada de método, um aplicativo não falhará se o provedor de conteúdo falhar. Em vez disso, `Android.OS.DeadObjectionException` será lançada de chamadas no provedor de conteúdo para informar um aplicativo que o provedor de conteúdo está ausente. Um provedor de conteúdo "instável" é útil ao interagir com provedores de conteúdo de outros aplicativos, é menos provável que o código com bug de outro aplicativo afetará o outro aplicativo.



### <a name="copy-and-paste-with-intents"></a>Copiar e colar com propósitos

O `Intent` classe agora pode ter um `ClipData` objeto associado a ele por meio de `Intent.ClipData` propriedade. Esse método permite que dados extras da área de transferência ser transmitido com a intenção. Uma instância de `ClipData` pode conter um ou mais `ClipData.Item`. `ClipData.Item`de itens dos seguintes tipos:

-   **Texto** – isso é qualquer cadeia de caracteres de texto, qualquer HTML ou qualquer cadeia de caracteres cujo formato é suportado pelo estilo Android interno abrange.
-  **Tentativa de** – qualquer `Intent` objeto.
-   **URI** – isso pode ser qualquer URI, como um indicador HTTP ou o URI para um provedor de conteúdo.




### <a name="isolated-services"></a>Serviços isolados

Um serviço isolado é um serviço que é executado em seu próprio processo especial e não tem permissões de seu próprio. A única comunicação com o serviço é quando iniciar o serviço e associação a ele por meio da API de serviço. É possível declarar um serviço como isolado, definindo a propriedade `IsolatedProcess="true"` no `ServiceAttribute` que adorna uma classe de serviço.


### <a name="media"></a>Mídia

O novo `Android.Media.MediaCodec` classe fornece uma API para codecs de nível baixo de mídia. Aplicativos podem consultar o sistema para saber quais codecs de nível inferior estão disponíveis no dispositivo.

O novo `Android.Media.Audiofx.AudioEffect` subclasses foram adicionadas para oferecer suporte a áudio adicional pré-processamento de áudio capturada:

-   `Android.Media.Audiofx.AcousticEchoCanceler` – Essa classe é usada para pré-processamento áudio para remover o sinal de um participante remoto de um sinal de áudio capturado. Por exemplo, removendo o eco de um aplicativo de comunicação de voz.
-   `Android.Media.Audiofx.AutomaticGainControl` – Essa classe é usado para normalizar o sinal capturado por aumentando ou diminuindo um sinal de entrada para que o sinal de saída é constante.
-   `Android.Media.Audiofx.NoiseSuppressor` – Essa classe removerá ruídos de fundo do sinal capturado.


Nem todos os dispositivos oferecerá suporte a esses efeitos. O método `AudioEffect.IsAvailable` deve ser chamado por um aplicativo para ver se o efeito de áudio em questão tem suporte no dispositivo que executa o aplicativo.

O `MediaPlayer` classe agora dá suporte a reprodução sem com o `SetNextMediaPlayer()` método. Esse novo método Especifica o Media Player Avançar para iniciar quando o player de mídia atual termina a reprodução.

As novas classes a seguir fornecem os mecanismos padrão e a interface do usuário para selecionar qual mídia será executada:

-   `MediaRouter` – Essa classe permite que os aplicativos controlar o roteamento de canais de mídia de um dispositivo para alto-falantes externos ou outros dispositivos.
-   `MediaRouterActionProvider` e `MediaRouteButton` – essas classes ajudam a fornecer uma interface do usuário consistente para selecionar e reprodução de mídia.




### <a name="notifications"></a>Notificações

Permite que aplicativos do Android 4.1 mais flexibilidade e controle de exibição de notificações. Aplicativos agora podem mostrar notificações maior e melhores para os usuários. Um novo método, `NotificationBuilder.SetStyle()` permite para um dos três novos novo estilo a ser definido em notificações:

-   `Notification.BigPictureStyle` – Esta é uma classe auxiliar que irá gerar notificações que terão uma imagem neles. A imagem a seguir mostra um exemplo de uma notificação com uma imagem grande:


 [![Captura de tela de exemplo de uma notificação BigPictureStyle](jelly-bean-images/image2.png)](jelly-bean-images/image2.png#lightbox)

-   `Notification.BigTextStyle` – Esta é uma classe auxiliar que irá gerar notificações que terão várias linhas de texto, como email. Um exemplo de como esse novo estilo de notificação pode ser visto na captura de tela a seguir:


 [![Captura de tela de exemplo de uma notificação BigTextStyle](jelly-bean-images/image3.png)](jelly-bean-images/image3.png#lightbox)

-   `Notification.InboxStyle` – Esta é uma classe auxiliar que irá gerar notificações que contêm uma lista de cadeias de caracteres, como trechos de código de uma mensagem de email, conforme mostrado nesta captura de tela:


 [![Captura de tela de exemplo de uma notificação Notification.InboxStyle](jelly-bean-images/image4.png)](jelly-bean-images/image4.png#lightbox)

É possível adicionar até dois botões de ação na parte inferior de uma mensagem de notificação quando a notificação é usando o estilo normal ou maior.
Um exemplo disso pode ser visto na seguinte captura de tela, onde os botões de ação estão visíveis na parte inferior da notificação:

 [![Captura de tela de exemplo de botões de ação exibida abaixo de uma mensagem de notificação](jelly-bean-images/image5.png)](jelly-bean-images/image5.png#lightbox)

O `Notification` classe recebeu novas constantes que permitem que um desenvolvedor especificar um dos cinco níveis de prioridade para uma notificação. Podem ser definidos em uma notificação usando o `Priority` propriedade.



### <a name="permissions"></a>Permissões

As novas permissões a seguir foram adicionadas:

-   `READ_EXTERNAL_STORAGE` -O aplicativo requer acesso somente leitura para o armazenamento externo. Atualmente a todos os aplicativos têm acesso de leitura por padrão, mas as versões futuras do Android exigirá aplicativos solicitarem explicitamente o acesso de leitura.
-   `READ_USER_DICTIONARY` -Permite um acesso de leitura ao dicionário do word do usuário.
-   `READ_CALL_LOG` -Permite que um aplicativo obter informações sobre as chamadas de entrada e saídas ao ler o log de chamada.
-   `WRITE_CALL_LOG` -Permite que um aplicativo gravar no log de chamada no telefone.
-   `WRITE_USER_DICTIONARY` -Permite que um aplicativo de gravação ao dicionário do word do usuário.


Uma alteração importante a observar `READ_EXTERNAL_STORAGE` – no momento essa permissão é concedida automaticamente pelo Android. Versões futuras do Android exigirá um aplicativo para solicitar essa permissão antes de receber a permissão.



## <a name="summary"></a>Resumo

Este artigo apresentou algumas do novas APIs que estão disponíveis no Android 4.1 (API nível 16). Ele realçado algumas das alterações de animações e a animação a inicialização de uma atividade e introduziu a nova API descoberta de rede de outros dispositivos usando protocolos como Bon jour ou UPnP. Outras alterações para a API foram realçadas, assim como a capacidade de copiar e colar dados por meio de tentativas, a capacidade de usar serviços isolados ou provedores de conteúdo "instáveis".

Este artigo entrou em apresentar as atualizações para notificações e discutido algumas das novas permissões que foram introduzidas com o Android 4.1


## <a name="related-links"></a>Links relacionados

- [Exemplo de animação de tempo (exemplo)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/TimeAnimatorExample/)
- [Android 4.1 APIs](http://developer.android.com/about/versions/android-4.1.html)
- [Tarefas e pilhas de retorno](http://developer.android.com/guide/components/tasks-and-back-stack.html)
- [Navegação com Up e voltar](http://developer.android.com/design/patterns/navigation.html)
