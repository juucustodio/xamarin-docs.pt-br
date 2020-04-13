---
title: Registro e resolução de DependencyService do Xamarin.Forms
description: Este artigo explica como usar a classe do DependencyService do Xamarin.Forms para invocar a funcionalidade de plataforma nativa.
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/05/2019
ms.openlocfilehash: 6e666c16c9b1afc3478f524cae2f84d6704319c2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70199230"
---
# <a name="xamarinforms-dependencyservice-registration-and-resolution"></a>Registro e resolução de DependencyService do Xamarin.Forms

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)

Ao usar o Xamarin.Forms [`DependencyService`](xref:Xamarin.Forms.DependencyService) para invocar a funcionalidade da plataforma `DependencyService`nativa, as implementações da plataforma devem ser registradas com o , e depois resolvidas a partir de código compartilhado para invocá-los.

## <a name="register-platform-implementations"></a>Registrar as implementações de plataforma

As implementações da plataforma [`DependencyService`](xref:Xamarin.Forms.DependencyService) devem ser registradas com o para que o Xamarin.Forms possa localizá-los em tempo de execução.

O registro pode [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute)ser realizado [`Register`](xref:Xamarin.Forms.DependencyService.Register*) com o , ou com os métodos.

> [!IMPORTANT]
> As compilações de lançamento de projetos UWP que usam [`Register`](xref:Xamarin.Forms.DependencyService.Register*) compilação nativa .NET devem registrar implementações da plataforma com os métodos.

### <a name="registration-by-attribute"></a>Registro por atributo

O [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) pode ser usado para registrar [`DependencyService`](xref:Xamarin.Forms.DependencyService)uma implementação da plataforma com o . O atributo indica que o tipo especificado fornece uma implementação concreta da interface.

O exemplo a [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) seguir mostra o uso `IDeviceOrientationService` do registro da implementação do iOS da interface:

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

Neste exemplo, [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) os registros `DeviceOrientationService` com [`DependencyService`](xref:Xamarin.Forms.DependencyService)o . Isso resulta no tipo concreto que está sendo registrado em relação à interface que ele implementa.

Da mesma forma, as `IDeviceOrientationService` implementações da interface em outras plataformas devem ser registradas no [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute).

> [!NOTE]
> A inscrição [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) com o é realizada no nível namespace.

### <a name="registration-by-method"></a>Registro por método

Os [`DependencyService.Register`](xref:Xamarin.Forms.DependencyService.Register*) métodos podem ser usados para [`DependencyService`](xref:Xamarin.Forms.DependencyService)registrar uma implementação da plataforma com o .

O exemplo a [`Register`](xref:Xamarin.Forms.DependencyService.Register*) seguir mostra usar o método `IDeviceOrientationService` para registrar a implementação do iOS da interface:

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

Neste exemplo, [`Register`](xref:Xamarin.Forms.DependencyService.Register*) o método registra o `DeviceOrientationService`tipo `IDeviceOrientationService` de concreto, contra a interface. Alternativamente, uma sobrecarga [`Register`](xref:Xamarin.Forms.DependencyService.Register*) do método pode ser usada [`DependencyService`](xref:Xamarin.Forms.DependencyService)para registrar uma implementação da plataforma com o :

```csharp
DependencyService.Register<DeviceOrientationService>();
```

Neste exemplo, [`Register`](xref:Xamarin.Forms.DependencyService.Register*) o método `DeviceOrientationService` registra [`DependencyService`](xref:Xamarin.Forms.DependencyService)o com o . Isso resulta no tipo concreto que está sendo registrado em relação à interface que ele implementa.

Da mesma forma, as `IDeviceOrientationService` implementações da interface em outras plataformas podem ser registradas com os [`Register`](xref:Xamarin.Forms.DependencyService.Register*) métodos.

> [!IMPORTANT]
> O registro [`Register`](xref:Xamarin.Forms.DependencyService.Register*) com os métodos deve ser realizado em projetos de plataforma, antes que a funcionalidade fornecida pela implementação da plataforma seja invocada a partir do código compartilhado.

## <a name="resolve-the-platform-implementations"></a>Resolver as implementações da plataforma

As implementações de plataforma devem ser resolvidas antes de serem invocadas. Isso é normalmente realizado em [`DependencyService.Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) código compartilhado usando o método. No entanto, também pode [`DependencyService.Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) ser realizado com o método.

Por padrão, [`DependencyService`](xref:Xamarin.Forms.DependencyService) o só resolverá implementações de plataformas que tenham construtores sem parâmetros. No entanto, é possível injetar no Xamarin.Forms um método de resolução de dependência que um contêiner de injeção de dependências ou métodos de fábrica para resolver implementações da plataforma. Essa abordagem pode ser usada para resolver implementações da plataforma que têm construtores com parâmetros. Para obter mais informações, confira [Resolução de dependências no Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md).

