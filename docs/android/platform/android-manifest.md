---
title: Trabalhando com o manifesto do Android
ms.prod: xamarin
ms.assetid: CB7CCF60-FEF1-3B28-215F-159391E74347
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/05/2018
ms.openlocfilehash: 1438c012608b367c21ebcc401c058b186b917f53
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027796"
---
# <a name="working-with-the-android-manifest"></a>Trabalhando com o manifesto do Android

O **AndroidManifest. xml** é um arquivo poderoso na plataforma Android que permite descrever a funcionalidade e os requisitos de seu aplicativo para o Android. No entanto, trabalhar com ele não é fácil. O Xamarin. Android ajuda a minimizar essa dificuldade, permitindo que você adicione atributos personalizados às suas classes, que serão então usadas para gerar automaticamente o manifesto para você. Nossa meta é que 99% de nossos usuários nunca precisem modificar manualmente o **AndroidManifest. xml**. 

**AndroidManifest. xml** é gerado como parte do processo de compilação, e o XML encontrado em **Properties/AndroidManifest. xml** é mesclado com XML que é gerado a partir de atributos personalizados. O **AndroidManifest. xml** mesclado resultante reside no subdiretório **obj** ; por exemplo, ele reside em **obj/Debug/Android/AndroidManifest. xml** para compilações de depuração. O processo de mesclagem é trivial: ele usa atributos personalizados dentro do código para gerar elementos XML e *insere* esses elementos em **AndroidManifest. xml**. 

## <a name="the-basics"></a>Noções básicas

No momento da compilação, os assemblies são verificados em busca de classes não`abstract` que derivam da [atividade](xref:Android.App.Activity) e têm o atributo [`[Activity]`](xref:Android.App.ActivityAttribute) declarado nelas. Em seguida, ele usa essas classes e atributos para compilar o manifesto. Por exemplo, considere o seguinte código: 

```csharp
namespace Demo
{
    public class MyActivity : Activity
    {
    }
}
```

Isso resulta em nada sendo gerado em **AndroidManifest. xml**. Se desejar que um elemento de `<activity/>` seja gerado, você precisará usar o [`[Activity]`](xref:Android.App.Activity) 
atributo personalizado: 

```csharp
namespace Demo
{
    [Activity]
    public class MyActivity : Activity
    {
    }
}
```

Este exemplo faz com que o fragmento XML a seguir seja adicionado ao **AndroidManifest. xml**:

```xml
<activity android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

O atributo `[Activity]` não tem nenhum efeito sobre tipos de `abstract`; os tipos de `abstract` são ignorados.

### <a name="activity-name"></a>Nome da Atividade

A partir do Xamarin. Android 5,1, o nome do tipo de uma atividade é baseado no MD5SUM do nome qualificado do assembly do tipo que está sendo exportado. Isso permite que o mesmo nome totalmente qualificado seja fornecido de dois assemblies diferentes e não obtenha um erro de empacotamento. (Antes do Xamarin. Android 5,1, o nome do tipo padrão da atividade foi criado a partir do namespace em minúsculas e do nome da classe.) 

Se você quiser substituir esse padrão e especificar explicitamente o nome da sua atividade, use a propriedade [`Name`](xref:Android.App.ActivityAttribute.Name) : 

```csharp
[Activity (Name="awesome.demo.activity")]
public class MyActivity : Activity
{
}
```

Este exemplo produz o seguinte fragmento XML:

```xml
<activity android:name="awesome.demo.activity" />
```

> [!NOTE]
> Você deve usar a propriedade `Name` somente para motivos de compatibilidade com versões anteriores, pois essa renomeação pode retardar a pesquisa de tipo em tempo de execução. Se você tiver um código herdado que espera que o nome do tipo padrão da atividade seja baseado no namespace com letras minúsculas e no nome da classe, consulte nomenclatura do wrapper que pode ser [chamado pelo Android](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#Android_Callable_Wrapper_Naming) para obter dicas sobre como manter a compatibilidade. 

### <a name="activity-title-bar"></a>Barra de título da atividade

Por padrão, o Android dá ao seu aplicativo uma barra de título quando ele é executado. O valor usado para isso é [`/manifest/application/activity/@android:label`](https://developer.android.com/guide/topics/manifest/activity-element.html#label). Na maioria dos casos, esse valor será diferente do nome da classe. Para especificar o rótulo do aplicativo na barra de título, use a propriedade [`Label`](xref:Android.App.ActivityAttribute.Label) .
Por exemplo: 

```csharp
[Activity (Label="Awesome Demo App")]
public class MyActivity : Activity
{
}
```

Este exemplo produz o seguinte fragmento XML:

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

### <a name="launchable-from-application-chooser"></a>Inicializável do seletor de aplicativo

Por padrão, sua atividade não aparecerá na tela do inicializador de aplicativos do Android. Isso ocorre porque provavelmente haverá muitas atividades em seu aplicativo e você não deseja um ícone para cada uma delas. Para especificar qual deles deve ser inicializável no iniciador de aplicativos, use a propriedade [`MainLauncher`](xref:Android.App.ActivityAttribute.MainLauncher) . Por exemplo: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true)] 
public class MyActivity : Activity
{
}
```

