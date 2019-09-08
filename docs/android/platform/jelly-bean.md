---
title: Recursos do Jelly Bean
description: 'Este documento fornecerá uma visão geral de alto nível dos novos recursos para desenvolvedores que foram introduzidos no Android 4,1. Esses recursos incluem: notificações aprimoradas, atualizações no feixe do Android para compartilhar arquivos grandes, atualizações em multimídia, descoberta de rede ponto a ponto, animações, novas permissões.'
ms.prod: xamarin
ms.assetid: 23F57634-2EF9-5C15-C710-B3E19A5AF7E1
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 7fa116f716c3c30e8d41dd19cbc09477a7709e49
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761544"
---
# <a name="jelly-bean-features"></a>Recursos do Jelly Bean

_Este documento fornecerá uma visão geral de alto nível dos novos recursos para desenvolvedores que foram introduzidos no Android 4,1. Esses recursos incluem: notificações aprimoradas, atualizações no feixe do Android para compartilhar arquivos grandes, atualizações em multimídia, descoberta de rede ponto a ponto, animações, novas permissões._

## <a name="overview"></a>Visão geral

O Android 4,1 (API nível 16), também conhecido como "Jelly Bean", foi lançado em 9 de julho de 2012. Este artigo fornecerá uma introdução de alto nível a alguns dos novos recursos do Android 4,1 para desenvolvedores que usam o Xamarin. Android. Alguns desses novos recursos introduzidos são aprimoramentos em animações para iniciar uma atividade, novos sons para uma câmera e suporte aprimorado para navegação na pilha de aplicativos. Agora é possível recortar e colar com tentativas.

A estabilidade dos aplicativos Android é aprimorada com a capacidade de isolar a dependência de provedores de conteúdo instável. Os serviços também podem ser isolados para que possam ser acessados apenas pela atividade que os iniciou.

Foi adicionado suporte para descoberta de serviço de rede usando serviços baseados em DNS Bonjour, UPnP ou multicast. Agora é possível que notificações mais avançadas com texto formatado, botões de ação e imagens grandes.

Finalmente, várias novas permissões foram adicionadas no Android 4,1.

## <a name="requirements"></a>Requisitos

O desenvolvimento de aplicativos xamarin. Android usando o Jelly Bean requer a instalação do xamarin. Android 4.2.6 ou posterior e Android 4.1 (API nível 16) por meio do Gerenciador de SDK do Android, conforme mostrado na seguinte captura de tela:

