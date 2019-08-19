---
title: Áudio do Android
description: O sistema operacional Android fornece amplo suporte para multimídia, abrangendo áudio e vídeo. Este guia enfoca o áudio no Android e aborda a reprodução e a gravação de áudio usando as classes internas de player de áudio e gravador, bem como a API de áudio de nível baixo. Ele também aborda o trabalho com a difusão de eventos de áudio por outros aplicativos, para que os desenvolvedores possam criar aplicativos bem comportados.
ms.prod: xamarin
ms.assetid: 646ED563-C34E-256D-4B56-29EE99881C27
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: 256871fd225808af1fdebf14c4eb2b43e575e105
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644349"
---
# <a name="android-audio"></a>Áudio do Android

_O sistema operacional Android fornece amplo suporte para multimídia, abrangendo áudio e vídeo. Este guia enfoca o áudio no Android e aborda a reprodução e a gravação de áudio usando as classes internas de player de áudio e gravador, bem como a API de áudio de nível baixo. Ele também aborda o trabalho com a difusão de eventos de áudio por outros aplicativos, para que os desenvolvedores possam criar aplicativos bem comportados._


## <a name="overview"></a>Visão geral

Os dispositivos móveis modernos adotaram uma funcionalidade que, anteriormente, teria exigido equipamentos &ndash; de câmeras de equipamento dedicados, players de música e gravadores de vídeo. Por isso, as estruturas de multimídia se tornaram um recurso de primeira classe em APIs móveis.

O Android fornece amplo suporte para multimídia. Este artigo examina o trabalho com áudio no Android e aborda os tópicos a seguir

1.  **Reproduzindo áudio com o MediaPlayer** Usar a `MediaPlayer` classe interna para reproduzir áudio, incluindo arquivos de áudio locais e arquivos de áudio em fluxo com a `AudioTrack` classe. &ndash;

2.  **Gravando áudio** Usando a `MediaRecorder` classe interna para gravar áudio. &ndash;

3.  **Trabalhando com notificações de áudio** &ndash; Usar notificações de áudio para criar aplicativos bem comportados que respondem corretamente a eventos (como chamadas telefônicas de entrada), suspendendo ou cancelando suas saídas de áudio.

4.  **Trabalhando com áudio de baixo nível** Reprodução de áudio usando `AudioTrack` a classe gravando diretamente nos buffers de memória. &ndash; Gravar áudio usando a `AudioRecord` classe e ler diretamente dos buffers de memória.


## <a name="requirements"></a>Requisitos

Este guia requer o Android 2,0 (API nível 5) ou superior. Observe que a depuração de áudio no Android deve ser feita em um dispositivo.

É necessário solicitar as `RECORD_AUDIO` permissões no **AndroidManifest. xml**:

![Seção de permissões necessárias do manifesto do Android\_com áudio de gravação habilitado](android-audio-images/image01.png)



## <a name="playing-audio-with-the-mediaplayer-class"></a>Reproduzindo áudio com a classe MediaPlayer

A maneira mais simples de reproduzir áudio no Android é com a classe [MediaPlayer](xref:Android.Media.MediaPlayer) interna.
`MediaPlayer`pode reproduzir arquivos locais ou remotos passando o caminho do arquivo. No entanto, `MediaPlayer` o é muito sensível a estado e a chamada de um de seus métodos no estado incorreto fará com que uma exceção seja gerada. É importante interagir com `MediaPlayer` a ordem descrita abaixo para evitar erros.



### <a name="initializing-and-playing"></a>Inicializando e jogando

Reproduzir áudio com `MediaPlayer` requer a seguinte sequência:

1. Crie uma instância de um novo objeto [MediaPlayer](xref:Android.Media.MediaPlayer) .

1. Configure o arquivo a ser executado por meio do método [setdataname](xref:Android.Media.MediaPlayer.SetDataSource*).

1. Chame o método [Prepare](xref:Android.Media.MediaPlayer.Prepare) para inicializar o Player.

1. Chame o método [Start](xref:Android.Media.MediaPlayer.Start) para iniciar a reprodução de áudio.


O exemplo de código abaixo ilustra esse uso:

