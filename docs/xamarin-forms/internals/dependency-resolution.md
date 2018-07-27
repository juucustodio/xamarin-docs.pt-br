---
title: Resolução de dependência no xamarin. Forms
description: Este artigo explica como injetar um método de resolução de dependência no xamarin. Forms para que o contêiner de injeção de dependência do aplicativo tem controle sobre a construção e o tempo de vida de renderizadores personalizados, efeitos e implementações de DependencyService.
ms.prod: xamarin
ms.assetid: 491B87DC-14CB-4ADC-AC6C-40A7627B2524
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/23/2018
ms.openlocfilehash: 2379c8ddc4bea6dd97bc4febd055dd8dfef39beb
ms.sourcegitcommit: 46bb04016d3c35d91ff434b38474e0cb8197961b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39270482"
---
# <a name="dependency-resolution-in-xamarinforms"></a>Resolução de dependência no xamarin. Forms

_Este artigo explica como injetar um método de resolução de dependência no xamarin. Forms para que o contêiner de injeção de dependência do aplicativo tem controle sobre a construção e o tempo de vida de renderizadores personalizados, efeitos e implementações de DependencyService. Os exemplos de código são tirados de [resolução de dependência](https://developer.xamarin.com/samples/xamarin-forms/Advanced/DependencyResolution/) exemplo._

No contexto de um aplicativo xamarin. Forms que usa o padrão Model-View-ViewModel (MVVM), um contêiner de injeção de dependência pode ser usado para registrar e resolver os modelos de exibição e para registrar os serviços e injetando-as nos modelos de exibição. Durante a criação do modelo de exibição, o contêiner injeta quaisquer dependências que são necessárias. Se essas dependências não tem sido criadas, o contêiner cria e resolve as dependências primeiro. Para obter mais informações sobre injeção de dependência, incluindo exemplos de injetar dependências nos modelos de exibição, consulte [injeção de dependência](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).

Controle sobre a criação e o tempo de vida dos tipos de projetos de plataforma é feito tradicionalmente por xamarin. Forms, que usa o `Activator.CreateInstance` método para criar instâncias de renderizadores personalizados, efeitos, e [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) implementações. Infelizmente, isso limita o controle do desenvolvedor sobre a criação e o tempo de vida desses tipos, e a capacidade de injetar dependências neles. No entanto, esse comportamento pode ser alterado por injetando um método de resolução de dependência no xamarin. Forms que controla como tipos serão criados – pelo contêiner de injeção de dependência do aplicativo, ou xamarin. Forms.

> [!NOTE]
> Não há nenhum requisito para injetar um método de resolução de dependência no xamarin. Forms. Xamarin. Forms continuarão criar e gerenciar o tempo de vida dos tipos de projetos de plataforma, se um método de resolução de dependência não é inserido.

## <a name="injecting-a-dependency-resolution-method"></a>Injetando um método de resolução de dependência

O [ `DependencyResolver` ](xref:Xamarin.Forms.Internals.DependencyResolver) classe fornece a capacidade de injetar um método de resolução de dependência no xamarin. Forms, usando um dos [ `ResolveUsing` ](Xamarin.Forms.Internals.DependencyResolver.ResolveUsing*) métodos. Em seguida, quando o xamarin. Forms precisa de uma instância de um tipo específico, o método de resolução de dependência tem a oportunidade de fornecer a instância. Se o método de resolução de dependência retornar `null` para um tipo solicitado, xamarin. Forms voltará à tentativa de criar o tipo de instância usando o `Activator.CreateInstance` método.

O exemplo a seguir mostra como definir o método de resolução de dependência com o [ `ResolveUsing` ](Xamarin.Forms.Internals.DependencyResolver.ResolveUsing*) método:

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

Neste exemplo, o método de resolução de dependência é definido como uma expressão lambda que usa o contêiner de injeção de dependência do Autofac para resolver quaisquer tipos que foram registrados com o contêiner. Caso contrário, `null` será retornado, que resultará na tentativa de resolver o tipo de xamarin. Forms.

> [!NOTE]
> A API usada por um contêiner de injeção de dependência é específica para o contêiner. Os exemplos de código neste artigo usam o Autofac como um contêiner de injeção de dependência, que fornece o `IContainer` e `ContainerBuilder` tipos. Contêineres de injeção de dependência alternativo igualmente poderia ser usados, mas usam APIs diferentes que são apresentados aqui.

Observe que não há nenhum requisito para definir o método de resolução de dependência durante a inicialização do aplicativo. Ele pode ser definido a qualquer momento. A única restrição é que o xamarin. Forms precisa saber sobre o método de resolução de dependência no momento em que o aplicativo tentar consumir tipos armazenados no contêiner de injeção de dependência. Portanto, se houver serviços no contêiner de injeção de dependência que o aplicativo exigirá durante a inicialização, o método de resolução de dependência precisará ser definido no início do ciclo de vida do aplicativo. Da mesma forma, se o contêiner de injeção de dependência gerencia a criação e o tempo de vida de um determinado [ `Effect` ](xref:Xamarin.Forms.Effect), xamarin. Forms precisará saber sobre o método de resolução de dependência, antes de tentar criar uma exibição que usa que `Effect`.

> [!WARNING]
> Registrar e resolver os tipos com um contêiner de injeção de dependência tem um custo devido ao uso do contêiner de reflexão para a criação de cada tipo, especialmente se as dependências estão sendo reconstruídas para cada navegação de página no aplicativo de desempenho. Se houver muitos ou profundas dependências, o custo de criação pode aumentar significativamente.

## <a name="registering-types"></a>Registrando tipos

Tipos devem ser registrados com o contêiner de injeção de dependência, antes que ele possa resolvê-los por meio do método de resolução de dependência. O exemplo de código a seguir mostra os métodos de registro que o aplicativo de exemplo expõe no `App` classe, para o contêiner Autofac:

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

    public static void BuildContainer()
    {
        container = builder.Build();
    }
    ...
}
```

Quando um aplicativo usa um método de resolução de dependência para resolver os tipos de um contêiner, os registros do tipo normalmente são executados de projetos de plataforma. Isso permite que projetos de plataforma ao registrar tipos de renderizadores personalizados, efeitos, e [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) implementações.

Após o registro de tipo de um projeto de plataforma, o `IContainer` objeto deve ser criado, o que é realizado chamando o `BuildContainer` método. Este método invoca do Autofac `Build` método no `ContainerBuilder` instância, que cria um novo contêiner de injeção de dependência que contém os registros que foram feitos.

Nas seções a seguirem, uma `Logger` classe que implementa o `ILogger` interface é injetado em construtores de classe. O `Logger` classe implementa o log simples funcionalidade usando o `Debug.WriteLine` método e é usado para demonstrar como os serviços possam ser injetados em renderizadores personalizados, efeitos, e [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) implementações.

### <a name="registering-custom-renderers"></a>Registrando renderizadores personalizados

O aplicativo de exemplo inclui uma página que desempenha vídeos da web, cujo código-fonte XAML é mostrado no exemplo a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             ...>
    <video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />
</ContentPage>
```

