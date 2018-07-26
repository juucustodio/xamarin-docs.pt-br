---
title: Trabalhando com o manifesto do Android
ms.prod: xamarin
ms.assetid: CB7CCF60-FEF1-3B28-215F-159391E74347
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: 0857b70e6e1d9104f62ec2e26f8edbab385d06f3
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242245"
---
# <a name="working-with-the-android-manifest"></a>Trabalhando com o manifesto do Android


## <a name="overview"></a>Visão geral

**Androidmanifest. XML** é um arquivo poderoso na plataforma do Android que permite que você descrevem a funcionalidade e os requisitos do seu aplicativo para Android. No entanto, trabalhar com ele, não é fácil. Xamarin. Android ajuda a minimizar essa dificuldade, permitindo que você adicione atributos personalizados para suas classes, que serão usados para gerar automaticamente o manifesto para você. Nosso objetivo é que 99% de nossos usuários nunca deve modificar manualmente **androidmanifest. XML**. 

**Androidmanifest. XML** é gerado como parte do processo de compilação e o XML encontrado dentro **androidmanifest** é mesclado com XML que é gerado a partir de atributos personalizados. Mesclado resultante **androidmanifest. XML** reside na **obj** subdiretório; por exemplo, ele reside na **obj/Debug/android/AndroidManifest.xml** para compilações de depuração . O processo de mesclagem é simples: ele usa os atributos personalizados dentro do código para gerar elementos XML, e *insere* esses elementos **androidmanifest. XML**. 



## <a name="the-basics"></a>Os conceitos básicos

