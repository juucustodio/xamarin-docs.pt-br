---
title: Reconhecimento de fala no Xamarin. iOS
description: Este artigo apresenta o novo Speech API e mostra como implementá-lo em um aplicativo Xamarin. iOS para dar suporte ao reconhecimento de fala contínuo e transcrever a fala (de fluxos de áudio dinâmicos ou gravados) em texto.
ms.prod: xamarin
ms.assetid: 64FED50A-6A28-4833-BEAE-63CEC9A09010
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 98cbcd333d223d741602786643ef1948915d7dfc
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436471"
---
# <a name="speech-recognition-in-xamarinios"></a>Reconhecimento de fala no Xamarin. iOS

_Este artigo apresenta o novo Speech API e mostra como implementá-lo em um aplicativo Xamarin. iOS para dar suporte ao reconhecimento de fala contínuo e transcrever a fala (de fluxos de áudio dinâmicos ou gravados) em texto._

Novo no iOS 10, a Apple lançou a API de reconhecimento de fala que permite que um aplicativo iOS dê suporte ao reconhecimento de fala contínuo e transcrevesse a fala (de fluxos de áudio dinâmicos ou gravados) em texto.

De acordo com a Apple, a API de reconhecimento de fala tem os seguintes recursos e benefícios:

- Altamente preciso
- Estado da arte
- Fácil de usar
- Rápido
- Dá suporte a vários idiomas
- Respeita a privacidade do usuário

## <a name="how-speech-recognition-works"></a>Como funciona o reconhecimento de fala

O reconhecimento de fala é implementado em um aplicativo iOS adquirindo áudio dinâmico ou gravado previamente (em qualquer um dos idiomas falados que a API suporta) e passando-o para um reconhecedor de fala que retorna uma transcrição de texto sem formatação das palavras faladas.

