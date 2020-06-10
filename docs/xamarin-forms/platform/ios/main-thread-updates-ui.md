---
Título: "principais atualizações de controle de thread no iOS": "as especificações de plataforma permitem consumir funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma específica do iOS que permite que o layout de controle e as atualizações de renderização sejam executadas no thread principal. "
MS. Prod: xamarin MS. AssetID: 945E711D-9BD2-4BF9-9FB3-CBE0D5B25A49 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 10/24/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="main-thread-control-updates-on-ios"></a>Principais atualizações de controle de thread no iOS

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma do iOS específica habilita o layout de controle e as atualizações de renderização a serem executadas no thread principal, em vez de serem executadas em um thread em segundo plano. Ele deve ser raramente necessário, mas, em alguns casos, pode evitar falhas. Seu consumido em XAML definindo a `Application.HandleControlUpdatesOnMainThread` propriedade vinculável como `true` :

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.HandleControlUpdatesOnMainThread="true">
    ...
</Application>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetHandleControlUpdatesOnMainThread(true);
```

O `Application.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O `Application.SetHandleControlUpdatesOnMainThread` método, no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para controlar se o layout de controle e as atualizações de renderização são executadas no thread principal, em vez de serem executadas em um thread em segundo plano. Além disso, o `Application.GetHandleControlUpdatesOnMainThread` método pode ser usado para retornar se o layout de controle e as atualizações de renderização estão sendo executadas no thread principal.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