> [!IMPORTANT]
> Invocar uma implementação de plataforma que não [`DependencyService`](xref:Xamarin.Forms.DependencyService) tenha sido `NullReferenceException` registrada com o resultará em um ser lançado.

### <a name="resolve-using-the-getlttgt-method"></a>Resolver usando o método Get&lt;T&gt;

O [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) método recupera a implementação da interface da plataforma `T` em tempo de execução e cria uma instância dela como um singleton. Essa instância deve durar o tempo de vida do aplicativo, e todas as chamadas subsequentes para resolver a mesma implementação de plataforma recuperará a mesma instância.

O código a seguir mostra [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) um exemplo `IDeviceOrientationService` de chamar o `GetOrientation` método para resolver a interface e, em seguida, invocar seu método:

```csharp
IDeviceOrientationService service = DependencyService.Get<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

Como alternativa, esse código pode ser condensado em uma única linha:

```csharp
DeviceOrientation orientation = DependencyService.Get<IDeviceOrientationService>().GetOrientation();
```

> [!NOTE]
> O [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) método cria uma instância da `T` implementação da interface da plataforma como um singleton, por padrão. No entanto, esse comportamento pode ser alterado. Para obter mais informações, confira [Gerenciar o tempo de vida de objetos resolvidos](#manage-the-lifetime-of-resolved-objects).

### <a name="resolve-using-the-resolvelttgt-method"></a>Resolver usando o método Resolve&lt;T&gt;

O [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) método recupera a implementação da interface da plataforma `T` em tempo de execução, usando um [`DependencyResolver`](xref:Xamarin.Forms.Internals.DependencyResolver) método de resolução de dependência que foi injetado em Xamarin.Forms com a classe. Se um método de resolução de dependência não tiver sido `Resolve<T>` injetado no Xamarin.Forms, o método recuará ao chamar o [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) método para recuperar a implementação da plataforma. Para obter mais informações sobre a injeção de um método de resolução de dependência no Xamarin.Forms, confira [Resolução de dependências no Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md).

O código a seguir mostra [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) um exemplo `IDeviceOrientationService` de chamar o `GetOrientation` método para resolver a interface e, em seguida, invocar seu método:

```csharp
IDeviceOrientationService service = DependencyService.Resolve<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

Como alternativa, esse código pode ser condensado em uma única linha:

```csharp
DeviceOrientation orientation = DependencyService.Resolve<IDeviceOrientationService>().GetOrientation();
```

> [!NOTE]
> Quando [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) o método volta [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) a chamar o método, ele cria `T` uma instância da implementação da interface da plataforma como um singleton, por padrão. No entanto, esse comportamento pode ser alterado. Para obter mais informações, confira [Gerenciar o tempo de vida de objetos resolvidos](#manage-the-lifetime-of-resolved-objects).

## <a name="manage-the-lifetime-of-resolved-objects"></a>Gerenciar o tempo de vida de objetos resolvidos

O comportamento padrão [`DependencyService`](xref:Xamarin.Forms.DependencyService) da classe é resolver implementações da plataforma como singletons. Portanto, as implementações de plataforma deverão durar o tempo de vida de um aplicativo.

Esse comportamento é especificado com o [`DependencyFetchTarget`](xref:Xamarin.Forms.DependencyFetchTarget) argumento opcional sobre os [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) métodos. [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) A [`DependencyFetchTarget`](xref:Xamarin.Forms.DependencyFetchTarget) enumeração define dois membros:

- `GlobalInstance`, que retorna a implementação de plataforma como um singleton.
- `NewInstance`, que retorna uma nova instância da implementação de plataforma. O aplicativo, então, é responsável por gerenciar o tempo de vida da instância de implementação de plataforma.

Os [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) métodos e métodos definem seus argumentos opcionais para [`DependencyFetchTarget.GlobalInstance`](xref:Xamarin.Forms.DependencyFetchTarget), e assim as implementações da plataforma são sempre resolvidas como singletons. Esse comportamento pode ser alterado, de modo que novas instâncias [`DependencyFetchTarget.NewInstance`](xref:Xamarin.Forms.DependencyFetchTarget) de implementações `Get<T>` da `Resolve<T>` plataforma sejam criadas, especificando como argumentos para os e métodos:

```csharp
ITextToSpeechService service = DependencyService.Get<ITextToSpeechService>(DependencyFetchTarget.NewInstance);
```

Neste exemplo, [`DependencyService`](xref:Xamarin.Forms.DependencyService) cria uma nova instância da `ITextToSpeechService` implementação da plataforma para a interface. Todas as chamadas subsequentes para resolver o `ITextToSpeechService` também criarão novas instâncias.

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
