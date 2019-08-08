---
title: Registro e resolução de DependencyService do Xamarin.Forms
description: Este artigo explica como usar a classe do DependencyService do Xamarin.Forms para invocar a funcionalidade de plataforma nativa.
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/05/2019
ms.openlocfilehash: 0bbe4310a32c11da35e41b01693f8cb1a66fc39b
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739332"
---
# <a name="xamarinforms-dependencyservice-registration-and-resolution"></a>Registro e resolução de DependencyService do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)

Ao usar o [`DependencyService`](xref:Xamarin.Forms.DependencyService) do Xamarin.Forms para invocar a funcionalidade de plataforma nativa, as implementações de plataforma devem ser registradas com o `DependencyService` e depois resolvidas do código compartilhado para invocá-las.

## <a name="register-platform-implementations"></a>Registrar as implementações de plataforma

As implementações de plataforma devem ser registradas no [`DependencyService`](xref:Xamarin.Forms.DependencyService) para que o Xamarin.Forms possa localizá-las em tempo de execução.

O registro pode ser executado com o [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) ou com os métodos [`Register`](xref:Xamarin.Forms.DependencyService.Register*).

> [!IMPORTANT]
> Os builds de versão de projetos da UWP que usam a compilação nativa do .NET devem registrar implementações de plataforma com os métodos [`Register`](xref:Xamarin.Forms.DependencyService.Register*).

### <a name="registration-by-attribute"></a>Registro por atributo

O [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) pode ser usado para registrar uma implementação de plataforma com o [`DependencyService`](xref:Xamarin.Forms.DependencyService). O atributo indica que o tipo especificado fornece uma implementação concreta da interface.

O exemplo a seguir mostra o uso do [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) para registrar a implementação do iOS da interface `IDeviceOrientationService`:

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

Neste exemplo, o [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) registra o `DeviceOrientationService` com o [`DependencyService`](xref:Xamarin.Forms.DependencyService). Isso resulta no tipo concreto que está sendo registrado em relação à interface que ele implementa.

Da mesma forma, as implementações da interface `IDeviceOrientationService` em outras plataformas devem ser registradas com o [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute).

> [!NOTE]
> O registro com o [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) é executado no nível do namespace.

### <a name="registration-by-method"></a>Registro por método

Os métodos [`DependencyService.Register`](xref:Xamarin.Forms.DependencyService.Register*) podem ser usados para registrar uma implementação de plataforma com o [`DependencyService`](xref:Xamarin.Forms.DependencyService).

O exemplo a seguir mostra o uso do método [`Register`](xref:Xamarin.Forms.DependencyService.Register*) para registrar a implementação do iOS da interface `IDeviceOrientationService`:

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

Neste exemplo, o método [`Register`](xref:Xamarin.Forms.DependencyService.Register*) registra o tipo concreto, `DeviceOrientationService`, na interface `IDeviceOrientationService`. Como alternativa, uma sobrecarga do método [`Register`](xref:Xamarin.Forms.DependencyService.Register*) pode ser usada para registrar uma implementação de plataforma com o [`DependencyService`](xref:Xamarin.Forms.DependencyService):

```csharp
DependencyService.Register<DeviceOrientationService>();
```

Neste exemplo, o método [`Register`](xref:Xamarin.Forms.DependencyService.Register*) registra o `DeviceOrientationService` com o [`DependencyService`](xref:Xamarin.Forms.DependencyService). Isso resulta no tipo concreto que está sendo registrado em relação à interface que ele implementa.

Da mesma forma, as implementações da interface `IDeviceOrientationService` em outras plataformas podem ser registradas com os métodos [`Register`](xref:Xamarin.Forms.DependencyService.Register*).

> [!IMPORTANT]
> O registro com os métodos [`Register`](xref:Xamarin.Forms.DependencyService.Register*) devem ser executados em projetos de plataforma, antes de a funcionalidade fornecida pela implementação da plataforma ser invocada do código compartilhado.

## <a name="resolve-the-platform-implementations"></a>Resolver as implementações da plataforma

