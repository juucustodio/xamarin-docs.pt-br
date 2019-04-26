---
title: Reconhecimento de fala no xamarin. IOS
description: Este artigo apresenta a nova API de fala e mostra como implementá-lo em um aplicativo xamarin. IOS para dar suporte a reconhecimento de fala contínua e transcrição de fala (de fluxos de áudio ao vivo ou gravados) em texto.
ms.prod: xamarin
ms.assetid: 64FED50A-6A28-4833-BEAE-63CEC9A09010
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: c1f488213f9b3be945fd98e09f630c243d0b0d62
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61382784"
---
# <a name="speech-recognition-in-xamarinios"></a>Reconhecimento de fala no xamarin. IOS

_Este artigo apresenta a nova API de fala e mostra como implementá-lo em um aplicativo xamarin. IOS para dar suporte a reconhecimento de fala contínua e transcrição de fala (de fluxos de áudio ao vivo ou gravados) em texto._

Novo para o iOS 10, a Apple tem liberar a API de reconhecimento de fala que permite que um aplicativo iOS para dar suporte a reconhecimento de fala contínua e transcrição de fala (de fluxos de áudio ao vivo ou gravados) em texto.

De acordo com a Apple, a API de reconhecimento de fala tem os seguintes benefícios:

- Altamente precisos
- De última geração
- Fácil de usar
- Rápido
- Dá suporte a vários idiomas
- Privacidade do usuário respeita

## <a name="how-speech-recognition-works"></a>Como funciona o reconhecimento de fala

Reconhecimento de fala é implementado em um aplicativo iOS pela aquisição de áudio ao vivo ou pré-gravado (em qualquer um dos idiomas falados que a API dá suporte) e passá-la para um reconhecedor de fala que retorna uma transcrição de texto sem formatação das palavras faladas.

