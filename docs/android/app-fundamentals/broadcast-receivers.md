---
title: Receptores de difusão em xamarin
description: Esta seção discute como usar um receptor de difusão.
ms.prod: xamarin
ms.assetid: B2727160-12F2-43EE-84B5-0B15C8FCF4BD
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 04/20/2018
ms.openlocfilehash: 6b2e316eaf67e51801be4fcd670e80ec81c8ff08
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935393"
---
# <a name="broadcast-receivers-in-xamarinandroid"></a>Receptores de difusão em xamarin

_Esta seção discute como usar um receptor de difusão._

## <a name="broadcast-receiver-overview"></a>Visão geral do receptor de difusão

Um _receptor difusão_ é um componente do Android que permite que um aplicativo responder às mensagens (um Android [ `Intent` ](https://developer.xamarin.com/api/type/Android.Content.Intent/)) que são transmitidas pelo sistema operacional Android ou por um aplicativo. Execute as transmissões um _de publicação / assinatura_ modelo &ndash; um evento faz com que uma difusão para ser publicado e recebidas por esses componentes que estejam interessados no evento. 

Android identifica dois tipos de difusões:

* **Difusão explícita** &ndash; esses tipos de difusões direcione um aplicativo específico. O uso mais comum de uma difusão explícita é iniciar uma atividade. Um exemplo de uma difusão explícito quando um aplicativo precisa para discar um número de telefone. ele enviará uma intenção que tem como alvo o aplicativo de telefone no Android e passar ao longo do número de telefone a ser discado. Android, em seguida, encaminhe a intenção para o aplicativo de telefone.
* **Difusão implícita** &ndash; essas difusões são despachados para todos os aplicativos no dispositivo. Um exemplo de uma difusão implícita é o `ACTION_POWER_CONNECTED` intenção. A intenção é publicada sempre que Android detecta que a bateria do dispositivo está carregando. Android roteará essa intenção a todos os aplicativos que foram registrados para este evento.

O receptor de difusão é uma subclasse do `BroadcastReceiver` devem substituir o tipo e o [ `OnReceive` ](https://developer.xamarin.com/api/member/Android.Content.BroadcastReceiver.OnReceive/p/Android.Content.Context/Android.Content.Intent/) método. Android executará `OnReceive` no thread principal, então esse método deve ser projetado para ser executado rapidamente. Tome cuidado ao reproduzir threads no `OnReceive` porque Android pode encerrar o processo quando o método é concluído. Se um receptor de difusão deve executar o trabalho de longa execução, é recomendável agendar uma _trabalho_ usando o `JobScheduler` ou _Firebase trabalho Dispatcher_. Agendar o trabalho com um trabalho será discutido em uma guia separada.

Um _filtro intenção_ é usado para registrar um receptor de difusão de modo que Android corretamente pode rotear mensagens. O filtro tentativa pode ser especificado em tempo de execução (às vezes, isso é conhecido como um _contexto registrado receptor_ ou como _registro dinâmico_) ou pode ser definido estaticamente no Android (um demanifesto_registrado manifesto receptor_). Xamarin fornece um atributo de c#, `IntentFilterAttribute`, estaticamente, que registrará o filtro intencional (Isso será discutido em mais detalhes posteriormente neste guia). A partir do Android 8.0, não é possível para um aplicativo registrar estaticamente para uma difusão implícita.

A principal diferença entre o receptor registrado de manifesto e o receptor contexto registrado é que um destinatário de contexto registrado só responderá a transmissões enquanto um aplicativo está em execução, enquanto um receptor registrado manifesto pode responder a transmite o mesmo que o aplicativo pode não estar em execução.  

Há dois conjuntos de APIs para gerenciar um receptor de difusão e transmissões de envio:

1. **`Context`** &ndash; O `Android.Content.Context` classe pode ser usada para registrar um receptor de difusão que responde a eventos de todo o sistema. O `Context` também é usado para publicar as transmissões de todo o sistema.
2. **[`LocalBroadcastManager`](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))** &ndash; Esta é uma API que está disponível por meio de [pacote do NuGet biblioteca de suporte de Xamarin v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Essa classe é usada para manter as transmissões e receptores difusão isolados no contexto do aplicativo que está utilizando. Essa classe pode ser útil para impedir que outros aplicativos de responder a difusões somente para aplicativos ou enviar mensagens para destinatários privados.

Um receptor de difusão não pode exibir caixas de diálogo e é recomendado para iniciar uma atividade de dentro de um receptor de difusão. Se um receptor de difusão deve notificar o usuário, ele deve publicar uma notificação.

Não é possível vincular a ou iniciar um serviço de dentro de um receptor de difusão. 

Este guia aborda como criar um receptor de difusão e como registrá-lo para que ele pode receber transmissões.

## <a name="creating-a-broadcast-receiver"></a>Criar um receptor de difusão

Para criar um receptor de difusão em xamarin, um aplicativo deve subclasse o `BroadcastReceiver` classe, Adornar com o `BroadcastReceiverAttribute`e substituir o `OnReceive` método:

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

Quando o xamarin compila a classe, ele também atualizará o AndroidManifest com os metadados necessários para registrar o receptor. Para um destinatário de difusão estaticamente registrado, o `Enabled` corretamente deve ser definido como `true`, caso contrário Android não poderá criar uma instância do receptor.
 
O `Exported` propriedade controla se o receptor difusão pode receber mensagens de fora do aplicativo. Se a propriedade não é definida explicitamente, o valor padrão da propriedade é determinado pelo Android com base em se não houver nenhum filtro de intenção associado com o receptor de difusão. Se houver pelo menos um filtro de intenção de para o receptor de difusão, Android assumirá que o `Exported` é de propriedade `true`. Se há filtros intenção associados com o receptor de difusão, Android assumirá que o valor é `false`. 

O `OnReceive` método recebe uma referência para o `Intent` que foi distribuída para o receptor de difusão. Isso faz com que é possível que o remetente da intenção de passar valores para o receptor de difusão.

### <a name="statically-registering-a-broadcast-receiver-with-an-intent-filter"></a>Estaticamente Registrando um receptor de difusão de uma tentativa de filtro

Quando um `BroadcastReceiver` é decorado com o [ `IntentFilterAttribute` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/), xamarin adicionará necessários `<intent-filter>` elemento para o Android manifesto em tempo de compilação. O trecho a seguir é um exemplo de um receptor de difusão que será executado quando um dispositivo concluiu a inicialização (se as permissões de Android apropriadas foram concedidas pelo usuário):

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

Também é possível criar um filtro de intenção responderá às tentativas personalizadas. Considere o exemplo a seguir: 

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

Aplicativos que se destinam a 8.0 Android (API nível 26) ou superior não pode estaticamente registrar uma difusão implícita. Aplicativos estaticamente ainda podem se registrar para uma transmissão explícita. Há uma pequena lista de difusões implícitas que são isentos dessa restrição. Essas exceções são descritas no [implícita exceções difusão](https://developer.android.com/guide/components/broadcast-exceptions.html) guia na documentação do Android. Aplicativos que estão interessados em transmissões implícita devem fazer isso dinamicamente usando a `RegisterReceiver` método. Isso é descrito a seguir.

### <a name="context-registering-a-broadcast-receiver"></a>Registro de contexto de um receptor de difusão

Contexto de registro (também conhecido como registro dinâmico) de um destinatário é executado invocando o `RegisterReceiver` método e o receptor difusão devem ser cancelado com uma chamada para o `UnregisterReceiver` método. Para evitar o vazamento de recursos, é importante cancelar o registro o receptor quando ele não é mais relevante para o contexto (a atividade ou serviço). Por exemplo, um serviço pode transmitir uma tentativa para informar uma atividade que as atualizações estão disponíveis para serem exibidos ao usuário. Quando a atividade for iniciado, ele seria registrar para os propósitos. Quando a atividade é movida para o plano de fundo e não mais visível para o usuário, ele deve cancelar o registro o receptor porque a interface do usuário para exibir as atualizações não estiver mais visível. O trecho de código a seguir é um exemplo de como registrar e cancelar o registro de um receptor de difusão no contexto de uma atividade:

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

No exemplo anterior, quando a atividade é colocado em primeiro plano, ele registrará um receptor de difusão escutará para um propósito personalizado usando o `OnResume` método de ciclo de vida. Como a atividade é movido para o plano de fundo, a `OnPause()` método cancelará o receptor.

## <a name="publishing-a-broadcast"></a>Publicando uma difusão

Uma difusão pode ser publicada para todos os aplicativos instalados no dispositivo de criação de um objeto de propósito e expedição com o `SendBroadcast` ou `SendOrderedBroadcast` método.  

1. **Métodos de Context.SendBroadcast** &ndash; há várias implementações do método.
   Esses métodos serão transmitidas a intenção de todo o sistema. Receptores de difusão obterem receber a intenção em uma ordem indeterminada. Isso proporciona uma grande flexibilidade, mas significa que é possível para outros aplicativos registrar e receber a intenção. Isso pode representar um risco à segurança. Aplicativos talvez seja necessário implementar a segurança de adição para evitar acesso não autorizado. Uma solução possível é usar o `LocalBroadcastManager` que enviará apenas mensagens dentro do espaço privado do aplicativo. Este trecho de código é um exemplo de como despachar uma tentativa de usar uma da `SendBroadcast` métodos:

   ```csharp
   Intent message = new Intent("com.xamarin.example.TEST");
   // If desired, pass some values to the broadcast receiver.
   intent.PutExtra("key", "value");
   SendBroadcast(intent);
   ```

    Este trecho de código é outro exemplo de como enviar uma transmissão usando o `Intent.SetAction` método para identificar a ação:

    ```csharp 
    Intent intent = new Intent();
    intent.SetAction("com.xamarin.example.TEST");
    intent.PutExtra("key", "value");
    SendBroadcast(intent);
    ```

2. **Context.SendOrderedBroadcast** &ndash; este é o método é muito semelhante ao `Context.SendBroadcast`, com a diferença é que a intenção será publicado quando para destinatários na ordem em que o recievers foram registrados.

### <a name="localbroadcastmanager"></a>LocalBroadcastManager

O [v4 da biblioteca de suporte de Xamarin](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) fornece uma classe auxiliar chamada [ `LocalBroadcastManager` ](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html). O `LocalBroadcastManager` destina-se a aplicativos que não deseja enviar ou receber transmissões de outros aplicativos no dispositivo. O `LocalBroadcastManager` publicará somente mensagens dentro do contexto do aplicativo e apenas os destinatários difusão que estão registrados com o `LocalBroadcastManager`. Este trecho de código é um exemplo de registro de um receptor de difusão com `LocalBroadcastManager`:

```csharp
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this). RegisterReceiver(receiver, new IntentFilter("com.xamarin.example.TEST"));
```

Outros aplicativos no dispositivo não podem receber as mensagens que são publicadas com o `LocalBroadcastManager`. Este trecho de código mostra como despachar uma intenção usando o `LocalBroadcastManager`:

```csharp
Intent message = new Intent("com.xamarin.example.TEST");
// If desired, pass some values to the broadcast receiver.
intent.PutExtra("key", "value");
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this).SendBroadcast(message);
```

## <a name="related-links"></a>Links relacionados

- [BroadcastReceiver](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiver/)
- [Context.RegisterReceiver](https://developer.xamarin.com/api/member/Android.Content.Context.RegisterReceiver/p/Android.Content.BroadcastReceiver/Android.Content.IntentFilter/System.String/Android.OS.Handler/)
- [Context.SendBroadcast](https://developer.xamarin.com/api/member/Android.Content.Context.SendBroadcast/p/Android.Content.Intent/)
- [Context.UnregisterReceiver](https://developer.xamarin.com/api/member/Android.Content.Context.UnregisterReceiver/p/Android.Content.BroadcastReceiver/)
- [Tentativa de](https://developer.xamarin.com/api/type/Android.Content.Intent/)
- [IntentFilter](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)
- [LocalBroadcastManager](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))
- [Notificações de locais no Android](~/android/app-fundamentals/notifications/local-notifications.md)
- [Suporte a Android v4 de biblioteca](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
