---
title: Trabalhando com o Manifesto do Android
ms.prod: xamarin
ms.assetid: CB7CCF60-FEF1-3B28-215F-159391E74347
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/05/2018
ms.openlocfilehash: 1438c012608b367c21ebcc401c058b186b917f53
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027796"
---
# <a name="working-with-the-android-manifest"></a>Trabalhando com o Manifesto do Android

**AndroidManifest.xml** é um arquivo poderoso na plataforma Android que permite descrever as funcionalidades e requisitos do seu aplicativo para Android. No entanto, trabalhar com ele não é fácil. Xamarin.Android ajuda a minimizar essa dificuldade, permitindo que você adicione atributos personalizados às suas classes, que serão usados para gerar automaticamente o manifesto para você. Nosso objetivo é que 99% dos nossos usuários nunca precisem modificar manualmente **o AndroidManifest.xml**. 

**O AndroidManifest.xml** é gerado como parte do processo de compilação, e o XML encontrado dentro **do Properties/AndroidManifest.xml** é mesclado com XML que é gerado a partir de atributos personalizados. O **AndroidManifest.xml** mesclado resultante reside no subdiretório **obj;** por exemplo, ele reside em **compilações obj/Debug/android/AndroidManifest.xml** para compilações Debug. O processo de fusão é trivial: ele usa atributos personalizados dentro do código para gerar elementos XML, e *insere* esses elementos no **AndroidManifest.xml**. 

## <a name="the-basics"></a>Noções básicas

No momento da compilação, as assembléias são digitalizadas para não-classes`abstract` que derivam da [Atividade](xref:Android.App.Activity) e têm o atributo [`[Activity]`](xref:Android.App.ActivityAttribute) declarado neles. Em seguida, usa essas classes e atributos para construir o manifesto. Por exemplo, considere o seguinte código: 

```csharp
namespace Demo
{
    public class MyActivity : Activity
    {
    }
}
```

Isso resulta em nada sendo gerado no **AndroidManifest.xml**. Se você `<activity/>` quiser que um elemento seja gerado, você precisa usar o[`[Activity]`](xref:Android.App.Activity) 
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

Este exemplo faz com que o seguinte fragmento xml seja adicionado ao **AndroidManifest.xml**:

