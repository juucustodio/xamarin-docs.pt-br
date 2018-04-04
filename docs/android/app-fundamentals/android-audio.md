---
title: Android Audio
description: O sistema operacional Android fornece amplo suporte para multimídia, abrangendo o áudio e vídeo. Este guia se concentra em áudio no Android e abrange a reprodução e gravação de áudio usando o player de áudio interno e classes de gravador, bem como a API de nível baixo de áudio. Ele também aborda trabalhar com eventos de áudio difusão por outros aplicativos, para que os desenvolvedores podem criar aplicativos com bom comportamento.
ms.prod: xamarin
ms.assetid: 646ED563-C34E-256D-4B56-29EE99881C27
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/28/2018
ms.openlocfilehash: aff0d67549707129bfc85246318c33c522e4f1f6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="android-audio"></a>Android Audio

_O sistema operacional Android fornece amplo suporte para multimídia, abrangendo o áudio e vídeo. Este guia se concentra em áudio no Android e abrange a reprodução e gravação de áudio usando o player de áudio interno e classes de gravador, bem como a API de nível baixo de áudio. Ele também aborda trabalhar com eventos de áudio difusão por outros aplicativos, para que os desenvolvedores podem criar aplicativos com bom comportamento._


## <a name="overview"></a>Visão geral

Dispositivos móveis modernos adotaram funcionalidade que anteriormente exigiria dedicados partes de equipamento &ndash; câmeras, players de música e os gravadores de vídeos. Por isso, estruturas de multimídia tornaram-se um recurso de primeira classe nas APIs portáteis.

Android fornece amplo suporte para multimídia. Este artigo examina a trabalhar com áudio no Android e abrange os seguintes tópicos

1.  **Reprodução de áudio com Media Player** &ndash; usando o interno `MediaPlayer` classe para executar o áudio, incluindo arquivos de áudio locais e arquivos de áudio em fluxo com o `AudioTrack` classe.

2.  **Gravação de áudio** &ndash; usando o interno `MediaRecorder` classe para gravar áudio.

3.  **Trabalhando com notificações de áudio** &ndash; usando notificações de áudio para criar aplicativos com bom comportamento corretamente responderem a eventos (como chamadas de entrada), suspender ou cancelar suas saídas de áudio.

4.  **Trabalhando com áudio baixo** &ndash; reproduzindo áudio usando o `AudioTrack` classe por gravar diretamente em buffers de memória. Gravação de áudio usando o `AudioRecord` classe e ler diretamente dos buffers de memória.


## <a name="requirements"></a>Requisitos

Este guia requer 2.0 Android (API nível 5) ou superior. Observe que a depuração de áudio no Android deve ser feito em um dispositivo.

É necessário para a solicitação de `RECORD_AUDIO` permissões em **AndroidManifest.XML**:

![Necessário seção permissões de manifesto do Android com registro\_áudio habilitado](android-audio-images/image01.png)



## <a name="playing-audio-with-the-mediaplayer-class"></a>Reprodução de áudio com a classe do Media Player

É a maneira mais simples para executar o áudio no Android com o interno [Media Player](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/) classe.
`MediaPlayer` pode reproduzir arquivos de locais ou remotos, passando o caminho do arquivo. No entanto, `MediaPlayer` muito diferenciação de estado e um de seus métodos de chamada em um estado inadequado fará com que uma exceção seja lançada. É importante interagir com `MediaPlayer` na ordem descrita abaixo para evitar erros.



### <a name="initializing-and-playing"></a>Inicializando e execução

Reproduzindo áudio com `MediaPlayer` exige a seguinte sequência:

1. Criar uma instância de um novo [Media Player](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/) objeto.

1. Configurar o arquivo para reproduzir por meio de [SetDataSource](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.SetDataSource/p/Java.IO.FileDescriptor/) método.

1. Chamar o [preparar](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Prepare/) método para inicializar o player.

1. Chamar o [iniciar](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Start/) método para iniciar a reprodução de áudio.


O exemplo de código a seguir ilustra esse uso:

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


### <a name="suspending-and-resuming-playback"></a>Suspender e retomar a reprodução

A reprodução pode ser suspenso chamando o [pausar](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Pause%28%29/) método:

```csharp
player.Pause();
```