As implementações de plataforma devem ser resolvidas antes de serem invocadas. Isso normalmente é feito no código compartilhado, usando o método [`DependencyService.Get<T>`](xref:Xamarin.Forms.DependencyService.Get*). No entanto, isso também pode ser realizado com o método [`DependencyService.Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*).

Por padrão, o [`DependencyService`](xref:Xamarin.Forms.DependencyService) resolverá somente as implementações de plataforma que têm construtores sem parâmetros. No entanto, é possível injetar no Xamarin.Forms um método de resolução de dependência que um contêiner de injeção de dependências ou métodos de fábrica para resolver implementações da plataforma. Essa abordagem pode ser usada para resolver implementações da plataforma que têm construtores com parâmetros. Para obter mais informações, confira [Resolução de dependências no Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md).

> [!IMPORTANT]
> Invocar uma implementação de plataforma que ainda não foi registrada com o [`DependencyService`](xref:Xamarin.Forms.DependencyService) resultará na geração de uma `NullReferenceException`.

### <a name="resolve-using-the-getlttgt-method"></a>Resolver usando o método Get&lt;T&gt;

O método [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) recupera a implementação de plataforma da interface `T` em tempo de execução e cria uma instância dela como um singleton. Essa instância deve durar o tempo de vida do aplicativo, e todas as chamadas subsequentes para resolver a mesma implementação de plataforma recuperará a mesma instância.

O código a seguir mostra um exemplo de chamada ao método [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) para resolver a interface `IDeviceOrientationService` e, em seguida, invocação de seu método `GetOrientation`:

```csharp
IDeviceOrientationService service = DependencyService.Get<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

Como alternativa, esse código pode ser condensado em uma única linha:

```csharp
DeviceOrientation orientation = DependencyService.Get<IDeviceOrientationService>().GetOrientation();
```

> [!NOTE]
> O método [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) cria uma instância da implementação de plataforma da interface `T` como um singleton, por padrão. No entanto, esse comportamento pode ser alterado. Para obter mais informações, confira [Gerenciar o tempo de vida de objetos resolvidos](#manage-the-lifetime-of-resolved-objects).

### <a name="resolve-using-the-resolvelttgt-method"></a>Resolver usando o método Resolve&lt;T&gt;

O método [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) recupera a implementação de plataforma da interface `T` em tempo de execução, usando um método de resolução de dependência injetado no Xamarin.Forms com a classe [`DependencyResolver`](xref:Xamarin.Forms.Internals.DependencyResolver). Se um método de resolução de dependência ainda não tiver sido injetado no Xamarin.Forms, o método `Resolve<T>` fará fallback para chamar o método [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) para recuperar a implementação de plataforma. Para obter mais informações sobre a injeção de um método de resolução de dependência no Xamarin.Forms, confira [Resolução de dependências no Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md).

O código a seguir mostra um exemplo de chamada ao método [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) para resolver a interface `IDeviceOrientationService` e, em seguida, invocação de seu método `GetOrientation`:

```csharp
IDeviceOrientationService service = DependencyService.Resolve<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

Como alternativa, esse código pode ser condensado em uma única linha:

```csharp
DeviceOrientation orientation = DependencyService.Resolve<IDeviceOrientationService>().GetOrientation();
```

> [!NOTE]
> Quando o método [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) fizer fallback para chamar o método [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*), ele criará uma instância da implementação da interface `T` como um singleton, por padrão. No entanto, esse comportamento pode ser alterado. Para obter mais informações, confira [Gerenciar o tempo de vida de objetos resolvidos](#manage-the-lifetime-of-resolved-objects).

## <a name="manage-the-lifetime-of-resolved-objects"></a>Gerenciar o tempo de vida de objetos resolvidos

O comportamento padrão da classe [`DependencyService`](xref:Xamarin.Forms.DependencyService) é resolver as implementações de plataforma como singletons. Portanto, as implementações de plataforma deverão durar o tempo de vida de um aplicativo.

Esse comportamento é especificado com o argumento opcional [`DependencyFetchTarget`](xref:Xamarin.Forms.DependencyFetchTarget) nos métodos [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) e [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*). A enumeração [`DependencyFetchTarget`](xref:Xamarin.Forms.DependencyFetchTarget) define dois membros:

- `GlobalInstance`, que retorna a implementação de plataforma como um singleton.
- `NewInstance`, que retorna uma nova instância da implementação de plataforma. O aplicativo, então, é responsável por gerenciar o tempo de vida da instância de implementação de plataforma.

Os métodos [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) e [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) definem seus argumentos opcionais como [`DependencyFetchTarget.GlobalInstance`](xref:Xamarin.Forms.DependencyFetchTarget) e, portanto, as implementações de plataforma são sempre resolvidas como singletons. Esse comportamento pode ser alterado para que novas instâncias de implementações de plataforma sejam criadas por meio da especificação de [`DependencyFetchTarget.NewInstance`](xref:Xamarin.Forms.DependencyFetchTarget) como argumentos para os métodos `Get<T>` e `Resolve<T>`:

```csharp
ITextToSpeechService service = DependencyService.Get<ITextToSpeechService>(DependencyFetchTarget.NewInstance);
```

Neste exemplo, o [`DependencyService`](xref:Xamarin.Forms.DependencyService) cria uma nova instância da implementação de plataforma para a interface `ITextToSpeechService`. Todas as chamadas subsequentes para resolver o `ITextToSpeechService` também criarão novas instâncias.

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

- [Demonstrações de DependencyService (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)
- [Resolução de dependências no Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md)