[![](speech-images/speech01.png "Como funciona o reconhecimento de fala")](speech-images/speech01.png#lightbox)

### <a name="keyboard-dictation"></a>Ditado de teclado

Quando a maioria dos usuários pensam de reconhecimento de fala em um dispositivo iOS, eles pensam o Assistente de voz Siri interno, que foi lançado junto com o ditado de teclado no iOS 5 com o iPhone 4S.

Ditado de teclado é compatível com qualquer elemento de interface que dá suporte a TextKit (como `UITextField` ou `UITextArea`) e é ativado quando o usuário clica no botão de ditado (diretamente à esquerda da barra de espaço) no teclado virtual iOS.

Apple lançou as seguintes estatísticas de ditado de teclado (coletadas desde 2011):

- Ditado de teclado tem sido amplamente usado, pois ele foi lançado no iOS 5.
- Aproximadamente 65.000 aplicativos usá-lo por dia.
- Sobre um terço do iOS de todas as ditado é feito em um aplicativo de terceiros 3ª.

Ditado de teclado é extremamente fácil de usar, pois ele não requer nenhum esforço de parte do desenvolvedor, além de usar um elemento de interface TextKit no design de interface do usuário do aplicativo. Ditado de teclado também tem a vantagem de não exigir a quaisquer solicitações de privilégio especial do aplicativo antes que ele pode ser usado.

Aplicativos que usam as novas APIs de reconhecimento de fala exigirá permissões especiais sejam concedidas pelo usuário, como reconhecimento de fala requer a transmissão e o armazenamento temporário de dados em servidores da Apple. Consulte nosso [aperfeiçoamentos de segurança e privacidade](~/ios/app-fundamentals/security-privacy.md) documentação para obter detalhes.

Enquanto o ditado de teclado é fácil de implementar, ela apresenta várias desvantagens e limitações:

- Ele requer o uso de um campo de entrada de texto e a exibição de um teclado.
- Ele funciona com apenas de entrada de áudio ao vivo e o aplicativo não tem controle sobre o processo de gravação de áudio.
- Ele não fornece controle sobre a linguagem que é usada para interpretar a voz do usuário.
- Não há nenhuma maneira do aplicativo saber se o botão de ditado estará disponível até mesmo para o usuário.
- O aplicativo não é possível personalizar o processo de gravação de áudio.
- Ele fornece um conjunto muito superficial de resultados que não tem informações como tempo e confiança.

### <a name="speech-recognition-api"></a>API de reconhecimento de fala

Novo para o iOS 10, a Apple lançou a API de reconhecimento de fala que fornece uma maneira mais eficiente para um aplicativo iOS implementar o reconhecimento de fala. Essa API é o mesmo que usa o Apple para alimentação Siri e ditado de teclado e é capaz de fornecer a transcrição rápida com precisão de última geração.

Os resultados fornecidos pela API de reconhecimento de fala transparentemente são personalizados para usuários individuais, sem o aplicativo precisar coletar ou acessar quaisquer dados particulares do usuário.

A API de reconhecimento de fala fornece resultados de volta para o aplicativo de chamada em quase em tempo real conforme o usuário está falando e fornece mais informações sobre os resultados da conversão do que apenas texto. Elas incluem:

- Várias interpretações do que o usuário disseram.
- Níveis de confiança para as conversões individuais.
- Informações de tempo.

Como mencionado acima, áudio para tradução pode ser fornecido tanto por um feed em tempo real, ou da origem pré-gravados e em qualquer um dos mais de 50 idiomas e dialetos com suporte no iOS 10.

A API de reconhecimento de fala pode ser usada em qualquer dispositivo iOS que executam o iOS 10 e na maioria dos casos, requer uma conexão de internet em tempo real, pois a maior parte das traduções ocorre em servidores da Apple. Dito isso, alguns dispositivos suportam AlwaysOn mais recentes do iOS, no dispositivo tradução de idiomas específicos.

Apple incluiu uma API de disponibilidade para determinar se um determinado idioma está disponível para a tradução no momento atual. O aplicativo deve usar essa API em vez de testar a conectividade com a internet em si diretamente.

Conforme observado acima na seção de ditado de teclado, reconhecimento de fala requer a transmissão e o armazenamento temporário de dados em servidores da Apple na Internet e como tal, o aplicativo _deve_ solicitar permissão do usuário para executar reconhecimento, incluindo o `NSSpeechRecognitionUsageDescription` chave em seu `Info.plist` arquivo e chamar o `SFSpeechRecognizer.RequestAuthorization` método. 

Com base na origem do áudio que está sendo usado para o reconhecimento de fala, outras alterações para o aplicativo `Info.plist` arquivo pode ser necessário. Consulte nosso [aperfeiçoamentos de segurança e privacidade](~/ios/app-fundamentals/security-privacy.md) documentação para obter detalhes.

## <a name="adopting-speech-recognition-in-an-app"></a>Adotando o reconhecimento de fala em um aplicativo

Há quatro etapas principais que o desenvolvedor deve executar para adotar o reconhecimento de fala em um aplicativo iOS:

- Forneça uma descrição de uso do aplicativo `Info.plist` de arquivos usando o `NSSpeechRecognitionUsageDescription` chave. Por exemplo, um aplicativo de câmera pode incluir a seguinte descrição, _"Permite que você tire uma foto apenas dizendo a palavra 'queijo'"._
- Solicitar autorização chamando o `SFSpeechRecognizer.RequestAuthorization` método para apresentar uma explicação (fornecido no `NSSpeechRecognitionUsageDescription` acima de chave) de por que o aplicativo deseja fala o reconhecimento de acesso para o usuário em uma caixa de diálogo e permitir que eles aceitar ou recusar.
- Crie uma solicitação de reconhecimento de fala:
    * Para áudio previamente gravado no disco, use o `SFSpeechURLRecognitionRequest` classe.
    * Para áudio ao vivo (ou áudio da memória), use o `SFSPeechAudioBufferRecognitionRequest` classe.
- Passar a solicitação de reconhecimento de fala para um reconhecedor de fala (`SFSpeechRecognizer`) para iniciar o reconhecimento. O aplicativo pode, opcionalmente, mantenha retornado `SFSpeechRecognitionTask` para monitorar e acompanhar os resultados do reconhecimento.

Essas etapas serão abordadas em detalhes abaixo.

### <a name="providing-a-usage-description"></a>Fornecendo uma descrição de uso

Para fornecer o necessária `NSSpeechRecognitionUsageDescription` principais no `Info.plist` de arquivo, faça o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Clique duas vezes o `Info.plist` arquivo para abri-lo para edição.
2. Alterne para o **origem** exibição: 

    [![](speech-images/speech02.png "A exibição da fonte")](speech-images/speech02.png#lightbox)
3. Clique em **adicionar nova entrada**, insira `NSSpeechRecognitionUsageDescription` para o **propriedade**, `String` para o **tipo** e um **descrição de uso** como o **valor**. Por exemplo: 

    [![](speech-images/speech03.png "Adicionando NSSpeechRecognitionUsageDescription")](speech-images/speech03.png#lightbox)
4. Se o aplicativo estará manipulando a transcrição de áudio ao vivo, ela também exigirá uma descrição de uso do microfone. Clique em **adicionar nova entrada**, insira `NSMicrophoneUsageDescription` para o **propriedade**, `String` para o **tipo** e um **descrição de uso** como o **valor**. Por exemplo: 

    [![](speech-images/speech04.png "Adicionando NSMicrophoneUsageDescription")](speech-images/speech04.png#lightbox)
4. Salve as alterações no arquivo.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Clique duas vezes o `Info.plist` arquivo para abri-lo para edição.
3. Clique em **adicionar nova entrada**, insira `NSSpeechRecognitionUsageDescription` para o **propriedade**, `String` para o **tipo** e um **descrição de uso** como o **valor**. Por exemplo: 

    [![](speech-images/speech03w.png "Adicionando NSSpeechRecognitionUsageDescription")](speech-images/speech03w.png#lightbox)
4. Se o aplicativo estará manipulando a transcrição de áudio ao vivo, ela também exigirá uma descrição de uso do microfone. Clique em **adicionar nova entrada**, insira `NSMicrophoneUsageDescription` para o **propriedade**, `String` para o **tipo** e um **descrição de uso** como o **valor**. Por exemplo: 

    [![](speech-images/speech04w.png "Adicionando NSMicrophoneUsageDescription")](speech-images/speech04w.png#lightbox)
4. Salve as alterações no arquivo.

-----

> [!IMPORTANT]
> Deixar de fornecer qualquer uma das opções acima `Info.plist` chaves (`NSSpeechRecognitionUsageDescription` ou `NSMicrophoneUsageDescription`) pode resultar em falha sem aviso ao tentar acessar o reconhecimento de fala ou o microfone para áudio ao vivo no aplicativo.




### <a name="requesting-authorization"></a>Solicitando a autorização

Para solicitar a autorização necessária para o usuário que permite que o aplicativo acessar o reconhecimento de fala, edite a classe de controlador de exibição principal e adicione o seguinte código:

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

O `RequestAuthorization` método da `SFSpeechRecognizer` classe solicitar permissão do usuário para o reconhecimento de fala de acesso usando o motivo pelo qual o desenvolvedor fornecido na `NSSpeechRecognitionUsageDescription` chave do `Info.plist` arquivo.

Um `SFSpeechRecognizerAuthorizationStatus` resultado é retornado para o `RequestAuthorization` rotina de retorno de chamada do método que pode ser usada para agir com base em permissão do usuário. 

> [!IMPORTANT]
> A Apple sugere aguardando até que o usuário iniciou uma ação no aplicativo que exige o reconhecimento de fala antes de solicitar essa permissão.

### <a name="recognizing-pre-recorded-speech"></a>Reconhecimento de fala pré-gravados

Se o aplicativo deseja reconhecer fala de um arquivo WAV ou MP3 pré-gravados, ele poderá usar o código a seguir:

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

Examinar esse código detalhadamente, primeiro, ele tenta criar um reconhecedor de fala (`SFSpeechRecognizer`). Se o idioma padrão não tem suporte para o reconhecimento de fala `null` é retornado e sair do functions.

Se o reconhecedor de fala está disponível para o idioma padrão, o aplicativo verifica para ver se ele está disponível atualmente para o reconhecimento usando a `Available` propriedade. Por exemplo, o reconhecimento não pode ser disponível se o dispositivo não tiver uma conexão de internet ativa.

Um `SFSpeechUrlRecognitionRequest` é criado a partir de `NSUrl` local do arquivo pré-gravados no dispositivo iOS e ele é passado para o reconhecedor de fala para serem processados com uma rotina de retorno de chamada.

Quando o retorno de chamada é chamado, se o `NSError` não é `null` houve um erro que deve ser tratado. Como o reconhecimento de fala é feito de forma incremental, a rotina de retorno de chamada pode ser chamada mais de uma vez para que o `SFSpeechRecognitionResult.Final` propriedade é testada para ver se a conversão for concluída e melhor a versão da tradução é gravado (`BestTranscription`).

### <a name="recognizing-live-speech"></a>Reconhecimento de fala em tempo real

Se o aplicativo deseja reconhecer fala em tempo real, o processo é muito semelhante ao reconhecer fala pré-gravado. Por exemplo:

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

Examinar esse código detalhadamente, ele cria várias variáveis privadas para manipular o processo de reconhecimento:

```csharp
private AVAudioEngine AudioEngine = new AVAudioEngine ();
private SFSpeechRecognizer SpeechRecognizer = new SFSpeechRecognizer ();
private SFSpeechAudioBufferRecognitionRequest LiveSpeechRequest = new SFSpeechAudioBufferRecognitionRequest ();
private SFSpeechRecognitionTask RecognitionTask;
```

Ele usa AV Foundation para gravar o áudio será passado para um `SFSpeechAudioBufferRecognitionRequest` para manipular a solicitação de reconhecimento:

```csharp
var node = AudioEngine.InputNode;
var recordingFormat = node.GetBusOutputFormat (0);
node.InstallTapOnBus (0, 1024, recordingFormat, (AVAudioPcmBuffer buffer, AVAudioTime when) => {
    // Append buffer to recognition request
    LiveSpeechRequest.Append (buffer);
});
```

O aplicativo tenta iniciar a gravação e os erros são tratados, se a gravação não pode ser iniciada:

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

O retorno de chamada é usado de maneira semelhante à usada acima em fala pré-gravado.

Se a gravação é parada pelo usuário, o mecanismo de áudio e a solicitação de reconhecimento de fala, eles serão informados:

```csharp
AudioEngine.Stop ();
LiveSpeechRequest.EndAudio ();
```

Se o usuário cancelar o reconhecimento, o mecanismo de áudio e a tarefa de reconhecimento serão informados:

```csharp
AudioEngine.Stop ();
RecognitionTask.Cancel ();
```

É importante chamar `RecognitionTask.Cancel` se o usuário cancelar a tradução para liberar a memória e processador do dispositivo.

> [!IMPORTANT]
> Deixar de fornecer a `NSSpeechRecognitionUsageDescription` ou `NSMicrophoneUsageDescription` `Info.plist` chaves podem resultar em falha sem aviso ao tentar acessar o reconhecimento de fala ou o microfone para áudio ao vivo no aplicativo (`var node = AudioEngine.InputNode;`). Consulte a **fornecendo uma descrição de uso** seção acima para obter mais informações.

## <a name="speech-recognition-limits"></a>Limites de reconhecimento de fala

Apple impõe as seguintes limitações ao trabalhar com o reconhecimento de fala em um aplicativo iOS:

- Reconhecimento de fala é gratuito para todos os aplicativos, mas seu uso não é ilimitado:
    - Os dispositivos iOS individuais têm um número limitado de reconhecimentos que podem ser executadas por dia.
    - Aplicativos serão limitados globalmente em uma base de solicitação por dia.
- O aplicativo deve estar preparado para lidar com falhas de limite de taxa de uso e conexão de rede de reconhecimento de fala.
- Reconhecimento de fala pode ter um alto custo em bateria e alto tráfego de rede no dispositivo do iOS do usuário, por isso, Apple impõe um limite de duração de áudio estrita de aproximadamente um minuto de fala max.

Se um aplicativo rotineiramente está atingindo seus limites de limitação de taxa, Apple solicita que o desenvolvedor contatá-lo.

## <a name="privacy-and-usability-considerations"></a>Privacidade e considerações de usabilidade

Apple tem a seguinte sugestão para sendo transparente e respeitando a privacidade do usuário ao incluir o reconhecimento de fala em um aplicativo iOS:

- Durante a gravação de voz do usuário, certifique-se de que indicam claramente que gravação está ocorrendo na Interface do usuário do aplicativo. Por exemplo, o aplicativo pode reproduzir som uma "gravação" e exibir um indicador de gravação.
- Não use o reconhecimento de fala para informações confidenciais do usuário, como senhas, dados de integridade ou informações financeiras.
- Mostrar os resultados de reconhecimento _antes de_ agir sobre eles. Isso não só fornece comentários sobre o que o aplicativo está fazendo, mas permite que o usuário lidar com erros de reconhecimento de que elas são feitas.

## <a name="summary"></a>Resumo

Este artigo tem apresentado a nova API de fala e mostrou como implementá-lo em um aplicativo xamarin. IOS para dar suporte a reconhecimento de fala contínua e transcrição de fala (de fluxos de áudio ao vivo ou gravados) em texto. 



## <a name="related-links"></a>Links relacionados

- [SpeakToMe (amostra)](https://developer.xamarin.com/samples/monotouch/ios10/SpeakToMe/)