O `VideoPlayer` exibição é implementada em cada plataforma por um `VideoPlayerRenderer` classe, que fornece a funcionalidade para reproduzir o vídeo. Para obter mais informações sobre essas classes de renderizador personalizado, consulte [implementando um player de vídeo](~/xamarin-forms/app-fundamentals/custom-renderer/video-player/index.md).

No iOS e Universal Windows Platform (UWP), o `VideoPlayerRenderer` classes têm o seguinte construtor, que exige um `ILogger` argumento:

```csharp
public VideoPlayerRenderer(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

Em todas as três plataformas, o tipo de registro com o contêiner de injeção de dependência é executado pela `RegisterTypes` método, que é invocado antes da plataforma de carregar o aplicativo com o `LoadApplication(new App())` método. A exemplo a seguir mostra o `RegisterTypes` método na plataforma iOS:

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterType<FormsVideoLibrary.iOS.VideoPlayerRenderer>();
    App.BuildContainer();
}
```

Neste exemplo, o `Logger` tipo concreto é registrado por meio de um mapeamento em relação a seu tipo de interface e o `VideoPlayerRenderer` tipo é registrado diretamente sem um mapeamento de interface. Quando o usuário navega até a página que contém o `VideoPlayer` modo de exibição, o método de resolução de dependência será invocado para resolver o `VideoPlayerRenderer` tipo de contêiner de injeção de dependência, que também resolver e injetar o `Logger` digitar em o `VideoPlayerRenderer` construtor.

O `VideoPlayerRenderer` construtor na plataforma Android é um pouco mais complicado, pois exige uma `Context` argumento além de `ILogger` argumento:

```csharp
public VideoPlayerRenderer(Context context, ILogger logger) : base(context)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

A exemplo a seguir mostra o `RegisterTypes` método na plataforma Android:

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterTypeWithParameters<FormsVideoLibrary.Droid.VideoPlayerRenderer>(typeof(Android.Content.Context), this, typeof(ILogger), "logger");
    App.BuildContainer();
}
```

Neste exemplo, o `App.RegisterTypeWithParameters` método registra o `VideoPlayerRenderer` com o contêiner de injeção de dependência. O método de registro garante que o `MainActivity` instância será injetada como o `Context` argumento e que o `Logger` tipo será injetado como o `ILogger` argumento.

### <a name="registering-effects"></a>Registrando efeitos

