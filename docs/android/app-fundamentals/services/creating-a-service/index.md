---
title: Criando um serviço
ms.prod: xamarin
ms.assetid: A78A55E7-FB5C-4C42-8E3E-939B5E98F9EB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/03/2018
ms.openlocfilehash: 8c2086025ccb5fe41b3ffddc9cd650c1e0c81fbc
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61013446"
---
# <a name="creating-a-service"></a>Criando um serviço

Serviços de xamarin. Android devem obedecer a duas regras inviolável de serviços do Android:

* Eles devem estender o [ `Android.App.Service` ](https://developer.xamarin.com/api/type/Android.App.Service/).
* Eles devem ser decorados com o [ `Android.App.ServiceAttribute` ](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/).

Outro requisito de serviços do Android é que eles devem ser registrados na **androidmanifest. XML** e recebe um nome exclusivo. Xamarin. Android serão registrados automaticamente o serviço no manifesto em tempo de compilação com o atributo XML necessário.

Este trecho de código é o exemplo mais simples de criar um serviço no xamarin. Android que atende a esses dois requisitos:  

```csharp
[Service]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

Em tempo de compilação, xamarin. Android registrará o serviço inserindo o seguinte elemento XML em **androidmanifest. XML** (Observe que o xamarin. Android gerado um nome aleatório para o serviço):

```xml
<service android:name="md5a0cbbf8da641ae5a4c781aaf35e00a86.DemoService" />
```

É possível compartilhar um serviço com outros aplicativos Android por _exportando_ -lo. Isso é feito definindo a `Exported` propriedade no `ServiceAttribute`. Ao exportar um serviço, o `ServiceAttribute.Name` propriedade também deve ser definida para fornecer um nome significativo público para o serviço. Este trecho de código demonstra como exportar e um serviço de nomes:

```csharp
[Service(Exported=true, Name="com.xamarin.example.DemoService")]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

O **androidmanifest. XML** elemento para este serviço será, em seguida, algo parecido com:

```xml
<service android:exported="true" android:name="com.xamarin.example.DemoService" />
```

Os serviços têm seu próprio ciclo de vida com métodos de retorno de chamada que são invocados como o serviço é criado. Exatamente quais métodos são invocados dependem do tipo de serviço. Um serviço iniciado deve implementar os métodos de ciclo de vida diferente que um serviço vinculado, enquanto um serviço híbrido deve implementar os métodos de retorno de chamada para um serviço iniciado e um serviço vinculado. Esses métodos são todos os membros de `Service` classe; como o serviço é iniciado determinará a quais métodos de ciclo de vida serão invocados. Esses métodos de ciclo de vida serão abordados em mais detalhes posteriormente.

Por padrão, um serviço será iniciado no mesmo processo que um aplicativo do Android. É possível iniciar um serviço em seu próprio processo, definindo o `ServiceAttribute.IsolatedProcess` propriedade como true:

```csharp
[Service(IsolatedProcess=true)]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things, in it's own process!
}
```

A próxima etapa é examinar como iniciar um serviço e, em seguida, passaremos para examinar como implementar os três tipos diferentes de serviços.

> [!NOTE]
> Um serviço é executado no thread da interface do usuário, portanto, se nenhum trabalho a ser executada que bloqueia a interface do usuário, o serviço deve usar threads para executar o trabalho.

## <a name="starting-a-service"></a>Iniciar um serviço

A maneira mais simples para iniciar um serviço no Android é expedir uma `Intent` que contém metadados para ajudar a identificar qual serviço deve ser iniciado. Há dois estilos diferentes de tentativas que podem ser usados para iniciar um serviço:

-   **Intenção explícita** &ndash; um _intenção explícita_ identificará exatamente qual serviço deve ser usado para concluir uma determinada ação. Uma intenção explícita pode ser pensada como uma letra que tem um endereço específico; Android roteará a intenção para o serviço que é identificado explicitamente. Este trecho de código é um exemplo de como usar uma intenção explícita para iniciar um serviço chamado `DownloadService`:

    ```csharp
    // Example of creating an explicit Intent in an Android Activity
    Intent downloadIntent = new Intent(this, typeof(DownloadService));
    downloadIntent.data = Uri.Parse(fileToDownload);
    ```

-   **Intenção implícita** &ndash; vagamente identifica esse tipo de intenção a ação que o usuário deseja executar, mas o serviço exato para concluir essa ação é desconhecido. Uma intenção implícita pode ser pensada como uma letra que é abordado "A preocupação de maio de TI de Whom...".
    Android examinar o conteúdo da intenção e determinar se há um serviço existente que corresponde a intenção.

    Uma _filtro intencional_ é usado para ajudar a encontrar a intenção implícita com um serviço registrado. Um filtro de intenção é um elemento XML que é adicionado ao **androidmanifest. XML** que contém os metadados necessários para ajudar a encontrar um serviço com uma intenção implícita.

    ```csharp
    Intent sendIntent = new Intent("common.xamarin.DemoService");
    sendIntent.Data = Uri.Parse(fileToDownload);
    ```

Se o Android tem mais de uma correspondência possível para uma intenção implícita, ele pode perguntar ao usuário selecionar o componente para lidar com a ação:

![Captura de tela de uma caixa de diálogo de Desambiguidade](images/creating-a-service-01.png "captura de tela de uma caixa de diálogo de Desambiguidade")

> [!IMPORTANT]
> No Android 5.0 (nível 21 da AP) uma intenção implícita não pode ser usada para iniciar um serviço.

Sempre que possível, os aplicativos devem usar intenções explícitas para iniciar um serviço. Não solicita uma intenção implícita para um serviço específico iniciar &ndash; é uma solicitação de algum serviço instalado no dispositivo para manipular a solicitação. Essa solicitação ambígua pode resultar em serviço errado tratando a solicitação ou outro aplicativo iniciando desnecessariamente (o que aumenta a pressão para recursos do dispositivo).

Como a intenção é expedida depende do tipo de serviço e será discutida em mais detalhes posteriormente os guias específicos para cada tipo de serviço.


### <a name="creating-an-intent-filter-for-implicit-intents"></a>Criando um filtro intencional para propósitos de implícitos

Para associar um serviço com uma intenção implícita, um aplicativo do Android deve fornecer alguns metadados para identificar os recursos do serviço. Esses metadados é fornecido pelo _filtros de intenção_. Filtros de intenção contêm algumas informações, como uma ação ou um tipo de dados, que devem estar presentes em uma tentativa de iniciar um serviço. No xamarin. Android, o filtro intencional está registrado no **androidmanifest. XML** decorando um serviço com o [ `IntentFilterAttribute` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/). Por exemplo, o código a seguir adiciona um filtro de intenção com a ação associada `com.xamarin.DemoService`:

```csharp
[Service]
[IntentFilter(new String[]{"com.xamarin.DemoService"})]
public class DemoService : Service
{
}
```

Isso resulta em uma entrada que está sendo incluída na **androidmanifest. XML** arquivo &ndash; uma entrada que é empacotada com o aplicativo de forma semelhante ao exemplo a seguir:

```xml
<service android:name="demoservice.DemoService">
    <intent-filter>
        <action android:name="com.xamarin.DemoService" />
    </intent-filter>
</service>
```

Com os conceitos básicos de um serviço de xamarin. Android fora do caminho, vamos examinar os subtipos diferentes de serviços em mais detalhes.


## <a name="related-links"></a>Links relacionados

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.App.ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/)
- [Android.App.Intent](https://developer.xamarin.com/api/type/Android.Content.Intent/)
- [Android.App.IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)