Este exemplo produz o seguinte fragmento XML:

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```

### <a name="activity-icon"></a>Ícone de atividade

Por padrão, a atividade receberá o ícone de iniciador padrão fornecido pelo sistema. Para usar um ícone personalizado, primeiro adicione o **. png** a **recursos/empates**, defina sua ação de Build como **AndroidResource**, em seguida, use a propriedade [`Icon`](xref:Android.App.ActivityAttribute.Icon) para especificar o ícone a ser usado. Por exemplo: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
public class MyActivity : Activity
{
}
```

Este exemplo produz o seguinte fragmento XML:

```xml
<activity android:icon="@drawable/myicon" android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```

### <a name="permissions"></a>Permissões

Quando você adiciona permissões ao manifesto do Android (conforme descrito em [adicionar permissões ao manifesto do Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)), essas permissões são registradas em **Properties/AndroidManifest. xml**. Por exemplo, se você definir a permissão `INTERNET`, o seguinte elemento será adicionado a **Properties/AndroidManifest. xml**: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

As compilações de depuração definem automaticamente algumas permissões para facilitar a depuração (como `INTERNET` e `READ_EXTERNAL_STORAGE`) &ndash; essas configurações são definidas somente no **obj/Debug/Android/AndroidManifest. xml** gerado e não são mostrados como habilitados noconfigurações de permissões. 

Por exemplo, se você examinar o arquivo de manifesto gerado em **obj/Debug/Android/AndroidManifest. xml**, poderá ver os seguintes elementos de permissão adicionados: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

Na versão Build da versão do manifesto (em **obj/Debug/Android/AndroidManifest. xml**), essas permissões *não* são configuradas automaticamente. Se você achar que mudar para uma compilação de versão faz com que seu aplicativo perca uma permissão que estava disponível na compilação de depuração, verifique se você definiu explicitamente essa permissão nas configurações de **permissões necessárias** para seu aplicativo (consulte **Build > Android Aplicativo** em Visual Studio para Mac; consulte **propriedades > manifesto do Android** no Visual Studio). 

## <a name="advanced-features"></a>Recursos avançados

### <a name="intent-actions-and-features"></a>Ações e recursos de intenção