```csharp
protected MediaPlayer player;
public void StartPlayer(String  filePath)
{
  if (player == null) {
    player = new MediaPlayer();
  } else {
    player.Reset();
    player.SetDataSource(filePath);
    player.Prepare();
    player.Start();
  }
}
```


### <a name="suspending-and-resuming-playback"></a>Suspendendo e retomando a reprodução

A reprodução pode ser suspensa chamando o método [Pause](xref:Android.Media.MediaPlayer.Pause) :

```csharp
player.Pause();
```

Para retomar a reprodução em pausa, chame o método [Start](xref:Android.Media.MediaPlayer.Start) .
Isso será retomado do local em pausa na reprodução:

```csharp
player.Start();
```

Chamar o método [Stop](xref:Android.Media.MediaPlayer.Stop) no Player termina uma reprodução em andamento:

```csharp
player.Stop();
```

Quando o Player não for mais necessário, os recursos deverão ser liberados chamando o método de [liberação](xref:Android.Media.MediaPlayer.Release) :

```csharp
player.Release();
```



## <a name="using-the-mediarecorder-class-to-record-audio"></a>Usando a classe MediaRecorder para gravar áudio

O registro para `MediaPlayer` gravar áudio no Android é a classe [MediaRecorder](xref:Android.Media.MediaRecorder) . Assim como `MediaPlayer`o, ele é sensível a estado e faz a transição por meio de vários Estados para chegar ao ponto em que ele pode iniciar a gravação. Para gravar áudio, a `RECORD_AUDIO` permissão deve ser definida. Para obter instruções sobre como definir permissões de aplicativo, consulte [trabalhando com AndroidManifest. xml](~/android/platform/android-manifest.md).


### <a name="initializing-and-recording"></a>Inicializando e gravando

A gravação de áudio `MediaRecorder` com o requer as seguintes etapas:

1. Crie uma instância de um novo objeto [MediaRecorder](xref:Android.Media.MediaRecorder) .

2. Especifique o dispositivo de hardware a ser usado para capturar a entrada de áudio por meio do método [SetAudio](xref:Android.Media.MediaRecorder.SetAudioSource*).

