---
title: Criando um serviço
ms.prod: xamarin
ms.assetid: A78A55E7-FB5C-4C42-8E3E-939B5E98F9EB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/03/2018
ms.openlocfilehash: 4cec06287963fb607ba2f523c6f47e56c08e655f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754903"
---
# <a name="creating-a-service"></a>Criando um serviço

Os serviços Xamarin. Android devem obedecer a duas regras Inviolable dos serviços Android:

- Eles devem estender o [`Android.App.Service`](xref:Android.App.Service).
- Eles devem ser decorados com o [`Android.App.ServiceAttribute`](xref:Android.App.ServiceAttribute).

Outro requisito de serviços Android é que eles devem ser registrados no **AndroidManifest. xml** e receber um nome exclusivo. O Xamarin. Android registrará automaticamente o serviço no manifesto no momento da compilação com o atributo XML necessário.

Esse trecho de código é o exemplo mais simples de criar um serviço no Xamarin. Android que atende a esses dois requisitos:  

```csharp
[Service]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

No momento da compilação, o Xamarin. Android registrará o serviço injetando o seguinte elemento XML em **AndroidManifest. xml** (Observe que o Xamarin. Android gerou um nome aleatório para o serviço):

```xml
<service android:name="md5a0cbbf8da641ae5a4c781aaf35e00a86.DemoService" />
```

É possível compartilhar um serviço com outros aplicativos Android _exportando_ -o. Isso é feito definindo a `Exported` propriedade `ServiceAttribute`no. Ao exportar um serviço, a `ServiceAttribute.Name` Propriedade também deve ser definida para fornecer um nome público significativo para o serviço. Este trecho de código demonstra como exportar e nomear um serviço:

```csharp
[Service(Exported=true, Name="com.xamarin.example.DemoService")]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

O elemento **AndroidManifest. xml** para esse serviço terá uma aparência semelhante a:

```xml
<service android:exported="true" android:name="com.xamarin.example.DemoService" />
```

Os serviços têm seu próprio ciclo de vida com métodos de retorno de chamada que são invocados conforme o serviço é criado. Exatamente quais métodos são invocados dependem do tipo de serviço. Um serviço iniciado deve implementar diferentes métodos de ciclo de vida que um serviço associado, enquanto um serviço híbrido deve implementar os métodos de retorno de chamada para um serviço iniciado e um serviço associado. Esses métodos são todos membros da `Service` classe; como o serviço é iniciado determinará quais métodos de ciclo de vida serão invocados. Esses métodos de ciclo de vida serão discutidos em mais detalhes posteriormente.

Por padrão, um serviço será iniciado no mesmo processo que um aplicativo Android. É possível iniciar um serviço em seu próprio processo definindo a `ServiceAttribute.IsolatedProcess` Propriedade como true:

```csharp
[Service(IsolatedProcess=true)]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things, in it's own process!
}
```

A próxima etapa é examinar como iniciar um serviço e, em seguida, prosseguir para examinar como implementar os três tipos diferentes de serviços.

> [!NOTE]
> Um serviço é executado no thread da interface do usuário, portanto, se qualquer trabalho for executado, o que bloqueará a interface do usuário, o serviço deverá usar threads para executar o trabalho.

## <a name="starting-a-service"></a>Iniciando um serviço

A maneira mais básica de iniciar um serviço no Android é distribuir um `Intent` que contém metadados para ajudar a identificar qual serviço deve ser iniciado. Há dois estilos diferentes de intenções que podem ser usados para iniciar um serviço:

- **Intenção explícita** Uma intenção explícita identificará exatamente qual serviço deve ser usado para concluir uma determinada ação. &ndash; Uma intenção explícita pode ser considerada como uma letra que tem um endereço específico; O Android roteará a intenção para o serviço identificado explicitamente. Este trecho de código é um exemplo de uso de uma intenção explícita para iniciar `DownloadService`um serviço chamado:

    ```csharp
    // Example of creating an explicit Intent in an Android Activity
    Intent downloadIntent = new Intent(this, typeof(DownloadService));
    downloadIntent.data = Uri.Parse(fileToDownload);
    ```

