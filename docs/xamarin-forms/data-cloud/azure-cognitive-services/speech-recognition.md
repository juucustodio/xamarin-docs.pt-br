---
title: Reconhecimento de fala usando a API do serviço de fala
description: Este artigo explica como usar a API do serviço de fala do Azure para transcrever a fala em texto em um aplicativo Xamarin. Forms.
ms.prod: xamarin
ms.assetid: B435FF6B-8785-48D9-B2D9-1893F5A87EA1
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 01/14/2020
ms.openlocfilehash: c10b8feea5fbec21fc127262c3f1bfda50beba7f
ms.sourcegitcommit: ba83c107c87b015dbcc9db13964fe111a0573dca
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76265134"
---
# <a name="speech-recognition-using-azure-speech-service"></a>Reconhecimento de fala usando o serviço de fala do Azure

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-cognitivespeechservice)

O serviço de fala do Azure é uma API baseada em nuvem que oferece a seguinte funcionalidade:

- A **conversão de fala em texto** transcreve arquivos de áudio ou fluxos para texto.
- Conversão de **texto em fala** converte o texto de entrada em fala sintetizada por meio do tipo humano.
- A **conversão de fala** permite tradução de vários idiomas em tempo real para conversão de fala em texto e de fala em fala.
- Os **assistentes de voz** podem criar interfaces de conversa do tipo humano para aplicativos.

Este artigo explica como a conversão de fala em texto é implementada no aplicativo Xamarin. Forms de exemplo usando o serviço de fala do Azure. As capturas de tela a seguir mostram o aplicativo de exemplo no iOS e no Android:

