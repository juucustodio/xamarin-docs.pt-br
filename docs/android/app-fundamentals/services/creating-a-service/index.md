---
title: Criando um serviço
ms.prod: xamarin
ms.assetid: A78A55E7-FB5C-4C42-8E3E-939B5E98F9EB
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 05/03/2018
ms.openlocfilehash: 00785ad161f5f05fd70b059bb0a3f1c8d6c31f97
ms.sourcegitcommit: daa089d41cfe1ed0456d6de2f8134cf96ae072b1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="creating-a-service"></a>Criando um serviço

Serviços de xamarin devem obedecer duas regras inviolável do Android services:

* Eles devem se estender a [ `Android.App.Service` ](https://developer.xamarin.com/api/type/Android.App.Service/).
* Eles devem ser decorados com o [ `Android.App.ServiceAttribute` ](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/).

Outro requisito do Android services é que eles devem ser registrados no **AndroidManifest.xml** e recebe um nome exclusivo. Xamarin automaticamente registrará o serviço no manifesto em tempo de compilação com o atributo XML necessário.

Este trecho de código é o exemplo mais simples de criar um serviço em xamarin que atende a esses dois requisitos:  

```csharp
[Service]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

Em tempo de compilação, xamarin registrará o serviço, inserindo o seguinte elemento XML em **AndroidManifest.xml** (Observe que o xamarin gerado um nome aleatório para o serviço):

```xml
<service android:name="md5a0cbbf8da641ae5a4c781aaf35e00a86.DemoService" />
```

É possível compartilhar um serviço com outros aplicativos do Android por _exportando_ -lo. Isso é feito definindo o `Exported` propriedade o `ServiceAttribute`. Ao exportar um serviço, o `ServiceAttribute.Name` propriedade também deve ser definida para fornecer um nome significativo público para o serviço. Este trecho de código demonstra como exportar e um serviço de nomes:

```csharp
[Service(Exported=true, Name="com.xamarin.example.DemoService")]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

O **AndroidManifest.xml** elemento para este serviço será, em seguida, algo parecido com:

```xml
<service android:exported="true" android:name="com.xamarin.example.DemoService" />
```

Os serviços têm seu próprios ciclo de vida com métodos de retorno de chamada que são invocados como o serviço é criado. Exatamente quais métodos são chamados depende do tipo de serviço. Um serviço iniciado deve implementar os métodos de ciclo de vida diferente de um serviço vinculado, enquanto um serviço híbrido deve implementar os métodos de retorno de chamada para um serviço iniciado e um serviço vinculado. Esses métodos são todos os membros de `Service` classe; como o serviço é iniciado determina quais métodos de ciclo de vida serão invocados. Esses métodos de ciclo de vida serão abordados em mais detalhes posteriormente.

Por padrão, um serviço será iniciado no mesmo processo como um aplicativo do Android. É possível iniciar um serviço em seu próprio processo, definindo o `ServiceAttribute.IsolatedProcess` propriedade como true:

```csharp
[Service(IsolatedProcess=true)]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things, in it's own process!
}
```

A próxima etapa é examinar como iniciar um serviço e, em seguida, ir para examinar como implementar os três tipos diferentes de serviços.

> [!NOTE]
> Um serviço é executado no thread da interface do usuário, portanto, se nenhum trabalho a ser executada que bloqueia a interface do usuário, o serviço deve usar threads para executar o trabalho.

## <a name="starting-a-service"></a>Iniciar um serviço

É a maneira mais simples para iniciar um serviço no Android despachar uma `Intent` que contém metadados para ajudar a identificar qual serviço deve ser iniciado. Há dois estilos diferentes de tentativas que podem ser usados para iniciar um serviço:

-   **Intenção explícita** &ndash; um _intenção explícita_ identificará exatamente qual serviço deve ser usado para concluir uma determinada ação. Uma tentativa explícita pode ser pensada como uma letra que tem um endereço específico; Android roteará a intenção para o serviço que é identificado explicitamente. Este trecho de código é um exemplo do uso de uma tentativa explícita para iniciar um serviço chamado `DownloadService`:

    ```csharp
    // Example of creating an explicit Intent in an Android Activity
    Intent downloadIntent = new Intent(this, typeof(DownloadService));
    downloadIntent.data = Uri.Parse(fileToDownload);
    ```

-   **Intenção implícita** &ndash; esse tipo de intenção de forma flexível identifica da ação que o usuário deseja executar, mas o serviço exato para concluir essa ação é desconhecido. Uma tentativa implícita pode ser pensada como uma letra que é abordado "Preocupação de maio de TI para Whom...".
    Android examinar o conteúdo da intenção e determinar se há um serviço existente que corresponda a intenção.

    Um _filtro intenção_ é usado para ajudar a encontrar a intenção implícita com um serviço registrado. Um filtro a intenção é um elemento XML que é adicionado ao **AndroidManifest.xml** que contém os metadados necessários para ajudar a encontrar um serviço com uma intenção implícita.

    ```csharp
    Intent sendIntent = new Intent("common.xamarin.DemoService");
    sendIntent.Data = Uri.Parse(fileToDownload);
    ```

Se o Android tem mais de uma correspondência possível para um propósito implícita, ele pode solicitar ao usuário selecionar o componente para lidar com a ação:

![Captura de tela de uma caixa de diálogo ambiguidades](images/creating-a-service-01.png "captura de tela de uma caixa de diálogo ambiguidades")

> [!IMPORTANT]
> No Android 5.0 (nível de PA 21) uma intenção implícita não pode ser usada para iniciar um serviço.

Sempre que possível, os aplicativos devem usar tentativas explícitas para iniciar um serviço. Uma tentativa implícita não pedir para um serviço específico iniciar &ndash; é uma solicitação de algum serviço instalado no dispositivo para manipular a solicitação. Essa solicitação ambígua pode resultar no serviço errado que manipula a solicitação ou outro aplicativo iniciando desnecessariamente (o que aumenta a pressão para recursos do dispositivo).

Como a intenção é expedida depende do tipo de serviço e será discutida em mais detalhes posteriormente as guias específicas para cada tipo de serviço.


### <a name="creating-an-intent-filter-for-implicit-intents"></a>Criando um filtro de tentativa para propósitos de implícita

Para associar um serviço com uma intenção implícita, um aplicativo do Android deve fornecer alguns metadados para identificar os recursos do serviço. Esses metadados é fornecido pelo _filtros intenção_. Tentativa de filtros contêm algumas informações, como uma ação ou um tipo de dados, que devem estar presentes em uma tentativa de iniciar um serviço. No xamarin, o filtro a intenção é registrado no **AndroidManifest.xml** decorando um serviço com o [ `IntentFilterAttribute` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/). Por exemplo, o código a seguir adiciona um filtro de tentativa com uma ação associada de `com.xamarin.DemoService`:

```csharp
[Service]
[IntentFilter(new String[]{"com.xamarin.DemoService"})]
public class DemoService : Service
{
}
```

Isso resulta em uma entrada seja incluída no **AndroidManifest.xml** arquivo &ndash; uma entrada que é fornecida com o aplicativo de maneira semelhante ao exemplo a seguir:

```xml
<service android:name="demoservice.DemoService">
    <intent-filter>
        <action android:name="com.xamarin.DemoService" />
    </intent-filter>
</service>
```

Com os conceitos básicos de um serviço xamarin fora do caminho, vamos examinar os subtipos diferentes de serviços em mais detalhes.


## <a name="related-links"></a>Links relacionados

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.App.ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/)
- [Android.App.Intent](https://developer.xamarin.com/api/type/Android.Content.Intent/)
- [Android.App.IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)