Em tempo de compilação, os assemblies são examinados de não -`abstract` classes que derivam [atividade](https://developer.xamarin.com/api/type/Android.App.Activity/) e ter o [ `[Activity]` ](https://developer.xamarin.com/api/type/Android.App.ActivityAttribute/) atributo declarado neles. Ele usa essas classes e atributos para compilar o manifesto. Por exemplo, considere o seguinte código: 

```csharp
namespace Demo
{
    public class MyActivity : Activity
    {
    }
}
```

Isso resulta em nada que está sendo gerado na **androidmanifest. XML**. Se você quiser que um `<activity/>` elemento a ser gerado, você precisa usar o [ `[Activity]` ](https://developer.xamarin.com/api/type/Android.App.Activity/Attribute) atributo personalizado: 

```csharp
namespace Demo
{
    [Activity]
    public class MyActivity : Activity
    {
    }
}
```

Este exemplo faz o seguinte fragmento xml a ser adicionado ao **androidmanifest. XML**:

```xml
<activity android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

O `[Activity]` atributo não tem nenhum efeito `abstract` tipos; `abstract` tipos são ignorados.



### <a name="activity-name"></a>Nome da Atividade

Começando com o xamarin. Android 5.1, o nome do tipo de uma atividade se baseia o MD5SUM do nome qualificado pelo assembly do tipo que está sendo exportado. Isso permite que o mesmo nome totalmente qualificado para ser fornecidos por meio de dois assemblies diferentes e não receber um erro de empacotamento. (Antes do xamarin. Android 5.1, o nome do tipo padrão de atividade foi criado do namespace em minúscula e o nome de classe). 

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

*Observação*: você deve usar o `Name` propriedade apenas por motivos de compatibilidade com versões anteriores, como tal, renomeando pode reduzir a velocidade de pesquisa do tipo em tempo de execução. Se você tiver código herdado que espera que o nome do tipo padrão da atividade deve ser baseado no namespace em minúscula e o nome de classe, consulte [nomenclatura de Wrapper que pode ser chamado de Android](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming) para obter dicas sobre como manter a compatibilidade. 


### <a name="activity-title-bar"></a>Barra de título da atividade

Por padrão, o Android fornece seu aplicativo uma barra de título quando ele é executado. É o valor usado para este [ `/manifest/application/activity/@android:label` ](http://developer.android.com/guide/topics/manifest/activity-element.html#label). Na maioria dos casos, esse valor será diferente de seu nome de classe. Para especificar o rótulo do seu aplicativo na barra de título, use o [ `Label` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Label/) propriedade.
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


### <a name="launchable-from-application-chooser"></a>Pode ser inicializado no seletor de aplicativo

Por padrão, sua atividade não aparecerá na tela de inicialização do aplicativo do Android. Isso ocorre porque, provavelmente haverá muitas atividades em seu aplicativo, e você não quer um ícone para cada um. Para especificar qual delas deve ser pode ser inicializado no iniciador do aplicativo, use o [ `MainLauncher` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.MainLauncher/) propriedade. Por exemplo: 

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

Por padrão, sua atividade terá o ícone do inicializador padrão fornecido pelo sistema. Para usar um ícone personalizado, primeiro adicione sua **. PNG** para **recursos/drawable**, defina sua ação de compilação **AndroidResource**, em seguida, use o [ `Icon` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Icon/) propriedade para especificar o ícone a ser usado. Por exemplo: 

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

Quando você adiciona as permissões para o manifesto do Android (conforme descrito em [adicionar permissões ao manifesto do Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)), essas permissões são registradas no **androidmanifest**. Por exemplo, se você definir a `INTERNET` permissão, o seguinte elemento é adicionado ao **androidmanifest**: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Compilações de depuração definida automaticamente algumas permissões para facilitar a depuração (como `INTERNET` e `READ_EXTERNAL_STORAGE`) &ndash; essas configurações são definidas no gerado **obj/Debug/android/AndroidManifest.xml** e não são mostrado como habilitado na **permissões necessárias** configurações. 

Por exemplo, se você examinar o arquivo de manifesto gerado em **obj/Debug/android/AndroidManifest.xml**, talvez você veja a seguir adicionou os elementos de permissão: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

Na versão versão do manifesto de compilação (no **obj/Debug/android/AndroidManifest.xml**), essas permissões são *não* configuradas automaticamente. Se você achar que alternar para um build de versão faz com que o aplicativo perca uma permissão que estava disponível no build de depuração, verifique se que você definiu explicitamente essa permissão na **permissões necessárias** configurações para seu aplicativo (veja  **Build > aplicativo Android** no Visual Studio para Mac; consulte **Propriedades > manifesto do Android** no Visual Studio). 




## <a name="advanced-features"></a>Recursos avançados


### <a name="intent-actions-and-features"></a>Recursos e ações intencionais

O manifesto do Android fornece uma maneira de descrever os recursos de sua atividade. Isso é feito por meio [intenções](http://developer.android.com/guide/topics/manifest/intent-filter-element.html) e o [ `[IntentFilter]` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) atributo personalizado. Você pode especificar quais ações são apropriadas para sua atividade com o [ `IntentFilter` ](https://developer.xamarin.com/api/constructor/Android.App.IntentFilterAttribute.IntentFilterAttribute/p/System.String[]/) construtor e quais categorias são apropriadas com o [ `Categories` ](https://developer.xamarin.com/api/property/Android.App.IntentFilterAttribute.Categories/) propriedade. Pelo menos uma atividade deve ser fornecida (motivo pelo qual as atividades são fornecidas no construtor). `[IntentFilter]` pode ser fornecido várias vezes, e cada uso resulta em um separado `<intent-filter/>` elemento dentro do `<activity/>`. Por exemplo:

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


### <a name="application-element"></a>Elemento Application

O manifesto do Android também fornece uma maneira para que você possa declarar propriedades para todo o seu aplicativo. Isso é feito por meio de `<application>` elemento e sua contraparte, o [aplicativo](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/) atributo personalizado. Observe que essas são as configurações (todo o assembly) de todo o aplicativo em vez de configurações por atividade. Normalmente, você declara `<application>` propriedades para todo o seu aplicativo e, em seguida, substituir essas configurações (conforme necessário) em uma base por atividade. 

Por exemplo, a seguinte `Application` atributo é adicionado à **AssemblyInfo.cs** para indicar que o aplicativo pode ser depurado, seu nome legível pelo usuário é **My App**, e que usa o `Theme.Light` estilo como o tema padrão para todas as atividades: 

```csharp
[assembly: Application (Debuggable=true,   
                        Label="My App",   
                        Theme="@android:style/Theme.Light")]
```

Essa declaração faz com que o seguinte fragmento XML a ser gerado no **obj/Debug/android/AndroidManifest.xml**:

```xml
<application android:label="My App" 
             android:debuggable="true" 
             android:theme="@android:style/Theme.Light"
                ... />
```
Neste exemplo, todas as atividades no aplicativo usará como padrão o `Theme.Light` estilo. Se você definir o tema de uma atividade como `Theme.Dialog`, apenas que atividade usará o `Theme.Dialog` estilo enquanto todas as outras atividades em seu aplicativo usará como padrão o `Theme.Light` estilo conforme definido no `<application>` elemento. 

O `Application` elemento não é a única maneira de configurar `<application>` atributos. Como alternativa, você pode inserir atributos diretamente para o `<application>` elemento de **androidmanifest**. Essas configurações são mescladas em final `<application>` que reside no elemento **obj/Debug/android/AndroidManifest.xml**. Observe que o conteúdo do **androidmanifest** sempre substituem os dados fornecidos por atributos personalizados. 

Há muitos atributos de todo o aplicativo que você pode configurar o `<application>` elemento; para obter mais informações sobre essas configurações, consulte a [propriedades públicas](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/#Public_Properties) seção [ApplicationAttribute](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/). 



## <a name="list-of-custom-attributes"></a>Lista de atributos personalizados

-   [Android.App.ActivityAttribute](https://developer.xamarin.com/api/type/Android.App.ActivityAttribute/) : gera uma [/manifest/application/activity](http://developer.android.com/guide/topics/manifest/activity-element.html) fragmento XML 
-   [Android.App.ApplicationAttribute](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/) : gera uma [manifesto/aplicativo](http://developer.android.com/guide/topics/manifest/application-element.html) fragmento XML 
-   [Android.App.InstrumentationAttribute](https://developer.xamarin.com/api/type/Android.App.InstrumentationAttribute/) : gera uma [manifesto/instrumentação](http://developer.android.com/guide/topics/manifest/instrumentation-element.html) fragmento XML 
-   [Android.App.IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) : gera uma [//intent-filter](http://developer.android.com/guide/topics/manifest/intent-filter-element.html) fragmento XML 
-   [Android.App.MetaDataAttribute](https://developer.xamarin.com/api/type/Android.App.MetaDataAttribute/) : gera uma [//meta-data](http://developer.android.com/guide/topics/manifest/meta-data-element.html) fragmento XML 
-   [Android.App.PermissionAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionAttribute/) : gera uma [//permission](http://developer.android.com/guide/topics/manifest/permission-element.html) fragmento XML 
-   [Android.App.PermissionGroupAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionGroupAttribute/) : gera uma [//permission-group](http://developer.android.com/guide/topics/manifest/permission-group-element.html) fragmento XML 
-   [Android.App.PermissionTreeAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionTreeAttribute/) : gera uma [//permission-tree](http://developer.android.com/guide/topics/manifest/permission-tree-element.html) fragmento XML 
-   [Android.App.ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/) : gera uma [/manifest/application/service](http://developer.android.com/guide/topics/manifest/service-element.html) fragmento XML 
-   [Android.App.UsesLibraryAttribute](https://developer.xamarin.com/api/type/Android.App.UsesLibraryAttribute/) : gera uma [/manifest/application/uses-library](http://developer.android.com/guide/topics/manifest/uses-library-element.html) fragmento XML 
-   [Android.App.UsesPermissionAttribute](https://developer.xamarin.com/api/type/Android.App.UsesPermissionAttribute/) : gera uma [/manifest/uses-permission](http://developer.android.com/guide/topics/manifest/uses-permission-element.html) fragmento XML 
-   [Android.Content.BroadcastReceiverAttribute](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiverAttribute/) : gera uma [/manifest/application/receiver](http://developer.android.com/guide/topics/manifest/receiver-element.html) fragmento XML 
-   [Android.Content.ContentProviderAttribute](https://developer.xamarin.com/api/type/Android.Content.ContentProviderAttribute/) : gera uma [/manifest/application/provider](http://developer.android.com/guide/topics/manifest/provider-element.html) fragmento XML 
-   [Android.Content.GrantUriPermissionAttribute](https://developer.xamarin.com/api/type/Android.Content.GrantUriPermissionAttribute/) : gera uma [/manifest/application/provider/grant-uri-permission](http://developer.android.com/guide/topics/manifest/grant-uri-permission-element.html) fragmento XML

