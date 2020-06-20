---
title: Resolução de dependência emXamarin.Forms
description: Este artigo explica como injetar um método de resolução de dependência no Xamarin.Forms para que o contêiner de injeção de dependência de um aplicativo tenha controle sobre a criação e o tempo de vida de renderizadores personalizados, efeitos e implementações de DependencyService.
ms.prod: xamarin
ms.assetid: 491B87DC-14CB-4ADC-AC6C-40A7627B2524
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/27/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ae30b4a4b75906613baf8a2568548c8890ccb33a
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139081"
---
# <a name="dependency-resolution-in-xamarinforms"></a>Resolução de dependência emXamarin.Forms

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo)

_Este artigo explica como injetar um método de resolução de dependência no Xamarin.Forms para que o contêiner de injeção de dependência de um aplicativo tenha controle sobre a criação e o tempo de vida de renderizadores personalizados, efeitos e implementações de DependencyService. Os exemplos de código neste artigo são extraídos da amostra de [resolução de dependência usando contêineres](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo) ._

No contexto de um Xamarin.Forms aplicativo que usa o padrão MVVM (Model-View-ViewModel), um contêiner de injeção de dependência pode ser usado para registrar e resolver modelos de exibição e para registrar serviços e inseri-los em modelos de exibição. Durante a criação do modelo de exibição, o contêiner injeta todas as dependências necessárias. Se essas dependências não tiverem sido criadas, o contêiner criará e resolverá as dependências primeiro. Para obter mais informações sobre injeção de dependência, incluindo exemplos de injeção de dependências em modelos de exibição, consulte [injeção de dependência](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).

O controle sobre a criação e o tempo de vida de tipos em projetos de plataforma é tradicionalmente executado pelo Xamarin.Forms , que usa o `Activator.CreateInstance` método para criar instâncias de renderizadores personalizados, efeitos e [`DependencyService`](xref:Xamarin.Forms.DependencyService) implementações. Infelizmente, isso limita o controle do desenvolvedor sobre a criação e o tempo de vida desses tipos e a capacidade de injetar dependências neles. Esse comportamento pode ser alterado injetando um método de resolução de dependência no Xamarin.Forms que controla como os tipos serão criados – seja pelo contêiner de injeção de dependência do aplicativo ou por Xamarin.Forms . No entanto, observe que não há nenhum requisito para injetar um método de resolução de dependência Xamarin.Forms . Xamarin.Formso continuará criando e gerenciando o tempo de vida dos tipos em projetos de plataforma se um método de resolução de dependência não for injetado.

> [!NOTE]
> Embora este artigo se concentre em injetar um método de resolução de dependências para Xamarin.Forms resolver tipos registrados usando um contêiner de injeção de dependência, também é possível injetar um método de resolução de dependência que usa métodos de fábrica para resolver tipos registrados. Para obter mais informações, consulte a amostra de [resolução de dependência usando métodos de fábrica](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-factoriesdemo) .

## <a name="injecting-a-dependency-resolution-method"></a>Injetando um método de resolução de dependência

A [`DependencyResolver`](xref:Xamarin.Forms.Internals.DependencyResolver) classe fornece a capacidade de injetar um método de resolução de dependência no Xamarin.Forms , usando o [`ResolveUsing`](xref:Xamarin.Forms.Internals.DependencyResolver.ResolveUsing*) método. Em seguida, quando Xamarin.Forms o precisar de uma instância de um tipo específico, o método de resolução de dependência terá a oportunidade de fornecer a instância. Se o método de resolução de dependência retornar `null` para um tipo solicitado, Xamarin.Forms volte a tentar criar a instância de tipo em si usando o `Activator.CreateInstance` método.

O exemplo a seguir mostra como definir o método de resolução de dependência com o [`ResolveUsing`](xref:Xamarin.Forms.Internals.DependencyResolver.ResolveUsing*) método:

```csharp
using Autofac;
using Xamarin.Forms.Internals;
...

public partial class App : Application
{
    // IContainer and ContainerBuilder are provided by Autofac
    static IContainer container;
    static readonly ContainerBuilder builder = new ContainerBuilder();

    public App()
    {
        ...
        DependencyResolver.ResolveUsing(type => container.IsRegistered(type) ? container.Resolve(type) : null);
        ...
    }
    ...
}
```