[![Como funciona o reconhecimento de fala](speech-images/speech01.png)](speech-images/speech01.png#lightbox)

### <a name="keyboard-dictation"></a>Ditado de teclado

Quando a maioria dos usuários imagina o reconhecimento de fala em um dispositivo iOS, eles consideram o assistente interno de voz Siri, que foi lançado junto com o ditado de teclado no iOS 5 com o 4S do iPhone.

O ditado de teclado tem suporte de qualquer elemento de interface que dê suporte a TextKit (como `UITextField` ou `UITextArea` ) e é ativado pelo usuário clicando no botão de ditado (diretamente à esquerda da barra de espaços) no teclado virtual do Ios.

A Apple lançou as seguintes estatísticas de ditado de teclado (coletadas desde 2011):

- O ditado de teclado foi amplamente usado desde que foi lançado no iOS 5.
- Aproximadamente 65.000 aplicativos usam-os por dia.
- Um terço de todos os ditados do iOS é feito em um aplicativo de terceiros.

O ditado de teclado é extremamente fácil de usar, pois não requer nenhum esforço na parte do desenvolvedor, além de usar um elemento de interface TextKit no design da interface do usuário do aplicativo. O ditado de teclado também tem a vantagem de não exigir nenhuma solicitação de privilégio especial do aplicativo antes de poder ser usado.

Os aplicativos que usam as novas APIs de reconhecimento de fala exigirão permissões especiais para serem concedidas pelo usuário, pois o reconhecimento de fala requer a transmissão e o armazenamento temporário de dados nos servidores da Apple. Consulte nossa documentação de [aprimoramentos de segurança e privacidade](~/ios/app-fundamentals/security-privacy.md) para obter detalhes.

Embora o ditado de teclado seja fácil de implementar, ele vem com várias limitações e desvantagens:

- Ele requer o uso de um campo de entrada de texto e a exibição de um teclado.
- Ele funciona apenas com entrada de áudio ao vivo e o aplicativo não tem controle sobre o processo de gravação de áudio.
- Ele não fornece controle sobre o idioma usado para interpretar a fala do usuário.
- Não há como o aplicativo saber se o botão de ditado está ainda disponível para o usuário.
- O aplicativo não pode personalizar o processo de gravação de áudio.
- Ele fornece um conjunto muito superficial de resultados que não tem informações como tempo e confiança.

### <a name="speech-recognition-api"></a>API de reconhecimento de fala

Novo no iOS 10, a Apple lançou a API de reconhecimento de fala, que fornece uma maneira mais eficiente para um aplicativo iOS implementar o reconhecimento de fala. Essa API é a mesma que a Apple usa para ligar o ditado de siri e de teclado e é capaz de fornecer uma transcrição rápida com a precisão do estado da arte.

Os resultados fornecidos pela API de reconhecimento de fala são personalizados de forma transparente para os usuários individuais, sem que o aplicativo precise coletar ou acessar dados de usuário privados.

A API de reconhecimento de fala fornece resultados de volta ao aplicativo de chamada quase em tempo real conforme o usuário está falando e fornece mais informações sobre os resultados da tradução do que apenas texto. Estão incluídos:

- Várias interpretações do que o usuário disse.
- Níveis de confiança para as traduções individuais.
- Informações de tempo.

Conforme mencionado acima, o áudio para tradução pode ser fornecido por um feed ao vivo ou de uma fonte previamente gravada e em qualquer um dos mais de 50 idiomas e dialetos com suporte no iOS 10.

A API de reconhecimento de fala pode ser usada em qualquer dispositivo iOS que executa o iOS 10 e, na maioria dos casos, requer uma conexão com a Internet ao vivo, desde que a grande parte das traduções ocorra nos servidores da Apple. Dito isso, alguns dispositivos iOS mais recentes dão suporte à tradução do Always on no dispositivo de idiomas específicos.

A Apple incluiu uma API de disponibilidade para determinar se um determinado idioma está disponível para tradução no momento atual. O aplicativo deve usar essa API em vez de testar diretamente a conectividade com a Internet.

Conforme observado acima na seção ditado do teclado, o reconhecimento de fala requer a transmissão e o armazenamento temporário de dados nos servidores da Apple pela Internet e, dessa forma, o aplicativo _deve_ solicitar a permissão do usuário para executar o reconhecimento, incluindo a `NSSpeechRecognitionUsageDescription` chave em seu `Info.plist` arquivo e chamar o `SFSpeechRecognizer.RequestAuthorization` método. 

Com base na origem do áudio que está sendo usado para reconhecimento de fala, outras alterações no arquivo do aplicativo `Info.plist` podem ser necessárias. Consulte nossa documentação de [aprimoramentos de segurança e privacidade](~/ios/app-fundamentals/security-privacy.md) para obter detalhes.

## <a name="adopting-speech-recognition-in-an-app"></a>Adoção do reconhecimento de fala em um aplicativo

Há quatro etapas principais que o desenvolvedor deve adotar para adotar o reconhecimento de fala em um aplicativo iOS:

- Forneça uma descrição de uso no arquivo do aplicativo `Info.plist` usando a `NSSpeechRecognitionUsageDescription` chave. Por exemplo, um aplicativo de câmera pode incluir a seguinte descrição: _"isso permite que você tire uma foto apenas dizendo a palavra" queijo "._
- Solicite autorização chamando o `SFSpeechRecognizer.RequestAuthorization` método para apresentar uma explicação (fornecida na `NSSpeechRecognitionUsageDescription` chave acima) de por que o aplicativo deseja acesso de reconhecimento de fala ao usuário em uma caixa de diálogo e permitir que eles aceitem ou recusem.
- Criar uma solicitação de reconhecimento de fala:
  - Para áudio previamente gravado em disco, use a `SFSpeechURLRecognitionRequest` classe.
  - Para áudio ao vivo (ou áudio da memória), use a `SFSPeechAudioBufferRecognitionRequest` classe.
- Passe a solicitação de reconhecimento de fala para um reconhecedor de fala ( `SFSpeechRecognizer` ) para iniciar o reconhecimento. O aplicativo pode, opcionalmente, manter o retorno `SFSpeechRecognitionTask` para monitorar e acompanhar os resultados de reconhecimento.

Essas etapas serão abordadas em detalhes abaixo.

### <a name="providing-a-usage-description"></a>Fornecendo uma descrição de uso

Para fornecer a `NSSpeechRecognitionUsageDescription` chave necessária no `Info.plist` arquivo, faça o seguinte:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Clique duas vezes no `Info.plist` arquivo para abri-lo para edição.
2. Alterne para a exibição de **origem** : 

    [![A exibição da fonte](speech-images/speech02.png)](speech-images/speech02.png#lightbox)
3. Clique em **Adicionar nova entrada**, insira `NSSpeechRecognitionUsageDescription` para a **Propriedade**, `String` para o **tipo** e uma **Descrição de uso** como o **valor**. Por exemplo: 

    [![Adicionando NSSpeechRecognitionUsageDescription](speech-images/speech03.png)](speech-images/speech03.png#lightbox)
4. Se o aplicativo for manipular a transcrição de áudio ao vivo, ele também exigirá uma descrição de uso do microfone. Clique em **Adicionar nova entrada**, insira `NSMicrophoneUsageDescription` para a **Propriedade**, `String` para o **tipo** e uma **Descrição de uso** como o **valor**. Por exemplo: 

    [![Adicionando NSMicrophoneUsageDescription](speech-images/speech04.png)](speech-images/speech04.png#lightbox)
5. Salve as alterações no arquivo.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Clique duas vezes no `Info.plist` arquivo para abri-lo para edição.
2. Clique em **Adicionar nova entrada**, insira `NSSpeechRecognitionUsageDescription` para a **Propriedade**, `String` para o **tipo** e uma **Descrição de uso** como o **valor**. Por exemplo: 

    [![Adicionando NSSpeechRecognitionUsageDescription](speech-images/speech03w.png)](speech-images/speech03w.png#lightbox)
3. Se o aplicativo for manipular a transcrição de áudio ao vivo, ele também exigirá uma descrição de uso do microfone. Clique em **Adicionar nova entrada**, insira `NSMicrophoneUsageDescription` para a **Propriedade**, `String` para o **tipo** e uma **Descrição de uso** como o **valor**. Por exemplo: 

    [![Adicionando NSMicrophoneUsageDescription](speech-images/speech04w.png)](speech-images/speech04w.png#lightbox)
4. Salve as alterações no arquivo.

-----

> [!IMPORTANT]
> A falha ao fornecer qualquer uma das `Info.plist` chaves acima ( `NSSpeechRecognitionUsageDescription` ou `NSMicrophoneUsageDescription` ) pode resultar na falha do aplicativo sem aviso ao tentar acessar o reconhecimento de fala ou o microfone para áudio ao vivo.

### <a name="requesting-authorization"></a>Solicitando autorização

Para solicitar a autorização de usuário necessária que permita que o aplicativo acesse o reconhecimento de fala, edite a classe do controlador de exibição principal e adicione o seguinte código:

```csharp
using System;
using UIKit;
using Speech;

namespace MonkeyTalk
{
    public partial class ViewController : UIViewController
    {
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Request user authorization
            SFSpeechRecognizer.RequestAuthorization ((SFSpeechRecognizerAuthorizationStatus status) => {
                // Take action based on status
                switch (status) {
                case SFSpeechRecognizerAuthorizationStatus.Authorized:
                    // User has approved speech recognition
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.Denied:
                    // User has declined speech recognition
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.NotDetermined:
                    // Waiting on approval
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.Restricted:
                    // The device is not permitted
                    ...
                    break;
                }
            });
        }
    }
}
```

O `RequestAuthorization` método da `SFSpeechRecognizer` classe solicitará permissão do usuário para acessar o reconhecimento de fala usando o motivo pelo qual o desenvolvedor forneceu a `NSSpeechRecognitionUsageDescription` chave do `Info.plist` arquivo.

Um `SFSpeechRecognizerAuthorizationStatus` resultado é retornado para a `RequestAuthorization` rotina de retorno de chamada do método que pode ser usada para agir com base na permissão do usuário. 

> [!IMPORTANT]
> A Apple sugere aguardar até que o usuário tenha iniciado uma ação no aplicativo que requer reconhecimento de fala antes de solicitar essa permissão.

### <a name="recognizing-pre-recorded-speech"></a>Reconhecendo fala previamente gravada

Se o aplicativo quiser reconhecer a fala a partir de um arquivo WAV ou MP3 previamente gravado, ele poderá usar o seguinte código:

```csharp
using System;
using UIKit;
using Speech;
using Foundation;
...

public void RecognizeFile (NSUrl url)
{
    // Access new recognizer
    var recognizer = new SFSpeechRecognizer ();

    // Is the default language supported?
    if (recognizer == null) {
        // No, return to caller
        return;
    }

    // Is recognition available?
    if (!recognizer.Available) {
        // No, return to caller
        return;
    }

    // Create recognition task and start recognition
    var request = new SFSpeechUrlRecognitionRequest (url);
    recognizer.GetRecognitionTask (request, (SFSpeechRecognitionResult result, NSError err) => {
        // Was there an error?
        if (err != null) {
            // Handle error
            ...
        } else {
            // Is this the final translation?
            if (result.Final) {
                Console.WriteLine ("You said, \"{0}\".", result.BestTranscription.FormattedString);
            }
        }
    });
}
```

Examinando esse código em detalhes primeiro, ele tenta criar um reconhecedor de fala ( `SFSpeechRecognizer` ). Se o idioma padrão não tiver suporte para reconhecimento de fala, `null` o será retornado e as funções serão encerradas.

Se o reconhecedor de fala estiver disponível para o idioma padrão, o aplicativo verificará se ele está disponível no momento para reconhecimento usando a `Available` propriedade. Por exemplo, o reconhecimento pode não estar disponível se o dispositivo não tiver uma conexão ativa com a Internet.

Um `SFSpeechUrlRecognitionRequest` é criado a partir do `NSUrl` local do arquivo previamente registrado no dispositivo IOS e ele é enviado para o reconhecedor de fala para processar com uma rotina de retorno de chamada.

Quando o retorno de chamada for chamado, se `NSError` não `null` houver um erro que deve ser manipulado. Como o reconhecimento de fala é feito incrementalmente, a rotina de retorno de chamada pode ser chamada mais de uma vez para que a `SFSpeechRecognitionResult.Final` propriedade seja testada para ver se a tradução foi concluída e a melhor versão da tradução é gravada ( `BestTranscription` ).

### <a name="recognizing-live-speech"></a>Reconhecendo a fala ao vivo

Se o aplicativo quiser reconhecer a fala ao vivo, o processo será muito semelhante a reconhecer a fala previamente gravada. Por exemplo:

```csharp
using System;
using UIKit;
using Speech;
using Foundation;
using AVFoundation;
...

#region Private Variables
private AVAudioEngine AudioEngine = new AVAudioEngine ();
private SFSpeechRecognizer SpeechRecognizer = new SFSpeechRecognizer ();
private SFSpeechAudioBufferRecognitionRequest LiveSpeechRequest = new SFSpeechAudioBufferRecognitionRequest ();
private SFSpeechRecognitionTask RecognitionTask;
#endregion
...

public void StartRecording ()
{
    // Setup audio session
    var node = AudioEngine.InputNode;
    var recordingFormat = node.GetBusOutputFormat (0);
    node.InstallTapOnBus (0, 1024, recordingFormat, (AVAudioPcmBuffer buffer, AVAudioTime when) => {
        // Append buffer to recognition request
        LiveSpeechRequest.Append (buffer);
    });

    // Start recording
    AudioEngine.Prepare ();
    NSError error;
    AudioEngine.StartAndReturnError (out error);

    // Did recording start?
    if (error != null) {
        // Handle error and return
        ...
        return;
    }

    // Start recognition
    RecognitionTask = SpeechRecognizer.GetRecognitionTask (LiveSpeechRequest, (SFSpeechRecognitionResult result, NSError err) => {
        // Was there an error?
        if (err != null) {
            // Handle error
            ...
        } else {
            // Is this the final translation?
            if (result.Final) {
                Console.WriteLine ("You said \"{0}\".", result.BestTranscription.FormattedString);
            }
        }
    });
}

public void StopRecording ()
{
    AudioEngine.Stop ();
    LiveSpeechRequest.EndAudio ();
}

public void CancelRecording ()
{
    AudioEngine.Stop ();
    RecognitionTask.Cancel ();
}
```

Observando esse código em detalhes, ele cria várias variáveis privadas para lidar com o processo de reconhecimento:

```csharp
private AVAudioEngine AudioEngine = new AVAudioEngine ();
private SFSpeechRecognizer SpeechRecognizer = new SFSpeechRecognizer ();
private SFSpeechAudioBufferRecognitionRequest LiveSpeechRequest = new SFSpeechAudioBufferRecognitionRequest ();
private SFSpeechRecognitionTask RecognitionTask;
```

Ele usa o AV Foundation para gravar áudio que será passado para um `SFSpeechAudioBufferRecognitionRequest` para lidar com a solicitação de reconhecimento:

```csharp
var node = AudioEngine.InputNode;
var recordingFormat = node.GetBusOutputFormat (0);
node.InstallTapOnBus (0, 1024, recordingFormat, (AVAudioPcmBuffer buffer, AVAudioTime when) => {
    // Append buffer to recognition request
    LiveSpeechRequest.Append (buffer);
});
```

O aplicativo tentará iniciar a gravação e os erros serão tratados se a gravação não puder ser iniciada:

```csharp
AudioEngine.Prepare ();
NSError error;
AudioEngine.StartAndReturnError (out error);

// Did recording start?
if (error != null) {
    // Handle error and return
    ...
    return;
}
```

A tarefa de reconhecimento é iniciada e um identificador é mantido para a tarefa de reconhecimento ( `SFSpeechRecognitionTask` ):

```csharp
RecognitionTask = SpeechRecognizer.GetRecognitionTask (LiveSpeechRequest, (SFSpeechRecognitionResult result, NSError err) => {
    ...
});
```

O retorno de chamada é usado de maneira semelhante à usada acima em fala previamente gravada.

Se a gravação for interrompida pelo usuário, o mecanismo de áudio e a solicitação de reconhecimento de fala serão informados:

```csharp
AudioEngine.Stop ();
LiveSpeechRequest.EndAudio ();
```

Se o usuário cancelar o reconhecimento, o mecanismo de áudio e a tarefa de reconhecimento serão informados:

```csharp
AudioEngine.Stop ();
RecognitionTask.Cancel ();
```

É importante chamar `RecognitionTask.Cancel` se o usuário cancelar a tradução para liberar memória e o processador do dispositivo.

> [!IMPORTANT]
> A falha ao fornecer `NSSpeechRecognitionUsageDescription` as `NSMicrophoneUsageDescription` `Info.plist` chaves ou pode resultar na falha do aplicativo sem aviso ao tentar acessar o reconhecimento de fala ou o microfone para áudio ao vivo ( `var node = AudioEngine.InputNode;` ). Consulte a seção **fornecendo uma descrição de uso** acima para obter mais informações.

## <a name="speech-recognition-limits"></a>Limites de reconhecimento de fala

A Apple impõe as seguintes limitações ao trabalhar com o reconhecimento de fala em um aplicativo iOS:

- O reconhecimento de fala é gratuito para todos os aplicativos, mas seu uso não é ilimitado:
  - Dispositivos iOS individuais têm um número limitado de reconhecimentos que podem ser executados por dia.
  - Os aplicativos serão limitados globalmente em uma base de solicitação por dia.
- O aplicativo deve estar preparado para lidar com as falhas de conexão de rede e de limite de taxa de uso do reconhecimento de fala.
- O reconhecimento de fala pode ter um alto custo tanto no desgaste da bateria quanto no alto tráfego de rede no dispositivo iOS do usuário, por isso, a Apple impõe um limite de duração de áudio estrito de aproximadamente um minuto de fala máx.

Se um aplicativo estiver atingindo rotineiramente seus limites de limitação de taxa, a Apple pedirá que o desenvolvedor entre em contato com eles.

## <a name="privacy-and-usability-considerations"></a>Considerações sobre privacidade e usabilidade

A Apple tem a seguinte sugestão para ser transparente e respeitar a privacidade do usuário ao incluir o reconhecimento de fala em um aplicativo iOS:

- Ao registrar a fala do usuário, lembre-se de indicar claramente que a gravação está ocorrendo na interface do usuário do aplicativo. Por exemplo, o aplicativo pode reproduzir um som de "gravação" e exibir um indicador de gravação.
- Não use o reconhecimento de fala para informações confidenciais do usuário, como senhas, dados de integridade ou informações financeiras.
- Mostre os resultados do reconhecimento _antes_ de agir neles. Isso não apenas fornece comentários sobre o que o aplicativo está fazendo, mas permite que o usuário manipule erros de reconhecimento conforme eles são feitos.

## <a name="summary"></a>Resumo

Este artigo apresentou o novo Speech API e mostrou como implementá-lo em um aplicativo Xamarin. iOS para dar suporte ao reconhecimento de fala contínuo e transcrever a fala (de fluxos de áudio dinâmicos ou gravados) em texto. 

## <a name="related-links"></a>Links Relacionados

- [SpeakToMe (exemplo)](/samples/xamarin/ios-samples/ios10-speaktome)