3. Defina o formato de áudio do arquivo de saída usando o método [SetOutputFormat](xref:Android.Media.MediaRecorder.SetOutputFormat*) . Para obter uma lista de tipos de áudio com suporte, consulte [formatos de mídia com suporte para Android](https://developer.android.com/guide/appendix/media-formats.html).

4. Chame o método [SetAudioEncoder](xref:Android.Media.MediaRecorder.SetAudioEncoder*) para definir o tipo de codificação de áudio.

5. Chame o método [setoutputtypefile](xref:Android.Media.MediaRecorder.SetOutputFile*) para especificar o nome do arquivo de saída no qual os dados de áudio são gravados.

6. Chame o método [Prepare](xref:Android.Media.MediaRecorder.Prepare) para inicializar o gravador.

7. Chame o método [Start](xref:Android.Media.MediaRecorder.Start) para iniciar a gravação.


O exemplo de código a seguir ilustra essa sequência:

```csharp
protected MediaRecorder recorder;
void RecordAudio (String filePath)
{
  try {
    if (File.Exists (filePath)) {
      File.Delete (filePath);
    }
    if (recorder == null) {
      recorder = new MediaRecorder (); // Initial state.
    } else {
      recorder.Reset ();
      recorder.SetAudioSource (AudioSource.Mic);
      recorder.SetOutputFormat (OutputFormat.ThreeGpp);
      recorder.SetAudioEncoder (AudioEncoder.AmrNb);
      // Initialized state.
      recorder.SetOutputFile (filePath);
      // DataSourceConfigured state.
      recorder.Prepare (); // Prepared state
      recorder.Start (); // Recording state.
    }
  } catch (Exception ex) {
    Console.Out.WriteLine( ex.StackTrace);
  }
}
```


### <a name="stopping-recording"></a>Parando a gravação

Para interromper a gravação, chame o `Stop` método `MediaRecorder`no:

```csharp
recorder.Stop();
```



### <a name="cleaning-up"></a>Limpando

Depois que `MediaRecorder` o tiver sido interrompido, chame o método [Reset](xref:Android.Media.MediaRecorder.Reset) para colocá-lo de volta em seu estado ocioso:

```csharp
recorder.Reset();
```

Quando o `MediaRecorder` não for mais necessário, seus recursos deverão ser liberados chamando o método de [liberação](xref:Android.Media.MediaRecorder.Release) :

```csharp
recorder.Release();
```


## <a name="managing-audio-notifications"></a>Gerenciando notificações de áudio



### <a name="the-audiomanager-class"></a>A classe Audiomanager

A classe [audiomanager](xref:Android.Media.AudioManager) fornece acesso a notificações de áudio que permitem que os aplicativos saibam quando ocorrem eventos de áudio. Esse serviço também fornece acesso a outros recursos de áudio, como o controle de volume e modo de toque. O `AudioManager` permite que um aplicativo manipule notificações de áudio para controlar a reprodução de áudio.



### <a name="managing-audio-focus"></a>Gerenciando o foco de áudio

Os recursos de áudio do dispositivo (o Player interno e o gravador) são compartilhados por todos os aplicativos em execução.

Conceitualmente, isso é semelhante a aplicativos em um computador desktop, em que apenas um aplicativo tem o foco do teclado: depois de selecionar um dos aplicativos em execução clicando com o botão do mouse nele, a entrada do teclado vai apenas para esse aplicativo.

O foco de áudio é uma ideia semelhante e impede que mais de um aplicativo reproduza ou grave áudio ao mesmo tempo. É mais complicado do que o foco do teclado porque é &ndash; voluntário que o aplicativo pode ignorar esse fato de que ele não tem foco no momento e &ndash; é executado independentemente e porque há diferentes tipos de foco de áudio que podem ser solicitado. Por exemplo, se for esperado que o solicitante reproduza áudio por um período muito curto, ele poderá solicitar um foco transitório.

O foco de áudio pode ser concedido imediatamente, ou inicialmente negado e concedido posteriormente. Por exemplo, se um aplicativo solicitar o foco de áudio durante uma chamada telefônica, ele será negado, mas o foco poderá ser bem concedido quando a chamada telefônica for concluída. Nesse caso, um ouvinte é registrado para responder de forma adequada se o foco de áudio for retirado. A solicitação do foco de áudio é usada para determinar se ele está ou não correto para reproduzir ou gravar áudio.

Para obter mais informações sobre o foco de áudio, consulte Gerenciando o [foco de áudio](https://developer.android.com/training/managing-audio/audio-focus.html).



#### <a name="registering-the-callback-for-audio-focus"></a>Registrando o retorno de chamada para o foco de áudio

Registrar o `FocusChangeListener` retorno `IOnAudioChangeListener` de chamada do é uma parte importante da obtenção e liberação do foco de áudio. Isso ocorre porque a concessão do foco de áudio pode ser adiada até uma hora posterior. Por exemplo, um aplicativo pode solicitar a reprodução de música enquanto houver uma chamada telefônica em andamento. O foco de áudio não será concedido até que a chamada telefônica seja concluída.

Por esse motivo, o objeto de retorno de chamada é passado como um `GetAudioFocus` parâmetro para o `AudioManager`método do, e é essa chamada que registra o retorno de chamada. Se o foco de áudio for inicialmente negado, mas posteriormente concedido, o aplicativo será `OnAudioFocusChange` informado invocando-se no retorno de chamada. O mesmo método é usado para informar ao aplicativo que o foco de áudio está sendo retirado.

Quando o aplicativo terminar de usar os recursos de áudio, ele chamará o `AbandonFocus` método `AudioManager`de e passará novamente no retorno de chamada. Isso cancela o registro do retorno de chamada e libera os recursos de áudio para que outros aplicativos possam obter o foco de áudio.



#### <a name="requesting-audio-focus"></a>Solicitando foco no áudio

As etapas necessárias para solicitar os recursos de áudio do dispositivo são as seguintes:

1.  Obtenha um identificador para o `AudioManager` serviço do sistema.

2.  Crie uma instância da classe de retorno de chamada.

3.  Solicite os recursos de áudio do dispositivo chamando o `RequestAudioFocus` método `AudioManager` no. Os parâmetros são o objeto de retorno de chamada, o tipo de fluxo (música, chamada de voz, anel, etc.) e o tipo de direito de acesso que está sendo solicitado (os recursos de áudio podem ser requeridos momentaneamente ou por um período indefinido, por exemplo).

4.  Se a solicitação for concedida, o `playMusic` método será invocado imediatamente e o áudio começará a reproduzir.

5.  Se a solicitação for negada, nenhuma ação adicional será executada. Nesse caso, o áudio só será tocado se a solicitação for concedida posteriormente.


O exemplo de código abaixo mostra estas etapas:

```csharp
Boolean RequestAudioResources(INotificationReceiver parent)
{
  AudioManager audioMan = (AudioManager) GetSystemService(Context.AudioService);
  AudioManager.IOnAudioFocusChangeListener listener  = new MyAudioListener(this);
  var ret = audioMan.RequestAudioFocus (listener, Stream.Music, AudioFocus.Gain );
  if (ret == AudioFocusRequest.Granted) {
    playMusic();
    return (true);
  } else if (ret == AudioFocusRequest.Failed) {
    return (false);
  }
  return (false);
}
```


#### <a name="releasing-audio-focus"></a>Liberando foco de áudio

Quando a reprodução da faixa é concluída, o `AbandonFocus` método ativado `AudioManager` é invocado. Isso permite que outro aplicativo receba os recursos de áudio do dispositivo. Outros aplicativos receberão uma notificação sobre essa alteração de foco de áudio se eles tiverem registrado seus próprios ouvintes.


## <a name="low-level-audio-api"></a>API de áudio de nível baixo

As APIs de áudio de baixo nível fornecem um maior controle sobre a reprodução e a gravação de áudio, pois interagem diretamente com buffers de memória em vez de usar URIs de arquivo. Há alguns cenários em que essa abordagem é preferível. Esses cenários incluem:

1.  Ao reproduzir de arquivos de áudio criptografados.

2.  Ao reproduzir uma sucessão de clipes curtos.

3.  Streaming de áudio.


### <a name="audiotrack-class"></a>Classe AudioTrack

A classe [AudioTrack](xref:Android.Media.AudioTrack) usa as APIs de áudio de baixo nível para gravação e é o equivalente de nível baixo da `MediaPlayer` classe.


#### <a name="initializing-and-playing"></a>Inicializando e jogando

Para reproduzir áudio, uma nova instância do `AudioTrack` deve ser instanciada. A lista de argumentos passada para o [Construtor](xref:Android.Media.AudioTrack) especifica como reproduzir o exemplo de áudio contido no buffer. Os argumentos são:

1.  Tipo &ndash; de fluxo Voice, toque, música, sistema ou alarme.

2.  Frequência &ndash; a taxa de amostragem expressa em Hz.

3.  Configuração &ndash; de canal mono ou estéreo.

4.  Codificação do &ndash; formato de áudio de 8 bits ou 16 bits.

5.  Tamanho &ndash; do buffer em bytes.

6.  Streaming de &ndash; modo de buffer ou estático.


Após a construção, o método [play](xref:Android.Media.AudioTrack.Play) do `AudioTrack` é invocado para configurá-lo para iniciar a reprodução. Gravar o buffer `AudioTrack` de áudio no inicia a reprodução:

```csharp
void PlayAudioTrack(byte[] audioBuffer)
{
  AudioTrack audioTrack = new AudioTrack(
    // Stream type
    Stream.Music,
    // Frequency
    11025,
    // Mono or stereo
    ChannelOut.Mono,
    // Audio encoding
    Android.Media.Encoding.Pcm16bit,
    // Length of the audio clip.
    audioBuffer.Length,
    // Mode. Stream or static.
    AudioTrackMode.Stream);

    audioTrack.Play();
    audioTrack.Write(audioBuffer, 0, audioBuffer.Length);
}
```


#### <a name="pausing-and-stopping-the-playback"></a>Pausando e parando a reprodução

Chame o método [Pause](xref:Android.Media.AudioTrack.Pause) para pausar a reprodução:

```csharp
audioTrack.Pause();
```

Chamar o método [Stop](xref:Android.Media.AudioTrack.Stop) terminará a reprodução permanentemente:

```csharp
audioTrack.Stop();
```


#### <a name="cleanup"></a>Limpeza

Quando o `AudioTrack` não for mais necessário, seus recursos deverão ser liberados chamando a [versão](xref:Android.Media.AudioTrack.Release):

```csharp
audioTrack.Release();
```


### <a name="the-audiorecord-class"></a>A classe AudioRecord

A classe [AudioRecord](xref:Android.Media.AudioRecord) é o equivalente de `AudioTrack` no lado da gravação. Como `AudioTrack`o, ele usa os buffers de memória diretamente, em vez de arquivos e URIs. Ele requer que a `RECORD_AUDIO` permissão seja definida no manifesto.


#### <a name="initializing-and-recording"></a>Inicializando e gravando

A primeira etapa é construir um novo objeto [AudioRecord](xref:Android.Media.AudioRecord) . A lista de argumentos passada para o [Construtor](xref:Android.Media.AudioRecord) fornece todas as informações necessárias para a gravação. Diferentemente `AudioTrack`do no, onde os argumentos são amplamente enumerações, os argumentos equivalentes no `AudioRecord` são inteiros. Elas incluem:

1.  Fonte de entrada de áudio de hardware, como microfone.

2.  Tipo &ndash; de fluxo Voice, toque, música, sistema ou alarme.

3.  Frequência &ndash; a taxa de amostragem expressa em Hz.

4.  Configuração &ndash; de canal mono ou estéreo.

5.  Codificação do &ndash; formato de áudio de 8 bits ou 16 bits.

6.  Tamanho do buffer-em bytes


Depois que `AudioRecord` o é construído, seu método [StartRecording](xref:Android.Media.AudioRecord.StartRecording) é invocado. Agora ele está pronto para começar a gravar. O `AudioRecord` lê continuamente o buffer de áudio para entrada e grava essa entrada em um arquivo de áudio.

```csharp
void RecordAudio()
{
  byte[] audioBuffer = new byte[100000];
  var audRecorder = new AudioRecord(
    // Hardware source of recording.
    AudioSource.Mic,
    // Frequency
    11025,
    // Mono or stereo
    ChannelIn.Mono,
    // Audio encoding
    Android.Media.Encoding.Pcm16bit,
    // Length of the audio clip.
    audioBuffer.Length
  );
  audRecorder.StartRecording();
  while (true) {
    try
    {
      // Keep reading the buffer while there is audio input.
      audRecorder.Read(audioBuffer, 0, audioBuffer.Length);
      // Write out the audio file.
    } catch (Exception ex) {
      Console.Out.WriteLine(ex.Message);
      break;
    }
  }
}
```


#### <a name="stopping-the-recording"></a>Parando a gravação

Chamar o método [Stop](xref:Android.Media.AudioRecord.Stop) encerra a gravação:

```csharp
audRecorder.Stop();
```


#### <a name="cleanup"></a>Limpeza

Quando o `AudioRecord` objeto não for mais necessário, chamar seu método [Release](xref:Android.Media.AudioRecord.Release) liberará todos os recursos associados a ele:

```csharp
audRecorder.Release();
```


## <a name="summary"></a>Resumo

O sistema operacional Android fornece uma estrutura poderosa para reproduzir, gravar e gerenciar áudio. Este artigo abordou como reproduzir e gravar áudio usando as classes e `MediaPlayer` `MediaRecorder` alto nível. Em seguida, ele explorou como usar as notificações de áudio para compartilhar os recursos de áudio do dispositivo entre diferentes aplicativos. Por fim, ele tratou de como reproduzir e gravar áudio usando as APIs de baixo nível, que são interface diretamente com buffers de memória.


## <a name="related-links"></a>Links relacionados

- [Trabalhando com áudio (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/example-workingwithaudio)
- [Player de mídia](xref:Android.Media.MediaPlayer)
- [Gravador de mídia](xref:Android.Media.MediaRecorder)
- [Gerenciador de áudio](xref:Android.Media.AudioManager)
- [Faixa de áudio](xref:Android.Media.AudioTrack)
- [Gravador de áudio](xref:Android.Media.AudioRecord)