Para continuar a reprodução em pausa, chame o [iniciar](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Start%28%29/) método.
Isso continuará a partir do local em pausa na reprodução:

```csharp
player.Start();
```

Chamando o [parar](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Stop%28%29/) método no player termina uma reprodução em andamento:

```csharp
player.Stop();
```

Quando o player não for mais necessário, os recursos devem ser liberados chamando o [versão](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Release%28%29/) método:

```csharp
player.Release();
```



## <a name="using-the-mediarecorder-class-to-record-audio"></a>Usando a classe MediaRecorder para gravar áudio

O resultado para `MediaPlayer` para gravação de áudio no Android é o [MediaRecorder](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/) classe. Como o `MediaPlayer`, ele depende do estado e passa por vários estados para chegar ao ponto em que ele pode iniciar a gravação. Para gravar áudio, o `RECORD_AUDIO` permissão deve ser definida. Para obter instruções sobre como configurar o aplicativo Consulte permissões [trabalhando com AndroidManifest.xml](~/android/platform/android-manifest.md).


### <a name="initializing-and-recording"></a>Inicializando e gravação

Gravação de áudio com o `MediaRecorder` exige as seguintes etapas:

1. Criar uma instância de um novo [MediaRecorder](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/) objeto.

2. Especifique qual dispositivo de hardware a ser usado para capturar a entrada de áudio por meio de [SetAudioSource](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetAudioSource/p/Android.Media.AudioSource/) método.

