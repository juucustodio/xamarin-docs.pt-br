---
title: Trabalhando com o manifesto do Android
ms.prod: xamarin
ms.assetid: CB7CCF60-FEF1-3B28-215F-159391E74347
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: 18817063900437baa625d8572f0ae28fec77be1e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30769800"
---
# <a name="working-with-the-android-manifest"></a>Trabalhando com o manifesto do Android


## <a name="overview"></a>Visão geral

**AndroidManifest.xml** é um arquivo eficiente na plataforma Android que descrevem a funcionalidade e os requisitos do seu aplicativo para Android. No entanto, trabalhar com ele não é fácil. Xamarin ajuda a minimizar essa dificuldade, permitindo que você adicione atributos personalizados para suas classes, que serão usados para gerar automaticamente o manifesto para você. Nosso objetivo é que 99% de nossos usuários nunca deve modificar manualmente **AndroidManifest.xml**. 

**AndroidManifest.xml** é gerado como parte do processo de compilação e o XML encontrado em **Properties/AndroidManifest.xml** é mesclado com o XML que é gerada a partir de atributos personalizados. Mesclado resultante **AndroidManifest.xml** reside no **obj** subdiretório; por exemplo, ela reside no **obj/Debug/android/AndroidManifest.xml** para compilações de depuração . O processo de mesclagem é simples: ele usa atributos personalizados dentro do código para gerar elementos XML, e *insere* esses elementos em **AndroidManifest.xml**. 



## <a name="the-basics"></a>Noções básicas

Em tempo de compilação, os assemblies são verificados para não -`abstract` classes que derivam de [atividade](https://developer.xamarin.com/api/type/Android.App.Activity/) e ter o [ `[Activity]` ](https://developer.xamarin.com/api/type/Android.App.ActivityAttribute/) atributo declarado neles. Ele então usa essas classes e atributos para criar o manifesto. Por exemplo, considere o seguinte código: 

```csharp
namespace Demo
{
    public class MyActivity : Activity
    {
    }
}
```

Isso resulta em nada que está sendo gerado em **AndroidManifest.xml**. Se você quiser uma `<activity/>` elemento a ser gerado, você precisa usar o [ `[Activity]` ](https://developer.xamarin.com/api/type/Android.App.Activity/Attribute) atributo personalizado: 

```csharp
namespace Demo
{
    [Activity]
    public class MyActivity : Activity
    {
    }
}
```

Este exemplo faz o seguinte fragmento xml a ser adicionado ao **AndroidManifest.xml**:

```xml
<activity android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

O `[Activity]` atributo não tem nenhum efeito `abstract` tipos; `abstract` tipos são ignorados.



### <a name="activity-name"></a>Nome da Atividade

Começando com o xamarin 5.1, o nome do tipo de uma atividade se baseia o MD5SUM do nome do assembly qualificado do tipo que está sendo exportado. Isso permite que o mesmo nome totalmente qualificado para ser fornecido de dois assemblies diferentes e não receber um erro de empacotamento. (Antes xamarin 5.1, o nome do tipo padrão da atividade foi criado de namespace minúscula e o nome de classe). 

Se você quiser substituir esse padrão e especificar explicitamente o nome da atividade, use o [ `Name` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Name/) propriedade: 

```csharp
[Activity (Name="awesome.demo.activity")]
public class MyActivity : Activity
{
}
```

Este exemplo produz o seguinte fragmento xml:

```xml
<activity android:name="awesome.demo.activity" />
```

*Observação*: você deve usar o `Name` propriedade apenas por motivos de compatibilidade com versões anteriores, como tal, renomeando pode causar lentidão na pesquisa do tipo em tempo de execução. Se você tiver código herdado que espera que o nome do tipo padrão da atividade deve se basear em minúscula namespace e nome de classe, consulte [nomenclatura Wrapper Callable Android](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming) para obter dicas sobre como manter a compatibilidade. 


### <a name="activity-title-bar"></a>Barra de título da atividade

Por padrão, o Android fornece seu aplicativo uma barra de título quando ele é executado. O valor usado para isso é [ `/manifest/application/activity/@android:label` ](http://developer.android.com/guide/topics/manifest/activity-element.html#label). Na maioria dos casos, esse valor será diferente de seu nome de classe. Para especificar o rótulo do aplicativo na barra de título, use o [ `Label` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Label/) propriedade.
Por exemplo: 

```csharp
[Activity (Label="Awesome Demo App")]
public class MyActivity : Activity
{
}
```

Este exemplo produz o seguinte fragmento xml:

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```


### <a name="launchable-from-application-chooser"></a>Pode ser iniciado a partir do seletor de aplicativo

Por padrão, a atividade não aparecerá na tela de iniciador do aplicativo do Android. Isso ocorre porque, provavelmente haverá muitas atividades em seu aplicativo, e você não quiser um ícone para cada um. Para especificar qual deve ser pode ser iniciado a partir do iniciador do aplicativo, use o [ `MainLauncher` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.MainLauncher/) propriedade. Por exemplo: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true)] 
public class MyActivity : Activity
{
}
```

Este exemplo produz o seguinte fragmento xml:

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

Por padrão, a atividade terá o ícone do inicializador padrão fornecido pelo sistema. Para usar um ícone personalizado, primeiro adicione o **. PNG** para **recursos/drawable**, definir sua ação de compilação **AndroidResource**, em seguida, use o [ `Icon` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Icon/) propriedade para especificar o ícone a ser usado. Por exemplo: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
public class MyActivity : Activity
{
}
```