O manifesto do Android fornece uma maneira de descrever os recursos de sua atividade. Isso é feito por meio de [tentativas](https://developer.android.com/guide/topics/manifest/intent-filter-element.html) e do [`[IntentFilter]`](xref:Android.App.IntentFilterAttribute)
atributo personalizado. Você pode especificar quais ações são apropriadas para sua atividade com o [`IntentFilter`](xref:Android.App.IntentFilterAttribute#ctor*)
e quais categorias são apropriadas com o [`Categories`](xref:Android.App.IntentFilterAttribute.Categories)
property. Pelo menos uma atividade deve ser fornecida (motivo pelo qual as atividades são fornecidas no Construtor). `[IntentFilter]` pode ser fornecida várias vezes, e cada uso resulta em um elemento `<intent-filter/>` separado dentro do `<activity/>`. Por exemplo:

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
[IntentFilter (new[]{Intent.ActionView}, 
        Categories=new[]{Intent.CategorySampleCode, "my.custom.category"})]
public class MyActivity : Activity
{
}
```

Este exemplo produz o seguinte fragmento XML:

```xml
<activity android:icon="@drawable/myicon" android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
  <intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.SAMPLE_CODE" />
    <category android:name="my.custom.category" />
  </intent-filter>
</activity>
```

### <a name="application-element"></a>Elemento Application

O manifesto do Android também fornece uma maneira de declarar Propriedades para todo o aplicativo. Isso é feito por meio do elemento `<application>` e sua contraparte, o atributo personalizado do [aplicativo](xref:Android.App.ApplicationAttribute) . Observe que são configurações de todo o aplicativo (em todo o assembly) em vez de configurações por atividade. Normalmente, você declara `<application>` Propriedades para todo o seu aplicativo e, em seguida, substitui essas configurações (conforme necessário) de acordo com a atividade. 

Por exemplo, o atributo `Application` a seguir é adicionado a **AssemblyInfo.cs** para indicar que o aplicativo pode ser depurado, que seu nome legível pelo usuário é **meu aplicativo**e que ele usa o estilo de `Theme.Light` como o tema padrão para todas as atividades: 

```csharp
[assembly: Application (Debuggable=true,   
                        Label="My App",   
                        Theme="@android:style/Theme.Light")]
```

Essa declaração faz com que o seguinte fragmento XML seja gerado em **obj/Debug/Android/AndroidManifest. xml**:

```xml
<application android:label="My App" 
             android:debuggable="true" 
             android:theme="@android:style/Theme.Light"
                ... />
```

Neste exemplo, todas as atividades no aplicativo usarão como padrão o estilo de `Theme.Light`. Se você definir o tema de uma atividade como `Theme.Dialog`, somente essa atividade usará o estilo de `Theme.Dialog`, enquanto todas as outras atividades em seu aplicativo usarão como padrão o estilo de `Theme.Light`, conforme definido no elemento `<application>`. 

O elemento `Application` não é a única maneira de configurar `<application>` atributos. Como alternativa, você pode inserir atributos diretamente no elemento `<application>` de **Properties/AndroidManifest. xml**. Essas configurações são mescladas no elemento final `<application>` que reside em **obj/Debug/Android/AndroidManifest. xml**. Observe que o conteúdo de **Properties/AndroidManifest. xml** sempre substitui os dados fornecidos por atributos personalizados. 

Há muitos atributos de todo o aplicativo que podem ser configurados no elemento `<application>`; para obter mais informações sobre essas configurações, consulte a seção [Propriedades públicas](xref:Android.App.ApplicationAttribute) de [applicationattribute](xref:Android.App.ApplicationAttribute). 

## <a name="list-of-custom-attributes"></a>Lista de atributos personalizados

- [Android. app. activityattribute](xref:Android.App.ActivityAttribute) : gera um fragmento XML [/manifest/Application/Activity](https://developer.android.com/guide/topics/manifest/activity-element.html) 
- [Android. app. applicationattribute](xref:Android.App.ApplicationAttribute) : gera um fragmento XML [/manifest/Application](https://developer.android.com/guide/topics/manifest/application-element.html) 
- [Android. app. InstrumentationAttribute](xref:Android.App.InstrumentationAttribute) : gera um fragmento XML [/manifest/Instrumentation](https://developer.android.com/guide/topics/manifest/instrumentation-element.html) 
- [Android. app. IntentFilterAttribute](xref:Android.App.IntentFilterAttribute) : gera um fragmento XML [//Intent-Filter](https://developer.android.com/guide/topics/manifest/intent-filter-element.html) 
- [Android. app. MetadataAttribute](xref:Android.App.MetaDataAttribute) : gera um fragmento XML [//meta-data](https://developer.android.com/guide/topics/manifest/meta-data-element.html) 
- [Android. app. PermissionAttribute](xref:Android.App.PermissionAttribute) : gera um fragmento XML [//Permission](https://developer.android.com/guide/topics/manifest/permission-element.html) 
- [Android. app. PermissionGroupAttribute](xref:Android.App.PermissionGroupAttribute) : gera um fragmento XML [//Permission-Group](https://developer.android.com/guide/topics/manifest/permission-group-element.html) 
- [Android. app. PermissionTreeAttribute](xref:Android.App.PermissionTreeAttribute) : gera um fragmento XML [//Permission-Tree](https://developer.android.com/guide/topics/manifest/permission-tree-element.html) 
- [Android. app. ServiceAttribute](xref:Android.App.ServiceAttribute) : gera um fragmento XML [/manifest/Application/Service](https://developer.android.com/guide/topics/manifest/service-element.html) 
- [Android. app. UsesLibraryAttribute](xref:Android.App.UsesLibraryAttribute) : gera um fragmento XML [/manifest/Application/uses-library](https://developer.android.com/guide/topics/manifest/uses-library-element.html) 
- [Android. app. UsesPermissionAttribute](xref:Android.App.UsesPermissionAttribute) : gera um fragmento XML [/manifest/uses-Permission](https://developer.android.com/guide/topics/manifest/uses-permission-element.html) 
- [Android. Content. BroadcastReceiverAttribute](xref:Android.Content.BroadcastReceiverAttribute) : gera um fragmento XML [/manifest/Application/Receiver](https://developer.android.com/guide/topics/manifest/receiver-element.html) 
- [Android. Content. ContentProviderAttribute](xref:Android.Content.ContentProviderAttribute) : gera um fragmento XML [/manifest/Application/Provider](https://developer.android.com/guide/topics/manifest/provider-element.html) 
- [Android. Content. GrantUriPermissionAttribute](xref:Android.Content.GrantUriPermissionAttribute) : gera um fragmento XML [/manifest/Application/Provider/Grant-URI-Permission](https://developer.android.com/guide/topics/manifest/grant-uri-permission-element.html)