Neste exemplo, o método de resolução de dependência é definido como uma expressão lambda que usa o contêiner de injeção de dependência Autofac para resolver quaisquer tipos que foram registrados com o contêiner. Caso contrário, `null` será retornado, o que resultará na Xamarin.Forms tentativa de resolver o tipo.

> [!NOTE]
> A API usada por um contêiner de injeção de dependência é específica para o contêiner. Os exemplos de código neste artigo usam Autofac como um contêiner de injeção de dependência, que fornece os `IContainer` `ContainerBuilder` tipos e. Os contêineres alternativos de injeção de dependência poderiam ser usados igualmente, mas usaria APIs diferentes daquelas apresentadas aqui.

Observe que não há nenhum requisito para definir o método de resolução de dependência durante a inicialização do aplicativo. Ele pode ser definido a qualquer momento. A única restrição é que Xamarin.Forms precisa saber sobre o método de resolução de dependências no momento em que o aplicativo tenta consumir tipos armazenados no contêiner de injeção de dependência. Portanto, se houver serviços no contêiner de injeção de dependência que o aplicativo precisará durante a inicialização, o método de resolução de dependência precisará ser definido no início do ciclo de vida do aplicativo. Da mesma forma, se o contêiner de injeção de dependência gerenciar a criação e o tempo de vida de um específico [`Effect`](xref:Xamarin.Forms.Effect) , Xamarin.Forms precisará saber sobre o método de resolução de dependência antes de tentar criar uma exibição que o use `Effect` .

> [!WARNING]
> O registro e a resolução de tipos com um contêiner de injeção de dependência tem um custo de desempenho devido ao uso do contêiner de reflexão para criar cada tipo, especialmente se as dependências estiverem sendo reconstruídas para cada navegação de página no aplicativo. Se houver muitas dependências ou se elas forem profundas, o custo da criação poderá aumentar significativamente.

## <a name="registering-types"></a>Registrando tipos

Os tipos devem ser registrados com o contêiner de injeção de dependência antes de poder resolvê-los por meio do método de resolução de dependência. O exemplo de código a seguir mostra os métodos de registro que o aplicativo de exemplo expõe na `App` classe, para o contêiner Autofac:

```csharp
using Autofac;
using Autofac.Core;
...

public partial class App : Application
{
    static IContainer container;
    static readonly ContainerBuilder builder = new ContainerBuilder();
    ...

    public static void RegisterType<T>() where T : class
    {
        builder.RegisterType<T>();
    }

    public static void RegisterType<TInterface, T>() where TInterface : class where T : class, TInterface
    {
        builder.RegisterType<T>().As<TInterface>();
    }

    public static void RegisterTypeWithParameters<T>(Type param1Type, object param1Value, Type param2Type, string param2Name) where T : class
    {
        builder.RegisterType<T>()
               .WithParameters(new List<Parameter>()
        {
            new TypedParameter(param1Type, param1Value),
            new ResolvedParameter(
                (pi, ctx) => pi.ParameterType == param2Type && pi.Name == param2Name,
                (pi, ctx) => ctx.Resolve(param2Type))
        });
    }

    public static void RegisterTypeWithParameters<TInterface, T>(Type param1Type, object param1Value, Type param2Type, string param2Name) where TInterface : class where T : class, TInterface
    {
        builder.RegisterType<T>()
               .WithParameters(new List<Parameter>()
        {
            new TypedParameter(param1Type, param1Value),
            new ResolvedParameter(
                (pi, ctx) => pi.ParameterType == param2Type && pi.Name == param2Name,
                (pi, ctx) => ctx.Resolve(param2Type))
        }).As<TInterface>();
    }

    public static void BuildContainer()
    {
        container = builder.Build();
    }
    ...
}
```

Quando um aplicativo usa um método de resolução de dependência para resolver tipos de um contêiner, os registros de tipo normalmente são executados de projetos de plataforma. Isso permite que os projetos de plataforma registrem tipos para renderizadores personalizados, efeitos e [`DependencyService`](xref:Xamarin.Forms.DependencyService) implementações.

Após o registro de tipo de um projeto de plataforma, o `IContainer` objeto deve ser criado, o que é realizado chamando o `BuildContainer` método. Esse método invoca `Build` o método de Autofac na `ContainerBuilder` instância, que cria um novo contêiner de injeção de dependência que contém os registros que foram feitos.

