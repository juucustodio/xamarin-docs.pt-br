---
title: Receptores de difusão no xamarin. Android
description: Esta seção discute como usar um receptor de difusão.
ms.prod: xamarin
ms.assetid: B2727160-12F2-43EE-84B5-0B15C8FCF4BD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/20/2018
ms.openlocfilehash: a411d4d85877c9868ec49f92b53ca8d7a81f9959
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61018636"
---
# <a name="broadcast-receivers-in-xamarinandroid"></a>Receptores de difusão no xamarin. Android

_Esta seção discute como usar um receptor de difusão._

## <a name="broadcast-receiver-overview"></a>Visão geral do receptor de difusão

Um _receptor de difusão_ é um componente do Android que permite que um aplicativo responder às mensagens (um Android [ `Intent` ](https://developer.xamarin.com/api/type/Android.Content.Intent/)) que são transmitidos pelo sistema operacional Android ou por um aplicativo. Execute as difusões uma _de publicação / assinatura_ modelo &ndash; um evento faz com que uma difusão ser publicado e recebidos por esses componentes que estejam interessados no evento.

Android identifica dois tipos de transmissões de:

* **Transmissão explícita** &ndash; esses tipos de transmissões de direcionar um aplicativo específico. O uso mais comum de uma transmissão explícita é iniciar uma atividade. Um exemplo de uma transmissão explícita quando um aplicativo precisa para discar um número de telefone. ele enviará uma intenção que tem como alvo o aplicativo de telefone no Android e passe junto o número de telefone a ser discado. Android, em seguida, encaminhe a intenção para o aplicativo de telefone.
* **Difusão implícita** &ndash; essas transmissões são expedidos para todos os aplicativos no dispositivo. Um exemplo de uma transmissão implícita é o `ACTION_POWER_CONNECTED` intenção. A intenção é publicada sempre que Android detecta que a bateria do dispositivo está carregando. Android roteará este destina-se a todos os aplicativos que foram registrados para este evento.

O receptor de difusão é uma subclasse do `BroadcastReceiver` devem substituir o tipo e o [ `OnReceive` ](https://developer.xamarin.com/api/member/Android.Content.BroadcastReceiver.OnReceive/p/Android.Content.Context/Android.Content.Intent/) método. Android executará `OnReceive` no thread principal, portanto, esse método deve ser projetado para executar rapidamente. Tome cuidado ao reproduzir os threads em `OnReceive` porque Android pode finalizar o processo quando o método for concluído. Se um receptor de difusão deve executar o trabalho de longa execução, é recomendável agendar uma _trabalho_ usando o `JobScheduler` ou o _Dispatcher de trabalho do Firebase_. Agendar o trabalho com um trabalho será discutida em um guia separado.

Uma _filtro intencional_ é usada para registrar um receptor de difusão para que o Android pode rotear corretamente as mensagens. Filtro de intenção pode ser especificado em tempo de execução (às vezes, isso é conhecido como um _receptor contexto registrado_ ou como _registro dinâmico_) ou podem ser estaticamente definido no manifesto do Android (um _registrado de manifesto receptor_). Xamarin. Android fornece um C# atributo, `IntentFilterAttribute`, que registrará estaticamente ao filtro intencional (Isso será discutido em mais detalhes mais adiante neste guia). A partir do Android 8.0, não é possível para um aplicativo registrar estaticamente para uma difusão implícita.

A principal diferença entre o receptor registrado de manifesto e o receptor de contexto registrado é que um receptor de contexto registrado só responderá a difusões enquanto um aplicativo está em execução, enquanto um receptor registrado o manifesto pode responder a transmite o mesmo que o aplicativo pode não estar em execução.  

Há dois conjuntos de APIs para gerenciar um receptor de difusão e enviar difusões:

1. **`Context`** &ndash; O `Android.Content.Context` classe pode ser usada para registrar um receptor de difusão que responderá a eventos de todo o sistema. O `Context` também é usado para publicar as difusões de todo o sistema.
2. **[`LocalBroadcastManager`](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))** &ndash; Esta é uma API que está disponível por meio de [pacote do NuGet biblioteca de suporte de Xamarin v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Essa classe é usada para manter a difusões e receptores de difusão isolados no contexto do aplicativo que está utilizando. Essa classe pode ser útil para impede que outros aplicativos respondendo a difusões somente no aplicativo ou enviar mensagens para destinatários privados.

Um receptor de difusão não pode exibir caixas de diálogo, e é altamente desaconselhável para iniciar uma atividade de dentro de um receptor de difusão. Se um receptor de difusão deve notificar o usuário, ele deve publicar uma notificação.

Não é possível associar a ou iniciar um serviço de dentro de um receptor de difusão. 

Este guia aborda como criar um receptor de difusão e como registrá-lo para que ele poderá receber transmissões.

## <a name="creating-a-broadcast-receiver"></a>Criar um receptor de difusão

Para criar um receptor de difusão no xamarin. Android, um aplicativo deve subclasse de `BroadcastReceiver` de classe, adorná-lo com o `BroadcastReceiverAttribute`e substituir o `OnReceive` método:

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

Quando o xamarin. Android compila a classe, ele também atualizará o AndroidManifest com os metadados necessários para registrar o receptor. Para um receptor de difusão registrado estaticamente, o `Enabled` corretamente, deve ser definida como `true`, caso contrário Android não poderá criar uma instância do receptor.
 
O `Exported` propriedade controla se o receptor de difusão pode receber mensagens de fora do aplicativo. Se a propriedade não é explicitamente definida, o valor padrão da propriedade é determinado pelo Android com base em se há quaisquer filtros de intenção associados com o receptor de difusão. Se houver pelo menos um filtro de intenção para o receptor de difusão, Android assumirá que o `Exported` é de propriedade `true`. Se existem filtros intenção associados com o receptor de difusão, Android pressupor que o valor é `false`. 

O `OnReceive` método recebe uma referência para o `Intent` que foi distribuída para o receptor de difusão. Isso faz com que é possível que o remetente da intenção para passar valores para o receptor de difusão.

### <a name="statically-registering-a-broadcast-receiver-with-an-intent-filter"></a>Registrando estaticamente um receptor de difusão com um filtro de intenção

Quando um `BroadcastReceiver` decorada com o [ `IntentFilterAttribute` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/), xamarin. Android adicionará o necessário `<intent-filter>` elemento para o Android manifesto em tempo de compilação. O trecho a seguir está um exemplo de um receptor de difusão que será executado quando um dispositivo tiver terminado de inicialização (se as permissões do Android apropriadas foram concedidas pelo usuário):

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

Também é possível criar um filtro de intenção que responderão às intenções personalizadas. Considere o exemplo a seguir: 

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

Aplicativos destinados a Android 8.0 (API nível 26) ou superior não pode estaticamente se registrar para uma difusão implícita. Aplicativos estaticamente ainda podem se registrar para uma transmissão explícita. Há uma pequena lista de transmissões implícitas que são isentos dessa restrição. Essas exceções são descritas na [implícita de exceções de difusão](https://developer.android.com/guide/components/broadcast-exceptions.html) guide na documentação do Android. Aplicativos que estejam interessados em transmissões implícitas devem fazer isso dinamicamente usando o `RegisterReceiver` método. Isso é descrito a seguir.

### <a name="context-registering-a-broadcast-receiver"></a>Registro de contexto de um receptor de difusão

Contexto de registro (também conhecido como registro dinâmico) de um destinatário é executado invocando o `RegisterReceiver` método e o receptor de difusão devem ser cancelado com uma chamada para o `UnregisterReceiver` método. Para evitar vazamento de recursos, é importante cancelar o registro o destinatário quando não é mais relevante para o contexto (a atividade ou um serviço). Por exemplo, um serviço pode transmitir uma intenção para informar uma atividade que atualizações estão disponíveis para serem exibidos ao usuário. Quando a atividade é iniciado, ele faria se registrar para essas intenções. Quando a atividade é movida para o plano de fundo e não é mais visível para o usuário, ele deve cancelar o registro o receptor porque a interface do usuário para exibir as atualizações não está mais visível. O trecho de código a seguir está um exemplo de como registrar e cancelar o registro de um receptor de difusão no contexto de uma atividade:

```csharp
[Activity(Label = "MainActivity", MainLauncher = true, Icon = "@mipmap/icon")]
public class MainActivity: Activity 
{
    MySampleBroadcastReceiver receiver;

    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        receiver = new MySampleBroadcastReceiver()

        // Code omitted for clarity
    }

    protected override OnResume() 
    {
        base.OnResume();
        RegisterReceiver(receiver, new IntentFilter("com.xamarin.example.TEST"));
        // Code omitted for clarity
    }

    protected override OnPause() 
    {
        UnregisterReceiver(receiver);
        // Code omitted for clarity
        base.OnPause();
    }
}
```

No exemplo anterior, quando a atividade entra em primeiro plano, ele registrará um receptor de difusão que escutará uma intenção personalizada usando o `OnResume` método de ciclo de vida. Conforme se move a atividade em segundo plano, o `OnPause()` método cancelará o receptor.

## <a name="publishing-a-broadcast"></a>Publicando uma difusão

Uma difusão pode ser publicada para todos os aplicativos instalados no dispositivo, criação de um objeto a intenção e expedição dele com o `SendBroadcast` ou o `SendOrderedBroadcast` método.  

1. **Métodos de Context.SendBroadcast** &ndash; há várias implementações desse método.
   Esses métodos irá transmitir a intenção de todo o sistema. Receptores de difusão que receberão a intenção em uma ordem indeterminada. Isso proporciona uma grande flexibilidade, mas significa que é possível que outros aplicativos para se registrar e receber a intenção. Isso pode representar um risco de segurança. Aplicativos, talvez seja necessário implementar a segurança de adição para evitar acesso não autorizado. Uma solução possível é usar o `LocalBroadcastManager` que enviará apenas mensagens dentro do espaço privado do aplicativo. Este trecho de código é um exemplo de como enviar uma intenção usando um do `SendBroadcast` métodos:

   ```csharp
   Intent message = new Intent("com.xamarin.example.TEST");
   // If desired, pass some values to the broadcast receiver.
   message.PutExtra("key", "value");
   SendBroadcast(message);
   ```

    Este trecho de código é outro exemplo de envio de uma transmissão usando o `Intent.SetAction` método para identificar a ação:

    ```csharp
    Intent intent = new Intent();
    intent.SetAction("com.xamarin.example.TEST");
    intent.PutExtra("key", "value");
    SendBroadcast(intent);
    ```

2. **Context.SendOrderedBroadcast** &ndash; isso é o método é muito semelhante ao `Context.SendBroadcast`, com a diferença é que a intenção será publicado um momento para os destinatários na ordem em que os receptores foram registrados.

### <a name="localbroadcastmanager"></a>LocalBroadcastManager

O [biblioteca de suporte de Xamarin v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) fornece uma classe auxiliar chamada [ `LocalBroadcastManager` ](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html). O `LocalBroadcastManager` é destinado a aplicativos que não deseja enviar ou receber transmissões de outros aplicativos no dispositivo. O `LocalBroadcastManager` publicará apenas mensagens dentro do contexto do aplicativo e apenas para os receptores de difusão que estão registrados com o `LocalBroadcastManager`. Este trecho de código é um exemplo de registro de um receptor de difusão com `LocalBroadcastManager`:

```csharp
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this). RegisterReceiver(receiver, new IntentFilter("com.xamarin.example.TEST"));
```

Outros aplicativos no dispositivo não podem receber as mensagens que são publicadas com o `LocalBroadcastManager`. Este trecho de código mostra como enviar uma intenção usando o `LocalBroadcastManager`:

```csharp
Intent message = new Intent("com.xamarin.example.TEST");
// If desired, pass some values to the broadcast receiver.
message.PutExtra("key", "value");
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this).SendBroadcast(message);
```

## <a name="related-links"></a>Links relacionados

- [BroadcastReceiver API](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiver/)
- [Context.RegisterReceiver API](https://developer.xamarin.com/api/member/Android.Content.Context.RegisterReceiver/p/Android.Content.BroadcastReceiver/Android.Content.IntentFilter/System.String/Android.OS.Handler/)
- [Context.SendBroadcast API](https://developer.xamarin.com/api/member/Android.Content.Context.SendBroadcast/p/Android.Content.Intent/)
- [Context.UnregisterReceiver API](https://developer.xamarin.com/api/member/Android.Content.Context.UnregisterReceiver/p/Android.Content.BroadcastReceiver/)
- [API de intenção](https://developer.xamarin.com/api/type/Android.Content.Intent/)
- [IntentFilter API](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)
- [LocalBroadcastManager (documentos do Android)](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))
- [Notificações locais no Android](~/android/app-fundamentals/notifications/local-notifications.md) guia
- [Suporte a Android v4 de biblioteca (NuGet)](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