- **Intenção implícita** &ndash; Esse tipo de tentativa identifica livremente a ação que o usuário deseja executar, mas o serviço exato para concluir essa ação é desconhecido. Uma intenção implícita pode ser considerada como uma letra que é endereçada "a quem ele pode se preocupar...".
    O Android examinará o conteúdo da intenção e determinará se há um serviço existente que corresponda à intenção.

    Um _filtro de intenção_ é usado para ajudar a corresponder a intenção implícita com um serviço registrado. Um filtro de intenção é um elemento XML que é adicionado a **AndroidManifest. xml** que contém os metadados necessários para ajudar a corresponder um serviço com uma intenção implícita.

    ```csharp
    Intent sendIntent = new Intent("common.xamarin.DemoService");
    sendIntent.Data = Uri.Parse(fileToDownload);
    ```

Se o Android tiver mais de uma possível correspondência para uma intenção implícita, poderá pedir ao usuário para selecionar o componente para manipular a ação:

![Captura de tela de uma caixa de diálogo de desambiguidade](images/creating-a-service-01.png "Captura de tela de uma caixa de diálogo de desambiguidade")

> [!IMPORTANT]
> A partir do Android 5,0 (AP nível 21), uma intenção implícita não pode ser usada para iniciar um serviço.

Sempre que possível, os aplicativos devem usar tentativas explícitas para iniciar um serviço. Uma intenção implícita não pede para um serviço específico iniciar &ndash; é uma solicitação de algum serviço instalado no dispositivo para lidar com a solicitação. Essa solicitação ambígua pode fazer com que o serviço incorreto esteja manipulando a solicitação ou outro aplicativo a partir do início desnecessariamente (o que aumenta a pressão de recursos no dispositivo).

A forma como a intenção é distribuída depende do tipo de serviço e será discutida em mais detalhes posteriormente nos guias específicos de cada tipo de serviço.

### <a name="creating-an-intent-filter-for-implicit-intents"></a>Criando um filtro de intenção para intenções implícitas

Para associar um serviço a uma intenção implícita, um aplicativo Android deve fornecer alguns metadados para identificar os recursos do serviço. Esses metadados são fornecidos por filtros de _intenção_. Os filtros de intenção contêm algumas informações, como uma ação ou um tipo de dados, que devem estar presentes em uma intenção de iniciar um serviço. No Xamarin. Android, o filtro de intenção é registrado em **AndroidManifest. xml** decorando um serviço com [`IntentFilterAttribute`](xref:Android.App.IntentFilterAttribute)o. Por exemplo, o código a seguir adiciona um filtro de intenção com uma ação `com.xamarin.DemoService`associada de:

```csharp
[Service]
[IntentFilter(new String[]{"com.xamarin.DemoService"})]
public class DemoService : Service
{
}
```

Isso resulta em uma entrada incluída no arquivo &ndash; **AndroidManifest. xml** uma entrada que é empacotada com o aplicativo de forma análoga ao exemplo a seguir:

```xml
<service android:name="demoservice.DemoService">
    <intent-filter>
        <action android:name="com.xamarin.DemoService" />
    </intent-filter>
</service>
```

Com os conceitos básicos de um serviço Xamarin. Android fora do caminho, vamos examinar os diferentes subtipos de serviços com mais detalhes.

## <a name="related-links"></a>Links relacionados

- [Android.App.Service](xref:Android.App.Service)
- [Android.App.ServiceAttribute](xref:Android.App.ServiceAttribute)
- [Android.App.Intent](xref:Android.Content.Intent)
- [Android.App.IntentFilterAttribute](xref:Android.App.IntentFilterAttribute)