Nas seções a seguir, uma `Logger` classe que implementa a `ILogger` interface é injetada em construtores de classe. A `Logger` classe implementa a funcionalidade de log simples usando o `Debug.WriteLine` método e é usada para demonstrar como os serviços podem ser injetados em renderizadores personalizados, efeitos e [`DependencyService`](xref:Xamarin.Forms.DependencyService) implementações.

### <a name="registering-custom-renderers"></a>Registrando renderizadores personalizados

O aplicativo de exemplo inclui uma página que reproduz vídeos da Web, cuja origem XAML é mostrada no exemplo a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             ...>
    <video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />
</ContentPage>
```

A `VideoPlayer` exibição é implementada em cada plataforma por uma `VideoPlayerRenderer` classe, que fornece a funcionalidade para reproduzir o vídeo. Para obter mais informações sobre essas classes de processador personalizado, consulte [implementando um player de vídeo](~/xamarin-forms/app-fundamentals/custom-renderer/video-player/index.md).

No iOS e no Plataforma Universal do Windows (UWP), as `VideoPlayerRenderer` classes têm o seguinte construtor, que requer um `ILogger` argumento:

```csharp
public VideoPlayerRenderer(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

Em todas as plataformas, o registro de tipo com o contêiner de injeção de dependência é executado pelo `RegisterTypes` método, que é invocado antes da plataforma que carrega o aplicativo com o `LoadApplication(new App())` método. O exemplo a seguir mostra o `RegisterTypes` método na plataforma Ios:

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterType<FormsVideoLibrary.iOS.VideoPlayerRenderer>();
    App.BuildContainer();
}
```

Neste exemplo, o `Logger` tipo concreto é registrado por meio de um mapeamento em relação a seu tipo de interface, e o `VideoPlayerRenderer` tipo é registrado diretamente sem um mapeamento de interface. Quando o usuário navega para a página que contém a `VideoPlayer` exibição, o método de resolução de dependência será invocado para resolver o `VideoPlayerRenderer` tipo do contêiner de injeção de dependência, que também resolverá e injetará o `Logger` tipo no `VideoPlayerRenderer` Construtor.

O `VideoPlayerRenderer` Construtor na plataforma Android é um pouco mais complicado, pois requer um `Context` argumento além do `ILogger` argumento:

```csharp
public VideoPlayerRenderer(Context context, ILogger logger) : base(context)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

