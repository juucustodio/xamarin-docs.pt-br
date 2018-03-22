---
title: Reconhecimento de fala
description: "Este artigo apresenta a nova API de fala e mostra como implementá-la em um aplicativo xamarin dá suporte ao reconhecimento de fala contínua e transcrever da fala (fluxos de áudio ao vivo ou gravados) em texto."
ms.topic: article
ms.prod: xamarin
ms.assetid: 64FED50A-6A28-4833-BEAE-63CEC9A09010
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: e868c0ee71688e208c5217d9f5a89ea3acec988c
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2018
---
# <a name="speech-recognition"></a>Reconhecimento de fala

_Este artigo apresenta a nova API de fala e mostra como implementá-la em um aplicativo xamarin dá suporte ao reconhecimento de fala contínua e transcrever da fala (fluxos de áudio ao vivo ou gravados) em texto._

Novo para iOS 10, Apple tem versão a API de reconhecimento de fala que permite que um aplicativo iOS para dar suporte a reconhecimento de fala contínua e transcrever da fala (fluxos de áudio ao vivo ou gravados) em texto.

De acordo com a Apple, a API de reconhecimento de fala tem os seguintes recursos e benefícios:

- Alta precisão
- De última geração
- Fácil de usar
- Rápido
- Oferece suporte a vários idiomas
- Privacidade do usuário aspectos

## <a name="how-speech-recognition-works"></a>Como funciona o reconhecimento de fala

O reconhecimento de fala é implementado em um aplicativo iOS ao adquirir o áudio ao vivo ou gravado (em qualquer uma das linguagens faladas que oferece suporte a API) e passando-o para um reconhecedor de fala que retorna uma transcrição de texto sem formatação das palavras faladas.