```xml
<activity android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

O `[Activity]` atributo não `abstract` tem efeito sobre os tipos; `abstract` tipos são ignorados.

### <a name="activity-name"></a>Nome da atividade

Começando com Xamarin.Android 5.1, o nome de uma atividade é baseado no MD5SUM do nome qualificado para montagem do tipo que está sendo exportado. Isso permite que o mesmo nome totalmente qualificado seja fornecido a partir de dois conjuntos diferentes e não obtenha um erro de embalagem. (Antes do Xamarin.Android 5.1, o nome padrão da atividade foi criado a partir do namespace minúsculo e do nome da classe.) 

Se desejar substituir esse padrão e especificar explicitamente o nome [`Name`](xref:Android.App.ActivityAttribute.Name) da sua atividade, use a propriedade: 

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

> [!NOTE]
> Você deve `Name` usar a propriedade apenas por razões de retrocompatibilidade, pois tal renomeação pode retardar a visualização do tipo em tempo de execução. Se você tiver um código legado que espera que o nome padrão da atividade seja baseado no namespace minúsculo e no nome da classe, consulte [O Nome do Wrapper Callable do Android](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#Android_Callable_Wrapper_Naming) para obter dicas sobre como manter a compatibilidade. 

### <a name="activity-title-bar"></a>Barra de título de atividade

Por padrão, o Android dá ao seu aplicativo uma barra de título quando ele é executado. O valor usado [`/manifest/application/activity/@android:label`](https://developer.android.com/guide/topics/manifest/activity-element.html#label)para isso é . Na maioria dos casos, esse valor será diferente do nome da sua classe. Para especificar o rótulo do seu aplicativo [`Label`](xref:Android.App.ActivityAttribute.Label) na barra de títulos, use a propriedade.
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

### <a name="launchable-from-application-chooser"></a>Lançável no App Chooser

Por padrão, sua atividade não aparecerá na tela do lançador de aplicativos do Android. Isso porque provavelmente haverá muitas atividades em seu aplicativo, e você não quer um ícone para cada um. Para especificar qual deve ser lançado a [`MainLauncher`](xref:Android.App.ActivityAttribute.MainLauncher) partir do lançador de aplicativos, use a propriedade. Por exemplo: 

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

Por padrão, sua atividade receberá o ícone de launcher padrão fornecido pelo sistema. Para usar um ícone personalizado, adicione primeiro o **seu .png** a **Recursos/drawable,** defina sua Build Action para **AndroidResource**e use a [`Icon`](xref:Android.App.ActivityAttribute.Icon) propriedade para especificar o ícone a ser usado. Por exemplo: 

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

Quando você adiciona permissões ao Manifesto do Android (conforme descrito em [Adicionar Permissões ao Manifesto do Android),](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)essas permissões são registradas em **Propriedades/AndroidManifest.xml**. Por exemplo, se `INTERNET` você definir a permissão, o elemento a seguir será adicionado ao **Properties/AndroidManifest.xml**: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Compilações de depuração definem automaticamente algumas permissões `INTERNET` `READ_EXTERNAL_STORAGE`para &ndash; facilitar a depuração (como e ) essas configurações são definidas apenas no **obj/Debug/android/AndroidManifest.xml** e não são mostradas como ativadas nas configurações **de permissões necessárias.** 

Por exemplo, se você examinar o arquivo manifesto gerado em **obj/Debug/android/AndroidManifest.xml,** você poderá ver os seguintes elementos de permissão adicionados: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

Na versão de compilação de versão do manifesto (em **obj/Debug/android/AndroidManifest.xml),** essas permissões *não* são configuradas automaticamente. Se você descobrir que a mudança para uma compilação de lançamento faz com que seu aplicativo perca uma permissão disponível na compilação Debug, verifique se você definiu explicitamente essa permissão nas configurações **de permissões necessárias** para o seu aplicativo (consulte **Build > Aplicativo Android** no Visual Studio para Mac; consulte Propriedades > O Manifesto do **Android** no Visual Studio). 

## <a name="advanced-features"></a>Recursos avançados

### <a name="intent-actions-and-features"></a>Ações e Recursos de Intenção

O manifesto do Android fornece uma maneira de você descrever as capacidades de sua atividade. Isso é feito através [de Intenções](https://developer.android.com/guide/topics/manifest/intent-filter-element.html) e o[`[IntentFilter]`](xref:Android.App.IntentFilterAttribute)
atributo personalizado. Você pode especificar quais ações são apropriadas para sua atividade com o[`IntentFilter`](xref:Android.App.IntentFilterAttribute#ctor*)
construtor, e quais categorias são apropriadas com o[`Categories`](xref:Android.App.IntentFilterAttribute.Categories)
property. Pelo menos uma atividade deve ser fornecida (razão pela qual as atividades são fornecidas na construtora). `[IntentFilter]`pode ser fornecido várias vezes, e `<intent-filter/>` cada uso `<activity/>`resulta em um elemento separado dentro do . Por exemplo:

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

O manifesto do Android também fornece uma maneira de você declarar propriedades para todo o seu aplicativo. Isso é feito `<application>` através do elemento e sua contrapartida, o atributo personalizado [do Aplicativo.](xref:Android.App.ApplicationAttribute) Observe que essas são configurações em todo o aplicativo (em toda a montagem) em vez de configurações por atividade. Normalmente, você `<application>` declara propriedades para todo o seu aplicativo e, em seguida, anula essas configurações (conforme necessário) em uma base por atividade. 

Por exemplo, `Application` o seguinte atributo é adicionado ao **AssemblyInfo.cs** para indicar que o aplicativo pode ser depurado, que seu nome legível pelo usuário é **Meu Aplicativo**e que ele usa o `Theme.Light` estilo como tema padrão para todas as atividades: 

```csharp
[assembly: Application (Debuggable=true,   
                        Label="My App",   
                        Theme="@android:style/Theme.Light")]
```

Esta declaração faz com que o seguinte fragmento XML seja gerado em **obj/Debug/android/AndroidManifest.xml**:

```xml
<application android:label="My App" 
             android:debuggable="true" 
             android:theme="@android:style/Theme.Light"
                ... />
