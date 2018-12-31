---
title: Notificações remotas com o Firebase Cloud Messaging
description: Este passo a passo fornece uma explicação passo a passo de como usar o Firebase Cloud Messaging para implementar notificações remotas (também chamadas de notificações por push) em um aplicativo xamarin. Android. Ele ilustra como implementar as várias classes que são necessários para comunicações com o Firebase Cloud Messaging (FCM), fornece exemplos de como configurar o manifesto do Android para acesso ao FCM e demonstra o downstream de mensagens usando o Firebase Console.
ms.prod: xamarin
ms.assetid: 4D7C5F46-C997-49F6-AFDA-6763E68CDC90
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/31/2018
ms.openlocfilehash: 0c84f530f759285c2cfc71f60d7b6f80fba6a03d
ms.sourcegitcommit: f890b5ec9b7c2702875070859e1a8cbf6e870e46
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/28/2018
ms.locfileid: "53814031"
---
# <a name="remote-notifications-with-firebase-cloud-messaging"></a>Notificações remotas com o Firebase Cloud Messaging

_Este passo a passo fornece uma explicação passo a passo de como usar o Firebase Cloud Messaging para implementar notificações remotas (também chamadas de notificações por push) em um aplicativo xamarin. Android. Ele ilustra como implementar as várias classes que são necessários para comunicações com o Firebase Cloud Messaging (FCM), fornece exemplos de como configurar o manifesto do Android para acesso ao FCM e demonstra o downstream de mensagens usando o Firebase Console._

## <a name="fcm-notifications-overview"></a>Visão geral de notificações FCM

Neste passo a passo, um aplicativo básico chamado **FCMClient** será criado para ilustrar os conceitos básicos de mensagens do FCM. **FCMClient** verifica a presença do Google Play Services, recebe tokens do registro do FCM, exibe notificações remotas que enviam do Console Firebase e assina mensagens do tópico:

[![Captura de tela de exemplo de aplicativo](remote-notifications-with-fcm-images/00-app-example-sml.png)](remote-notifications-with-fcm-images/00-app-example.png#lightbox)

As seguintes áreas de tópico serão exploradas:

1.  Notificações da tela de fundo

2.  Mensagens do tópico

3.  Notificações de primeiro plano

Durante este passo a passo, você irá incrementalmente adicionar funcionalidade ao **FCMClient** e executá-lo em um dispositivo ou emulador para entender como ele interage com o FCM. Você usará o registro em log para testemunhar transações de aplicativo em tempo real com os servidores do FCM e você vai observar como as notificações são geradas de mensagens do FCM que você insere na GUI de notificações do Console do Firebase.

## <a name="requirements"></a>Requisitos


Isso poderá ser útil para se familiarizar com o [diferentes tipos de mensagens](https://firebase.google.com/docs/cloud-messaging/concept-options#notifications_and_data_messages) que podem ser enviadas pelo Firebase Cloud Messaging. A carga da mensagem determina como um aplicativo cliente receberá e processará a mensagem.

Antes de continuar com este passo a passo, você deve adquirir as credenciais necessárias para usar servidores FCM do Google. Esse processo é explicado [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#setup_fcm).
Em particular, você deve baixar o **google-Services. JSON** arquivo a ser usado com o código de exemplo apresentado neste passo a passo. Se você não ainda tiver criado um projeto no Console do Firebase (ou se você ainda não baixou as **google-Services. JSON** arquivo), consulte [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md).

Para executar o aplicativo de exemplo, você precisará de um dispositivo de teste em Android ou o emulador é compatível com a com o Firebase. O firebase Cloud Messaging dá suporte a clientes que executam no Android 4.0 ou superior, e esses dispositivos também devem ter o aplicativo do Google Play Store instalado (Google Play Services 9.2.1 ou posterior é necessário). Se você ainda não tiver o aplicativo do Google Play Store instalado em seu dispositivo, visite o [Google Play](https://support.google.com/googleplay) site da web para baixar e instalá-lo. Como alternativa, você pode usar o emulador do SDK do Android com o Google Play Services instalado, em vez de um dispositivo de teste (não é necessário instalar o Google Play Store, se você estiver usando o emulador do SDK do Android).

## <a name="start-an-app-project"></a>Iniciar um projeto de aplicativo

Para começar, crie um novo projeto de xamarin. Android vazio chamado **FCMClient**. Se você não estiver familiarizado com a criação de projetos do xamarin. Android, consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).
Depois que o novo aplicativo é criado, a próxima etapa é configurar o nome do pacote e instalar vários pacotes do NuGet que serão usados para comunicação com o FCM.

### <a name="set-the-package-name"></a>Defina o nome do pacote

Na [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md), você especificou um nome de pacote para o aplicativo habilitado pelo FCM. Esse nome de pacote também serve como o [ *ID do aplicativo* ](./firebase-cloud-messaging.md#fcm-in-action-app-id) que está associado com o [chave API](firebase-cloud-messaging.md#fcm-in-action-api-key). Configure o aplicativo para usar esse nome de pacote:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  Abra as propriedades para o **FCMClient** projeto.

2.  No **manifesto do Android** , defina o nome do pacote.

No exemplo a seguir, o nome do pacote é definido como `com.xamarin.fcmexample`:

[![Definir o nome do pacote](remote-notifications-with-fcm-images/01-package-name-vs-sml.png)](remote-notifications-with-fcm-images/01-package-name-vs.png#lightbox)

Enquanto você estiver atualizando o **manifesto do Android**, também de verificar para certificar-se de que o `Internet` permissão está habilitada.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1.  Abra as propriedades para o **FCMClient** projeto.

2.  No **aplicativo Android** , defina o nome do pacote.

No exemplo a seguir, o nome do pacote é definido como `com.xamarin.fcmexample`:

[![Definir o nome do pacote](remote-notifications-with-fcm-images/01-package-name-xs-sml.png)](remote-notifications-with-fcm-images/01-package-name-xs.png#lightbox)

Enquanto você estiver atualizando o **manifesto do Android**, também de verificar para certificar-se de que o `INTERNET` permissão está habilitada (sob **as permissões necessárias**).

-----

> [!IMPORTANT]
> O aplicativo cliente não poderão receber um token de registro do FCM, se esse nome de pacote não *exatamente* corresponder ao nome do pacote que foi inserido no Console do Firebase.

### <a name="add-the-xamarin-google-play-services-base-package"></a>Adicione o pacote Base do Xamarin Google Play Services

Porque o Firebase Cloud Messaging depende do Google Play Services, o [Xamarin Google Play Services - Base](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Base/) pacote do NuGet deve ser adicionado ao projeto xamarin. Android. Você precisará versão 29.0.0.2 ou posterior.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  No Visual Studio, clique com botão direito **referências > Gerenciar pacotes NuGet...** .

2.  Clique o **navegue** guia e pesquise **Xamarin.GooglePlayServices.Base**.

3.  Instalar esse pacote para o **FCMClient** projeto:

    [![Instalar o Base de serviços do Google Play](remote-notifications-with-fcm-images/02-google-play-services-vs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1.  No Visual Studio para Mac, clique com botão direito **pacotes > Adicionar pacotes...** .

2.  Pesquise **Xamarin.GooglePlayServices.Base**.

3.  Instalar esse pacote para o **FCMClient** projeto:

    [![Instalar o Base de serviços do Google Play](remote-notifications-with-fcm-images/02-google-play-services-xs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-xs.png#lightbox)

-----

Se você receber um erro durante a instalação do NuGet, feche o **FCMClient** do projeto, abra-o novamente e repetir a instalação do NuGet.

Quando você instala **Xamarin.GooglePlayServices.Base**, todas as dependências necessárias também são instaladas. Edite **MainActivity.cs** e adicione o seguinte `using` instrução:

```csharp
using Android.Gms.Common;
```

Essa instrução faz com que o `GoogleApiAvailability` classe **Xamarin.GooglePlayServices.Base** disponíveis para **FCMClient** código.
`GoogleApiAvailability` é usado para verificar a presença do Google Play Services.

### <a name="add-the-xamarin-firebase-messaging-package"></a>Adicione o pacote Xamarin Firebase Messaging

Para receber mensagens do FCM, o [Xamarin Firebase - mensagens](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/) pacote do NuGet deve ser adicionado ao projeto de aplicativo. Sem esse pacote, um aplicativo do Android não pode receber mensagens de servidores do FCM.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  No Visual Studio, clique com botão direito **referências > Gerenciar pacotes NuGet...** .

2. Pesquise **firebase**.

3.  Instalar esse pacote para o **FCMClient** projeto:

    [![Instalando o Xamarin Firebase de mensagens](remote-notifications-with-fcm-images/03-firebase-messaging-vs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1.  No Visual Studio para Mac, clique com botão direito **pacotes > Adicionar pacotes...** .

2.  Pesquise **firebase**.

3.  Instalar esse pacote para o **FCMClient** projeto:

    [![Instalando o Xamarin Firebase de mensagens](remote-notifications-with-fcm-images/03-firebase-messaging-xs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-xs.png#lightbox)

-----

Quando você instala **firebase**, todas as dependências necessárias também são instaladas.

Em seguida, edite **MainActivity.cs** e adicione o seguinte `using` instruções:

```csharp
using Firebase.Messaging;
using Firebase.Iid;
using Android.Util;
```

As duas primeiras instruções tornar os tipos na **firebase** pacote do NuGet para **FCMClient** código. **Android.Util** adiciona a funcionalidade de log que será usada para observar as transações com FMS.

### <a name="add-googleplayservices-json"></a>Adicione o arquivo JSON de serviços do Google

A próxima etapa é adicionar o **google-Services. JSON** arquivo para o diretório raiz do seu projeto:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  Cópia **google-Services. JSON** à pasta do projeto.

2.  Adicione **google-Services. JSON** ao projeto de aplicativo (clique em **Show All Files** no **Gerenciador de soluções**, clique com botão direito **google-Services. JSON**, em seguida, selecione **Include in Project**).

3.  Selecione **google-Services. JSON** na **Gerenciador de soluções** janela.

4.  No **propriedades** painel, defina as **Build Action** para **GoogleServicesJson**:

    [![Definir a ação de compilação como GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-vs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-vs.png#lightbox)

    > [!NOTE] 
    > Se o **GoogleServicesJson** ação de compilação não é mostrada, salve e feche a solução e reabri-lo.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1.  Cópia **google-Services. JSON** à pasta do projeto.

2.  Adicione **google-Services. JSON** ao projeto de aplicativo.

3.  Clique com botão direito **google-Services. JSON**.

4.  Defina as **ação de Build** à **GoogleServicesJson**:

    [![Definir a ação de compilação como GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-xs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-xs.png#lightbox)

-----

Quando **google-Services. JSON** é adicionado ao projeto (e o **GoogleServicesJson** ação de compilação é definida), o processo de build extrai a ID do cliente e [chave API](./firebase-cloud-messaging.md#fcm-in-action-api-key) e, em seguida, adiciona essas credenciais para o mesclado/gerado **androidmanifest. XML** que reside na **obj/Debug/android/AndroidManifest.xml**. Esse processo de mesclagem adiciona automaticamente todas as permissões e outros elementos FCM que são necessárias para conexão a servidores do FCM.


## <a name="check-for-google-play-services-and-create-a-notification-channel"></a>Verificar serviços do Google Play e criar um canal de notificação

Google recomenda que os aplicativos Android verificam a presença do Google Play Services APK antes de acessar recursos do Google Play Services (para obter mais informações, consulte [procura os serviços do Google Play](https://firebase.google.com/docs/cloud-messaging/android/client#sample-play)).

Um layout inicial para a interface do usuário do aplicativo será criado pela primeira vez. Edite **Resources/layout/Main.axml** e substitua seu conteúdo pelo seguinte XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="10dp">
    <TextView
        android:text=" "
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/msgText"
        android:textAppearance="?android:attr/textAppearanceMedium"
        android:padding="10dp" />
</LinearLayout>
```

Isso `TextView` será usado para exibir as mensagens que indicam se o Google Play Services está instalado. Salve as alterações em **Main. axml**.


Edite **MainActivity.cs** e adicione as seguintes variáveis de instância para o `MainActivity` classe:

```csharp
public class MainActivity : AppCompatActivity
{
    static readonly string TAG = "MainActivity";

    internal static readonly string CHANNEL_ID = "my_notification_channel";
    internal static readonly int NOTIFICATION_ID = 100;

    TextView msgText;
```

As variáveis `CHANNEL_ID` e `NOTIFICATION_ID` será usado no método [ `CreateNotificationChannel` ](#create-notification-channel-code) que será adicionado ao `MainActivity` posteriormente neste passo a passo.


No exemplo a seguir, o `OnCreate` método verificará que o Google Play Services está disponível antes do aplicativo tenta usar os serviços do FCM.
Adicione o seguinte método para o `MainActivity` classe:

```csharp
public bool IsPlayServicesAvailable ()
{
    int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable (this);
    if (resultCode != ConnectionResult.Success)
    {
        if (GoogleApiAvailability.Instance.IsUserResolvableError (resultCode))
            msgText.Text = GoogleApiAvailability.Instance.GetErrorString (resultCode);
        else
        {
            msgText.Text = "This device is not supported";
            Finish ();
        }
        return false;
    }
    else
    {
        msgText.Text = "Google Play Services is available.";
        return true;
    }
}
```

Esse código verifica o dispositivo para ver se o APK Google Play Services está instalado. Se não estiver instalado, será exibida uma mensagem no `TextBox` que instrui o usuário para fazer o download de um APK o Google Play Store (ou para habilitá-lo nas configurações do sistema do dispositivo).

<a name="create-notification-channel-code"></a>Aplicativos que estão em execução no Android 8.0 (API nível 26) ou superior devem criar uma [ _canal de notificação_ ](~/android/app-fundamentals/notifications/local-notifications.md) para a publicação de suas notificações.  Adicione o seguinte método para o `MainActivity` classe que criará o canal de notificação (se necessário):

```csharp
void CreateNotificationChannel()
{
    if (Build.VERSION.SdkInt < BuildVersionCodes.O)
    {
        // Notification channels are new in API 26 (and not a part of the
        // support library). There is no need to create a notification
        // channel on older versions of Android.
        return;
    }

    var channel = new NotificationChannel(CHANNEL_ID,
                                          "FCM Notifications",
                                          NotificationImportance.Default)
                  {

                      Description = "Firebase Cloud Messages appear in this channel"
                  };

    var notificationManager = (NotificationManager)GetSystemService(Android.Content.Context.NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

Substitua o método `OnCreate` pelo seguinte código:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);
    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    IsPlayServicesAvailable ();

    CreateNotificationChannel();
}
```

`IsPlayServicesAvailable` é chamado no final do `OnCreate` para que o Google Play Services verificação executa cada vez que o aplicativo é iniciado. O método `CreateNotificationChannel` é chamado para garantir a existência de um canal de notificação para dispositivos que executam o Android 8 ou superior. Se seu aplicativo tem um `OnResume` método, ele deverá chamar `IsPlayServicesAvailable` de `OnResume` também. Completamente recompile e execute o aplicativo. Se tudo estiver configurado corretamente, você verá uma tela semelhante à seguinte captura de tela:

[![Aplicativo indica que o Google Play Services está disponível](remote-notifications-with-fcm-images/05-gps-available-sml.png)](remote-notifications-with-fcm-images/05-gps-available.png#lightbox)

Se você não receber esse resultado, verifique se o APK de serviços do Google Play é instalado em seu dispositivo (para obter mais informações, consulte [configuração de backup de serviços do Google Play](https://developers.google.com/android/guides/setup)).
Também verifique se você adicionou o **Xamarin.Google.Play.Services.Base** de pacote para seu **FCMClient** projeto conforme explicado anteriormente.


## <a name="add-the-instance-id-receiver"></a>Adicionar o receptor de ID de instância

A próxima etapa é adicionar um serviço que estende `FirebaseInstanceIdService` para lidar com a criação, rotação, atualização e das [tokens do registro Firebase](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#fcm-in-action-registration-token). O `FirebaseInstanceIdService` serviço é necessário para FCM ser capaz de enviar mensagens para o dispositivo. Quando o `FirebaseInstanceIdService` serviço é adicionado ao aplicativo cliente, o aplicativo automaticamente receberá mensagens do FCM e exibi-los como notificações sempre que o aplicativo é colocado em segundo plano.

### <a name="declare-the-receiver-in-the-android-manifest"></a>Declare o receptor no manifesto do Android

Edite **androidmanifest. XML** e insira o seguinte `<receiver>` elementos no `<application>` seção:

```xml
<receiver
    android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver"
    android:exported="false" />
<receiver
    android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver"
    android:exported="true"
    android:permission="com.google.android.c2dm.permission.SEND">
    <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
    </intent-filter>
</receiver>
```

Esse XML faz o seguinte:

-   Declara um `FirebaseInstanceIdReceiver` implementação que fornece uma [identificador exclusivo](https://developers.google.com/instance-id/) para cada instância do aplicativo. Esse receptor também autentica e autoriza ações.

-   Declara um interno `FirebaseInstanceIdInternalReceiver` implementação que é usada para iniciar os serviços com segurança.

-   O [ID do aplicativo](./firebase-cloud-messaging.md#fcm-in-action-app-id) é armazenado na **google-Services. JSON** arquivo que estava [adicionado ao projeto](#add-googleplayservices-json). As associações do xamarin. Android Firebase substituirá o token `${applicationId}` com a ID do aplicativo; nenhum código adicional é necessário pelo aplicativo cliente para fornecer a ID do aplicativo.

O `FirebaseInstanceIdReceiver` é um `WakefulBroadcastReceiver` que recebe `FirebaseInstanceId` e `FirebaseMessaging` eventos e os entrega à classe que derivam de `FirebaseInstanceIdService`.

### <a name="implement-the-firebase-instance-id-service"></a>Implementar o serviço de ID de instância do Firebase

O trabalho de registro de aplicativo com o FCM é tratado pelo personalizado `FirebaseInstanceIdService` serviço que você fornece.
`FirebaseInstanceIdService` executa as seguintes etapas:

1.  Usa o [API de ID de instância](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID) para gerar tokens de segurança que autorizam o aplicativo cliente para acessar o FCM e o servidor de aplicativo. Em troca, o aplicativo obtém de volta uma [token de registro](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#fcm-in-action-registration-token) do FCM.

2.  Encaminha o token de registro para o servidor de aplicativo, se for necessário para o servidor de aplicativo.

Adicionar um novo arquivo chamado **MyFirebaseIIDService.cs** e substitua o código de modelo a seguir:

```csharp
using System;
using Android.App;
using Firebase.Iid;
using Android.Util;

namespace FCMClient
{
    [Service]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class MyFirebaseIIDService : FirebaseInstanceIdService
    {
        const string TAG = "MyFirebaseIIDService";
        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "Refreshed token: " + refreshedToken);
            SendRegistrationToServer(refreshedToken);
        }
        void SendRegistrationToServer(string token)
        {
            // Add custom implementation, as needed.
        }
    }
}
```

Este serviço implementa um `OnTokenRefresh` método é invocado quando o token de registro é inicialmente criado ou alterado. Quando `OnTokenRefresh` é executado, ele recupera o token mais recente do `FirebaseInstanceId.Instance.Token` propriedade (que é atualizada de forma assíncrona pelo FCM). Neste exemplo, o token atualizado é registrado para que ela pode ser exibida na janela de saída:

```csharp
var refreshedToken = FirebaseInstanceId.Instance.Token;
Log.Debug(TAG, "Refreshed token: " + refreshedToken);
```

`OnTokenRefresh` é invocado com pouca frequência: ele é usado para atualizar o token nas seguintes circunstâncias:

-   Quando o aplicativo é instalado ou desinstalado.

-   Quando o usuário exclui dados de aplicativo.

-   Quando o aplicativo apaga a ID da instância.

-   Quando a segurança do token foi comprometida.

De acordo com a do Google [ID da instância](https://developers.google.com/instance-id/guides/android-implementation) documentação, o serviço de ID de instância do FCM solicita que o aplicativo atualize seu token periodicamente (normalmente, a cada 6 meses).

`OnTokenRefresh` também chama `SendRegistrationToAppServer` para associar o registro do usuário token com a conta do lado do servidor (se houver) que é mantido pelo aplicativo:

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

Como essa implementação depende do design do servidor de aplicativo, um corpo de método vazio é fornecido neste exemplo. Se o servidor de aplicativo requer informações de registro do FCM, modifique `SendRegistrationToAppServer` para associar o token de ID de instância FCM do usuário com qualquer conta de servidor mantida pelo seu aplicativo. (Observe que o token é opaco para o aplicativo cliente.)

Quando um token é enviado para o servidor de aplicativo, `SendRegistrationToAppServer` deve manter um valor booliano que indica se o token foi enviado ao servidor. Se esse valor booliano for falso, `SendRegistrationToAppServer` envia o token para o servidor de aplicativo &ndash; caso contrário, o token já foi enviado para o servidor de aplicativo em uma chamada anterior. Em alguns casos (como este `FCMClient` exemplo), o servidor de aplicativo não precisa de token; portanto, esse método não é necessário para este exemplo.

## <a name="implement-client-app-code"></a>Implementar o código do aplicativo cliente

Agora que os serviços receptores estão em vigor, código do aplicativo cliente pode ser escrito para tirar proveito desses serviços. Nas seções a seguir, um botão é adicionado à interface do usuário para o token de registro de log (também chamado de *token de ID de instância*), e mais código é adicionado à `MainActivity` para exibir `Intent` informações quando o aplicativo é iniciado a partir um notificação:

[![Botão de Token de registro adicionado à tela do aplicativo](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

### <a name="log-tokens"></a>Tokens de log

O código adicionado nesta etapa é destinado somente para fins de demonstração &ndash; um aplicativo de cliente de produção não teria nenhuma necessidade de tokens do registro de log. Edite **Resources/layout/Main.axml** e adicione o seguinte `Button` declaração imediatamente após o `TextView` elemento:

```xml
<Button
  android:id="@+id/logTokenButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:text="Log Token" />
```

Adicione o código a seguir ao final do método `MainActivity.OnCreate`:

```csharp
var logTokenButton = FindViewById<Button>(Resource.Id.logTokenButton);
logTokenButton.Click += delegate {
    Log.Debug(TAG, "InstanceID token: " + FirebaseInstanceId.Instance.Token);
};
```

Esse código registra o token atual para a janela de saída quando o **Log Token** botão é tocado.

### <a name="handle-notification-intents"></a>Identificam as intenções de notificação

Quando o usuário toca uma notificação emitida a partir **FCMClient**, quaisquer dados que acompanha essa notificação de mensagem é disponibilizada no `Intent` extras. Edite **MainActivity.cs** e adicione o seguinte código na parte superior do `OnCreate` método (antes da chamada para `IsPlayServicesAvailable`):

```csharp
if (Intent.Extras != null)
{
    foreach (var key in Intent.Extras.KeySet())
    {
        var value = Intent.Extras.GetString(key);
        Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
    }
}
```

Iniciador do aplicativo `Intent` é acionado quando o usuário toca em sua mensagem de notificação, portanto, esse código registrará em log todos os dados que acompanha este artigo no `Intent` à janela de saída. Se outro `Intent` deve ser acionado, o `click_action` campo da mensagem de notificação deve ser definido para que `Intent` (o iniciador `Intent` é usado quando nenhum `click_action` for especificado).


## <a name="background-notifications"></a>Notificações da tela de fundo

Compilar e executar o **FCMClient** aplicativo. O **Log Token** botão é exibido:

[![Botão de Token de log é exibido](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

Toque o **Log Token** botão. Deve ser exibida uma mensagem semelhante à seguinte na janela de saída do IDE:

[![Token de ID de instância exibido na janela Saída](remote-notifications-with-fcm-images/07-token-received-sml.png)](remote-notifications-with-fcm-images/07-token-received.png#lightbox)

A cadeia de caracteres longa rotulados com **token** é o token de ID de instância que será colada no Console do Firebase &ndash; selecione e copie essa cadeia de caracteres para a área de transferência. Se você não vir um token de ID de instância, adicione a seguinte linha na parte superior do `OnCreate` método para verificar se **google-Services. JSON** foi analisada corretamente:

```csharp
Log.Debug(TAG, "google app id: " + GetString(Resource.String.google_app_id));
```

O `google_app_id` valor registrado em log para a janela de saída deve corresponder a `mobilesdk_app_id` valor registrado no **google-Services. JSON**.

### <a name="send-a-message"></a>Enviar uma mensagem

Entrar a [Console Firebase](https://console.firebase.google.com), selecione seu projeto, clique em **notificações**e clique em **envie sua primeira mensagem**:

[![Enviar mensagem primeiro seu botão](remote-notifications-with-fcm-images/08-first-notification-sml.png)](remote-notifications-with-fcm-images/08-first-notification.png#lightbox)

Sobre o **mensagem redigir** página, insira o texto da mensagem e selecione **único dispositivo**. Copiar o token de ID de instância da janela de saída do IDE e cole-o para o **token de registro FCM** campo do Console do Firebase:

[![Compor a caixa de diálogo de mensagem](remote-notifications-with-fcm-images/09-compose-message-sml.png)](remote-notifications-with-fcm-images/09-compose-message.png#lightbox)

No Android dispositivo (ou emulador), em segundo plano o aplicativo tocando o Android **visão geral** botão e tocar a tela inicial. Quando o dispositivo estiver pronto, clique em **enviar mensagem** no Console do Firebase:

[![Enviar mensagem de botão](remote-notifications-with-fcm-images/10-send-message-sml.png)](remote-notifications-with-fcm-images/10-send-message.png#lightbox)

Quando o **mensagem de revisão** caixa de diálogo é exibida, clique em **enviar**.
O ícone de notificação deve aparecer na área de notificação do dispositivo (ou emulador):

[![Ícone de notificação é mostrada](remote-notifications-with-fcm-images/11-notification-icon-sml.png)](remote-notifications-with-fcm-images/11-notification-icon.png#lightbox)

Abra o ícone de notificação para exibir a mensagem. A mensagem de notificação deve ser exatamente o que foi digitado para o **texto da mensagem** campo do Console do Firebase:

[![Mensagem de notificação é exibida no dispositivo](remote-notifications-with-fcm-images/12-notification-sml.png)](remote-notifications-with-fcm-images/12-notification.png#lightbox)

Toque no ícone de notificação para iniciar o **FCMClient** aplicativo. O `Intent` extras enviados ao **FCMClient** são listados na janela de saída do IDE:

[![Listas de extras intencionais de chave, a ID da mensagem e a chave de recolher](remote-notifications-with-fcm-images/13-intent-extras-sml.png)](remote-notifications-with-fcm-images/13-intent-extras.png#lightbox)

Neste exemplo, o **partir** chave é definida como o número do projeto Firebase do aplicativo (neste exemplo, `41590732`) e o **collapse_key** é definido como seu nome de pacote ( **com.xamarin.fcmexample**).
Se você não receber uma mensagem, tente excluir o **FCMClient** aplicativo no dispositivo (ou emulador) e repita as etapas acima.


> [!NOTE]
> Se você forçar-feche o aplicativo, FCM deixará de entrega de notificações. Android impede que difusões de serviço em segundo plano desnecessariamente ou inadvertidamente inicialização dos componentes de aplicativos interrompidos. (Para obter mais informações sobre esse comportamento, consulte [inicie controles em aplicativos interrompidos](https://developer.android.com/about/versions/android-3.1.html#launchcontrols).) Por esse motivo, é necessário desinstalar manualmente o aplicativo cada vez que você executá-la e interrompê-lo de uma sessão de depuração &ndash; isso força o FCM para gerar um novo token para que as mensagens continuarão a ser recebida.

### <a name="add-a-custom-default-notification-icon"></a>Adicionar um ícone de notificação padrão personalizado

No exemplo anterior, o ícone de notificação é definido como o ícone do aplicativo. O XML a seguir configura um ícone personalizado padrão para notificações. Android exibe este ícone personalizado padrão para todas as mensagens de notificação quando o ícone de notificação não é explicitamente definido.

Para adicionar um ícone de notificação padrão personalizado, adicione seu ícone para o **recursos/drawable** diretório, edite **androidmanifest. XML**e insira o seguinte `<meta-data>` elemento para o `<application>`seção:

```xml
<meta-data
    android:name="com.google.firebase.messaging.default_notification_icon"
    android:resource="@drawable/ic_stat_ic_notification" />
```

Neste exemplo, o ícone de notificação que reside na **recursos/drawable/ic\_stat\_ic\_notification.png** será usado como o ícone de notificação padrão personalizada. Se um ícone padrão personalizado não está configurado no **androidmanifest. XML** e nenhum ícone é definida na carga de notificação, o Android usa o ícone do aplicativo como o ícone de notificação (como visto na notificação ícone captura de tela acima).

## <a name="handle-topic-messages"></a>Lidar com mensagens do tópico

O código escrito até agora manipula tokens do registro e adiciona a funcionalidade de notificação remota para o aplicativo. O exemplo a seguir adiciona o código que escuta *mensagens do tópico* e as encaminha para o usuário remotas como notificações. Mensagens do tópico são mensagens do FCM que são enviadas para um ou mais dispositivos que assinem um tópico específico. Para obter mais informações sobre mensagens de tópico, consulte [sistema de mensagens do tópico](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md).

### <a name="subscribe-to-a-topic"></a>Assinar um tópico

Edite **Resources/layout/Main.axml** e adicione o seguinte `Button` declaração imediatamente após o anterior `Button` elemento:

```xml
<Button
  android:id="@+id/subscribeButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:layout_marginTop="20dp"
  android:text="Subscribe to Notifications" />
```

Esse XML adiciona uma **inscrever-se a notificação** botão no layout.
Edite **MainActivity.cs** e adicione o seguinte código ao final do `OnCreate` método:

```csharp
var subscribeButton = FindViewById<Button>(Resource.Id.subscribeButton);
subscribeButton.Click += delegate {
    FirebaseMessaging.Instance.SubscribeToTopic("news");
    Log.Debug(TAG, "Subscribed to remote notifications");
};
```

Esse código localiza o **inscrever-se a notificação** botão no layout e atribui seu manipulador de clique ao código que chama `FirebaseMessaging.Instance.SubscribeToTopic`, passando o tópico inscrito, _notícias_. Quando o usuário toca a **Subscribe** botão, o aplicativo assina os _notícias_ tópico. Na seção a seguir, uma _notícias_ tópico de mensagem será enviado a partir da GUI de notificações do Console Firebase.

### <a name="send-a-topic-message"></a>Enviar uma mensagem de tópico

Desinstalar o aplicativo, recriá-lo e executá-lo novamente. Clique o **assinar notificações** botão:

[![Inscrever-se ao botão de notificações](remote-notifications-with-fcm-images/14-subscribe-sml.png)](remote-notifications-with-fcm-images/14-subscribe.png#lightbox)

Se o aplicativo tiver assinado com êxito, você deve ver **sincronização de tópico bem-sucedida** janela de saída no IDE:

[![Janela de saída mostra a mensagem de sincronização bem-sucedida do tópico](remote-notifications-with-fcm-images/15-topic-sync-sml.png)](remote-notifications-with-fcm-images/15-topic-sync.png#lightbox)

Use as etapas a seguir para enviar uma mensagem de tópico:

1.  No Console do Firebase, clique em **nova mensagem**.

2.  Sobre o **mensagem de composição** página, insira o texto da mensagem e selecione **tópico**.

3.  No **tópico** menu suspenso, selecione o tópico **notícias**:

    [![Selecionando o tópico de notícias](remote-notifications-with-fcm-images/16-topic-message-sml.png)](remote-notifications-with-fcm-images/16-topic-message.png#lightbox)

4.  No Android dispositivo (ou emulador), em segundo plano o aplicativo tocando o Android **visão geral** botão e tocar a tela inicial.

5.  Quando o dispositivo estiver pronto, clique em **enviar mensagem** no Console do Firebase.

6.  Verifique a janela de saída do IDE para ver **notícias/tópicos/** na saída de log:

    [![Mensagem de /topic/news é mostrada](remote-notifications-with-fcm-images/17-message-arrived-sml.png)](remote-notifications-with-fcm-images/17-message-arrived.png#lightbox)

Quando esta mensagem é exibida na janela de saída, o ícone de notificação também deve aparecer na área de notificação no dispositivo Android. Abra o ícone de notificação para exibir a mensagem de tópico:

[![A mensagem de tópico é exibida como uma notificação](remote-notifications-with-fcm-images/18-other-news-sml.png)](remote-notifications-with-fcm-images/18-other-news.png#lightbox)

Se você não receber uma mensagem, tente excluir o **FCMClient** aplicativo no dispositivo (ou emulador) e repita as etapas acima.

## <a name="foreground-notifications"></a>Notificações de primeiro plano

Para receber notificações em aplicativos foregrounded, você deve implementar `FirebaseMessagingService`. Esse serviço também é necessário para receber os conteúdos de dados e para enviar mensagens upstream. Os exemplos a seguir ilustram como implementar um serviço que estende `FirebaseMessagingService` &ndash; o aplicativo resultante será capaz de lidar com notificações remotas enquanto ele está em execução em primeiro plano.

### <a name="implement-firebasemessagingservice"></a>Implementar FirebaseMessagingService

O `FirebaseMessagingService` serviço é responsável por receber e processar as mensagens do Firebase. Cada aplicativo deve ter como subclasse esse tipo e a substituição de `OnMessageReceived` para processar uma mensagem de entrada. Quando um aplicativo está em primeiro plano, o `OnMessageReceived` retorno de chamada sempre irá manipular a mensagem.

> [!NOTE]
> Os aplicativos têm apenas 10 segundos em que lidar com uma mensagem de nuvem Firebase entrada. Qualquer trabalho que demora mais do que isso deve ser agendado para execução em segundo plano usando uma biblioteca, como o [Agendador de trabalhos do Android](~/android/platform/android-job-scheduler.md) ou o [Dispatcher de trabalho do Firebase](~/android/platform/firebase-job-dispatcher.md).

Adicionar um novo arquivo chamado **MyFirebaseMessagingService.cs** e substitua o código de modelo a seguir:

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Media;
using Android.Util;
using Firebase.Messaging;

namespace FCMClient
{
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    public class MyFirebaseMessagingService : FirebaseMessagingService
    {
        const string TAG = "MyFirebaseMsgService";
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
        }
    }
}
```

Observe que o `MESSAGING_EVENT` filtro intencional deve ser declarado para que novas mensagens FCM sejam direcionadas para `MyFirebaseMessagingService`:

```csharp
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
```

Quando o aplicativo cliente recebe uma mensagem do FCM, `OnMessageReceived` extrai o conteúdo da mensagem de passado `RemoteMessage` objeto chamando seu `GetNotification` método. Em seguida, ele registra o conteúdo da mensagem para que ela pode ser exibida na janela de saída do IDE:

```csharp
var body = message.GetNotification().Body;
Log.Debug(TAG, "Notification Message Body: " + body);
```

> [!NOTE]
> Se você definir pontos de interrupção em `FirebaseMessagingService`, sua sessão de depuração pode ou não pode atingir esses pontos de interrupção devido a como FCM entrega mensagens.


### <a name="send-another-message"></a>Enviar outra mensagem

Desinstalar o aplicativo, recriá-lo, execute-o novamente e siga estas etapas para enviar outra mensagem:

1.  No Console do Firebase, clique em **nova mensagem**.

2.  Sobre o **mensagem redigir** página, insira o texto da mensagem e selecione **único dispositivo**.

3.  Copie a cadeia de caracteres de token da janela de saída do IDE e cole-o na **token de registro FCM** campo do Console do Firebase, como antes.

4.  Certifique-se de que o aplicativo está em execução em primeiro plano e, em seguida, clique em **enviar mensagem** no Console do Firebase:

    [![Enviar outra mensagem no Console do](remote-notifications-with-fcm-images/19-hello-again-sml.png)](remote-notifications-with-fcm-images/19-hello-again.png#lightbox)

5.  Quando o **mensagem de revisão** caixa de diálogo é exibida, clique em **enviar**.

6.  A mensagem de entrada é registrada na janela de saída do IDE:

    [![Corpo da mensagem é impressa à janela de saída](remote-notifications-with-fcm-images/20-logged-message.png)](remote-notifications-with-fcm-images/20-logged-message.png#lightbox)


### <a name="add-a-local-notification-sender"></a>Adicionar um remetente de notificação de local

Neste exemplo restantes, a mensagem de entrada FCM será convertida em uma notificação de local que é iniciada enquanto o aplicativo está em execução em primeiro plano. Edite **MyFirebaseMessageService.cs** e adicione o seguinte `using` instruções:

```csharp
using FCMClient;
using System.Collections.Generic;
```

Adicione o seguinte método à `MyFirebaseMessagingService`:

<a name="sendnotification-method"></a>
```csharp
void SendNotification(string messageBody, IDictionary<string, string> data)
{
    var intent = new Intent(this, typeof(MainActivity));
    intent.AddFlags(ActivityFlags.ClearTop);
    foreach (var key in data.Keys)
    {
        intent.PutExtra(key, data[key]);
    }

    var pendingIntent = PendingIntent.GetActivity(this,
                                                  MainActivity.NOTIFICATION_ID,
                                                  intent,
                                                  PendingIntentFlags.OneShot);

    var notificationBuilder = new  NotificationCompat.Builder(this, MainActivity.CHANNEL_ID)
                              .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
                              .SetContentTitle("FCM Message")
                              .SetContentText(messageBody)
                              .SetAutoCancel(true)
                              .SetContentIntent(pendingIntent);

    var notificationManager = NotificationManagerCompat.From(this);
    notificationManager.Notify(MainActivity.NOTIFICATION_ID, notificationBuilder.Build());
}
```

Para distinguir esta notificação de notificações do plano de fundo, esse código marca notificações com um ícone que é diferente do ícone do aplicativo. Adicione o arquivo [ic\_stat\_ic\_notification.png](remote-notifications-with-fcm-images/ic-stat-ic-notification.png) para **recursos/drawable** e incluí-lo no **FCMClient** projeto .

O `SendNotification` método usos ` NotificationCompat.Builder` para criar a notificação e `NotificationManagerCompat` é usado para iniciar a notificação. A notificação contém um `PendingIntent` que permitirá que o usuário abrir o aplicativo e exibir o conteúdo da cadeia de caracteres passada para `messageBody`. Para obter mais informações sobre `NotificationCompat.Builder`, consulte [notificações locais](~/android/app-fundamentals/notifications/local-notifications.md).

Chame o `SendNotification` método no final do `OnMessageReceived` método:

```csharp
public override void OnMessageReceived(RemoteMessage message)
{
    Log.Debug(TAG, "From: " + message.From);

    var body = message.GetNotification().Body;
    Log.Debug(TAG, "Notification Message Body: " + body);
    SendNotification(body, message.Data);
}
```

Como resultado dessas alterações, `SendNotification` será executada sempre que uma notificação é recebida enquanto o aplicativo está em primeiro plano e a notificação será exibida na área de notificação.

Quando um aplicativo está em segundo plano, o [carga da mensagem](https://firebase.google.com/docs/cloud-messaging/concept-options#notifications_and_data_messages) determinará como a mensagem é tratada:

* **Notificação** &ndash; mensagens serão enviadas para o **bandeja do sistema**. Uma notificação local aparecerá lá. Quando o usuário toca na notificação iniciará o aplicativo.
* **Dados** &ndash; mensagens serão manipuladas pelo `OnMessageReceived`.
* **Ambos** &ndash; as mensagens que tenham uma notificação e dados de carga serão entregue a bandeja do sistema. Quando o aplicativo for iniciado, a carga de dados aparecerá na `Extras` do `Intent` que foi usado para iniciar o aplicativo.

Neste exemplo, se o aplicativo é colocado em segundo plano, `SendNotification` será executado se a mensagem tem uma carga de dados. Caso contrário, uma notificação de plano de fundo (ilustrada anteriormente neste passo a passo) será iniciada.

### <a name="send-the-last-message"></a>Enviar a última mensagem

Desinstalar o aplicativo, recriá-lo, execute-o novamente e usar as etapas a seguir para enviar a última mensagem:

1.  No Console do Firebase, clique em **nova mensagem**.

2.  Sobre o **mensagem redigir** página, insira o texto da mensagem e selecione **único dispositivo**.

3.  Copie a cadeia de caracteres de token da janela de saída do IDE e cole-o na **token de registro FCM** campo do Console do Firebase, como antes.

4.  Certifique-se de que o aplicativo está em execução em primeiro plano e, em seguida, clique em **enviar mensagem** no Console do Firebase:

    [![Enviar a mensagem de primeiro plano](remote-notifications-with-fcm-images/21-console-fg-msg-sml.png)](remote-notifications-with-fcm-images/21-console-fg-msg.png#lightbox)

Neste momento, a mensagem que foi registrada na janela de saída também é empacotada em uma nova notificação &ndash; o ícone de notificação é exibido na bandeja de notificação enquanto o aplicativo está em execução em primeiro plano:

[![Ícone de notificação de mensagem do primeiro plano](remote-notifications-with-fcm-images/22-foreground-icon-sml.png)](remote-notifications-with-fcm-images/22-foreground-icon.png#lightbox)

Quando você abre a notificação, você deverá ver a última mensagem enviada a partir da GUI de notificações do Console Firebase:

[![Notificação de primeiro plano mostrada com ícone de primeiro plano](remote-notifications-with-fcm-images/23-foreground-msg-sml.png)](remote-notifications-with-fcm-images/23-foreground-msg.png#lightbox)


## <a name="disconnecting-from-fcm"></a>Desconectando do FCM

Para cancelar a assinatura de um tópico, chame o [UnsubscribeFromTopic](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging.html#unsubscribeFromTopic%28java.lang.String%29) método sobre o [FirebaseMessaging](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging) classe. Por exemplo, para cancelar a assinatura a _notícias_ tópico assinado anteriormente, um **Unsubscribe** botão poderia ser adicionado ao layout com o seguinte código de manipulador:

```csharp
var unSubscribeButton = FindViewById<Button>(Resource.Id.unsubscribeButton);
unSubscribeButton.Click += delegate {
    FirebaseMessaging.Instance.UnsubscribeFromTopic("news");
    Log.Debug(TAG, "Unsubscribed from remote notifications");
};
```

Para cancelar o registro do dispositivo na totalmente FCM, excluir a ID de instância chamando o [DeleteInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId.html#deleteInstanceId%28%29) método sobre o [FirebaseInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId) classe. Por exemplo:

```csharp
FirebaseInstanceId.Instance.DeleteInstanceId();
```

Esta chamada de método exclui a ID de instância e os dados associados a ele. Como resultado, o envio periódica de dados FCM para o dispositivo é interrompido.


## <a name="troubleshooting"></a>Solução de problemas

A seguir descrevem os problemas e soluções alternativas que podem surgir ao usar o Firebase Cloud Messaging com xamarin. Android.

### <a name="firebaseapp-is-not-initialized"></a>FirebaseApp não foi inicializado.

Em alguns casos, você poderá ver esta mensagem de erro:

```shell
Java.Lang.IllegalStateException: Default FirebaseApp is not initialized in this process
Make sure to call FirebaseApp.initializeApp(Context) first.
```

Esse é um problema conhecido que você pode contornar a solução de limpeza e recompilar o projeto (**Build > Limpar solução**, **compilar > recompilar solução**).

## <a name="summary"></a>Resumo

Este passo a passo detalhadas as etapas para implementar o Firebase Cloud Messaging notificações remotas em um aplicativo xamarin. Android. Ele descreveu como instalar os pacotes necessários, necessários para comunicações do FCM e explicou como configurar o manifesto do Android para o acesso aos servidores do FCM. Ele fornecia o código de exemplo que ilustra como verificar a presença do Google Play Services. Ele demonstrou como implementar um serviço de escuta de ID de instância que negocia com o FCM para um token de registro e ele explicou como esse código cria notificações em segundo plano enquanto o aplicativo é colocado em segundo plano. Ele explicou como assinar as mensagens de tópico e ele tiver fornecido um exemplo de implementação de um serviço de escuta de mensagem que é usado para receber e exibir notificações remotas, enquanto o aplicativo está em execução em primeiro plano.


## <a name="related-links"></a>Links relacionados

- [FCMNotifications (amostra)](https://developer.xamarin.com/samples/monodroid/Firebase/FCMNotifications)
- [Mensagens na nuvem do Firebase](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
- [Sobre mensagens de FCM](https://firebase.google.com/docs/cloud-messaging/concept-options)