[![capturas de tela do aplicativo de exemplo no iOS e no Android](speech-recognition-images/speech-recognition-cropped.png)](speech-recognition-images/speech-recognition.png#lightbox "Capturas de tela do aplicativo de exemplo no iOS e no Android")

## <a name="create-an-azure-speech-service-resource"></a>Criar um recurso do serviço de fala do Azure

O serviço de fala do Azure faz parte dos serviços cognitivas do Azure, que fornece APIs baseadas em nuvem para tarefas como reconhecimento de imagem, reconhecimento de fala e Tradução e pesquisa do Bing. Para obter mais informações, consulte [o que são os serviços cognitivas do Azure?](https://docs.microsoft.com/azure/cognitive-services/welcome).

O projeto de exemplo requer que um recurso de serviços cognitivas do Azure seja criado no seu portal do Azure. Um recurso de serviços cognitivas pode ser criado para um único serviço, como o serviço de fala ou como um recurso de vários serviços. As etapas para criar um recurso de serviço de fala são as seguintes:

1. Faça logon em seu [portal do Azure](https://portal.azure.com).
1. Crie um recurso de vários serviços ou de serviço único.
1. Obtenha a chave de API e as informações de região para seu recurso.
1. Atualize o arquivo **Constants.cs** de exemplo.

Para obter um guia passo a passo para criar um recurso, consulte [criar um recurso de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).

> [!NOTE]
> Se você não tiver uma [assinatura do Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), crie uma [conta gratuita](https://aka.ms/azfree-docs-mobileapps) antes de começar. Quando você tiver uma conta, um recurso de serviço único poderá ser criado na camada gratuita para experimentar o serviço.

## <a name="configure-your-app-with-the-speech-service"></a>Configurar seu aplicativo com o serviço de fala

Depois de criar um recurso de serviços cognitivas, o arquivo **Constants.cs** pode ser atualizado com a região e a chave de API do recurso do Azure:

```csharp
public static class Constants
{
    public static string CognitiveServicesApiKey = "YOUR_KEY_GOES_HERE";
    public static string CognitiveServicesRegion = "westus";
}
```

## <a name="install-nuget-speech-service-package"></a>Instalar o pacote de serviço de fala do NuGet

O aplicativo de exemplo usa o pacote NuGet **Microsoft. cognitivaservices. fala** para se conectar ao serviço de fala do Azure. Instale este pacote NuGet no projeto compartilhado e em cada projeto de plataforma.

## <a name="create-an-imicrophoneservice-interface"></a>Criar uma interface IMicrophoneService

Cada plataforma requer permissão para acessar o microfone. O projeto de exemplo fornece uma interface `IMicrophoneService` no projeto compartilhado e usa o `DependencyService` Xamarin. Forms para obter implementações de plataforma da interface.

```csharp
public interface IMicrophoneService
{
    Task<bool> GetPermissionAsync();
    void OnRequestPermissionResult(bool isGranted);
}
```

## <a name="create-the-page-layout"></a>Criar o layout da página

O projeto de exemplo define um layout de página básico no arquivo **MainPage. XAML** . Os elementos de layout de chave são um `Button` que inicia o processo de transcrição, um `Label` para conter o texto transcrita e um `ActivityIndicator` para mostrar quando a transcrição está em andamento:

```xaml
<ContentPage ...>
    <StackLayout>
        <Frame ...>
            <ScrollView x:Name="scroll"
                        ...>
                <Label x:Name="transcribedText"
                       ... />
            </ScrollView>
        </Frame>

        <ActivityIndicator x:Name="transcribingIndicator"
                           IsRunning="False" />
        <Button x:Name="transcribeButton"
                ...
                Clicked="TranscribeClicked"/>
    </StackLayout>
</ContentPage>
```

## <a name="implement-the-speech-service"></a>Implementar o serviço de fala

O arquivo code-behind **MainPage.XAML.cs** contém toda a lógica para enviar áudio e receber texto transcrita do serviço de fala do Azure.

O Construtor `MainPage` Obtém uma instância da interface `IMicrophoneService` do `DependencyService`:

```csharp
public partial class MainPage : ContentPage
{
    SpeechRecognizer recognizer;
    IMicrophoneService micService;
    bool isTranscribing = false;

    public MainPage()
    {
        InitializeComponent();

        micService = DependencyService.Resolve<IMicrophoneService>();
    }

    // ...
}
```

O método `TranscribeClicked` é chamado quando a instância de `transcribeButton` é tocada:

```csharp
async void TranscribeClicked(object sender, EventArgs e)
{
    bool isMicEnabled = await micService.GetPermissionAsync();

    // EARLY OUT: make sure mic is accessible
    if (!isMicEnabled)
    {
        UpdateTranscription("Please grant access to the microphone!");
        return;
    }

    // initialize speech recognizer 
    if (recognizer == null)
    {
        var config = SpeechConfig.FromSubscription(Constants.CognitiveServicesApiKey, Constants.CognitiveServicesRegion);
        recognizer = new SpeechRecognizer(config);
        recognizer.Recognized += (obj, args) =>
        {
            UpdateTranscription(args.Result.Text);
        };
    }

    // if already transcribing, stop speech recognizer
    if (isTranscribing)
    {
        try
        {
            await recognizer.StopContinuousRecognitionAsync();
        }
        catch(Exception ex)
        {
            UpdateTranscription(ex.Message);
        }
        isTranscribing = false;
    }

    // if not transcribing, start speech recognizer
    else
    {
        Device.BeginInvokeOnMainThread(() =>
        {
            InsertDateTimeRecord();
        });
        try
        {
            await recognizer.StartContinuousRecognitionAsync();
        }
        catch(Exception ex)
        {
            UpdateTranscription(ex.Message);
        }
        isTranscribing = true;
    }
    UpdateDisplayState();
}
```

O método `TranscribeClicked` faz o seguinte:

1. Verifica se o aplicativo tem acesso ao microfone e sai antecipadamente se não tiver.
1. Cria uma instância de `SpeechRecognizer` classe, caso ela ainda não exista.
1. Interrompe a transcrição contínua se ela estiver em andamento.
1. Insere um carimbo de data/hora e inicia a transcrição contínua se ela não estiver em andamento.
1. Notifica o aplicativo para atualizar sua aparência com base no novo estado do aplicativo.

O restante dos métodos de classe de `MainPage` são auxiliares para exibir o estado do aplicativo:

```csharp
void UpdateTranscription(string newText)
{
    Device.BeginInvokeOnMainThread(() =>
    {
        if (!string.IsNullOrWhiteSpace(newText))
        {
            transcribedText.Text += $"{newText}\n";
        }
    });
}

void InsertDateTimeRecord()
{
    var msg = $"=================\n{DateTime.Now.ToString()}\n=================";
    UpdateTranscription(msg);
}

void UpdateDisplayState()
{
    Device.BeginInvokeOnMainThread(() =>
    {
        if (isTranscribing)
        {
            transcribeButton.Text = "Stop";
            transcribeButton.BackgroundColor = Color.Red;
            transcribingIndicator.IsRunning = true;
        }
        else
        {
            transcribeButton.Text = "Transcribe";
            transcribeButton.BackgroundColor = Color.Green;
            transcribingIndicator.IsRunning = false;
        }
    });
}
```

O método `UpdateTranscription` grava o `newText` fornecido `string` para o elemento `Label` chamado `transcribedText`. Isso força essa atualização a acontecer no thread da interface do usuário para que possa ser chamada de qualquer contexto sem causar exceções. O `InsertDateTimeRecord` grava a data e a hora atuais na instância de `transcribedText` para marcar o início de uma nova transcrição. Por fim, o método `UpdateDisplayState` atualiza os elementos `Button` e `ActivityIndicator` para refletir se a transcrição está ou não em andamento.

## <a name="create-platform-microphone-services"></a>Criar serviços de microfone de plataforma

O aplicativo deve ter acesso ao microfone para coletar dados de fala. A interface de `IMicrophoneService` deve ser implementada e registrada com o `DependencyService` em cada plataforma para que o aplicativo funcione.

### <a name="android"></a>Android

O projeto de exemplo define uma implementação de `IMicrophoneService` para o Android chamada `AndroidMicrophoneService`:

```csharp
[assembly: Dependency(typeof(AndroidMicrophoneService))]
namespace CognitiveSpeechService.Droid.Services
{
    public class AndroidMicrophoneService : IMicrophoneService
    {
        public const int RecordAudioPermissionCode = 1;
        private TaskCompletionSource<bool> tcsPermissions;
        string[] permissions = new string[] { Manifest.Permission.RecordAudio };

        public Task<bool> GetPermissionAsync()
        {
            tcsPermissions = new TaskCompletionSource<bool>();

            if ((int)Build.VERSION.SdkInt < 23)
            {
                tcsPermissions.TrySetResult(true);
            }
            else
            {
                var currentActivity = MainActivity.Instance;
                if (ActivityCompat.CheckSelfPermission(currentActivity, Manifest.Permission.RecordAudio) != (int)Permission.Granted)
                {
                    RequestMicPermissions();
                }
                else
                {
                    tcsPermissions.TrySetResult(true);
                }

            }

            return tcsPermissions.Task;
        }

        public void OnRequestPermissionResult(bool isGranted)
        {
            tcsPermissions.TrySetResult(isGranted);
        }

        void RequestMicPermissions()
        {
            if (ActivityCompat.ShouldShowRequestPermissionRationale(MainActivity.Instance, Manifest.Permission.RecordAudio))
            {
                Snackbar.Make(MainActivity.Instance.FindViewById(Android.Resource.Id.Content),
                        "Microphone permissions are required for speech transcription!",
                        Snackbar.LengthIndefinite)
                        .SetAction("Ok", v =>
                        {
                            ((Activity)MainActivity.Instance).RequestPermissions(permissions, RecordAudioPermissionCode);
                        })
                        .Show();
            }
            else
            {
                ActivityCompat.RequestPermissions((Activity)MainActivity.Instance, permissions, RecordAudioPermissionCode);
            }
        }
    }
}
```

O `AndroidMicrophoneService` tem os seguintes recursos:

1. O atributo `Dependency` registra a classe com o `DependencyService`.
1. O método `GetPermissionAsync` verifica se as permissões são necessárias com base na versão do SDK do Android e chama `RequestMicPermissions` se a permissão ainda não foi concedida.
1. O método `RequestMicPermissions` usa a classe `Snackbar` para solicitar permissões do usuário se uma lógica for necessária; caso contrário, ele solicitará permissões de gravação de áudio diretamente.
1. O método `OnRequestPermissionResult` é chamado com um resultado de `bool` depois que o usuário responde à solicitação de permissões.

A classe `MainActivity` é personalizada para atualizar a instância de `AndroidMicrophoneService` quando as solicitações de permissões são concluídas:

```csharp
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    IMicrophoneService micService;
    internal static MainActivity Instance { get; private set; }
    
    protected override void OnCreate(Bundle savedInstanceState)
    {
        Instance = this;
        // ...
        micService = DependencyService.Resolve<IMicrophoneService>();
    }
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, [GeneratedEnum] Android.Content.PM.Permission[] grantResults)
    {
        // ...
        switch(requestCode)
        {
            case AndroidMicrophoneService.RecordAudioPermissionCode:
                if (grantResults[0] == Permission.Granted)
                {
                    micService.OnRequestPermissionResult(true);
                }
                else
                {
                    micService.OnRequestPermissionResult(false);
                }
                break;
        }
    }
}
```

A classe `MainActivity` define uma referência estática chamada `Instance`, que é exigida pelo objeto `AndroidMicrophoneService` ao solicitar permissões. Ele substitui o método `OnRequestPermissionsResult` para atualizar o objeto `AndroidMicrophoneService` quando a solicitação de permissões é aprovada ou negada pelo usuário.

Por fim, o aplicativo Android deve incluir a permissão para gravar áudio no arquivo **AndroidManifest. xml** :

```xml
<manifest ...>
    ...
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
</manifest>
```

### <a name="ios"></a>iOS

O projeto de exemplo define uma implementação de `IMicrophoneService` para iOS chamada `iOSMicrophoneService`:

```csharp
[assembly: Dependency(typeof(iOSMicrophoneService))]
namespace CognitiveSpeechService.iOS.Services
{
    public class iOSMicrophoneService : IMicrophoneService
    {
        TaskCompletionSource<bool> tcsPermissions;

        public Task<bool> GetPermissionAsync()
        {
            tcsPermissions = new TaskCompletionSource<bool>();
            RequestMicPermission();
            return tcsPermissions.Task;
        }

        public void OnRequestPermissionResult(bool isGranted)
        {
            tcsPermissions.TrySetResult(isGranted);
        }

        void RequestMicPermission()
        {
            var session = AVAudioSession.SharedInstance();
            session.RequestRecordPermission((granted) =>
            {
                tcsPermissions.TrySetResult(granted);
            });
        }
    }
}
```

O `iOSMicrophoneService` tem os seguintes recursos:

1. O atributo `Dependency` registra a classe com o `DependencyService`.
1. O método `GetPermissionAsync` chama `RequestMicPermissions` para solicitar permissões do usuário do dispositivo.
1. O método `RequestMicPermissions` usa a instância `AVAudioSession` compartilhada para solicitar permissões de gravação.
1. O método `OnRequestPermissionResult` atualiza a instância de `TaskCompletionSource` com o valor de `bool` fornecido.

Finalmente, o app **info. plist** do IOS deve incluir uma mensagem que informa ao usuário por que o aplicativo está solicitando acesso ao microfone. Edite o arquivo info. plist para incluir as seguintes marcas no elemento `<dict>`:

```xml
<plist>
    <dict>
        ...
        <key>NSMicrophoneUsageDescription</key>
        <string>Voice transcription requires microphone access</string>
    </dict>
</plist>
```

### <a name="uwp"></a>UWP

O projeto de exemplo define uma implementação de `IMicrophoneService` para UWP chamada `UWPMicrophoneService`:

```csharp
[assembly: Dependency(typeof(UWPMicrophoneService))]
namespace CognitiveSpeechService.UWP.Services
{
    public class UWPMicrophoneService : IMicrophoneService
    {
        public async Task<bool> GetPermissionAsync()
        {
            bool isMicAvailable = true;
            try
            {
                var mediaCapture = new MediaCapture();
                var settings = new MediaCaptureInitializationSettings();
                settings.StreamingCaptureMode = StreamingCaptureMode.Audio;
                await mediaCapture.InitializeAsync(settings);
            }
            catch(Exception ex)
            {
                isMicAvailable = false;
            }

            if(!isMicAvailable)
            {
                await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-settings:privacy-microphone"));
            }

            return isMicAvailable;
        }

        public void OnRequestPermissionResult(bool isGranted)
        {
            // intentionally does nothing
        }
    }
}
```

O `UWPMicrophoneService` tem os seguintes recursos:

1. O atributo `Dependency` registra a classe com o `DependencyService`.
1. O método `GetPermissionAsync` tenta inicializar uma instância de `MediaCapture`. Se isso falhar, ele iniciará uma solicitação de usuário para habilitar o microfone.
1. O método `OnRequestPermissionResult` existe para satisfazer a interface, mas não é necessário para a implementação de UWP.

Finalmente, o **pacote UWP. appxmanifest** deve especificar que o aplicativo usa o microfone. Clique duas vezes no arquivo Package. appxmanifest e selecione a opção **microfone** na guia **recursos** no Visual Studio 2019:

[![captura de tela do manifesto no Visual Studio 2019](speech-recognition-images/package-manifest-cropped.png)](speech-recognition-images/package-manifest.png#lightbox "Captura de tela do manifesto no Visual Studio 2019")

## <a name="test-the-application"></a>Testar o aplicativo

Execute o aplicativo e clique no botão **transcrever** . O aplicativo deve solicitar acesso ao microfone e iniciar o processo de transcrição. O `ActivityIndicator` será animado, mostrando que a transcrição está ativa. À medida que você fala, o aplicativo transmitirá dados de áudio para o recurso de serviços de fala do Azure, que responderá com texto transcrita. O texto transcrita aparecerá no elemento `Label` conforme recebido.

> [!NOTE]
> Os emuladores do Android falham ao carregar e inicializar as bibliotecas de serviço de fala. O teste em um dispositivo físico é recomendado para a plataforma Android.

## <a name="related-links"></a>Links relacionados

- [Exemplo de serviço de fala do Azure](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-cognitivespeechservice)
- [Visão geral do serviço de fala do Azure](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)
- [Criar um recurso de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)
- [Início rápido: reconhecer a fala de um microfone](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/speech-to-text-from-microphone)