---
title: Xamarin.Forms Registro e resolução do DependencyService
description: Este artigo explica como usar a Xamarin.Forms classe DependencyService para invocar a funcionalidade da plataforma nativa.
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/05/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: da3c6c2f55f1cdbed1b3c4e1bb16c4f78e32cf23
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556042"
---
# <a name="no-locxamarinforms-dependencyservice-registration-and-resolution"></a>Xamarin.Forms Registro e resolução do DependencyService

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)

Ao usar o Xamarin.Forms [`DependencyService`](xref:Xamarin.Forms.DependencyService) para invocar a funcionalidade da plataforma nativa, as implementações de plataforma devem ser registradas com o `DependencyService` e, em seguida, resolvidas do código compartilhado para chamá-las.

## <a name="register-platform-implementations"></a>Registrar as implementações de plataforma

Implementações de plataforma devem ser registradas com o [`DependencyService`](xref:Xamarin.Forms.DependencyService) para que o Xamarin.Forms possa localizá-las em tempo de execução.

O registro pode ser executado com o [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) , ou com [`Register`](xref:Xamarin.Forms.DependencyService.Register*) os `RegisterSingleton` métodos e.

> [!IMPORTANT]
> As compilações de versões de projetos UWP que usam a compilação nativa do .NET devem registrar implementações de plataforma com os [`Register`](xref:Xamarin.Forms.DependencyService.Register*) métodos.

### <a name="registration-by-attribute"></a>Registro por atributo

O [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) pode ser usado para registrar uma implementação de plataforma com o [`DependencyService`](xref:Xamarin.Forms.DependencyService) . O atributo indica que o tipo especificado fornece uma implementação concreta da interface.

O exemplo a seguir mostra como usar o [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) para registrar a implementação do IOS da `IDeviceOrientationService` interface:

```csharp
using Xamarin.Forms;

[assembly: Dependency(typeof(DeviceOrientationService))]
namespace DependencyServiceDemos.iOS
{
    public class DeviceOrientationService : IDeviceOrientationService
    {
        public DeviceOrientation GetOrientation()
        {
            ...
        }
    }
}
```

Neste exemplo, o [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) registra o `DeviceOrientationService` com o [`DependencyService`](xref:Xamarin.Forms.DependencyService) . Isso resulta no tipo concreto que está sendo registrado em relação à interface que ele implementa.

Da mesma forma, as implementações da `IDeviceOrientationService` interface em outras plataformas devem ser registradas com o [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) .

> [!NOTE]
> O registro com o [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) é executado no nível do namespace.

### <a name="registration-by-method"></a>Registro por método

Os [`DependencyService.Register`](xref:Xamarin.Forms.DependencyService.Register*) métodos e o `RegisterSingleton` método podem ser usados para registrar uma implementação de plataforma com o [`DependencyService`](xref:Xamarin.Forms.DependencyService) .

O exemplo a seguir mostra o uso do [`Register`](xref:Xamarin.Forms.DependencyService.Register*) método para registrar a implementação do IOS da `IDeviceOrientationService` interface:

```csharp
[Register("AppDelegate")]
public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
{
    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
        global::Xamarin.Forms.Forms.Init();
        LoadApplication(new App());
        DependencyService.Register<IDeviceOrientationService, DeviceOrientationService>();
        return base.FinishedLaunching(app, options);
    }
}
```

Neste exemplo, o [`Register`](xref:Xamarin.Forms.DependencyService.Register*) método registra o tipo concreto, `DeviceOrientationService` , em relação à `IDeviceOrientationService` interface. Como alternativa, uma sobrecarga do [`Register`](xref:Xamarin.Forms.DependencyService.Register*) método pode ser usada para registrar uma implementação de plataforma com o [`DependencyService`](xref:Xamarin.Forms.DependencyService) :

```csharp
DependencyService.Register<DeviceOrientationService>();
```

Neste exemplo, o [`Register`](xref:Xamarin.Forms.DependencyService.Register*) método registra o `DeviceOrientationService` com o [`DependencyService`](xref:Xamarin.Forms.DependencyService) . Isso resulta no tipo concreto que está sendo registrado em relação à interface que ele implementa.