[![](speech-images/speech01.png "Como funciona o reconhecimento de fala")](speech-images/speech01.png#lightbox)

### <a name="keyboard-dictation"></a>Teclado ditado

Quando a maioria dos usuários acha do reconhecimento de fala em um dispositivo iOS, eles pensam o Assistente de voz Siri interno, que foi lançado junto com o teclado ditado em iOS 5 com o 4S iPhone.

Teclado ditado é suportado por qualquer elemento de interface que oferece suporte a TextKit (como `UITextField` ou `UITextArea`) e é ativada quando o usuário clica no botão ditado (diretamente à esquerda da barra de espaço) no teclado virtual iOS.

Apple lançou as seguintes estatísticas de teclado ditado (coletadas desde 2011):

- Teclado ditado foi usado amplamente desde que ele foi lançado no iOS 5.
- Aproximadamente 65.000 aplicativos usá-lo por dia.
- Sobre um terço do iOS todos ditado é feito em um aplicativo de parte 3.

Teclado ditado é extremamente fácil de usar, pois ele não requer nenhum esforço da parte do desenvolvedor, diferentes do uso de um elemento de interface TextKit no design de interface do usuário do aplicativo. Teclado ditado também tem a vantagem de não exigir quaisquer solicitações de privilégio especial do aplicativo antes que ele possa ser usado.

Aplicativos que usam as novas APIs de reconhecimento de fala exigirá permissões especiais para ser concedida pelo usuário, como reconhecimento de fala requer a transmissão e o armazenamento temporário de dados em servidores da Apple. Confira nosso [segurança e privacidade aprimoramentos](~/ios/app-fundamentals/security-privacy.md) documentação para obter detalhes.

Enquanto o teclado ditado é fácil de implementar, ele vêm com várias desvantagens e limitações:

- Ele requer o uso de um campo de entrada de texto e a exibição de um teclado.
- Ele funciona com apenas de entrada de áudio em tempo real e o aplicativo não tem controle sobre o processo de gravação de áudio.
- Ele não fornece controle sobre a linguagem que é usada para interpretar a voz do usuário.
- Não é possível para o aplicativo saber se o botão ditado está disponível para o usuário.
- O aplicativo não pode personalizar o processo de gravação de áudio.
- Ele fornece um conjunto muito superficial de resultados que não tem informações como tempo e confiança.

### <a name="speech-recognition-api"></a>API de reconhecimento de fala

Novo para iOS 10, Apple lançou a API de reconhecimento de fala que fornece uma maneira mais eficiente para um aplicativo iOS implementar o reconhecimento de fala. Esta API é o mesmo que a Apple usa a energia Siri e teclado ditado e é capaz de fornecer transcrição rápida com precisão de última geração.

Os resultados fornecidos pela API de reconhecimento de fala transparentemente são personalizados para usuários individuais, sem o aplicativo precisar coletar ou acessar os dados privados de usuário.

A API de reconhecimento de fala fornece resultados de volta para o aplicativo de chamada em quase em tempo real, como o usuário está falando e fornece mais informações sobre os resultados da conversão do que apenas texto. Elas incluem:

- Vários interpretações de que o usuário diz.
- Níveis de confiança para as conversões individuais.
- Informações de tempo.

Como mencionado acima, áudio para conversão pode ser fornecido um por um feed, ou da origem previamente gravada e em qualquer um dos mais de 50 idiomas e dialetos suportados pelo iOS 10.

A API de reconhecimento de fala pode ser usada em qualquer dispositivo iOS com iOS 10 e na maioria dos casos, requer uma conexão de internet ao vivo, desde que a maior parte das traduções ocorre em servidores da Apple. Dito isso, alguns iOS mais recente dispositivos suportam AlwaysOn no, no dispositivo tradução de idiomas específicos.

Apple incluiu uma API de disponibilidade para determinar se um determinado idioma está disponível para a tradução no momento atual. O aplicativo deve usar essa API em vez de testar a conectividade da internet em si diretamente.

Como observado anteriormente na seção de teclado ditado, reconhecimento de fala exige a transmissão e o armazenamento temporário de dados em servidores da Apple pela internet e como tal, o aplicativo _deve_ solicitar a permissão do usuário para executar reconhecimento, incluindo o `NSSpeechRecognitionUsageDescription` chave em seu `Info.plist` de arquivo e chamar o `SFSpeechRecognizer.RequestAuthorization` método. 

De acordo com a fonte de áudio que está sendo usado para o reconhecimento de fala, outras alterações para o aplicativo `Info.plist` arquivo pode ser necessário. Confira nosso [segurança e privacidade aprimoramentos](~/ios/app-fundamentals/security-privacy.md) documentação para obter detalhes.

## <a name="adopting-speech-recognition-in-an-app"></a>Adotando o reconhecimento de fala em um aplicativo

Há quatro etapas principais que o desenvolvedor deve executar para adotar o reconhecimento de fala em um aplicativo do iOS:

- Forneça uma descrição do uso do aplicativo em `Info.plist` arquivo usando o `NSSpeechRecognitionUsageDescription` chave. Por exemplo, um aplicativo de câmera pode incluir a seguinte descrição, _"Isso permite tirar uma foto bastando informando que a palavra 'cheese'."_
- Solicitar autorização chamando o `SFSpeechRecognizer.RequestAuthorization` método para apresentar uma explicação (fornecidas a `NSSpeechRecognitionUsageDescription` chave acima) de por que o aplicativo deseja fala reconhecimento de acesso para o usuário na caixa de diálogo e permiti-las aceitar ou recusar.
- Crie uma solicitação de reconhecimento de fala:
    * Para áudio gravado anteriormente no disco, use o `SFSpeechURLRecognitionRequest` classe.
    * Áudio ao vivo (ou áudio de memória), use o `SFSPeechAudioBufferRecognitionRequest` classe.
- Passar a solicitação de reconhecimento de fala para o reconhecedor de fala (`SFSpeechRecognizer`) para iniciar o reconhecimento. O aplicativo pode, opcionalmente, mantenha retornado `SFSpeechRecognitionTask` para monitorar e acompanhar os resultados de reconhecimento.

Essas etapas serão abordadas em detalhes abaixo.

### <a name="providing-a-usage-description"></a>Fornece uma descrição do uso.

Para fornecer necessários `NSSpeechRecognitionUsageDescription` chave no `Info.plist` de arquivo, faça o seguinte:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Clique duas vezes o `Info.plist` arquivo para abri-lo para edição.
2. Alterne para o **fonte** exibição: 

    [![](speech-images/speech02.png "A exibição da fonte")](speech-images/speech02.png#lightbox)
3. Clique em **adicionar nova entrada**, digite `NSSpeechRecognitionUsageDescription` para o **propriedade**, `String` para o **tipo** e um **descrição do uso** como o **valor**. Por exemplo: 

    [![](speech-images/speech03.png "Adicionando NSSpeechRecognitionUsageDescription")](speech-images/speech03.png#lightbox)
4. Se o aplicativo será manipula transcrição de áudio em tempo real, também será necessária uma descrição de uso do microfone. Clique em **adicionar nova entrada**, digite `NSMicrophoneUsageDescription` para o **propriedade**, `String` para o **tipo** e um **descrição do uso** como o **valor**. Por exemplo: 

    [![](speech-images/speech04.png "Adicionando NSMicrophoneUsageDescription")](speech-images/speech04.png#lightbox)
4. Salve as alterações no arquivo.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Clique duas vezes o `Info.plist` arquivo para abri-lo para edição.
3. Clique em **adicionar nova entrada**, digite `NSSpeechRecognitionUsageDescription` para o **propriedade**, `String` para o **tipo** e um **descrição do uso** como o **valor**. Por exemplo: 

    [![](speech-images/speech03w.png "Adicionando NSSpeechRecognitionUsageDescription")](speech-images/speech03w.png#lightbox)
4. Se o aplicativo será manipula transcrição de áudio em tempo real, também será necessária uma descrição de uso do microfone. Clique em **adicionar nova entrada**, digite `NSMicrophoneUsageDescription` para o **propriedade**, `String` para o **tipo** e um **descrição do uso** como o **valor**. Por exemplo: 

    [![](speech-images/speech04w.png "Adicionando NSMicrophoneUsageDescription")](speech-images/speech04w.png#lightbox)
4. Salve as alterações no arquivo.

-----

> [!IMPORTANT]
> Falha ao fornecer qualquer um dos itens acima `Info.plist` chaves (`NSSpeechRecognitionUsageDescription` ou `NSMicrophoneUsageDescription`) pode resultar no aplicativo com falha sem aviso ao tentar acessar o reconhecimento de fala ou microfone de áudio em tempo real.




### <a name="requesting-authorization"></a>Solicitação de autorização

Para solicitar a autorização de usuário necessário que permite que o aplicativo acesse o reconhecimento de fala, edite a classe principal do controlador de exibição e adicione o seguinte código:

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

O `RequestAuthorization` método o `SFSpeechRecognizer` classe solicitar permissão do usuário para o reconhecimento de fala de acesso com o motivo pelo qual o desenvolvedor fornecido no `NSSpeechRecognitionUsageDescription` chave do `Info.plist` arquivo.

Um `SFSpeechRecognizerAuthorizationStatus` resultado é retornado para o `RequestAuthorization` rotina de retorno de chamada do método que pode ser usada para executar uma ação com base na permissão do usuário. 

> [!IMPORTANT]
> Apple sugere esperar até que o usuário iniciou uma ação no aplicativo que exige o reconhecimento de fala antes de solicitar essa permissão.

### <a name="recognizing-pre-recorded-speech"></a>Reconhecimento de fala previamente registrada

Se o aplicativo deseja reconhecer fala de um arquivo WAV ou MP3 previamente registrado, poderá usar o código a seguir:

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

Examinar este código detalhadamente, primeiro, ele tenta criar um reconhecedor de fala (`SFSpeechRecognizer`). Se o idioma padrão não tem suporte para o reconhecimento de fala, `null` será retornado e fecha a funções.

Se o reconhecedor de fala está disponível para o idioma padrão, o aplicativo verifica para ver se ele está disponível atualmente para usar o reconhecimento de `Available` propriedade. Por exemplo, reconhecimento pode não ser disponível se o dispositivo não tiver uma conexão de internet ativa.

Um `SFSpeechUrlRecognitionRequest` é criada a partir de `NSUrl` local do arquivo gravado no dispositivo iOS e ele é entregue para o reconhecedor de fala para processar com uma rotina de retorno de chamada.

Quando o retorno de chamada é chamado, se o `NSError` não `null` houve um erro que deve ser tratado. Como reconhecimento de fala é feito de forma incremental, a rotina de retorno de chamada pode ser chamada mais de uma vez para que o `SFSpeechRecognitionResult.Final` propriedade é testada para ver se a conversão for concluída e a versão recomendada da tradução é gravado (`BestTranscription`).

### <a name="recognizing-live-speech"></a>Reconhecimento de fala ao vivo

Se o aplicativo deseja reconhecer fala ao vivo, o processo é muito semelhante ao reconhecimento de fala previamente registrada. Por exemplo:

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

Examinar este código detalhadamente, ele cria várias variáveis privadas para lidar com o processo de reconhecimento:

```csharp
private AVAudioEngine AudioEngine = new AVAudioEngine ();
private SFSpeechRecognizer SpeechRecognizer = new SFSpeechRecognizer ();
private SFSpeechAudioBufferRecognitionRequest LiveSpeechRequest = new SFSpeechAudioBufferRecognitionRequest ();
private SFSpeechRecognitionTask RecognitionTask;
```

Ele usa o AV Foundation gravar áudio que será passado para um `SFSpeechAudioBufferRecognitionRequest` para manipular a solicitação de reconhecimento:

```csharp
var node = AudioEngine.InputNode;
var recordingFormat = node.GetBusOutputFormat (0);
node.InstallTapOnBus (0, 1024, recordingFormat, (AVAudioPcmBuffer buffer, AVAudioTime when) => {
    // Append buffer to recognition request
    LiveSpeechRequest.Append (buffer);
});
```

O aplicativo tenta iniciar a gravação e os erros são tratados se a gravação não pode ser iniciada:

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

A tarefa de reconhecimento é iniciada e um identificador é mantido para a tarefa de reconhecimento (`SFSpeechRecognitionTask`):

```csharp
RecognitionTask = SpeechRecognizer.GetRecognitionTask (LiveSpeechRequest, (SFSpeechRecognitionResult result, NSError err) => {
    ...
});
```

O retorno de chamada é usado de maneira semelhante à usada acima na fala previamente registrada.

Se o registro é interrompido pelo usuário, o mecanismo de áudio e a solicitação de reconhecimento de fala, eles serão informados:

```csharp
AudioEngine.Stop ();
LiveSpeechRequest.EndAudio ();
```

Se o usuário cancelar o reconhecimento, o mecanismo de áudio e tarefas de reconhecimento serão informados:

```csharp
AudioEngine.Stop ();
RecognitionTask.Cancel ();
```

É importante chamar `RecognitionTask.Cancel` se o usuário cancelar a conversão para liberar a memória e processador do dispositivo.

> [!IMPORTANT]
> Falha ao fornecer o `NSSpeechRecognitionUsageDescription` ou `NSMicrophoneUsageDescription` `Info.plist` chaves podem resultar no aplicativo com falha sem aviso ao tentar acessar o reconhecimento de fala ou microfone de áudio em tempo real (`var node = AudioEngine.InputNode;`). Consulte o **fornecendo uma descrição de uso** seção acima para obter mais informações.

## <a name="speech-recognition-limits"></a>Limites de reconhecimento de fala

Apple impõe as seguintes limitações ao trabalhar com reconhecimento de fala em um aplicativo do iOS:

- Reconhecimento de fala está livre para todos os aplicativos, mas seu uso não é ilimitado:
    - Dispositivos iOS individuais têm um número limitado de reconhecimentos que podem ser executadas por dia.
    - Aplicativos serão limitados globalmente em uma base de solicitação por dia.
- O aplicativo deve estar preparado para lidar com falhas de limite de taxa de uso e conexão de rede de reconhecimento de fala.
- O reconhecimento de fala pode ter um alto custo na bateria e alto tráfego de rede no dispositivo do iOS do usuário, por isso, Apple impõe um limite de duração de áudio estrito de aproximadamente um minuto de fala max.

Se um aplicativo normalmente está atingindo os limites de taxa de limitação, Apple solicita que o desenvolvedor do contato.

## <a name="privacy-and-usability-considerations"></a>Considerações sobre a usabilidade e privacidade

Apple tem a seguinte sugestão para ser transparente e respeitando a privacidade do usuário ao incluir o reconhecimento de fala em um aplicativo iOS:

- Durante a gravação de voz do usuário, certifique-se de que indicam claramente que gravação está ocorrendo na Interface do usuário do aplicativo. Por exemplo, o aplicativo pode reproduzir uma "gravação" som e exibir um indicador de gravação.
- Não use o reconhecimento de fala para informações confidenciais do usuário, como senhas, dados de integridade ou informações financeiras.
- Mostrar os resultados do reconhecimento _antes de_ atuando neles. Isso não só fornece comentários sobre o que está fazendo o aplicativo, mas permite que o usuário tratar erros de reconhecimento de como eles são feitos.

## <a name="summary"></a>Resumo

Este artigo foi apresentado a nova API de fala e mostrou como implementá-la em um aplicativo xamarin dá suporte ao reconhecimento de fala contínua e transcrever da fala (fluxos de áudio ao vivo ou gravados) em texto. 



## <a name="related-links"></a>Links relacionados

- [SpeakToMe (exemplo)](https://developer.xamarin.com/samples/monotouch/ios10/SpeakToMe/)
