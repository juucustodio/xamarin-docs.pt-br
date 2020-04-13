---
title: Características do feijão-geléia
description: 'Este documento fornecerá uma visão geral de alto nível dos novos recursos para desenvolvedores que foram introduzidos no Android 4.1. Esses recursos incluem: notificações aprimoradas, atualizações para o Android Beam para compartilhar arquivos grandes, atualizações para multimídia, detecção de rede ponto a ponto, animações, novas permissões.'
ms.prod: xamarin
ms.assetid: 23F57634-2EF9-5C15-C710-B3E19A5AF7E1
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: a638ccf7810c737faaeded7fcc98fcf657c85288
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027204"
---
# <a name="jelly-bean-features"></a>Características do feijão-geléia

_Este documento fornecerá uma visão geral de alto nível dos novos recursos para desenvolvedores que foram introduzidos no Android 4.1. Esses recursos incluem: notificações aprimoradas, atualizações para o Android Beam para compartilhar arquivos grandes, atualizações para multimídia, detecção de rede ponto a ponto, animações, novas permissões._

## <a name="overview"></a>Visão geral

Android 4.1 (API Nível 16), também conhecido como "Jelly Bean", foi lançado em 9 de julho de 2012. Este artigo fornecerá uma introdução de alto nível para alguns dos novos recursos no Android 4.1 para desenvolvedores que usam Xamarin.Android. Alguns desses novos recursos introduzidos são melhorias nas animações para o lançamento de uma atividade, novos sons para uma câmera e suporte aprimorado para navegação em pilha de aplicativos. Agora é possível cortar e colar com intenções.

A estabilidade dos aplicativos Android é melhorada com a capacidade de isolar a dependência de provedores de conteúdo instáveis. Os serviços também podem ser isolados para que sejam acessíveis apenas pela atividade que os iniciou.

O suporte foi adicionado para a descoberta de serviços de rede usando serviços baseados em DNS bonjour, UPnP ou multicast. Agora é possível notificações mais ricas que formataram texto, botões de ação e imagens grandes.

Finalmente várias novas permissões foram adicionadas no Android 4.1.

## <a name="requirements"></a>Requisitos

Para desenvolver aplicativos Xamarin.Android usando Jelly Bean requer Xamarin.Android 4.2.6 ou superior e Android 4.1 (API Nível 16) ser instalado através do Android SDK Manager como mostrado na seguinte captura de tela:

[![Selecionando o Android 4.1 no Android SDK Manager](jelly-bean-images/image1.png)](jelly-bean-images/image1.png#lightbox)

## <a name="whats-new"></a>What's New

### <a name="animations"></a>Animations

As atividades podem ser iniciadas usando animações `ActivityOptions` de zoom ou animações personalizadas usando a classe. Os novos métodos a seguir são fornecidos para suportar essas animações:

- `MakeScaleUpAnimation`– Isso criará uma animação que dimensiona uma janela de atividade a partir de uma posição inicial e tamanho na tela.
- `MakeThumbnailScaleUpAnimation`– Isso criará uma animação que se dimensiona a partir de uma imagem em miniatura a partir da posição especificada na tela.
- `MakeCustomAnimation`– Isso cria uma animação a partir de recursos no aplicativo. Há uma animação para quando a atividade é aberta e outra para quando a atividade parar.

A `TimeAnimator` nova classe `TimeAnimator.ITimeListener` fornece uma interface que pode notificar um aplicativo toda vez que um quadro muda em uma animação. Por exemplo, considere a `TimeAnimator.ITimeListener`seguinte implementação de:

```csharp
class MyTimeListener : Java.Lang.Object,  TimeAnimator.ITimeListener
{
    public void OnTimeUpdate(TimeAnimator animation, long totalTime, long deltaTime)
    {
        Log.Debug("Activity1", "totalTime={0}, deltaTime={1}", totalTime, deltaTime);
    }
}
```

E agora, para usar a `TimeAnimator` classe, uma instância de é criada, e o ouvinte é definido:

```csharp
var animator = new TimeAnimator();
animator.SetTimeListener(new MyTimeListener());
animator.Start();
```

Como `TimeAnimator` a instância está sendo `ITimeAnimator.ITimeListener`realizada, ele invocará , o que registrará o tempo que o animador está em execução e quanto tempo ele está desde a última vez que o método foi invocado.

### <a name="application-stack-navigation"></a>Navegação de pilha de aplicativos

O Android 4.1 melhora a navegação da pilha de aplicativos que foi introduzida no Android 3.0. Ao especificar `ParentName` a `ActivityAttribute`propriedade do , O Android pode abrir a atividade pai adequada quando o usuário pressiona `ParentName` o botão [Up](https://developer.android.com/design/patterns/navigation.html#up-vs-back) na barra de ação - o Android irá instanciar a atividade especificada pela propriedade. Isso permite que os aplicativos preservem a hierarquia de atividades que fazem uma determinada tarefa.

Para a maioria `ParentName` dos aplicativos que configuram a atividade é informação suficiente para o Android fornecer o comportamento correto para navegar na pilha de aplicativos; O Android sintetizará a pilha de costas necessária criando uma série de Intenções para cada atividade dos pais. No entanto, por se trata de uma pilha de aplicativos artificiais, cada atividade sintética não terá o estado salvo que uma atividade natural teria. Para fornecer estado salvo a uma atividade pai `OnPrepareNavigationUpTaskStack` sintética, uma atividade pode substituir o método. Este método `TaskStackBuilder` recebe uma instância que terá uma coleção de objetos de Intenção que o Android usará para criar a pilha traseira. A atividade pode modificar essas Intenções para que, à medida que a atividade sintética for criada, receba as informações do estado adequado.

Para cenários mais complexos, existem novos métodos na classe Atividade que podem ser usados para lidar com o comportamento da navegação Up e construir a pilha traseira:

- `OnNavigateUp`– Ao sobrepor este método é possível executar uma ação personalizada quando o botão **Up** é pressionado.
- `NavigateUpTo`– Chamar este método fará com que o aplicativo navegue da atividade atual para a atividade especificada por uma determinada intenção.
- `ParentActivityIntent`– Isso é usado para obter uma Intenção que iniciará a atividade-mãe da atividade atual.
- `ShouldUpRecreateTask`– Este método é usado para consultar se a pilha traseira sintética deve ser criada para navegar até uma atividade pai. Retorna `true` se a pilha sintética deve ser criada. 
- `FinishAffinity`– Chamar esse método terminará a atividade atual e todas as atividades abaixo dela na tarefa atual que possuem a mesma afinidade de tarefas.
- `OnCreateNavigateUpTaskStack`– Este método é substituído quando é necessário ter controle completo sobre como a pilha sintética é criada.

### <a name="camera"></a>Câmera

Há uma nova `Camera.IAutoFocusMoveCallback`interface, que pode ser usada para detectar quando o foco automático começou ou parou de se mover. Um exemplo desta nova interface pode ser visto no seguinte trecho:

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

A nova `MediaActionSound` classe fornece um conjunto de API's para produzir sons para as várias ações de mídia. Existem várias ações que podem ocorrer com uma `Android.Media.MediaActionSoundType`câmera, estas são definidas pelo enum :

- `MediaActionSoundType.FocusComplete`– Este som que é reproduzido quando o foco foi concluído.
- `MediaActionSoundType.ShutterClick`– Este som será reproduzido quando uma imagem parada for tirada.
- `MediaActionSoundType.StartVideoRecording`– Este som é usado indica o início da gravação de vídeo.
- `MediaActionSoundType.StopVideoRecording`– Este som será reproduzido para indicar o fim da gravação de vídeo.

Um exemplo de como `MediaActionSound` usar a classe pode ser visto no seguinte trecho:

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

Android Beam é uma tecnologia baseada em NFC que permite que dois dispositivos Android se comuniquem entre si. O Android 4.1 oferece melhor suporte para a transferência de arquivos grandes. Ao usar o `NfcAdapter.SetBeamPushUris()` novo método, o Android alternará entre mecanismos de transporte alternativos (como bluetooth) para alcançar uma velocidade de transferência rápida.

#### <a name="network-services-discovery"></a>Descoberta de serviços de rede

O Android 4.1 contém novas API's para descoberta de serviços baseados em DNS multicast.
Isso permite que um aplicativo detecte e conecte via Wi-Fi a outros dispositivos, como impressoras, câmeras e dispositivos de mídia. Essas novas API's `Android.Net.Nsd` estão no pacote.

Para criar um serviço que possa ser `NsdServiceInfo` consumido por outros serviços, a classe é usada para criar um objeto que definirá as propriedades de um serviço. Este objeto é então `NsdManager.RegisterService()` fornecido para, `NsdManager.ResolveListener`juntamente com uma implementação de . Implementações `NsdManager.ResolveListener` de são usadas para notificar um registro bem-sucedido e para cancelar o registro do serviço.

Para descobrir serviços na rede, e implementação de `Nsd.DiscoveryListener` repassados para `NsdManager.discoverServices()`.

#### <a name="network-usage"></a>Uso de Rede

Um novo `ConnectivityManager.IsActiveNetworkMetered` método permite que um dispositivo verifique se está conectado a uma rede mísera. Esse método pode ser usado para ajudar a gerenciar o uso de dados, informando com precisão aos usuários que pode haver cobranças caras para operações de dados.

#### <a name="wifi-direct-service-discovery"></a>Descoberta do serviço direto WiFi

A `WifiP2pManager` classe foi introduzida no Android 4.0 para suportar *zeroconf*. Zeroconf (rede de configuração zero) é um conjunto de técnicas que permite que dispositivos (computadores, impressoras, telefones) se conectem automaticamente às redes, com a intervenção de operadores de rede humana ou servidores de configuração especiais.

Em Jelly Bean, `WifiP2pManager` pode descobrir dispositivos próximos usando *Bonjour* ou *Upnp*. Bonjour é a implementação da Apple do zeroconf. Upnp é um conjunto de protocolos de rede que também suporta zeroconf. Os seguintes métodos `WiFiP2pManager` adicionados ao suporte à descoberta de serviços Wi-Fi para suportar:

- `AddLocalService()`– Este método é usado para anunciar um aplicativo como um serviço via Wi-Fi para descoberta por pares.
- `AddServiceRequest(`) – Este método é enviar uma solicitação de descoberta de serviço para a estrutura. É usado para inicializar a descoberta do serviço Wi-Fi.
- `SetDnsSdResponseListeners()`– Este método é usado para registrar retornos de chamadas a serem invocados ao receber uma resposta às solicitações de descoberta da Bonjour.
- `SetUpnpServiceResponseListener()`– Este método é usado para registrar retornos de chamada a serem invocados ao receber uma resposta às solicitações de detecção Upnp.

### <a name="content-providers"></a>Provedores de conteúdo

A `ContentResolver` classe recebeu um `AcquireUnstableContentProvider`novo método, . Este método permite que um aplicativo adquira um provedor de conteúdo "instável". Normalmente, quando um aplicativo adquire um provedor de conteúdo, e esse provedor de conteúdo falha, o aplicativo também. Com essa chamada de método, um aplicativo não falhará se o provedor de conteúdo falhar. Em `Android.OS.DeadObjectionException` vez disso, será lançada a partir de chamadas para o provedor de conteúdo para informar um aplicativo que o provedor de conteúdo foi embora. Um provedor de conteúdo "instável" é útil ao interagir com provedores de conteúdo de outros aplicativos – é menos provável que o código de buggy de outro aplicativo afete outro aplicativo.

### <a name="copy-and-paste-with-intents"></a>Copiar e colar com intenções

A `Intent` classe agora `ClipData` pode ter um `Intent.ClipData` objeto associado a ele através da propriedade. Este método permite que dados extras da área de transferência sejam transmitidos com a intenção. Uma instância `ClipData` de pode `ClipData.Item`conter um ou mais . `ClipData.Item`são itens dos seguintes tipos:

- **Texto** – Esta é qualquer seqüência de texto, seja HTML ou qualquer string cujo formato seja suportado pelos intervalos de estilo android embutidos.
- **Intenção** `Intent` – Qualquer objeto.
- **Uri** – Isso pode ser qualquer URI, como um marcador HTTP ou o URI para um provedor de conteúdo.

### <a name="isolated-services"></a>Serviços Isolados

Um serviço isolado é um serviço que funciona sob seu próprio processo especial e não tem permissões próprias. A única comunicação com o serviço é ao iniciar o serviço e vinculá-lo através da API de serviço. É possível declarar um serviço como isolado, definindo a propriedade `IsolatedProcess="true"` no `ServiceAttribute` que adorna uma classe de serviço.

### <a name="media"></a>Mídia

A `Android.Media.MediaCodec` nova classe fornece uma API para codecs de mídia de baixo nível. Os aplicativos podem consultar o sistema para descobrir quais codecs de baixo nível estão disponíveis no dispositivo.

As `Android.Media.Audiofx.AudioEffect` novas subclasses foram adicionadas para suportar o pré-processamento de áudio adicional no áudio capturado:

- `Android.Media.Audiofx.AcousticEchoCanceler`– Esta classe é usada para pré-processamento de áudio para remover o sinal de uma parte remota de um sinal de áudio capturado. Por exemplo, remover o eco de um aplicativo de comunicação de voz.
- `Android.Media.Audiofx.AutomaticGainControl`– Esta classe é usada para normalizar o sinal capturado, aumentando ou baixando um sinal de entrada para que o sinal de saída seja constante.
- `Android.Media.Audiofx.NoiseSuppressor`– Esta classe removerá o ruído de fundo do sinal capturado.

Nem todos os dispositivos suportarão esses efeitos. O `AudioEffect.IsAvailable` método deve ser chamado por um aplicativo para ver se o efeito de áudio em questão é suportado no dispositivo que executa o aplicativo.

A `MediaPlayer` classe agora suporta reprodução `SetNextMediaPlayer()` sem falhas com o método. Este novo método especifica o próximo MediaPlayer para iniciar quando o player de mídia atual terminar sua reprodução.

As novas classes a seguir fornecem mecanismos padrão e ui para selecionar onde a mídia será reproduzida:

- `MediaRouter`– Esta classe permite que os aplicativos controlem o roteamento de canais de mídia de um dispositivo para alto-falantes externos ou outros dispositivos.
- `MediaRouterActionProvider`e `MediaRouteButton` – Essas aulas ajudam a fornecer uma ui consistente para selecionar e reproduzir mídia.

### <a name="notifications"></a>Notificações

O Android 4.1 permite aos aplicativos mais flexibilidade e controle com a exibição de notificações. Os aplicativos agora podem mostrar notificações maiores e melhores aos usuários. Um novo `NotificationBuilder.SetStyle()` método permite que um dos três novos estilos seja definido em notificações:

- `Notification.BigPictureStyle`– Esta é uma classe auxiliar que vai gerar notificações que terão uma imagem neles. A imagem a seguir mostra um exemplo de uma notificação com uma imagem grande:

 [![Exemplo de captura de tela de uma notificação BigPictureStyle](jelly-bean-images/image2.png)](jelly-bean-images/image2.png#lightbox)

- `Notification.BigTextStyle`– Esta é uma classe auxiliar que gerará notificações que terão várias linhas de texto, como e-mail. Um exemplo desse novo estilo de notificação pode ser visto na captura de tela a seguir:

 [![Exemplo de captura de tela de uma notificação BigTextStyle](jelly-bean-images/image3.png)](jelly-bean-images/image3.png#lightbox)

- `Notification.InboxStyle`– Esta é uma classe auxiliar que gerará notificações que contêm uma lista de strings, como trechos de uma mensagem de e-mail, como mostrado nesta captura de tela:

 [![Exemplo de captura de tela de uma notificação.InboxStyle](jelly-bean-images/image4.png)](jelly-bean-images/image4.png#lightbox)

É possível adicionar dois botões de ação na parte inferior de uma mensagem de notificação quando a notificação estiver usando o estilo normal ou maior.
Um exemplo disso pode ser visto na captura de tela a seguir, onde os botões de ação são visíveis na parte inferior da notificação:

 [![Exemplo de captura de tela de botões de ação exibidos abaixo de uma mensagem de notificação](jelly-bean-images/image5.png)](jelly-bean-images/image5.png#lightbox)

A `Notification` classe recebeu novas constantes que permitem que um desenvolvedor especifique um dos cinco níveis prioritários para uma notificação. Estes podem ser definidos `Priority` em uma notificação usando a propriedade.

### <a name="permissions"></a>Permissões

As seguintes novas permissões foram adicionadas:

- `READ_EXTERNAL_STORAGE`- O aplicativo requer acesso somente à leitura ao armazenamento externo. Atualmente, todos os aplicativos têm acesso à leitura por padrão, mas versões futuras do Android exigirão que os aplicativos solicitem explicitamente acesso à leitura.
- `READ_USER_DICTIONARY`- Permite um acesso de leitura ao dicionário de palavras do usuário.
- `READ_CALL_LOG`- Permite que um aplicativo obtenha informações sobre chamadas recebidas e de saída lendo o registro de chamadas.
- `WRITE_CALL_LOG`- Permite que um aplicativo escreva para o registro de chamadas no telefone.
- `WRITE_USER_DICTIONARY`- Permite que um aplicativo escreva para o dicionário de palavras do usuário.

Uma mudança importante `READ_EXTERNAL_STORAGE` a ser nota – atualmente essa permissão é concedida automaticamente pelo Android. Versões futuras do Android exigirão um aplicativo para solicitar essa permissão antes de conceder a permissão.

## <a name="summary"></a>Resumo

Este artigo introduziu algumas das novas API's que estão disponíveis no Android 4.1 (API Nível 16). Ele destacou algumas das mudanças para animações e animando o lançamento de uma atividade, e introduziu as novas API's para a descoberta de rede de outros dispositivos usando protocolos como Bonjour ou UPnP. Outras mudanças na API também foram destacadas, como a capacidade de cortar e colar dados por meio de intenções, a capacidade de usar serviços isolados ou provedores de conteúdo "instáveis".

Este artigo então passou a introduzir as atualizações às notificações, e discutiu algumas das novas permissões que foram introduzidas com o Android 4.1

## <a name="related-links"></a>Links relacionados

- [Exemplo de animação de tempo (amostra)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-timeanimatorexample)
- [Android 4.1 APIs](https://developer.android.com/about/versions/android-4.1.html)
- [Tarefas e Pilhas de Costas](https://developer.android.com/guide/components/tasks-and-back-stack.html)
- [Navegação com back and up](https://developer.android.com/design/patterns/navigation.html)
