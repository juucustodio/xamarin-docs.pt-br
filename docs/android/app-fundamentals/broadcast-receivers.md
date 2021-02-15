---
title: Receptores de difusão no Xamarin. Android
description: Esta seção discute como usar um receptor de difusão.
ms.prod: xamarin
ms.assetid: B2727160-12F2-43EE-84B5-0B15C8FCF4BD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/20/2018
ms.openlocfilehash: 9e702bdb31563588d20b9cd70e35b67aefe67e0c
ms.sourcegitcommit: d1980b2251999224e71c1289e4b4097595b7e261
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92928367"
---
# <a name="broadcast-receivers-in-xamarinandroid"></a>Receptores de difusão no Xamarin. Android

_Esta seção discute como usar um receptor de difusão._

## <a name="broadcast-receiver-overview"></a>Visão geral do receptor de difusão

Um _receptor de difusão_ é um componente do Android que permite que um aplicativo responda às mensagens (um Android [`Intent`](xref:Android.Content.Intent) ) que são transmitidas pelo sistema operacional Android ou por um aplicativo. As difusões seguem um modelo de _publicação/assinatura_ &ndash; um evento faz com que uma difusão seja publicada e recebida por esses componentes interessados no evento.

O Android identifica dois tipos de difusões:

- **Difusão explícita** &ndash; Esses tipos de difusão visam um aplicativo específico. O uso mais comum de uma difusão explícita é iniciar uma atividade. Um exemplo de uma difusão explícita quando um aplicativo precisa discar um número de telefone; Ele enviará uma intenção que tem como alvo o aplicativo de telefone no Android e passa o número de telefone a ser discado. Em seguida, o Android roteará a intenção para o aplicativo de telefone.
- **Difusão implícita** &ndash; Essas difusões são expedidas para todos os aplicativos no dispositivo. Um exemplo de difusão implícita é a `ACTION_POWER_CONNECTED` intenção. Essa intenção é publicada toda vez que o Android detecta que a bateria no dispositivo está carregando. O Android roteará essa intenção para todos os aplicativos que se registraram para esse evento.

O receptor de difusão é uma subclasse do `BroadcastReceiver` tipo e deve substituir o [`OnReceive`](xref:Android.Content.BroadcastReceiver.OnReceive*) método. O Android será executado `OnReceive` no thread principal, portanto, esse método deve ser projetado para ser executado rapidamente. Deve-se ter cuidado ao gerar threads no, `OnReceive` pois o Android pode encerrar o processo quando o método for concluído. Se um receptor de difusão precisar executar o trabalho de longa execução, é recomendável agendar um _trabalho_ usando o `JobScheduler` ou o _Dispatcher do trabalho firebase_ . O agendamento do trabalho com um trabalho será discutido em um guia separado.

Um _filtro de intenção_ é usado para registrar um receptor de difusão para que o Android possa rotear mensagens corretamente. O filtro de intenção pode ser especificado em tempo de execução (às vezes, isso é chamado de _receptor de contexto registrado_ ou de _registro dinâmico_ ) ou pode ser definido estaticamente no manifesto do Android (um _receptor registrado pelo manifesto_ ). O Xamarin. Android fornece um atributo C#, `IntentFilterAttribute` , que registrará estaticamente o filtro de intenção (isso será discutido em mais detalhes posteriormente neste guia). A partir do Android 8,0, não é possível que um aplicativo se registre estaticamente para uma difusão implícita.

A principal diferença entre o receptor registrado pelo manifesto e o receptor registrado pelo contexto é que um receptor registrado pelo contexto responderá apenas a difusões enquanto um aplicativo estiver em execução, enquanto um receptor registrado pelo manifesto pode responder a difusões, mesmo que o aplicativo possa não estar em execução.  

Há dois conjuntos de APIs para gerenciar um receptor de difusão e enviar difusões:

1. **`Context`**&ndash;A `Android.Content.Context` classe pode ser usada para registrar um receptor de difusão que responderá a eventos de todo o sistema. O `Context` também é usado para publicar difusões de todo o sistema.
2. **[`LocalBroadcastManager`](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))**&ndash;Esta é uma API que está disponível por meio do [pacote NuGet da biblioteca de suporte do Xamarin v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Essa classe é usada para manter difusões e receptores de difusão isolados no contexto do aplicativo que os está usando. Essa classe pode ser útil para impedir que outros aplicativos respondam a difusões somente do aplicativo ou enviem mensagens para destinatários particulares.

Um receptor de difusão pode não exibir caixas de diálogo e não é recomendável iniciar uma atividade dentro de um receptor de difusão. Se um receptor de difusão precisar notificar o usuário, ele deverá publicar uma notificação.

Não é possível associar ou iniciar um serviço de dentro de um receptor de difusão.

Este guia explicará como criar um receptor de difusão e como registrá-lo para que ele possa receber difusões.

## <a name="creating-a-broadcast-receiver"></a>Criando um receptor de difusão

Para criar um receptor de difusão no Xamarin. Android, um aplicativo deve subclassear a `BroadcastReceiver` classe, adorna-la com o `BroadcastReceiverAttribute` e substituir o `OnReceive` método:

```csharp
[BroadcastReceiver(Enabled = true, Exported = false)]
public class SampleReceiver : BroadcastReceiver
{
    public override void OnReceive(Context context, Intent intent)
    {
        // Do stuff here.

        String value = intent.GetStringExtra("key");
    }
}
```

Quando o Xamarin. Android compila a classe, ele também atualizará o AndroidManifest com os metadados necessários para registrar o receptor. Para um receptor de difusão estaticamente registrado, o `Enabled` deve ser adequadamente definido como `true` ; caso contrário, o Android não será capaz de criar uma instância do receptor.

A `Exported` propriedade controla se o receptor de difusão pode receber mensagens de fora do aplicativo. Se a propriedade não estiver definida explicitamente, o valor padrão da propriedade será determinado pelo Android com base em se houver filtros de intenção associados ao receptor de difusão. Se houver pelo menos um filtro de intenção para o receptor de difusão, o Android irá pressupor que a `Exported` propriedade é `true` . Se não houver nenhum filtro de intenção associado ao receptor de difusão, o Android assumirá que o valor é `false` .

O `OnReceive` método recebe uma referência ao `Intent` que foi expedida para o receptor de difusão. Isso possibilita que o remetente da intenção passe valores para o receptor de difusão.

### <a name="statically-registering-a-broadcast-receiver-with-an-intent-filter"></a>Registrando estaticamente um receptor de difusão com um filtro de intenção

Quando um `BroadcastReceiver` é decorado com o [`IntentFilterAttribute`](xref:Android.App.IntentFilterAttribute) , o Xamarin. Android adicionará o `<intent-filter>` elemento necessário ao manifesto do Android no momento da compilação. O trecho a seguir é um exemplo de um receptor de difusão que será executado quando um dispositivo terminar a inicialização (se as permissões do Android apropriadas foram concedidas pelo usuário):

```csharp
[BroadcastReceiver(Enabled = true)]
[IntentFilter(new[] { Android.Content.Intent.ActionBootCompleted })]
public class MyBootReceiver : BroadcastReceiver
{
    public override void OnReceive(Context context, Intent intent)
    {
        // Work that should be done when the device boots.     
    }
}
```

> [!NOTE]
> No Android 8,0 (API 26 e superior), o [Google colocou limitações](https://developer.android.com/about/versions/oreo/background) sobre o que os aplicativos podem fazer, enquanto os usuários não estão interagindo diretamente com eles. Essas limitações afetam os serviços em segundo plano e os receptores de difusão implícitas, como `Android.Content.Intent.ActionBootCompleted` . Devido a essas limitações, você pode ter dificuldades para registrar um `Boot Completed` receptor de difusão em versões mais recentes do Android. Se esse for o caso, observe que essas restrições não se aplicam aos serviços de primeiro plano, que podem ser chamados do receptor de difusão.

Também é possível criar um filtro de intenção que responderá a tentativas personalizadas. Considere o seguinte exemplo:

```csharp
[BroadcastReceiver(Enabled = true)]
[IntentFilter(new[] { "com.xamarin.example.TEST" })]
public class MySampleBroadcastReceiver : BroadcastReceiver
{
    public override void OnReceive(Context context, Intent intent)
    {
        // Do stuff here
    }
}
```

Os aplicativos destinados ao Android 8,0 (API nível 26) ou superior podem não se registrar estaticamente em uma difusão implícita. Os aplicativos ainda podem se registrar estaticamente em uma difusão explícita. Há uma pequena lista de difusões implícitas que são isentas dessa restrição. Essas exceções são descritas no guia de [exceções de difusão implícita](https://developer.android.com/guide/components/broadcast-exceptions.html) na documentação do Android. Aplicativos interessados em difusões implícitas devem fazer isso dinamicamente usando o `RegisterReceiver` método. Isso é descrito a seguir.

### <a name="context-registering-a-broadcast-receiver"></a>Context-Registering um receptor de difusão

O registro de contexto (também conhecido como registro dinâmico) de um receptor é executado invocando o `RegisterReceiver` método, e o receptor de difusão deve ter o registro cancelado com uma chamada para o `UnregisterReceiver` método. Para evitar vazamento de recursos, é importante cancelar o registro do receptor quando ele não for mais relevante para o contexto (a atividade ou o serviço). Por exemplo, um serviço pode difundir uma intenção para informar uma atividade de que as atualizações estão disponíveis para serem exibidas ao usuário. Quando a atividade é iniciada, ela se registra para essas intenções. Quando a atividade é movida para o plano de fundo e deixa de ser visível para o usuário, ela deve cancelar o registro do receptor porque a interface do usuário para exibir as atualizações não está mais visível. O trecho de código a seguir é um exemplo de como registrar e cancelar o registro de um receptor de difusão no contexto de uma atividade:

```csharp
[Activity(Label = "MainActivity", MainLauncher = true, Icon = "@mipmap/icon")]
public class MainActivity: Activity
{
    MySampleBroadcastReceiver receiver;

    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        receiver = new MySampleBroadcastReceiver();

        // Code omitted for clarity
    }

    protected override void OnResume()
    {
        base.OnResume();
        RegisterReceiver(receiver, new IntentFilter("com.xamarin.example.TEST"));
        // Code omitted for clarity
    }

    protected override void OnPause()
    {
        UnregisterReceiver(receiver);
        // Code omitted for clarity
        base.OnPause();
    }
}
```

No exemplo anterior, quando a atividade entra em primeiro plano, ela registrará um receptor de difusão que escutará uma intenção personalizada usando o método de `OnResume` ciclo de vida. À medida que a atividade é movida para o plano de fundo, o `OnPause()` método cancelará o registro do receptor.

## <a name="publishing-a-broadcast"></a>Publicando uma difusão

Uma difusão pode ser publicada em todos os aplicativos instalados no dispositivo, criando um objeto de tentativa e expedindo-o com o `SendBroadcast` `SendOrderedBroadcast` método ou.  

1. Métodos Context. **SendBroadcast** &ndash; Há várias implementações desse método.
   Esses métodos vão difundir a intenção para todo o sistema. Receptores de difusão que receberão a intenção em uma ordem indeterminada. Isso proporciona uma grande flexibilidade, mas significa que é possível que outros aplicativos se registrem e recebam a intenção. Isso pode representar um risco de segurança potencial. Os aplicativos podem precisar implementar a segurança adicional para impedir o acesso não autorizado. Uma solução possível é usar o `LocalBroadcastManager` que enviará apenas mensagens no espaço privado do aplicativo. Este trecho de código é um exemplo de como despachar uma intenção usando um dos `SendBroadcast` métodos:

   ```csharp
   Intent message = new Intent("com.xamarin.example.TEST");
   // If desired, pass some values to the broadcast receiver.
   message.PutExtra("key", "value");
   SendBroadcast(message);
   ```

    Esse trecho de código é outro exemplo de envio de uma difusão usando o  `Intent.SetAction` método para identificar a ação:

    ```csharp
    Intent intent = new Intent();
    intent.SetAction("com.xamarin.example.TEST");
    intent.PutExtra("key", "value");
    SendBroadcast(intent);
    ```

2. **Context. SendOrderedBroadcast** &ndash; esse método é muito semelhante a `Context.SendBroadcast` , com a diferença de que a intenção será publicada uma às vezes para os destinatários, na ordem em que os destinatários foram registrados.

### <a name="localbroadcastmanager"></a>LocalBroadcastManager

A [biblioteca de suporte do Xamarin v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) fornece uma classe auxiliar chamada [`LocalBroadcastManager`](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html) . O destina-se a `LocalBroadcastManager` aplicativos que não desejam enviar ou receber difusões de outros aplicativos no dispositivo. O `LocalBroadcastManager` só publicará mensagens dentro do contexto do aplicativo e apenas aos receptores de difusão registrados com o `LocalBroadcastManager` . Este trecho de código é um exemplo de registro de um receptor de difusão com `LocalBroadcastManager` :

```csharp
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this). RegisterReceiver(receiver, new IntentFilter("com.xamarin.example.TEST"));
```

Outros aplicativos no dispositivo não podem receber as mensagens que são publicadas com o `LocalBroadcastManager` . Este trecho de código mostra como despachar uma intenção usando o `LocalBroadcastManager` :

```csharp
Intent message = new Intent("com.xamarin.example.TEST");
// If desired, pass some values to the broadcast receiver.
message.PutExtra("key", "value");
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this).SendBroadcast(message);
```

## <a name="related-links"></a>Links Relacionados

- [API BroadcastReceiver](xref:Android.Content.BroadcastReceiver)
- [API. RegisterReceiver de contexto](xref:Android.Content.Context.RegisterReceiver*)
- [API. SendBroadcast de contexto](xref:Android.Content.Context.SendBroadcast*)
- [API. UnregisterReceiver de contexto](xref:Android.Content.Context.UnregisterReceiver*)
- [API de intenção](xref:Android.Content.Intent)
- [API IntentFilter](xref:Android.App.IntentFilterAttribute)
- [LocalBroadcastManager (documentos do Android)](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))
- [Notificações locais no guia do Android](~/android/app-fundamentals/notifications/local-notifications.md)
- [Biblioteca de suporte do Android V4 (NuGet)](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