```

Neste exemplo, todas as atividades no `Theme.Light` aplicativo serão padrão para o estilo. Se você definir o tema `Theme.Dialog`de uma atividade `Theme.Dialog` para , apenas que a Atividade `Theme.Light` usará o estilo, enquanto todas as outras atividades em seu aplicativo serão padrão para o estilo definido no `<application>` elemento. 

O `Application` elemento não é a `<application>` única maneira de configurar atributos. Alternativamente, você pode inserir atributos diretamente no `<application>` elemento de **Propriedades/AndroidManifest.xml**. Essas configurações são mescladas `<application>` no elemento final que reside no **obj/Debug/android/AndroidManifest.xml**. Observe que o conteúdo de **Propriedades/AndroidManifest.xml** sempre anula dados fornecidos por atributos personalizados. 

Existem muitos atributos em todo o `<application>` aplicativo que você pode configurar no elemento; para obter mais informações sobre essas configurações, consulte a seção [Propriedades Públicas](xref:Android.App.ApplicationAttribute) do [ApplicationAttribute](xref:Android.App.ApplicationAttribute). 

## <a name="list-of-custom-attributes"></a>Lista de atributos personalizados

- [Android.App.ActivityAttribute](xref:Android.App.ActivityAttribute) : Gera um fragmento [de XML /manifesto/aplicativo/atividade](https://developer.android.com/guide/topics/manifest/activity-element.html) 
- [Android.App.ApplicationAttribute](xref:Android.App.ApplicationAttribute) : Gera um fragmento [de XML /manifesto/aplicativo](https://developer.android.com/guide/topics/manifest/application-element.html) 
- [Android.App.InstrumentationAttribute](xref:Android.App.InstrumentationAttribute) : Gera um fragmento [XML de /manifesto/instrumentação](https://developer.android.com/guide/topics/manifest/instrumentation-element.html) 
- [Android.App.IntentFilterAttribute](xref:Android.App.IntentFilterAttribute) : Gera um fragmento [XML de filtro de //intenção](https://developer.android.com/guide/topics/manifest/intent-filter-element.html) 
- [Android.App.MetaDataAttribute](xref:Android.App.MetaDataAttribute) : Gera um fragmento [XML //meta-data](https://developer.android.com/guide/topics/manifest/meta-data-element.html) 
- [Android.App.PermissionAttribute](xref:Android.App.PermissionAttribute) : Gera um fragmento [XML de //permissão](https://developer.android.com/guide/topics/manifest/permission-element.html) 
- [Android.App.PermissionGroupAttribute](xref:Android.App.PermissionGroupAttribute) : Gera um fragmento [XML do grupo de permissões](https://developer.android.com/guide/topics/manifest/permission-group-element.html) 
- [Android.App.PermissionTreeAttribute](xref:Android.App.PermissionTreeAttribute) : Gera um fragmento [XML de árvore de //permissão](https://developer.android.com/guide/topics/manifest/permission-tree-element.html) 
- [Android.App.ServiceAttribute](xref:Android.App.ServiceAttribute) : Gera um fragmento [de /manifesto/aplicativo/serviço](https://developer.android.com/guide/topics/manifest/service-element.html) XML 
- [Android.App.UsesLibraryAttribute](xref:Android.App.UsesLibraryAttribute) : Gera um fragmento [XML de /manifesto/aplicativo/usa-biblioteca](https://developer.android.com/guide/topics/manifest/uses-library-element.html) 
- [Android.App.UsesPermissionAttribute](xref:Android.App.UsesPermissionAttribute) : Gera um fragmento XML [/manifesto/uso-permissão](https://developer.android.com/guide/topics/manifest/uses-permission-element.html) 
- [Android.Content.BroadcastReceiverAttribute](xref:Android.Content.BroadcastReceiverAttribute) : Gera um fragmento [de XML /manifesto/aplicativo/receptor](https://developer.android.com/guide/topics/manifest/receiver-element.html) 
- [Android.Content.ContentProviderAttribute](xref:Android.Content.ContentProviderAttribute) : Gera um fragmento [xml de /manifesto/aplicativo/provedor](https://developer.android.com/guide/topics/manifest/provider-element.html) 
- [Android.Content.GrantUriPermissionAttribute](xref:Android.Content.GrantUriPermissionAttribute) : Gera um fragmento [XML /manifesto/application/provider/grant-uri-permission](https://developer.android.com/guide/topics/manifest/grant-uri-permission-element.html)
