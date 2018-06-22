---
title: Notificações remotas com Firebase mensagens de nuvem
description: Este passo a passo fornece uma explicação passo a passo de como usar mensagens de nuvem Firebase para implementar notificações remotas (também chamadas de notificações por push) em um aplicativo xamarin. Ele ilustra como implementar várias classes que são necessários para as comunicações com o sistema de mensagens de nuvem com a Firebase (FCM), fornece exemplos de como configurar o manifesto do Android para acessar FCM e demonstra o downstream de mensagens usando o Firebase Console.
ms.prod: xamarin
ms.assetid: 4D7C5F46-C997-49F6-AFDA-6763E68CDC90
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/12/2018
ms.openlocfilehash: e2f25504b971a0332dc51dc9b017c9c83222ec57
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
ms.locfileid: "31044932"
---
# <a name="remote-notifications-with-firebase-cloud-messaging"></a>Notificações remotas com Firebase mensagens de nuvem

_Este passo a passo fornece uma explicação passo a passo de como usar mensagens de nuvem Firebase para implementar notificações remotas (também chamadas de notificações por push) em um aplicativo xamarin. Ele ilustra como implementar várias classes que são necessários para as comunicações com o sistema de mensagens de nuvem com a Firebase (FCM), fornece exemplos de como configurar o manifesto do Android para acessar FCM e demonstra o downstream de mensagens usando o Firebase Console._

## <a name="fcm-notifications-overview"></a>Visão geral de notificações FCM

Neste passo a passo, chamado de um aplicativo básico **FCMClient** será criado para ilustrar os conceitos básicos de mensagens FCM. **FCMClient** verifica a presença do Google executar serviços, recebe tokens do registro do FCM, exibe notificações remotas que você enviar no Console do Firebase e assina as mensagens do tópico:

[![Captura de tela de exemplo de aplicativo](remote-notifications-with-fcm-images/00-app-example-sml.png)](remote-notifications-with-fcm-images/00-app-example.png#lightbox)

Os tópicos a seguir serão explorados:

1.  Notificações de plano de fundo

2.  Mensagens do tópico

3.  Notificações de primeiro plano

Durante este passo a passo, você irá incrementalmente adicionar funcionalidade ao **FCMClient** e executá-lo em um dispositivo ou emulador para entender como ele interage com FCM. Você usará o registro em log para transações de aplicativo em tempo real com servidores FCM de testemunha, e você vai observar como as notificações são geradas mensagens FCM que você inserir em GUI Firebase Console notificações. 

## <a name="requirements"></a>Requisitos

Antes de prosseguir com este passo a passo, você deve adquirir as credenciais necessárias para usar servidores FCM do Google. Esse processo é explicado em [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md). Em particular, você deve baixar o **services.json google** arquivo a ser usado com o código de exemplo apresentado neste passo a passo. Se você não ainda tiver criado um projeto no Console do Firebase (ou se você ainda não baixou o **google services.json** arquivo), consulte [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md). 

Para executar o exemplo de aplicativo, você precisará de um dispositivo Android teste ou um emulador que é compatível com a Firebase. Mensagens de nuvem firebase oferece suporte a clientes que executam no Android 2.3 (biscoito) ou superior, e esses dispositivos também devem ter o aplicativo do Google Play Store instalado (Google Play serviços 9.2.1 ou posterior é necessário). Se você ainda não tiver o aplicativo do Google Play Store instalado em seu dispositivo, visite o [Google Play](https://support.google.com/googleplay) site para baixar e instalá-lo. Como alternativa, você pode usar o emulador do SDK do Android que executam o Android 2.3 ou posterior, em vez de um dispositivo de teste (não é necessário instalar o Google Play Store, se você estiver usando o emulador Android SDK). 

## <a name="start-an-app-project"></a>Iniciar um projeto de aplicativo

Para começar, crie um novo projeto xamarin vazio chamado **FCMClient**. Se você não estiver familiarizado com a criação de projetos de xamarin, consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md). Depois que o novo aplicativo é criado, a próxima etapa é configurar o nome do pacote e instalar vários pacotes do NuGet que serão usados para comunicação com FCM. 

### <a name="set-the-package-name"></a>Defina o nome do pacote

Em [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md), você especificou um nome de pacote para o aplicativo habilitado para FCM. Esse nome de pacote também serve como o *ID do aplicativo* que está associado com a chave de API. Configure o aplicativo para usar esse nome de pacote:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Abra as propriedades para o **FCMClient** projeto. 

2.  No **manifesto do Android** , defina o nome do pacote. 

No exemplo a seguir, o nome do pacote é definido como `com.xamarin.fcmexample`: 

[![Definir o nome do pacote](remote-notifications-with-fcm-images/01-package-name-vs-sml.png)](remote-notifications-with-fcm-images/01-package-name-vs.png#lightbox)

Enquanto você estiver atualizando o **manifesto do Android**, também uma verificação para certificar-se de que o `Internet` permissão está habilitada. 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1.  Abra as propriedades para o **FCMClient** projeto. 

2.  No **aplicativo Android** , defina o nome do pacote. 

No exemplo a seguir, o nome do pacote é definido como `com.xamarin.fcmexample`: 

[![Definir o nome do pacote](remote-notifications-with-fcm-images/01-package-name-xs-sml.png)](remote-notifications-with-fcm-images/01-package-name-xs.png#lightbox)

Enquanto você estiver atualizando o **manifesto do Android**, também uma verificação para certificar-se de que o `INTERNET` permissão estiver ativada (em **as permissões necessárias**). 

-----

Observe que o aplicativo cliente poderão receber um token de registro de FCM se esse nome de pacote não *exatamente* corresponde ao nome de pacote que foi inserido no Console do Firebase. 

### <a name="add-the-xamarin-google-play-services-base-package"></a>Adicionar o pacote de Base de serviços do Xamarin Google Play

Porque Firebase Cloud Messaging depende do Google executar serviços, o [Xamarin Google executar serviços - Base](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Base/) pacote NuGet deve ser adicionado ao projeto xamarin. Você precisará versão 29.0.0.2 ou posterior.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  No Visual Studio, clique com botão direito **referências > Gerenciar pacotes NuGet...** . 

2.  Clique o **procurar** guia e procure **Xamarin.GooglePlayServices.Base**. 

3.  Instalar esse pacote para o **FCMClient** projeto: 

    [![Instalação de Base de serviços do Google Play](remote-notifications-with-fcm-images/02-google-play-services-vs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1.  No Visual Studio para Mac, clique com botão direito **pacotes > Adicionar pacotes de...** . 

2.  Procurar **Xamarin.GooglePlayServices.Base**. 

3.  Instalar esse pacote para o **FCMClient** projeto: 

    [![Instalação de Base de serviços do Google Play](remote-notifications-with-fcm-images/02-google-play-services-xs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-xs.png#lightbox)

-----

Se você receber um erro durante a instalação do NuGet, feche o **FCMClient** projeto, abra-o novamente e repita a instalação do NuGet. 

Quando você instala o **Xamarin.GooglePlayServices.Base**, todas as dependências necessárias também são instaladas. Editar **MainActivity.cs** e adicione o seguinte `using` instrução: 

```csharp
using Android.Gms.Common;
```

Essa instrução faz o `GoogleApiAvailability` classe em **Xamarin.GooglePlayServices.Base** disponíveis para **FCMClient** código. 
`GoogleApiAvailability` é usado para verificar a presença de serviços do Google reproduzir. 

### <a name="add-the-xamarin-firebase-messaging-package"></a>Adicionar o Xamarin Firebase pacote do sistema de mensagens

Para receber mensagens de FCM, o [Xamarin Firebase - mensagens](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/) pacote NuGet deve ser adicionado ao projeto de aplicativo. Sem esse pacote, um aplicativo do Android não pode receber mensagens de servidores FCM.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  No Visual Studio, clique com botão direito **referências > Gerenciar pacotes NuGet...** . 

2.  Verificar **incluir pré-lançamento** e procure **Xamarin.Firebase.Messaging**. 

3.  Instalar esse pacote para o **FCMClient** projeto: 

    [![Instalar Xamarin Firebase de mensagens](remote-notifications-with-fcm-images/03-firebase-messaging-vs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1.  No Visual Studio para Mac, clique com botão direito **pacotes > Adicionar pacotes de...** . 

2.  Verificar **Mostrar pacotes de pré-lançamento** e procure **Xamarin.Firebase.Messaging**. 

3.  Instalar esse pacote para o **FCMClient** projeto: 

    [![Instalar Xamarin Firebase de mensagens](remote-notifications-with-fcm-images/03-firebase-messaging-xs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-xs.png#lightbox)

-----
 
Quando você instala o **Xamarin.Firebase.Messaging**, todas as dependências necessárias também são instaladas.

Em seguida, edite **MainActivity.cs** e adicione o seguinte `using` instruções:

```csharp
using Firebase.Messaging;
using Firebase.Iid;
using Android.Util;
```

As primeiras duas instruções tornar tipos no **Xamarin.Firebase.Messaging** pacote do NuGet para **FCMClient** código. **Android.Util** adiciona a funcionalidade de log que será usada para observar as transações com FMS. 


### <a name="add-the-google-services-json-file"></a>Adicione o arquivo JSON de serviços do Google

A próxima etapa é adicionar o **services.json google** arquivo para o diretório raiz do seu projeto: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Cópia **services.json google** à pasta do projeto.

2.  Adicionar **google services.json** ao projeto de aplicativo (clique **Mostrar todos os arquivos** no **Solution Explorer**, clique com botão direito **google-services.json**, em seguida, selecione **incluir no projeto**). 

3.  Selecione **google services.json** no **Solution Explorer** janela.

4.  No **propriedades** painel, defina o **ação de compilação** para **GoogleServicesJson** (se o **GoogleServicesJson** ação de compilação não for exibida, Salve e feche a solução e reabri-lo):

    [![Definir a ação de compilação como GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-vs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-vs.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1.  Cópia **services.json google** à pasta do projeto.

2.  Adicionar **services.json google** ao projeto de aplicativo. 

3.  Clique com botão direito **services.json google**.

4.  Definir o **ação de compilação** para **GoogleServicesJson**: 

    [![Definir a ação de compilação como GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-xs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-xs.png#lightbox)
 
-----
 

Quando **google services.json** é adicionado ao projeto (e o **GoogleServicesJson** ação de compilação é definida), o processo de compilação extrai a chave de ID e a API de cliente e, em seguida, adiciona essas credenciais para o mesclada / gerado **AndroidManifest.xml** que reside no **obj/Debug/android/AndroidManifest.xml**. Esse processo de mesclagem adiciona automaticamente todas as permissões e outros elementos FCM que são necessárias para conexão a servidores FCM. 


## <a name="check-for-google-play-services"></a>Verificação de serviços do Google Play

Um layout inicial para a interface de usuário do aplicativo será criado pela primeira vez. Editar **Resources/layout/Main.axml** e substitua o seu conteúdo com o seguinte XML: 

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

Isso `TextView` será usado para exibir as mensagens que indicam se o Google reproduzir Services está instalado. Salvar as alterações em **Main.axml**. Editar **MainActivity.cs** e adicione a seguinte declaração de variável de instância para o `MainActivity` classe: 

```csharp
TextView msgText;
```

Google recomenda que aplicativos Android Verifique a presença do Google executar serviços APK antes de acessar os recursos de serviços do Google reproduzir (para obter mais informações, consulte [procura os serviços do Google Play](https://firebase.google.com/docs/cloud-messaging/android/client#sample-play)). No exemplo a seguir, o `OnCreate` método verificará que o Google executar serviços está disponível antes do aplicativo tenta usar FCM serviços. Adicione o seguinte método para o `MainActivity` classe: 

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

Esse código verifica o dispositivo para ver se o Google executar serviços APK está instalado. Se não estiver instalado, será exibida uma mensagem no `TextBox` que instrui o usuário para baixar um APK do Google Play Store (ou para habilitá-lo em configurações do sistema do dispositivo). 

Substitua o método `OnCreate` pelo seguinte código: 

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);
    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);
    IsPlayServicesAvailable ();
}
```

`IsPlayServicesAvailable` é chamado no final do `OnCreate` para que os serviços do Google executar a verificação executa cada vez que o aplicativo é iniciado. Se seu aplicativo tiver um `OnResume` método, é necessário chamar `IsPlayServicesAvailable` de `OnResume` também. Completamente recriar e executar o aplicativo. Se tudo está configurado corretamente, você verá uma tela semelhante à captura de tela a seguir: 

[![Aplicativo indica que os serviços do Google reproduzir está disponível](remote-notifications-with-fcm-images/05-gps-available-sml.png)](remote-notifications-with-fcm-images/05-gps-available.png#lightbox)

Se você não obtiver esse resultado, verifique se o Google executar serviços APK é instalado em seu dispositivo (para obter mais informações, consulte [configuração Google executar serviços](https://developers.google.com/android/guides/setup)). Também verifique se você adicionou o **Xamarin.Google.Play.Services.Base** do pacote para o **FCMClient** projeto conforme explicado anteriormente.


## <a name="add-the-instance-id-receiver"></a>Adicionar o receptor da ID de instância

A próxima etapa é adicionar um serviço que estende `FirebaseInstanceIdService` para lidar com a criação da rotação e a atualização dos tokens de registro Firebase. (Para obter mais informações sobre tokens de registro, consulte [registro com FCM](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md).) O `FirebaseInstanceIdService` serviço é necessário para FCM poder enviar mensagens para o dispositivo. Quando o `FirebaseInstanceIdService` serviço é adicionado para o aplicativo cliente, o aplicativo automaticamente receberá mensagens FCM e exibi-los como notificações sempre que o aplicativo é backgrounded. 

### <a name="declare-the-receiver-in-the-android-manifest"></a>Declare o receptor no manifesto do Android

Editar **AndroidManifest.xml** e insira o seguinte `<receiver>` elementos para o `<application>` seção: 

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

-   Declara um `FirebaseInstanceIdReceiver` implementação que fornece um [identificador exclusivo](https://developers.google.com/instance-id/) para cada instância do aplicativo. Esse destinatário também autentica e autoriza ações. 

-   Declara um interno `FirebaseInstanceIdInternalReceiver` implementação que é usada para iniciar os serviços com segurança.

O `FirebaseInstanceIdReceiver` é um `WakefulBroadcastReceiver` que recebe `FirebaseInstanceId` e `FirebaseMessaging` eventos e entrega para a classe que derivam de `FirebaseInstanceIdService`. 

### <a name="implement-the-firebase-instance-id-service"></a>Implementar o serviço de ID de instância Firebase

O trabalho de registrar o aplicativo FCM é tratado pelo personalizado `FirebaseInstanceIdService` serviço que você fornecer. 
`FirebaseInstanceIdService` executa as seguintes etapas: 

1.  Usa o [API de ID de instância](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID) para gerar tokens de segurança que autorizam o aplicativo cliente acessar FCM e o servidor de aplicativo. Em troca, o aplicativo obtém de volta um registro de token do FCM. 

2.  Encaminha o token de registro para o servidor de aplicativos se o servidor de aplicativo exija isso.

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

De acordo com a do Google [ID de instância](https://developers.google.com/instance-id/guides/android-implementation) documentação, o serviço de ID da instância FCM solicitará que o aplicativo de atualização de seu token periodicamente (normalmente, a cada 6 meses). 

`OnTokenRefresh` também chama `SendRegistrationToAppServer` para associar o registro do usuário token com a conta do servidor (se houver) que é mantida pelo aplicativo: 

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

Como essa implementação depende do design do servidor de aplicativo, um corpo de método vazio é fornecido neste exemplo. Se o servidor de aplicativo requer informações de registro FCM, modifique `SendRegistrationToAppServer` para associar o token de ID de instância do usuário FCM a qualquer conta do servidor mantida pelo seu aplicativo. (Observe que o token é opaco para o aplicativo cliente.) 

Quando um token é enviado para o servidor de aplicativos, `SendRegistrationToAppServer` deve manter um valor booleano para indicar se o token foi enviado ao servidor. Se esse valor booliano for falso, `SendRegistrationToAppServer` envia o token para o servidor de aplicativo &ndash; caso contrário, o token já foi enviado para o servidor de aplicativos em uma chamada anterior. Em alguns casos (como esta `FCMClient` exemplo), o servidor de aplicativo não é necessário o token; portanto, esse método não é necessário para este exemplo. 

## <a name="implement-client-app-code"></a>Código do aplicativo cliente implementar

Agora que os serviços receptores entram em vigor, o código do aplicativo cliente pode ser gravado para tirar proveito desses serviços. Nas seções a seguir, um botão é adicionado à interface do usuário para o token de registro de log (também chamado de *token de ID de instância*), e mais código é adicionado à `MainActivity` para exibir `Intent` informações quando o aplicativo é iniciado de um notificação: 

[![Botão de Token de registro adicionado à tela do aplicativo](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

### <a name="log-tokens"></a>Tokens de log

O código adicionado nesta etapa destina-se apenas para fins de demonstração &ndash; um aplicativo de cliente de produção teria que não há necessidade de tokens de registro de log. Editar **Resources/layout/Main.axml** e adicione o seguinte `Button` declaração imediatamente após o `TextView` elemento: 

```xml
<Button
  android:id="@+id/logTokenButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:text="Log Token" />
```

Editar **MainActivity.cs** e adicione a seguinte declaração de membro para o `MainActivity` classe:

```csharp
const string TAG = "MainActivity";
```

Adicione o seguinte código ao final do `OnCreate` método: 

```csharp
var logTokenButton = FindViewById<Button>(Resource.Id.logTokenButton);
logTokenButton.Click += delegate {
    Log.Debug(TAG, "InstanceID token: " + FirebaseInstanceId.Instance.Token);
};
```

Este código faz o token atual para a janela de saída quando o **Log Token** botão é tocado. 

### <a name="handle-notification-intents"></a>Identificador de tentativas de notificação

Quando o usuário toca uma notificação emitida **FCMClient**, quaisquer dados que acompanha essa notificação de mensagem é disponibilizada em `Intent` extras. Editar **MainActivity.cs** e adicione o seguinte código na parte superior do `OnCreate` método (antes da chamada para `IsPlayServicesAvailable`): 

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

Iniciador do aplicativo `Intent` é acionado quando o usuário toca em sua mensagem de notificação, portanto esse código registrará seus dados no `Intent` a janela de saída. Se outro `Intent` deve ser acionado, o `click_action` campo da mensagem de notificação deve ser definido para que `Intent` (o iniciador `Intent` é usado quando nenhum `click_action` for especificado). 


## <a name="background-notifications"></a>Notificações de plano de fundo

Compilar e executar o **FCMClient** aplicativo. O **Log Token** botão é exibido:

[![Botão de Token de log é exibido](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

Toque na **Log Token** botão. Uma mensagem semelhante ao seguinte deve ser exibida na janela de saída do IDE: 

[![Token de ID de instância exibido na janela Saída](remote-notifications-with-fcm-images/07-token-received-sml.png)](remote-notifications-with-fcm-images/07-token-received.png#lightbox)

A cadeia de caracteres longa rotulada com **token** é o token de ID de instância que será colada no Console do Firebase &ndash; selecione e copie essa cadeia de caracteres para a área de transferência. Se você não vir um token de ID de instância, adicione a seguinte linha à parte superior do `OnCreate` método para verificar se **services.json google** foi analisada corretamente:

```csharp
Log.Debug(TAG, "google app id: " + GetString(Resource.String.google_app_id));
```

O `google_app_id` valor registrado em log para a janela de saída deve corresponder a `mobilesdk_app_id` valor registrado em **services.json google**. 

### <a name="send-a-message"></a>Enviar uma mensagem

Entrar a [Firebase Console](https://console.firebase.google.com), selecione o seu projeto, clique em **notificações**e clique em **enviar sua primeira mensagem**: 

[![Enviar sua mensagem primeiro botão](remote-notifications-with-fcm-images/08-first-notification-sml.png)](remote-notifications-with-fcm-images/08-first-notification.png#lightbox)

Sobre o **redigir mensagem** página, insira o texto da mensagem e selecione **único dispositivo**. Copie o token de ID de instância da janela de saída do IDE e cole-o no **token de registro FCM** campo do Console Firebase: 

[![Compor a caixa de diálogo de mensagem](remote-notifications-with-fcm-images/09-compose-message-sml.png)](remote-notifications-with-fcm-images/09-compose-message.png#lightbox)

No Android dispositivo (ou emulador), em segundo plano do aplicativo, toque o Android **visão geral** botão e tocar na tela inicial. Quando o dispositivo estiver pronto, clique em **enviar mensagem** no Console do Firebase: 

[![Enviar mensagem de botão](remote-notifications-with-fcm-images/10-send-message-sml.png)](remote-notifications-with-fcm-images/10-send-message.png#lightbox)

Quando o **mensagem revisão** caixa de diálogo é exibida, clique em **enviar**.
O ícone de notificação deve aparecer na área de notificação do dispositivo (ou emulador): 

[![Ícone de notificação é mostrada](remote-notifications-with-fcm-images/11-notification-icon-sml.png)](remote-notifications-with-fcm-images/11-notification-icon.png#lightbox)

Abra o ícone de notificação para exibir a mensagem. A mensagem de notificação deve ser exatamente o que foi digitado no **texto da mensagem** campo do Console Firebase: 

[![Mensagem de notificação é exibida no dispositivo](remote-notifications-with-fcm-images/12-notification-sml.png)](remote-notifications-with-fcm-images/12-notification.png#lightbox)

Toque no ícone de notificação para retornar para o **FCMClient** aplicativo. O `Intent` extras enviados para **FCMClient** são listados na janela de saída do IDE: 

[![Listas de intenção extras de chave, a ID de mensagem e a chave de recolhimento](remote-notifications-with-fcm-images/13-intent-extras-sml.png)](remote-notifications-with-fcm-images/13-intent-extras.png#lightbox)

Neste exemplo, o **de** chave é definida como o número do projeto Firebase do aplicativo (neste exemplo, `41590732`) e o **collapse_key** é definido como seu nome de pacote ( **com.xamarin.fcmexample**). Se você não receber uma mensagem, tente excluir o **FCMClient** aplicativo no dispositivo (ou emulador) e repita as etapas acima. 


> [!NOTE]
> Se você forçar o fechamento do aplicativo, FCM interromperá a entrega de notificações. Android impede que transmissões do serviço de plano de fundo inadvertidamente ou desnecessariamente iniciem componentes de aplicativos interrompidos. (Para obter mais informações sobre esse comportamento, consulte [iniciar controles em aplicativos interrompidos](https://developer.android.com/about/versions/android-3.1.html#launchcontrols).) Por esse motivo, é necessário desinstalar manualmente o aplicativo sempre que você executá-lo e interrompê-lo de uma sessão de depuração &ndash; isso força FCM para gerar um novo token para que as mensagens continuarão a ser recebida.

### <a name="add-a-custom-default-notification-icon"></a>Adicionar um ícone de notificação padrão personalizado

No exemplo anterior, o ícone de notificação é definido para o ícone do aplicativo. O XML a seguir configura um ícone padrão personalizado para notificações. Android exibe este ícone padrão para todas as mensagens de notificação quando o ícone de notificação não é explicitamente definido. 

Para adicionar um ícone de notificação padrão personalizado, adicione o ícone para o **recursos/drawable** diretório, editar **AndroidManifest.xml**e insira o seguinte `<meta-data>` elemento para o `<application>`seção: 

```xml
<meta-data
    android:name="com.google.firebase.messaging.default_notification_icon"
    android:resource="@drawable/ic_stat_ic_notification" />
```

Neste exemplo, o ícone de notificação que reside no **recursos/drawable/ic\_stat\_ic\_notification.png** será usado como o ícone de notificação personalizada padrão. Se um ícone padrão personalizado não está configurado no **AndroidManifest.xml** e nenhum ícone é definida na carga de notificação, Android usa o ícone do aplicativo como o ícone de notificação (como visto na notificação ícone captura de tela acima). 

## <a name="handle-topic-messages"></a>Manipular mensagens do tópico

O código escrito até o momento lida com tokens de registro e adiciona a funcionalidade de notificação remoto para o aplicativo. O exemplo a seguir adiciona o código que escuta *mensagens do tópico* e encaminha-os para o usuário remotas notificações. Mensagens do tópico são FCM mensagens são enviadas para um ou mais dispositivos que se inscrever para um tópico específico. Para obter mais informações sobre mensagens de tópico, consulte [tópico mensagens](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md). 

### <a name="subscribe-to-a-topic"></a>Inscrever-se em um tópico

Editar **Resources/layout/Main.axml** e adicione o seguinte `Button` declaração imediatamente após o anterior `Button` elemento:

```xml
<Button
  android:id="@+id/subscribeButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:layout_marginTop="20dp"
  android:text="Subscribe to Notifications" />
```

Esse XML adiciona uma **se inscrever para notificação** botão no layout.
Editar **MainActivity.cs** e adicione o seguinte código ao final do `OnCreate` método: 

```csharp
var subscribeButton = FindViewById<Button>(Resource.Id.subscribeButton);
subscribeButton.Click += delegate {
    FirebaseMessaging.Instance.SubscribeToTopic("news");
    Log.Debug(TAG, "Subscribed to remote notifications");
};
```

Esse código localiza o **se inscrever para notificação** botão no layout e atribui seu manipulador de clique ao código que chama `FirebaseMessaging.Instance.SubscribeToTopic`, passando o tópico inscrito, _notícias_. Quando o usuário toca o **assinar** botão, o aplicativo assina o _notícias_ tópico. Na seção a seguir, uma _notícias_ tópico mensagem será enviada a partir da GUI do Firebase Console notificações. 

### <a name="send-a-topic-message"></a>Enviar uma mensagem de tópico

Desinstalar o aplicativo, recriá-lo e executá-lo novamente. Clique o **assinar notificações** botão:

[![Inscrever-se no botão de notificações](remote-notifications-with-fcm-images/14-subscribe-sml.png)](remote-notifications-with-fcm-images/14-subscribe.png#lightbox)

Se o aplicativo tiver assinado com êxito, você deverá ver **sincronização tópico bem-sucedida** janela de saída no IDE: 

[![Janela de saída mostra a mensagem de sincronização bem-sucedida do tópico](remote-notifications-with-fcm-images/15-topic-sync-sml.png)](remote-notifications-with-fcm-images/15-topic-sync.png#lightbox)

Use as etapas a seguir para enviar uma mensagem de tópico:

1.  No Console do Firebase, clique em **nova mensagem**. 

2.  Sobre o **redigir mensagem** página, insira o texto da mensagem e selecione **tópico**. 

3.  No **tópico** menu suspenso, selecione o tópico **notícias**: 

    [![Selecionando o tópico de notícias](remote-notifications-with-fcm-images/16-topic-message-sml.png)](remote-notifications-with-fcm-images/16-topic-message.png#lightbox)

4.  No Android dispositivo (ou emulador), em segundo plano do aplicativo, toque o Android **visão geral** botão e tocar na tela inicial. 

5.  Quando o dispositivo estiver pronto, clique em **enviar mensagem** no Console do Firebase. 

6.  Verifique a janela de saída do IDE para ver **tópicos/notícias** na saída do log: 

    [![Mensagem de /topic/news é mostrada](remote-notifications-with-fcm-images/17-message-arrived-sml.png)](remote-notifications-with-fcm-images/17-message-arrived.png#lightbox)

Quando esta mensagem é exibida na janela de saída, o ícone de notificação também deve aparecer na área de notificação no dispositivo Android. Abra o ícone de notificação para exibir a mensagem de tópico: 

[![A mensagem de tópico é exibida como uma notificação](remote-notifications-with-fcm-images/18-other-news-sml.png)](remote-notifications-with-fcm-images/18-other-news.png#lightbox)

Se você não receber uma mensagem, tente excluir o **FCMClient** aplicativo no dispositivo (ou emulador) e repita as etapas acima. 

## <a name="foreground-notifications"></a>Notificações de primeiro plano

Para receber notificações em aplicativos foregrounded, você deve implementar `FirebaseMessagingService`. Esse serviço também é necessário para o recebimento de cargas de dados e para enviar mensagens de upstream. Os exemplos a seguir ilustram como implementar um serviço que estende o `FirebaseMessagingService` &ndash; o aplicativo resultante será capaz de lidar com notificações remotas enquanto ele está em execução em primeiro plano. 

### <a name="implement-firebasemessagingservice"></a>Implementar FirebaseMessagingService

O `FirebaseMessagingService` serviço inclui um `OnMessageReceived` método que você grava para manipular as mensagens de entrada. Observe que `OnMessageReceived` é invocado para mensagens de notificação *somente* quando o aplicativo está em execução em primeiro plano. Quando o aplicativo é executado em segundo plano, é exibida uma notificação gerada automaticamente (conforme demonstrado anteriormente neste passo a passo). 

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

Observe que o `MESSAGING_EVENT` tentativa filtro deve ser declarado para que novas mensagens FCM são direcionadas para `MyFirebaseMessagingService`: 

```csharp
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
```

Quando o aplicativo cliente recebe uma mensagem de FCM, `OnMessageReceived` extrai o conteúdo da mensagem de transmitido `RemoteMessage` objeto chamando seu `GetNotification` método. Em seguida, ele registra o conteúdo da mensagem, para que ela pode ser exibida na janela de saída do IDE: 

```csharp
Log.Debug(TAG, "From: " + message.From);
Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
```

> [!NOTE]
> Se você definir pontos de interrupção em `FirebaseMessagingService`, sua sessão de depuração pode ou não pode atingir esses pontos de interrupção devido como FCM entrega mensagens.
 

### <a name="send-another-message"></a>Enviar outra mensagem

Desinstalar o aplicativo, recriá-lo, execute-o novamente e siga estas etapas para enviar outra mensagem:

1.  No Console do Firebase, clique em **nova mensagem**. 

2.  Sobre o **redigir mensagem** página, insira o texto da mensagem e selecione **único dispositivo**. 

3.  Copiar a cadeia de caracteres de token da janela de saída do IDE e cole-o no **token de registro FCM** campo do Console do Firebase como antes. 

4.  Certifique-se de que o aplicativo está em execução em primeiro plano e clique em **enviar mensagem** no Console do Firebase: 

    [![Enviar outra mensagem no Console do](remote-notifications-with-fcm-images/19-hello-again-sml.png)](remote-notifications-with-fcm-images/19-hello-again.png#lightbox)

5.  Quando o **mensagem revisão** caixa de diálogo é exibida, clique em **enviar**.

6.  A mensagem de entrada é registrada na janela de saída do IDE:

    [![Corpo da mensagem impressa para a janela de saída](remote-notifications-with-fcm-images/20-logged-message.png)](remote-notifications-with-fcm-images/20-logged-message.png#lightbox)


### <a name="add-a-local-notifications-sender"></a>Adicionar um remetente notificações Local

Neste exemplo restantes, a mensagem de entrada FCM será convertida em uma notificação de local que é iniciada enquanto o aplicativo está em execução em primeiro plano. Editar **MyFirebaseMessageService.cs** e adicione o seguinte `using` instruções: 

```csharp
using FCMClient;
using System.Collections.Generic;
```

Adicione o seguinte método para `MyFirebaseMessagingService`: 

```csharp
void SendNotification(string messageBody, IDictionary<string, string> data)
{
    var intent = new Intent(this, typeof(MainActivity));
    intent.AddFlags(ActivityFlags.ClearTop);
    foreach (string key in data.Keys)
    {
        intent.PutExtra(key, data[key]);
    }
    var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

    var notificationBuilder = new Notification.Builder(this)
        .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
        .SetContentTitle("FCM Message")
        .SetContentText(messageBody)
        .SetAutoCancel(true)
        .SetContentIntent(pendingIntent);

    var notificationManager = NotificationManager.FromContext(this);
    notificationManager.Notify(0, notificationBuilder.Build());
}
```

Para distinguir esta notificação de notificações do plano de fundo, esse código marca notificações com um ícone que é diferente do ícone applicatiion. Adicionar [ic\_stat\_ic\_notification.png](remote-notifications-with-fcm-images/ic-stat-ic-notification.png) para **recursos/drawable** e incluí-lo no **FCMClient** projeto. 

O `SendNotification` método usa `Notification.Builder` para criar a notificação, e `NotificationManager` é usado para iniciar a notificação. A notificação contém um `PendingIntent` que permitirá que o usuário abra o aplicativo e exibir o conteúdo da cadeia de caracteres passada para `messageBody`. Dependendo de versões do Android que você pretende de destino com seu aplicativo, você talvez queira usar `NotificationCompat.Builder` em vez disso. Para obter mais informações sobre `Notification.Builder`, consulte [notificações Local](~/android/app-fundamentals/notifications/local-notifications.md). 

Chamar o `SendNotification` método final de `OnMessageReceived` método: 

```csharp
SendNotification(message.GetNotification().Body, message.Data);
```

Como resultado dessas alterações, `SendNotification` será executado sempre que uma notificação é recebida, enquanto o aplicativo estiver em primeiro plano e a notificação será exibida na área de notificação. Se o aplicativo é backgrounded, `SendNotification` não será executado e, em vez disso, uma notificação de plano de fundo (ilustrada anteriormente) será iniciada. 

### <a name="send-the-last-message"></a>Enviar a última mensagem

Desinstalar o aplicativo, recriá-lo, execute-o novamente e use as etapas a seguir para enviar a última mensagem:

1.  No Console do Firebase, clique em **nova mensagem**. 

2.  Sobre o **redigir mensagem** página, insira o texto da mensagem e selecione **único dispositivo**. 

3.  Copiar a cadeia de caracteres de token da janela de saída do IDE e cole-o no **token de registro FCM** campo do Console do Firebase como antes. 

4.  Certifique-se de que o aplicativo está em execução em primeiro plano e clique em **enviar mensagem** no Console do Firebase: 

    [![Enviar a mensagem de primeiro plano](remote-notifications-with-fcm-images/21-console-fg-msg-sml.png)](remote-notifications-with-fcm-images/21-console-fg-msg.png#lightbox)

Neste momento, a mensagem que foi registrada na janela de saída também é fornecida em uma nova notificação &ndash; o ícone de notificação aparece na bandeja de notificação enquanto o aplicativo está em execução em primeiro plano: 

[![Ícone de notificação de mensagem do primeiro plano](remote-notifications-with-fcm-images/22-foreground-icon-sml.png)](remote-notifications-with-fcm-images/22-foreground-icon.png#lightbox)

Quando você abre a notificação, você verá a última mensagem enviada da GUI Firebase Console notificações: 

[![Notificação de primeiro plano mostrada com ícone de primeiro plano](remote-notifications-with-fcm-images/23-foreground-msg-sml.png)](remote-notifications-with-fcm-images/23-foreground-msg.png#lightbox)


## <a name="disconnecting-from-fcm"></a>Desconectando FCM

Para cancelar a assinatura de um tópico, chame o [UnsubscribeFromTopic](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging.html#unsubscribeFromTopic%28java.lang.String%29) método sobre o [FirebaseMessaging](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging) classe. Por exemplo, para cancelar a assinatura de _notícias_ tópico assinado anteriormente, um **Unsubscribe** botão foi adicionado ao layout com o seguinte código de manipulador:

```csharp
var unSubscribeButton = FindViewById<Button>(Resource.Id.unsubscribeButton);
unSubscribeButton.Click += delegate {
    FirebaseMessaging.Instance.UnsubscribeFromTopic("news");
    Log.Debug(TAG, "Unsubscribed from remote notifications");
};
```

Para cancelar o registro do dispositivo de totalmente FCM, excluir a ID de instância, chamando o [DeleteInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId.html#deleteInstanceId%28%29) método sobre o [FirebaseInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId) classe. Por exemplo:

```csharp
FirebaseInstanceId.Instance.DeleteInstanceId();
```

Essa chamada de método exclui a ID de instância e os dados associados a ele. Como resultado, o envio periódica de dados FCM para o dispositivo é interrompido.

 
## <a name="troubleshooting"></a>Solução de problemas

A seguir descrevem os problemas e soluções alternativas que podem ocorrer ao usar mensagens de nuvem Firebase com xamarin.

### <a name="firebaseapp-is-not-initialized"></a>FirebaseApp não foi inicializado

Em alguns casos, você verá essa mensagem de erro:

```shell
Java.Lang.IllegalStateException: Default FirebaseApp is not initialized in this process
Make sure to call FirebaseApp.initializeApp(Context) first.
```

Esse é um problema conhecido que você pode contornar a solução de limpeza e recompilar o projeto (**Construir > Limpar solução**, **Build > recompilar solução**). Para obter mais informações, consulte [discussão de fórum](https://forums.xamarin.com/discussion/96263/default-firebaseapp-is-not-initialized-in-this-process).


## <a name="summary"></a>Resumo

Este passo a passo detalhadas as etapas para implementar notificações de remoto Firebase mensagens de nuvem em um aplicativo xamarin. Ele descrito como instalar os pacotes necessários necessários para comunicações FCM e ele explicado como configurar o manifesto do Android para o acesso a servidores FCM. Ele fornecido o código de exemplo que ilustra como verificar a presença de serviços do Google reproduzir. Isso demonstrou como implementar um serviço de escuta de ID de instância que negocia com FCM para um token de registro e ele explicado como esse código cria notificações de plano de fundo enquanto o aplicativo é backgrounded. Ele explicado como assinar mensagens do tópico, e ele tiver fornecido um exemplo de implementação de um serviço de escuta de mensagem que é usado para receber e exibir notificações remotas enquanto o aplicativo está em execução em primeiro plano. 


## <a name="related-links"></a>Links relacionados

- [FCMNotifications (exemplo)](https://developer.xamarin.com/samples/monodroid/Firebase/FCMNotifications)
- [Mensagens na nuvem do Firebase](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