Como alternativa, uma instância de objeto existente pode ser registrada como um singleton com o `RegisterSingleton` método:

```csharp
var service = new DeviceOrientationService();
DependencyService.RegisterSingleton<IDeviceOrientationService>(service);
```

Neste exemplo, o `RegisterSingleton` método registra a `DeviceOrientationService` instância do objeto em relação à `IDeviceOrientationService` interface, como um singleton.

Da mesma forma, as implementações da `IDeviceOrientationService` interface em outras plataformas podem ser registradas com os [`Register`](xref:Xamarin.Forms.DependencyService.Register*) métodos ou o `RegisterSingleton` método.

> [!IMPORTANT]
> O registro com [`Register`](xref:Xamarin.Forms.DependencyService.Register*) os `RegisterSingleton` métodos e deve ser executado em projetos de plataforma, antes que a funcionalidade fornecida pela implementação da plataforma seja invocada do código compartilhado.

## <a name="resolve-the-platform-implementations"></a>Resolver as implementações da plataforma

As implementações de plataforma devem ser resolvidas antes de serem invocadas. Normalmente, isso é executado em código compartilhado usando o [`DependencyService.Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) método. No entanto, ele também pode ser feito com o [`DependencyService.Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) método.

Por padrão, o [`DependencyService`](xref:Xamarin.Forms.DependencyService) só resolverá implementações de plataforma que têm construtores sem parâmetros. No entanto, um método de resolução de dependência pode ser injetado no Xamarin.Forms que usa um contêiner de injeção de dependência ou métodos de fábrica para resolver implementações de plataforma. Essa abordagem pode ser usada para resolver implementações da plataforma que têm construtores com parâmetros. Para obter mais informações, consulte a [resolução Xamarin.Forms de dependência em ](~/xamarin-forms/internals/dependency-resolution.md).

> [!IMPORTANT]
> Invocar uma implementação de plataforma que não tenha sido registrada com o [`DependencyService`](xref:Xamarin.Forms.DependencyService) resultará em uma `NullReferenceException` geração.

### <a name="resolve-using-the-getlttgt-method"></a>Resolver usando o método Get&lt;T&gt;

O [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) método recupera a implementação da plataforma da interface `T` em tempo de execução e:

- Cria uma instância dela como um singleton.
- Retorna uma instância existente como singleton, que foi registrada com o `DependencyService` pelo `RegisterSingleton` método.

Em ambos os casos, a instância residirá durante o tempo de vida do aplicativo e todas as chamadas subsequentes para resolver a mesma implementação de plataforma recuperarão a mesma instância.

O código a seguir mostra um exemplo de como chamar o [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) método para resolver a `IDeviceOrientationService` interface e, em seguida, invocar seu `GetOrientation` método:

```csharp
IDeviceOrientationService service = DependencyService.Get<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

Como alternativa, esse código pode ser condensado em uma única linha:

```csharp
DeviceOrientation orientation = DependencyService.Get<IDeviceOrientationService>().GetOrientation();
```

> [!NOTE]
> O [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) método retorna uma instância da implementação da plataforma da interface `T` como um singleton, por padrão. No entanto, esse comportamento pode ser alterado. Para obter mais informações, confira [Gerenciar o tempo de vida de objetos resolvidos](#manage-the-lifetime-of-resolved-objects).

### <a name="resolve-using-the-resolvelttgt-method"></a>Resolver usando o método Resolve&lt;T&gt;

O [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) método recupera a implementação da plataforma da interface `T` em tempo de execução, usando um método de resolução de dependência que foi injetado no Xamarin.Forms com a [`DependencyResolver`](xref:Xamarin.Forms.Internals.DependencyResolver) classe. Se um método de resolução de dependência não tiver sido injetado no Xamarin.Forms , o `Resolve<T>` método fará fallback para chamar o [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) método para recuperar a implementação da plataforma. Para obter mais informações sobre como injetar um método de resolução de dependência no Xamarin.Forms , consulte [resolução de dependência em Xamarin.Forms ](~/xamarin-forms/internals/dependency-resolution.md).

O código a seguir mostra um exemplo de como chamar o [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) método para resolver a `IDeviceOrientationService` interface e, em seguida, invocar seu `GetOrientation` método:

```csharp
IDeviceOrientationService service = DependencyService.Resolve<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