Este exemplo produz o seguinte fragmento xml:

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

Quando você adicionar permissões para o manifesto do Android (conforme descrito em [adicionar permissões ao manifesto do Android](https://developer.xamarin.com/recipes/android/general/projects/add_permissions_to_android_manifest/)), essas permissões são registradas no **Properties/AndroidManifest.xml**. Por exemplo, se você definir o `INTERNET` permissão, o seguinte elemento é adicionado ao **Properties/AndroidManifest.xml**: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Compilações de depuração definido automaticamente algumas permissões para tornar mais fácil depurar (como `INTERNET` e `READ_EXTERNAL_STORAGE`) &ndash; essas configurações são definidas no gerado **obj/Debug/android/AndroidManifest.xml** e não são mostrado como ativada no **as permissões necessárias** configurações. 

Por exemplo, se você examinar o arquivo de manifesto gerado em **obj/Debug/android/AndroidManifest.xml**, você poderá ver o seguinte adicionou os elementos de permissão: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

Na versão de versão do manifesto de compilação (no **obj/Debug/android/AndroidManifest.xml**), essas permissões são *não* configurada automaticamente. Se você achar que alternar para um build de versão faz com que o aplicativo perca uma permissão que estava disponível na compilação de depuração, verifique se que você definiu explicitamente essa permissão no **as permissões necessárias** as configurações do seu aplicativo (consulte  **Compilação > aplicativo Android** no Visual Studio para Mac; consulte **Propriedades > manifesto do Android** no Visual Studio). 




## <a name="advanced-features"></a>Recursos avançados


### <a name="intent-actions-and-features"></a>Recursos e ações intencionais

O manifesto do Android fornece uma maneira de descrever os recursos de sua atividade. Isso é feito por meio de [tentativas](http://developer.android.com/guide/topics/manifest/intent-filter-element.html) e [ `[IntentFilter]` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) atributo personalizado. Você pode especificar quais ações são apropriadas para a atividade com o [ `IntentFilter` ](https://developer.xamarin.com/api/constructor/Android.App.IntentFilterAttribute.IntentFilterAttribute/p/System.String[]/) construtor e as categorias que são apropriadas com o [ `Categories` ](https://developer.xamarin.com/api/property/Android.App.IntentFilterAttribute.Categories/) propriedade. Pelo menos uma atividade deve ser fornecida (que é o motivo pelo qual as atividades são fornecidas no construtor). `[IntentFilter]` pode ser fornecido várias vezes, e cada uso resulta em uma separada `<intent-filter/>` elemento dentro do `<activity/>`. Por exemplo:

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
[IntentFilter (new[]{Intent.ActionView}, 
        Categories=new[]{Intent.CategorySampleCode, "my.custom.category"})]
public class MyActivity : Activity
{
}
```

Este exemplo produz o seguinte fragmento xml:

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


### <a name="application-element"></a>Elemento de aplicativo

O manifesto do Android também fornece uma maneira para que você declarar propriedades para o aplicativo inteiro. Isso é feito por meio de `<application>` elemento e seu complemento, o [aplicativo](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/) atributo personalizado. Observe que esses são o nível de aplicativo (assembly) todo em vez de configurações por atividade. Em geral, você declara `<application>` propriedades para o aplicativo inteiro e, em seguida, substituir essas configurações (conforme necessário) em uma base por atividade. 

Por exemplo, a seguinte `Application` atributo é adicionado à **AssemblyInfo.cs** para indicar que o aplicativo pode ser depurado, seu nome de usuário legível é **meu aplicativo**, e que ele usa o `Theme.Light` estilo como o tema padrão para todas as atividades: 

```csharp
[assembly: Application (Debuggable=true,   
                        Label="My App",   
                        Theme="@android:style/Theme.Light")]
```

Esta declaração faz com que o seguinte fragmento XML a ser gerado no **obj/Debug/android/AndroidManifest.xml**:

```xml
<application android:label="My App" 
             android:debuggable="true" 
             android:theme="@android:style/Theme.Light"
                ... />
```
Neste exemplo, todas as atividades no aplicativo padrão serão o `Theme.Light` estilo. Se você definir o tema de uma atividade `Theme.Dialog`, só que atividade usará o `Theme.Dialog` estilo enquanto todas as outras atividades em seu aplicativo usará como padrão o `Theme.Light` estilo conforme definido no `<application>` elemento. 

O `Application` elemento não é a única maneira de configurar `<application>` atributos. Como alternativa, você pode inserir atributos diretamente para o `<application>` elemento **Properties/AndroidManifest.xml**. Essas configurações são mescladas em final `<application>` que reside no elemento **obj/Debug/android/AndroidManifest.xml**. Observe que o conteúdo de **Properties/AndroidManifest.xml** sempre substituir dados fornecidos por atributos personalizados. 

Há muitos atributos de nível de aplicativo que você pode configurar o `<application>` elemento; para obter mais informações sobre essas configurações, consulte o [propriedades públicas](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/#Public_Properties) seção [ApplicationAttribute](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/). 



## <a name="list-of-custom-attributes"></a>Lista de atributos personalizados

-   [Android.App.ActivityAttribute](https://developer.xamarin.com/api/type/Android.App.ActivityAttribute/) : gera um [/manifest/application/activity](http://developer.android.com/guide/topics/manifest/activity-element.html) fragmento XML 
-   [Android.App.ApplicationAttribute](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/) : gera um [/manifesto de aplicativo](http://developer.android.com/guide/topics/manifest/application-element.html) fragmento XML 
-   [Android.App.InstrumentationAttribute](https://developer.xamarin.com/api/type/Android.App.InstrumentationAttribute/) : gera um [/manifesto de instrumentação](http://developer.android.com/guide/topics/manifest/instrumentation-element.html) fragmento XML 
-   [Android.App.IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) : gera um [//intent-filter](http://developer.android.com/guide/topics/manifest/intent-filter-element.html) fragmento XML 
-   [Android.App.MetaDataAttribute](https://developer.xamarin.com/api/type/Android.App.MetaDataAttribute/) : gera um [//meta-data](http://developer.android.com/guide/topics/manifest/meta-data-element.html) fragmento XML 
-   [Android.App.PermissionAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionAttribute/) : gera um [//permission](http://developer.android.com/guide/topics/manifest/permission-element.html) fragmento XML 
-   [Android.App.PermissionGroupAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionGroupAttribute/) : gera um [//permission-group](http://developer.android.com/guide/topics/manifest/permission-group-element.html) fragmento XML 
-   [Android.App.PermissionTreeAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionTreeAttribute/) : gera um [//permission-tree](http://developer.android.com/guide/topics/manifest/permission-tree-element.html) fragmento XML 
-   [Android.App.ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/) : gera um [/manifest/application/service](http://developer.android.com/guide/topics/manifest/service-element.html) fragmento XML 
-   [Android.App.UsesLibraryAttribute](https://developer.xamarin.com/api/type/Android.App.UsesLibraryAttribute/) : gera um [/manifest/application/uses-library](http://developer.android.com/guide/topics/manifest/uses-library-element.html) fragmento XML 
-   [Android.App.UsesPermissionAttribute](https://developer.xamarin.com/api/type/Android.App.UsesPermissionAttribute/) : gera um [/manifest/uses-permission](http://developer.android.com/guide/topics/manifest/uses-permission-element.html) fragmento XML 
-   [Android.Content.BroadcastReceiverAttribute](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiverAttribute/) : gera um [/manifest/application/receiver](http://developer.android.com/guide/topics/manifest/receiver-element.html) fragmento XML 
-   [Android.Content.ContentProviderAttribute](https://developer.xamarin.com/api/type/Android.Content.ContentProviderAttribute/) : gera um [/manifest/application/provider](http://developer.android.com/guide/topics/manifest/provider-element.html) fragmento XML 
-   [Android.Content.GrantUriPermissionAttribute](https://developer.xamarin.com/api/type/Android.Content.GrantUriPermissionAttribute/) : gera um [/manifest/application/provider/grant-uri-permission](http://developer.android.com/guide/topics/manifest/grant-uri-permission-element.html) fragmento XML