O aplicativo de exemplo inclui uma página que usa um efeito de controle de toque para arrastar [ `BoxView` ](xref:Xamarin.Forms.BoxView) instâncias em torno da página. O [ `Effect` ](xref:Xamarin.Forms.Effect) é adicionado para o `BoxView` usando o seguinte código:

```csharp
var boxView = new BoxView { ... };
var touchEffect = new TouchEffect();
boxView.Effects.Add(touchEffect);
```

O `TouchEffect` classe é um [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) que é implementada em cada plataforma por um `TouchEffect` classe que tem um `PlatformEffect`. A plataforma `TouchEffect` classe fornece a funcionalidade de arrastar o `BoxView` em torno da página. Para obter mais informações sobre essas classes de efeito, consulte [invocação de eventos de efeitos](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md).

Em todas as três plataformas, o `TouchEffect` classe tem o seguinte construtor, que exige um `ILogger` argumento:

```csharp
public TouchEffect(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

Em todas as três plataformas, o tipo de registro com o contêiner de injeção de dependência é executado pela `RegisterTypes` método, que é invocado antes da plataforma de carregar o aplicativo com o `LoadApplication(new App())` método. A exemplo a seguir mostra o `RegisterTypes` método na plataforma Android:

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterType<TouchTracking.Droid.TouchEffect>();
    App.BuildContainer();
}
```

Neste exemplo, o `Logger` tipo concreto é registrado por meio de um mapeamento em relação a seu tipo de interface e o `TouchEffect` tipo é registrado diretamente sem um mapeamento de interface. Quando o usuário navega até a página que contém um [ `BoxView` ](xref:Xamarin.Forms.BoxView) instância que tem o `TouchEffect` anexado a ele, o método de resolução de dependência será invocado para resolver a plataforma `TouchEffect` tipo de dependência contêiner de injeção, que também resolver e injetar o `Logger` digitar no `TouchEffect` construtor.

### <a name="registering-dependencyservice-implementations"></a>Registrando as implementações do DependencyService

O aplicativo de exemplo inclui uma página que usa [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) implementações em cada plataforma para permitir que o usuário escolha uma foto da biblioteca de imagens do dispositivo. O `IPhotoPicker` interface define a funcionalidade que é implementada pelo `DependencyService` implementações e é mostrado no exemplo a seguir:

```csharp
public interface IPhotoPicker
{
    Task<Stream> GetImageStreamAsync();
}
```

Em cada projeto de plataforma, o `PhotoPicker` classe implementa o `IPhotoPicker` interface usando as APIs da plataforma. Para obter mais informações sobre esses serviços de dependência, consulte [escolhendo uma foto na biblioteca de imagens](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md).

Em todas as três plataformas, o `PhotoPicker` classe tem o seguinte construtor, que exige um `ILogger` argumento:

```csharp
public PhotoPicker(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

Em todas as três plataformas, o tipo de registro com o contêiner de injeção de dependência é executado pela `RegisterTypes` método, que é invocado antes da plataforma de carregar o aplicativo com o `LoadApplication(new App())` método. A exemplo a seguir mostra o `RegisterTypes` método na UWP:

```csharp
void RegisterTypes()
{
    DIContainerDemo.App.RegisterType<ILogger, Logger>();
    DIContainerDemo.App.RegisterType<IPhotoPicker, Services.UWP.PhotoPicker>();
    DIContainerDemo.App.BuildContainer();
}
```

Neste exemplo, o `Logger` tipo concreto é registrado por meio de um mapeamento em relação a seu tipo de interface e o `PhotoPicker` tipo também é registrado por meio de um mapeamento de interface. Quando o usuário navega para a página de separação de fotos e optar por selecionar uma foto, o `OnSelectPhotoButtonClicked` manipulador é executado:

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

Quando o [ `DependencyService.Resolve<T>` ](xref:Xamarin.Forms.DependencyService.Resolve*) método é invocado, o método de resolução de dependência será invocado para resolver o `PhotoPicker` tipo de contêiner de injeção de dependência, que também resolver e injetar o `Logger` tipo para o `PhotoPicker` construtor.

> [!NOTE]
> O [ `Resolve<T>` ](xref:Xamarin.Forms.DependencyService.Resolve*) método deve ser usado durante a resolução de um tipo de contêiner de injeção de dependência do aplicativo por meio de [ `DependencyService` ](xref:Xamarin.Forms.DependencyService).

## <a name="related-links"></a>Links relacionados

- [Resolução de dependência (amostra)](https://developer.xamarin.com/samples/xamarin-forms/Advanced/DependencyResolution/)
- [Injeção de dependência](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)
- [Implementando um player de vídeo](~/xamarin-forms/app-fundamentals/custom-renderer/video-player/index.md)
- [Invocação de eventos de efeitos](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
- [Escolhendo uma foto na biblioteca de imagens](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