Como alternativa, esse código pode ser condensado em uma única linha:

```csharp
DeviceOrientation orientation = DependencyService.Resolve<IDeviceOrientationService>().GetOrientation();
```

> [!NOTE]
> Quando o [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) método volta a chamar o [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) método, ele retorna uma instância da implementação da plataforma da interface `T` como um singleton, por padrão. No entanto, esse comportamento pode ser alterado. Para obter mais informações, confira [Gerenciar o tempo de vida de objetos resolvidos](#manage-the-lifetime-of-resolved-objects).

## <a name="manage-the-lifetime-of-resolved-objects"></a>Gerenciar o tempo de vida de objetos resolvidos

O comportamento padrão da [`DependencyService`](xref:Xamarin.Forms.DependencyService) classe é resolver implementações de plataforma como singletons. Portanto, as implementações de plataforma deverão durar o tempo de vida de um aplicativo.

Esse comportamento é especificado com o [`DependencyFetchTarget`](xref:Xamarin.Forms.DependencyFetchTarget) argumento opcional nos [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) métodos e [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) . A [`DependencyFetchTarget`](xref:Xamarin.Forms.DependencyFetchTarget) enumeração define dois membros:

- `GlobalInstance`, que retorna a implementação de plataforma como um singleton.
- `NewInstance`, que retorna uma nova instância da implementação de plataforma. O aplicativo, então, é responsável por gerenciar o tempo de vida da instância de implementação de plataforma.

Os [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) métodos e definem seus argumentos opcionais como e, [`DependencyFetchTarget.GlobalInstance`](xref:Xamarin.Forms.DependencyFetchTarget) portanto, implementações de plataforma são sempre resolvidas como singletons. Esse comportamento pode ser alterado, para que novas instâncias de implementações de plataforma sejam criadas, especificando [`DependencyFetchTarget.NewInstance`](xref:Xamarin.Forms.DependencyFetchTarget) como argumentos para `Get<T>` os `Resolve<T>` métodos e:

```csharp
ITextToSpeechService service = DependencyService.Get<ITextToSpeechService>(DependencyFetchTarget.NewInstance);
```

Neste exemplo, o [`DependencyService`](xref:Xamarin.Forms.DependencyService) cria uma nova instância da implementação da plataforma para a `ITextToSpeechService` interface. Todas as chamadas subsequentes para resolver o `ITextToSpeechService` também criarão novas instâncias.

A consequência de sempre criar uma nova instância de uma implementação de plataforma é que o aplicativo se torna responsável por gerenciar o tempo de vida das instâncias. Isso significa que, se você assinar um evento definido em uma implementação de plataforma, deverá cancelar o evento quando a implementação de plataforma não for mais necessária. Além disso, isso significa que talvez seja necessário que as implementações de plataforma implementem `IDisposable` e limpem seus recursos em métodos `Dispose`. O aplicativo de exemplo demonstra esse cenário em suas implementações de plataforma `TextToSpeechService`.

Quando um aplicativo terminar de usar uma implementação de plataforma que implementa `IDisposable`, ele deverá chamar a implementação `Dispose` do objeto. Uma maneira de realizar isso é com uma instrução `using`:

```csharp
ITextToSpeechService service = DependencyService.Get<ITextToSpeechService>(DependencyFetchTarget.NewInstance);
using (service as IDisposable)
{
    await service.SpeakAsync("Hello world");
}
```

Neste exemplo, depois que o método `SpeakAsync` é invocado, a instrução `using` automaticamente descarta o objeto de implementação de plataforma. Isso resulta no método `Dispose` do objeto que está sendo invocado, que executa a limpeza necessária.

Para obter mais informações sobre como chamar o método `Dispose` de um objeto, veja [Como usar objetos que implementam IDisposable](/dotnet/standard/garbage-collection/using-objects).

## <a name="related-links"></a>Links relacionados

- [Demonstrações de DependencyService (exemplo)](/samples/xamarin/xamarin-forms-samples/dependencyservice/)
- [Resolução de dependência em Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md)