O exemplo a seguir mostra o `RegisterTypes` método na plataforma Android:

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterTypeWithParameters<FormsVideoLibrary.Droid.VideoPlayerRenderer>(typeof(Android.Content.Context), this, typeof(ILogger), "logger");
    App.BuildContainer();
}
```

Neste exemplo, o `App.RegisterTypeWithParameters` método registra o `VideoPlayerRenderer` com o contêiner de injeção de dependência. O método de registro garante que a `MainActivity` instância será injetada como o `Context` argumento e que o `Logger` tipo será injetado como o `ILogger` argumento.

### <a name="registering-effects"></a>Registrando efeitos

O aplicativo de exemplo inclui uma página que usa um efeito de controle de toque para arrastar [`BoxView`](xref:Xamarin.Forms.BoxView) instâncias em toda a página. O [`Effect`](xref:Xamarin.Forms.Effect) é adicionado ao `BoxView` usando o seguinte código:

```csharp
var boxView = new BoxView { ... };
var touchEffect = new TouchEffect();
boxView.Effects.Add(touchEffect);
```

A `TouchEffect` classe é [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) implementada em cada plataforma por uma `TouchEffect` classe que é um `PlatformEffect` . A `TouchEffect` classe Platform fornece a funcionalidade para arrastar a `BoxView` página ao todo. Para obter mais informações sobre essas classes de efeito, consulte [invocando eventos de efeitos](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md).

Em todas as plataformas, a `TouchEffect` classe tem o seguinte construtor, que requer um `ILogger` argumento:

```csharp
public TouchEffect(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

Em todas as plataformas, o registro de tipo com o contêiner de injeção de dependência é executado pelo `RegisterTypes` método, que é invocado antes da plataforma que carrega o aplicativo com o `LoadApplication(new App())` método. O exemplo a seguir mostra o `RegisterTypes` método na plataforma Android:

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterType<TouchTracking.Droid.TouchEffect>();
    App.BuildContainer();
}
```

Neste exemplo, o `Logger` tipo concreto é registrado por meio de um mapeamento em relação a seu tipo de interface, e o `TouchEffect` tipo é registrado diretamente sem um mapeamento de interface. Quando o usuário navega para a página que contém uma [`BoxView`](xref:Xamarin.Forms.BoxView) instância que está `TouchEffect` anexada a ela, o método de resolução de dependência será invocado para resolver o `TouchEffect` tipo de plataforma do contêiner de injeção de dependência, que também resolverá e injetará o `Logger` tipo no `TouchEffect` Construtor.

### <a name="registering-dependencyservice-implementations"></a>Registrando implementações de DependencyService

O aplicativo de exemplo inclui uma página que usa [`DependencyService`](xref:Xamarin.Forms.DependencyService) implementações em cada plataforma para permitir que o usuário escolha uma foto da biblioteca de imagens do dispositivo. A `IPhotoPicker` interface define a funcionalidade implementada pelas `DependencyService` implementações e é mostrada no exemplo a seguir:

```csharp
public interface IPhotoPicker
{
    Task<Stream> GetImageStreamAsync();
}
```

Em cada projeto de plataforma, a `PhotoPicker` classe implementa a `IPhotoPicker` interface usando APIs de plataforma. Para obter mais informações sobre esses serviços de dependência, consulte [escolher uma foto da biblioteca de imagens](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md).

No iOS e UWP, as `PhotoPicker` classes têm o Construtor a seguir, que requer um `ILogger` argumento:

```csharp
public PhotoPicker(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

Em todas as plataformas, o registro de tipo com o contêiner de injeção de dependência é executado pelo `RegisterTypes` método, que é invocado antes da plataforma que carrega o aplicativo com o `LoadApplication(new App())` método. O exemplo a seguir mostra o `RegisterTypes` método em UWP:

```csharp
void RegisterTypes()
{
    DIContainerDemo.App.RegisterType<ILogger, Logger>();
    DIContainerDemo.App.RegisterType<IPhotoPicker, Services.UWP.PhotoPicker>();
    DIContainerDemo.App.BuildContainer();
}
```

Neste exemplo, o `Logger` tipo concreto é registrado por meio de um mapeamento em relação a seu tipo de interface, e o `PhotoPicker` tipo também é registrado por meio de um mapeamento de interface.

O `PhotoPicker` Construtor na plataforma Android é um pouco mais complicado, pois requer um `Context` argumento além do `ILogger` argumento:

```csharp
public PhotoPicker(Context context, ILogger logger)
{
    _context = context ?? throw new ArgumentNullException(nameof(context));
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

O exemplo a seguir mostra o `RegisterTypes` método na plataforma Android:

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterTypeWithParameters<IPhotoPicker, Services.Droid.PhotoPicker>(typeof(Android.Content.Context), this, typeof(ILogger), "logger");
    App.BuildContainer();
}
```

Neste exemplo, o `App.RegisterTypeWithParameters` método registra o `PhotoPicker` com o contêiner de injeção de dependência. O método de registro garante que a `MainActivity` instância será injetada como o `Context` argumento e que o `Logger` tipo será injetado como o `ILogger` argumento.

Quando o usuário navega para a página de separação de fotos e escolhe selecionar uma foto, o `OnSelectPhotoButtonClicked` manipulador é executado:

```csharp
async void OnSelectPhotoButtonClicked(object sender, EventArgs e)
{
    ...
    var photoPickerService = DependencyService.Resolve<IPhotoPicker>();
    var stream = await photoPickerService.GetImageStreamAsync();
    if (stream != null)
    {
        image.Source = ImageSource.FromStream(() => stream);
    }
    ...
}
```

Quando o [`DependencyService.Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) método é invocado, o método de resolução de dependência será invocado para resolver o `PhotoPicker` tipo do contêiner de injeção de dependência, que também resolverá e injetará o `Logger` tipo no `PhotoPicker` Construtor.

> [!NOTE]
> O [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) método deve ser usado ao resolver um tipo do contêiner de injeção de dependência do aplicativo por meio do [`DependencyService`](xref:Xamarin.Forms.DependencyService) .

## <a name="related-links"></a>Links relacionados

- [Resolução de dependência usando contêineres (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo)
- [Injeção de dependência](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)
- [Implementando um player de vídeo](~/xamarin-forms/app-fundamentals/custom-renderer/video-player/index.md)
- [Invocando eventos de efeitos](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
- [Escolhendo uma foto da biblioteca de imagens](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