[![Selecionando Android 4,1 no Gerenciador de SDK do Android](jelly-bean-images/image1.png)](jelly-bean-images/image1.png#lightbox)

## <a name="whats-new"></a>O Que Há de Novo

### <a name="animations"></a>Animations

As atividades podem ser iniciadas usando animações de zoom ou animações personalizadas usando `ActivityOptions` a classe. Os novos métodos a seguir são fornecidos para dar suporte a essas animações:

- `MakeScaleUpAnimation`– Isso criará uma animação que escala uma janela de atividade de uma posição inicial e tamanho na tela.
- `MakeThumbnailScaleUpAnimation`– Isso criará uma animação que será dimensionada de uma imagem em miniatura da posição especificada na tela.
- `MakeCustomAnimation`– Isso cria uma animação de recursos no aplicativo. Há uma animação para quando a atividade é aberta e outra para quando a atividade é interrompida.

A nova `TimeAnimator` classe fornece uma interface `TimeAnimator.ITimeListener` que pode notificar um aplicativo toda vez que um quadro é alterado em uma animação. Por exemplo, considere a seguinte implementação de `TimeAnimator.ITimeListener`:

```csharp
class MyTimeListener : Java.Lang.Object,  TimeAnimator.ITimeListener
{
    public void OnTimeUpdate(TimeAnimator animation, long totalTime, long deltaTime)
    {
        Log.Debug("Activity1", "totalTime={0}, deltaTime={1}", totalTime, deltaTime);
    }
}
```

E agora, para usar a classe, uma instância `TimeAnimator` do é criada e o ouvinte é definido:

```csharp
var animator = new TimeAnimator();
animator.SetTimeListener(new MyTimeListener());
animator.Start();
```

Como a `TimeAnimator` instância está em execução, ela invocará `ITimeAnimator.ITimeListener`, que então registrará por quanto tempo o Animator está em execução e por quanto tempo desde a última vez que o método foi invocado.

### <a name="application-stack-navigation"></a>Navegação da pilha de aplicativos

O Android 4,1 melhora a navegação na pilha de aplicativos que foi introduzida no Android 3,0. Ao especificar a `ParentName` propriedade `ActivityAttribute`do, o Android pode abrir a atividade pai adequada quando o usuário pressiona o [botão para cima](https://developer.android.com/design/patterns/navigation.html#up-vs-back) na barra de ação-o Android instanciará a atividade especificada pela `ParentName` propriedade. Isso permite que os aplicativos preservem a hierarquia de atividades que fazem uma determinada tarefa.

Para a maioria dos aplicativos `ParentName` , a configuração da atividade é suficiente para que o Android forneça o comportamento correto para navegar na pilha de aplicativos; O Android irá sintetizar a pilha voltar necessária criando uma série de tentativas para cada atividade pai. No entanto, como essa é uma pilha de aplicativos artificial, cada atividade sintética não terá o estado salvo que uma atividade natural teria. Para fornecer o estado salvo a uma atividade pai sintética, uma atividade pode substituir `OnPrepareNavigationUpTaskStack` o método. Esse método recebe uma `TaskStackBuilder` instância que terá uma coleção de objetos de intenção que o Android usará para criar a pilha voltar. A atividade pode modificar essas intenções de forma que, à medida que a atividade sintética é criada, ela receberá as informações de estado apropriadas.

Para cenários mais complexos, há novos métodos na classe Activity que podem ser usados para lidar com o comportamento de navegação e construção da pilha voltar:

- `OnNavigateUp`– Ao substituir esse método, é possível executar uma ação personalizada quando o botão **para cima** é pressionado.
- `NavigateUpTo`– Chamar esse método fará com que o aplicativo navegue da atividade atual para a atividade especificada por uma determinada tentativa.
- `ParentActivityIntent`– Isso é usado para obter uma intenção que iniciará a atividade pai da atividade atual.
- `ShouldUpRecreateTask`– Esse método é usado para consultar se a pilha de fundo sintética deve ser criada para navegar até uma atividade pai. Retorna `true` se a pilha sintética deve ser criada. 
- `FinishAffinity`– Chamar esse método concluirá a atividade atual e todas as atividades abaixo dela na tarefa atual que têm a mesma afinidade de tarefa.
- `OnCreateNavigateUpTaskStack`– Esse método é substituído quando é necessário ter controle completo sobre como a pilha sintética é criada.

### <a name="camera"></a>Câmera

Há uma nova interface, `Camera.IAutoFocusMoveCallback`, que pode ser usada para detectar quando o foco automático foi iniciado ou parado de se mover. Um exemplo dessa nova interface pode ser visto no trecho a seguir:

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

A nova classe `MediaActionSound` fornece um conjunto de APIs para produzir sons para as várias ações de mídia. Há várias ações que podem ocorrer com uma câmera, elas são definidas pela enumeração `Android.Media.MediaActionSoundType`:

- `MediaActionSoundType.FocusComplete`– Este som é reproduzido quando o foco estiver concluído.
- `MediaActionSoundType.ShutterClick`– Esse som será reproduzido quando uma imagem de imagem ainda for tirada.
- `MediaActionSoundType.StartVideoRecording`– Esse som é usado para indicar o início da gravação de vídeo.
- `MediaActionSoundType.StopVideoRecording`– Esse som será reproduzido para indicar o fim da gravação de vídeo.

Um exemplo de como usar a `MediaActionSound` classe pode ser visto no trecho de código a seguir:

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

#### <a name="android-beam"></a>Beam do Android

O feixe do Android é uma tecnologia baseada em NFC que permite que dois dispositivos Android se comuniquem entre si. O Android 4,1 fornece melhor suporte para a transferência de arquivos grandes. Ao usar o novo método `NfcAdapter.SetBeamPushUris()` , o Android alternará entre mecanismos de transporte alternativos (como Bluetooth) para obter uma velocidade de transferência rápida.

#### <a name="network-services-discovery"></a>Descoberta de serviços de rede

O Android 4,1 contém novas APIs para a descoberta de serviço de multicast baseado em DNS.
Isso permite que um aplicativo detecte e se conecte por Wi-Fi a outros dispositivos, como impressoras, câmeras e dispositivos de mídia. Essas novas APIs estão no `Android.Net.Nsd` pacote.

Para criar um serviço que pode ser consumido por outros serviços `NsdServiceInfo` , a classe é usada para criar um objeto que definirá as propriedades de um serviço. Em seguida, esse objeto é `NsdManager.RegisterService()` fornecido junto com uma implementação `NsdManager.ResolveListener`de. As implementações do `NsdManager.ResolveListener` são usadas para notificar um registro bem-sucedido e para cancelar o registro do serviço.

Para descobrir serviços na rede e a implementação do `Nsd.DiscoveryListener` passado para `NsdManager.discoverServices()`o.

#### <a name="network-usage"></a>Uso de rede

Um novo método `ConnectivityManager.IsActiveNetworkMetered` permite que um dispositivo Verifique se ele está conectado a uma rede limitada. Esse método pode ser usado para ajudar a gerenciar o uso de dados, informando com precisão os usuários de que pode haver encargos caros para operações de dados.

#### <a name="wifi-direct-service-discovery"></a>Descoberta de serviço do WiFi Direct

A `WifiP2pManager` classe foi introduzida no Android 4,0 para dar suporte a *Zeroconf*. Zeroconf (Zero Configuration Networking) é um conjunto de técnicas que permite que dispositivos (computadores, impressoras, telefones) se conectem a redes automaticamente, com a intervenção de operadores de rede humana ou de servidores de configuração especiais.

No bean do Jelly `WifiP2pManager` , o pode descobrir dispositivos próximos usando o *Bonjour* ou *UPnP*. Bonjour é a implementação de Zeroconf da Apple. UPnP é um conjunto de protocolos de rede que também dá suporte a Zeroconf. Os seguintes métodos foram adicionados ao `WiFiP2pManager` para dar suporte à descoberta do serviço Wi-Fi:

- `AddLocalService()`– Esse método é usado para anunciar um aplicativo como um serviço por Wi-Fi para descoberta por pares.
- `AddServiceRequest(`) – Esse método é enviar uma solicitação de descoberta de serviço para a estrutura. Ele é usado para inicializar a descoberta do serviço Wi-Fi.
- `SetDnsSdResponseListeners()`– Esse método é usado para registrar retornos de chamada a serem invocados no recebimento de uma resposta para solicitações de descoberta de Bonjour.
- `SetUpnpServiceResponseListener()`– Esse método é usado para registrar retornos de chamada a serem invocados no recebimento de uma resposta para solicitações de descoberta UPnP.

### <a name="content-providers"></a>Provedores de conteúdo

A `ContentResolver` classe recebeu um novo método, `AcquireUnstableContentProvider`. Esse método permite que um aplicativo adquira um provedor de conteúdo "instável". Normalmente, quando um aplicativo adquire um provedor de conteúdo, e esse provedor de conteúdo falha, portanto, o aplicativo será. Com essa chamada de método, um aplicativo não falhará se o provedor de conteúdo falhar. Em vez `Android.OS.DeadObjectionException` disso, serão lançadas de chamadas no provedor de conteúdo para informar a um aplicativo que o provedor de conteúdo desapareceu. Um provedor de conteúdo "instável" é útil ao interagir com provedores de conteúdo de outros aplicativos – é menos provável que o código de bugs de outro aplicativo afete outro aplicativo.

### <a name="copy-and-paste-with-intents"></a>Copiar e colar com tentativas

A `Intent` classe agora pode ter um `ClipData` objeto associado a ela por meio `Intent.ClipData` da propriedade. Esse método permite que dados adicionais da área de transferência sejam transmitidos com a intenção. Uma instância do `ClipData` pode conter um ou mais `ClipData.Item`. `ClipData.Item`são itens dos seguintes tipos:

- **Texto** – é qualquer cadeia de caracteres de texto, HTML ou qualquer cadeia de caracteres cujo formato é suportado pelas extensões de estilo Android internas.
- **Intenção** – qualquer `Intent` objeto.
- **URI** – pode ser qualquer URI, como um indicador http ou o URI para um provedor de conteúdo.

### <a name="isolated-services"></a>Serviços isolados

Um serviço isolado é um serviço que é executado em seu próprio processo especial e não tem permissões próprias. A única comunicação com o serviço é ao iniciar o serviço e associá-lo por meio da API de serviço. É possível declarar um serviço como isolado definindo a propriedade `IsolatedProcess="true"` `ServiceAttribute` no que adorna uma classe de serviço.

### <a name="media"></a>Mídia

A nova `Android.Media.MediaCodec` classe fornece uma API para codecs de mídia de nível baixo. Os aplicativos podem consultar o sistema para descobrir quais codecs de nível baixo estão disponíveis no dispositivo.

As novas `Android.Media.Audiofx.AudioEffect` subclasses foram adicionadas para dar suporte ao pré-processamento de áudio adicional no áudio capturado:

- `Android.Media.Audiofx.AcousticEchoCanceler`– Essa classe é usada para pré-processamento de áudio para remover o sinal de uma parte remota de um sinal de áudio capturado. Por exemplo, remover o eco de um aplicativo de comunicação de voz.
- `Android.Media.Audiofx.AutomaticGainControl`– Essa classe é usada para normalizar o sinal capturado aumentando ou diminuindo um sinal de entrada para que o sinal de saída seja constante.
- `Android.Media.Audiofx.NoiseSuppressor`– Essa classe removerá o ruído de fundo do sinal capturado.

Nem todos os dispositivos terão suporte para esses efeitos. O método `AudioEffect.IsAvailable` deve ser chamado por um aplicativo para ver se o efeito de áudio em questão tem suporte no dispositivo que executa o aplicativo.

A `MediaPlayer` classe agora dá suporte à reprodução de `SetNextMediaPlayer()` intervalo com o método. Esse novo método especifica o próximo MediaPlayer a ser iniciado quando o player de mídia atual concluir sua reprodução.

As novas classes a seguir fornecem mecanismos padrão e interface do usuário para selecionar onde a mídia será reproduzida:

- `MediaRouter`– Essa classe permite que os aplicativos controlem o roteamento de canais de mídia de um dispositivo para alto-falantes externos ou outros dispositivos.
- `MediaRouterActionProvider`e `MediaRouteButton` – essas classes ajudam a fornecer uma interface do usuário consistente para selecionar e reproduzir mídia.

### <a name="notifications"></a>Notificações

O Android 4,1 permite que os aplicativos tenham mais flexibilidade e controle com a exibição de notificações. Agora, os aplicativos podem mostrar notificações maiores e melhores aos usuários. Um novo método `NotificationBuilder.SetStyle()` permite que um dos três novos estilos sejam definidos nas notificações:

- `Notification.BigPictureStyle`– Essa é uma classe auxiliar que irá gerar notificações que terão uma imagem nelas. A imagem a seguir mostra um exemplo de uma notificação com uma imagem grande:

 [![Captura de tela de exemplo de uma notificação de BigPictureStyle](jelly-bean-images/image2.png)](jelly-bean-images/image2.png#lightbox)

- `Notification.BigTextStyle`– Essa é uma classe auxiliar que irá gerar notificações que terão várias linhas de texto, como email. Um exemplo desse novo estilo de notificação pode ser visto na seguinte captura de tela:

 [![Captura de tela de exemplo de uma notificação de BigTextStyle](jelly-bean-images/image3.png)](jelly-bean-images/image3.png#lightbox)

- `Notification.InboxStyle`– Essa é uma classe auxiliar que irá gerar notificações que contenham uma lista de cadeias de caracteres, como trechos de código de uma mensagem de email, conforme mostrado nesta captura de tela:

 [![Captura de tela de exemplo de notificação. Inboxstyle](jelly-bean-images/image4.png)](jelly-bean-images/image4.png#lightbox)

É possível adicionar até dois botões de ação na parte inferior de uma mensagem de notificação quando a notificação estiver usando o estilo normal ou maior.
Um exemplo disso pode ser visto na captura de tela a seguir, em que os botões de ação estão visíveis na parte inferior da notificação:

 [![Captura de tela de exemplo dos botões de ação exibidos abaixo de uma mensagem de notificação](jelly-bean-images/image5.png)](jelly-bean-images/image5.png#lightbox)

A `Notification` classe recebeu novas constantes que permitem que um desenvolvedor especifique um dos cinco níveis de prioridade para uma notificação. Eles podem ser definidos em uma notificação usando a `Priority` propriedade.

### <a name="permissions"></a>Permissões

As novas permissões a seguir foram adicionadas:

- `READ_EXTERNAL_STORAGE`-O aplicativo requer acesso somente leitura ao armazenamento externo. Atualmente, todos os aplicativos têm acesso de leitura por padrão, mas versões futuras do Android exigirão que aplicativos solicitem acesso de leitura explicitamente.
- `READ_USER_DICTIONARY`– Permite um acesso de leitura ao dicionário de palavras do usuário.
- `READ_CALL_LOG`– Permite que um aplicativo Obtenha informações sobre chamadas de entrada e saída lendo o log de chamadas.
- `WRITE_CALL_LOG`– Permite que um aplicativo grave no log de chamadas no telefone.
- `WRITE_USER_DICTIONARY`– Permite que um aplicativo grave no dicionário de palavras do usuário.

Uma alteração importante a ser `READ_EXTERNAL_STORAGE` observada: atualmente, essa permissão é concedida automaticamente pelo Android. As versões futuras do Android exigirão que um aplicativo solicite essa permissão antes de conceder a permissão.

## <a name="summary"></a>Resumo

Este artigo introduziu algumas das novas APIs que estão disponíveis no Android 4,1 (API nível 16). Ele realçou algumas alterações em animações e animando o lançamento de uma atividade e introduziu as novas APIs para a descoberta de rede de outros dispositivos usando protocolos como Bonjour ou UPnP. Outras alterações na API também foram destacadas, como a capacidade de recortar e colar dados por meio de intenções, a capacidade de usar serviços isolados ou provedores de conteúdo "instáveis".

Este artigo foi lançado para apresentar as atualizações de notificações e discutiu algumas das novas permissões que foram introduzidas com o Android 4,1

## <a name="related-links"></a>Links relacionados

- [Exemplo de animação de tempo (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-timeanimatorexample)
- [APIs do Android 4,1](https://developer.android.com/about/versions/android-4.1.html)
- [Tarefas e pilhas posteriores](https://developer.android.com/guide/components/tasks-and-back-stack.html)
- [Navegação com back e up](https://developer.android.com/design/patterns/navigation.html)