3. Definir o formato de áudio de arquivo de saída usando o [SetOutputFormat](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetOutputFormat/p/Android.Media.OutputFormat/) método. Para obter uma lista de tipos de áudio com suporte, consulte [Android suporte para formatos Media](http://developer.android.com/guide/appendix/media-formats.html).

4. Chamar o [SetAudioEncoder](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetAudioEncoder/p/Android.Media.AudioEncoder/) método para definir o tipo de codificação de áudio.

5. Chamar o [SetOutputFile](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetOutputFile/p/System.String/) método para especificar o nome do arquivo de saída que os dados de áudio são gravados.

6. Chamar o [preparar](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Prepare%28%29/) método para inicializar o gravador.

7. Chamar o [iniciar](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Start%28%29/) método para iniciar a gravação.


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


### <a name="stopping-recording"></a>Interrompendo a gravação

Para interromper a gravação, chame o `Stop` método sobre o `MediaRecorder`:

```csharp
recorder.Stop();
```



### <a name="cleaning-up"></a>Limpando

Uma vez o `MediaRecorder` foi interrompido, chame o [redefinir](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Reset%28%29/) método para colocá-lo de volta em seu estado ocioso:

```csharp
recorder.Reset();
```

Quando o `MediaRecorder` é não é mais necessário, seus recursos devem ser liberados chamando o [versão](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Release%28%29/) método:

```csharp
recorder.Release();
```


## <a name="managing-audio-notifications"></a>Gerenciar notificações de áudio



### <a name="the-audiomanager-class"></a>A classe AudioManager

O [AudioManager](https://developer.xamarin.com/api/type/Android.Media.AudioManager/) classe fornece acesso às notificações de áudio que permitem que os aplicativos saber quando ocorrem eventos de áudio. Esse serviço também fornece acesso a outros recursos de áudio, como controle de modo de toque e volume. O `AudioManager` permite que um aplicativo lidar com notificações de áudio para controlar a reprodução de áudio.



### <a name="managing-audio-focus"></a>Gerenciando o foco de áudio

Os recursos de áudio do dispositivo (o player interno e o gravador) são compartilhados por todos os aplicativos em execução.

Conceitualmente, isso é semelhante a aplicativos em um computador em que apenas um aplicativo tem o foco do teclado: depois de selecionar um dos aplicativos em execução, clique do mouse, a entrada do teclado irá somente para esse aplicativo.

Áudio foco é uma ideia semelhante e impede que mais de um aplicativo de reprodução ou gravação de áudio ao mesmo tempo. É mais complicado do que o foco do teclado porque é voluntária &ndash; o aplicativo pode ignorar esse fato que atualmente não tem o foco de áudio e reproduzir independentemente &ndash; e porque há tipos diferentes de foco de áudio que podem ser solicitado. Por exemplo, se o solicitante só deve executar o áudio para um período muito curto, ele pode solicitar foco transitório.

Áudio foco pode ser concedido imediatamente, ou inicialmente negado e concedido mais tarde. Por exemplo, se um aplicativo solicitações áudio foco durante uma chamada telefônica, será negado, mas também pode ser concedido o foco quando a chamada telefônica é concluída. Nesse caso, um ouvinte está registrado para responder adequadamente, se o foco de áudio é excluído. Solicitar o foco de áudio é usado para determinar se ele é Okey executar ou gravar áudio.

Para obter mais informações sobre o foco de áudio, consulte [Gerenciando áudio foco](http://developer.android.com/training/managing-audio/audio-focus.html).



#### <a name="registering-the-callback-for-audio-focus"></a>Registrando o retorno de chamada para o foco do áudio

Registrando o `FocusChangeListener` retorno de chamada do `IOnAudioChangeListener` é uma parte importante de como adquirir e liberar o foco de áudio. Isso ocorre porque a concessão de foco de áudio pode ser adiada até um momento posterior. Por exemplo, um aplicativo pode solicitar para reproduzir música, embora não haja uma chamada telefônica em andamento. Áudio foco não receberá até que a chamada telefônica é concluída.

Por esse motivo, o objeto de retorno de chamada é passado como um parâmetro para o `GetAudioFocus` método o `AudioManager`, e é essa chamada que registra o retorno de chamada. Se o foco de áudio é inicialmente negado mas posteriormente concedido, o aplicativo é informado invocando `OnAudioFocusChange` no retorno de chamada. O mesmo método é usado para informar o aplicativo que o foco de áudio está sendo colocado fora.

Quando o aplicativo terminar de usar os recursos de áudio, ele chama o `AbandonFocus` método o `AudioManager`e passa novamente no retorno de chamada. Isso cancela o registro de retorno de chamada e libera os recursos de áudio, para que outros aplicativos podem obter o foco de áudio.



#### <a name="requesting-audio-focus"></a>Solicitando o foco de áudio

As etapas necessárias para os recursos do dispositivo de áudio de solicitação são os seguintes:

1.  Obter um identificador para o `AudioManager` o serviço do sistema.

2.  Crie uma instância da classe de retorno de chamada.

3.  Os recursos do dispositivo de áudio de solicitação chamando o `RequestAudioFocus` método o `AudioManager` . Os parâmetros são o objeto de retorno de chamada, o tipo de fluxo (música, chamada de voz, anel etc.) e o tipo do acesso à direita que está sendo solicitado (os recursos de áudio podem ser solicitados momentaneamente ou por um período indefinido, por exemplo).

4.  Se a solicitação é atendida, o `playMusic` método é chamado imediatamente, e o áudio começa a reproduzir.

5.  Se a solicitação for negada, nenhuma outra ação é executada. Nesse caso, o áudio só funcionará se a solicitação é concedida em um momento posterior.


O exemplo de código a seguir mostra estas etapas:

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


#### <a name="releasing-audio-focus"></a>Liberando o foco de áudio

Quando a reprodução da faixa for concluída, o `AbandonFocus` método `AudioManager` é invocado. Isso permite que outro aplicativo obter os recursos de áudio do dispositivo. Outros aplicativos receberá uma notificação dessa alteração foco áudio se eles registraram seus próprios ouvintes.


## <a name="low-level-audio-api"></a>API de áudio de nível baixo

As APIs de áudio baixo nível fornecem um maior controle sobre o áudio de reprodução e gravação porque eles interagem diretamente com buffers de memória em vez de usar o arquivo URIs. Existem alguns cenários em que essa abordagem é preferível. Esses cenários incluem:

1.  Durante a reprodução de arquivos de áudio criptografados.

2.  Ao executar uma sucessão de clipes curtos.

3.  Fluxo de áudio.


### <a name="audiotrack-class"></a>Classe AudioTrack

O [AudioTrack](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/) classe usa as APIs de áudio de baixo nível para gravação e é o equivalente de nível baixo do `MediaPlayer` classe.


#### <a name="initializing-and-playing"></a>Inicializando e execução

Para executar o áudio, uma nova instância da `AudioTrack` deve ser instanciada. A lista de argumentos passados para o [construtor](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/#memberlist) Especifica como executar a amostra de áudio contida no buffer. Os argumentos são:

1.  Tipo de fluxo &ndash; voz, toque, música, sistema ou alarme.

2.  Frequência &ndash; a taxa de amostragem, expressada em Hz.

3.  Configuração de canal &ndash; Mono ou estéreo.

4.  Formato de áudio &ndash; 8 bits ou codificação de 16 bits.

5.  Tamanho do buffer &ndash; em bytes.

6.  Modo de buffer &ndash; streaming ou estático.


Após a construção, o [reproduzir](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Play%28%29/) método `AudioTrack` é invocado para defini-la para iniciar a execução. Gravar o buffer de áudio de `AudioTrack` inicia a reprodução:

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


#### <a name="pausing-and-stopping-the-playback"></a>Pausar e parar a reprodução

Chamar o [pausar](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Pause%28%29/) método para pausar a reprodução:

```csharp
audioTrack.Pause();
```

Chamando o [parar](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Stop%28%29/) método terminará permanentemente a reprodução:

```csharp
audioTrack.Stop();
```


#### <a name="cleanup"></a>Limpeza

Quando o `AudioTrack` é não é mais necessário, seus recursos devem ser liberados chamando [versão](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Release%28%29/):

```csharp
audioTrack.Release();
```


### <a name="the-audiorecord-class"></a>A classe AudioRecord

O [AudioRecord](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/) classe é o equivalente de `AudioTrack` no lado de gravação. Como `AudioTrack`, ele usa os buffers de memória diretamente, em vez de arquivos e URIs. Isso requer que o `RECORD_AUDIO` permissão de ser definida no manifesto.


#### <a name="initializing-and-recording"></a>Inicializando e gravação

A primeira etapa é criar um novo [AudioRecord](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/) objeto. A lista de argumentos passados para o [construtor](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/#memberlist) fornece todas as informações necessárias para gravação. Diferentemente de `AudioTrack`, onde os argumentos são amplamente enumerações, os argumentos equivalentes no `AudioRecord` são números inteiros. Elas incluem:

1.  Hardware áudio fonte de entrada como microfone.

2.  Tipo de fluxo &ndash; voz, toque, música, sistema ou alarme.

3.  Frequência &ndash; a taxa de amostragem, expressada em Hz.

4.  Configuração de canal &ndash; Mono ou estéreo.

5.  Formato de áudio &ndash; 8 bits ou codificação de 16 bits.

6.  Tamanho em bytes do buffer


Uma vez o `AudioRecord` é construída, seu [Iniciargravacao](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.StartRecording%28%29/) método é invocado. Agora está pronto para começar a gravar. O `AudioRecord` continuamente lê o buffer de áudio de entrada e grava essa entrada limite para um arquivo de áudio.

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


#### <a name="stopping-the-recording"></a>Parar a gravação

Chamando o [parar](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.Stop%28%29/) método encerra a gravação:

```csharp
audRecorder.Stop();
```


#### <a name="cleanup"></a>Limpeza

Quando o `AudioRecord` objeto não for mais necessário, chamando seu [versão](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.Release%28%29/) método libera todos os recursos associados a ele:

```csharp
audRecorder.Release();
```


## <a name="summary"></a>Resumo

O sistema operacional Android fornece uma estrutura eficiente para reprodução, gravação e gerenciamento de áudio. Este artigo coberto como reproduzir e gravar áudio usando o alto nível `MediaPlayer` e `MediaRecorder` classes. Em seguida, ele explorou como usar notificações de áudio para compartilhar os recursos de áudio do dispositivo entre diferentes aplicativos. Finalmente, ele tratadas como para reprodução e grave áudio usando as APIs de baixo nível, que a interface diretamente com buffers de memória.


## <a name="related-links"></a>Links relacionados

- [Trabalhando com áudio (exemplo)](https://developer.xamarin.com/samples/Example_WorkingWithAudio/)
- [Reprodutor de mídia](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/)
- [Gravador de mídia](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/)
- [Audio Manager](https://developer.xamarin.com/api/type/Android.Media.AudioManager/)
- [Faixa de áudio](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/)
- [Gravador de áudio](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/)
