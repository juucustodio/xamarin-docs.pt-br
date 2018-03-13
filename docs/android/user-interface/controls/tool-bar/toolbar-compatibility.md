---
title: Compatibilidade de barra de ferramentas
ms.topic: article
ms.prod: xamarin
ms.assetid: A0798CA1-2C7D-43B6-9E91-4435CC7B6683
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: a17ad79d3f3b537332494fc368c878f2733d5db2
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="toolbar-compatibility"></a>Compatibilidade de barra de ferramentas


## <a name="overview"></a>Visão geral

Esta seção explica como usar `Toolbar` em versões anteriores ao Android 5.0 de pirulito do Android. Se seu aplicativo não oferece suporte a versões anteriores ao Android 5.0 do Android, você poderá ignorar esta seção. 

Porque `Toolbar` faz parte da biblioteca de suporte do Android v7, ele pode ser usado em dispositivos que executam o Android 2.1 (API nível 7) e superior. No entanto, o [biblioteca de suporte Android v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) NuGet deve ser instalado e o código modificado para que ele usa o `Toolbar` implementação fornecida nesta biblioteca. Esta seção explica como instalar esse NuGet e modificar o **ToolbarFun** aplicativo de [adicionando uma segunda barra de ferramentas](~/android/user-interface/controls/tool-bar/adding-a-second-toolbar.md) para que ele seja executado em versões anteriores à 5.0 pirulito do Android.

Para modificar um aplicativo para usar a versão de AppCompat da barra de ferramentas: 

1.  Defina as versões de mínimo e o destino Android para o aplicativo.

2.  Instale o pacote NuGet de AppCompat.

3.  Use um tema de AppCompat em vez de um tema Android interno.

4.  Modificar `MainActivity` para que ele subclasses `AppCompatActivity` em vez de `Activity`. 

Cada uma dessas etapas é explicada em detalhes nas seções a seguir.



## <a name="set-the-minimum-and-target-android-version"></a>Definir o mínimo e a versão destino do Android

Estrutura de destino do aplicativo deve ser definida como 21 de nível de API ou superior ou o aplicativo não será implantado corretamente. Se um erro como **nenhum identificador de recurso encontrado para o atributo 'tileModeX' no pacote 'android'** é visto durante a implantação do aplicativo, isso é porque a estrutura de destino não está definida como **Android 5.0 (API nível 21 - pirulito)**  ou maior. 

Defina a estrutura de destino a 21 de nível de API ou maior e definir as configurações de nível de projeto de API do Android para a versão do Android mínimo que o aplicativo deve dar suporte. Para obter mais informações sobre como definir níveis de API do Android, consulte [Noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md). No `ToolbarFun` exemplo, a versão do Android mínimo é definida como KitKat (API nível 4.4). 


## <a name="install-the-appcompat-nuget-package"></a>Instale o pacote NuGet de AppCompat

Em seguida, adicione o [biblioteca de suporte Android v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) pacote ao projeto. No Visual Studio, clique com botão direito **referências** e selecione **gerenciar pacotes NuGet...** . Clique em **procurar** e procure **biblioteca de suporte Android v7 AppCompat**. Selecione **Xamarin.Android.Support.v7.AppCompat** e clique em **instalar**: 

[![Captura de tela de Appcompat V7 pacote selecionado em Gerenciar pacotes do NuGet](toolbar-compatibility-images/01-appcompat-nuget-sml.png)](toolbar-compatibility-images/01-appcompat-nuget.png#lightbox)

Quando este NuGet é instalado, vários outros pacotes do NuGet também são instalados se não estiver presente (como **Xamarin.Android.Support.Animated.Vector.Drawable**, **Xamarin.Android.Support.v4**, e **Xamarin.Android.Support.Vector.Drawable**). Para obter mais informações sobre como instalar pacotes do NuGet, consulte [passo a passo: incluindo um NuGet em seu projeto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough). 


## <a name="use-an-appcompat-theme-and-toolbar"></a>Usar um tema de AppCompat e a barra de ferramentas

A biblioteca de AppCompat vem com vários `Theme.AppCompat` temas que podem ser usados em qualquer versão do Android com suporte pela biblioteca de AppCompat. O `ToolbarFun` tema do aplicativo de exemplo é derivado de `Theme.Material.Light.DarkActionBar`, que não está disponível no Android versões anteriores de pirulito. Portanto, `ToolbarFun` devem ser adaptados para usar a contraparte de AppCompat para este tema `Theme.AppCompat.Light.DarkActionBar`. Além disso, porque `Toolbar` é não está disponível em versões do Android anteriores pirulito, podemos deve usar a versão de AppCompat do `Toolbar`. Portanto, devem usar os layouts `android.support.v7.widget.Toolbar` em vez de `Toolbar`. 


### <a name="update-layouts"></a>Layouts de atualização

Editar **Resources/layout/Main.axml** e substitua o `Toolbar` elemento com o XML a seguir: 

```xml
<android.support.v7.widget.Toolbar
    android:id="@+id/edit_toolbar"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorAccent"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

Editar **Resources/layout/toolbar.xml** e substitua o seu conteúdo com o seguinte XML: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v7.widget.Toolbar xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"/>
```

Observe que o `?attr` valores não são prefixados com `android:` (Lembre-se de que o `?` notação faz referência a um recurso no tema atual). Se `?android:attr` ainda foram usados aqui, Android deve referenciar o valor do atributo da plataforma em execução no momento, em vez da biblioteca de AppCompat. Como este exemplo usa o `actionBarSize` definido pela biblioteca AppCompat, o `android:` prefixo é descartado. Da mesma forma, `@android:style` é alterado para `@style` para que o `android:theme` atributo é definido como um tema na biblioteca de AppCompat &ndash; o `ThemeOverlay.AppCompat.Dark.ActionBar` tema é usado aqui, em vez de `ThemeOverlay.Material.Dark.ActionBar`. 


### <a name="update-the-style"></a>O estilo de atualização

Editar **Resources/values/styles.xml** e substitua o seu conteúdo com o seguinte XML: 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<resources>
  <style name="MyTheme" parent="MyTheme.Base"> </style>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light.DarkActionBar">
    <item name="windowNoTitle">true</item>
    <item name="windowActionBar">false</item>
    <item name="colorPrimary">#5A8622</item>
    <item name="colorAccent">#A88F2D</item>
  </style>
</resources>
```

Os nomes de item e o tema do pai neste exemplo não são prefixados com `android:` porque estamos usando a biblioteca de AppCompat. Além disso, o tema do pai for alterado para a versão de AppCompat do `Light.DarkActionBar`. 



### <a name="update-menus"></a>Menus de atualização

Para dar suporte a versões anteriores do Android, a biblioteca de AppCompat usa atributos personalizados que espelha os atributos do `android:` namespace. No entanto, alguns atributos (como o `showAsAction` atributo usado no `<menu>` marca) não existe no framework Android em dispositivos mais antigos &ndash; `showAsAction` foi introduzido em 11 de API do Android, mas não está disponível em 7 de API do Android. Por esse motivo, um namespace personalizado deve ser usado para prefixar todos os atributos definidos pela biblioteca de suporte. Nos arquivos de recurso de menu, um namespace chamado `local` está definido para prefixar o `showAsAction` atributo. 

Editar **Resources/menu/top_menus.xml** e substitua o seu conteúdo com o seguinte XML:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:local="http://schemas.android.com/apk/res-auto">
  <item
       android:id="@+id/menu_edit"
       android:icon="@mipmap/ic_action_content_create"
       local:showAsAction="ifRoom"
       android:title="Edit" />
  <item
       android:id="@+id/menu_save"
       android:icon="@mipmap/ic_action_content_save"
       local:showAsAction="ifRoom"
       android:title="Save" />
  <item
       android:id="@+id/menu_preferences"
       local:showAsAction="never"
       android:title="Preferences" />
</menu>
```

O `local` namespace é adicionado com essa linha:

```xml
xmlns:local="http://schemas.android.com/apk/res-auto">
```

O `showAsAction` antecedido com esse atributo `local:` namespace em vez de `android:` 

```csharp
local:showAsAction="ifRoom"
```

Da mesma forma, editar **Resources/menu/edit_menus.xml** e substitua o seu conteúdo com o seguinte XML:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:local="http://schemas.android.com/apk/res-auto">
  <item
       android:id="@+id/menu_cut"
       android:icon="@mipmap/ic_menu_cut_holo_dark"
       local:showAsAction="ifRoom"
       android:title="Cut" />
  <item
       android:id="@+id/menu_copy"
       android:icon="@mipmap/ic_menu_copy_holo_dark"
       local:showAsAction="ifRoom"
       android:title="Copy" />
  <item
       android:id="@+id/menu_paste"
       android:icon="@mipmap/ic_menu_paste_holo_dark"
       local:showAsAction="ifRoom"
       android:title="Paste" />
</menu>
```

Como essa opção namespace fornecem suporte para o `showAsAction` atributo no Android versões antes de 11 de nível de API? O atributo personalizado `showAsAction` e todos os seus valores possíveis são incluídos no aplicativo quando o AppCompat NuGet está instalado. 


## <a name="subclass-appcompatactivity"></a>Subclasse AppCompatActivity

A etapa final na conversão é modificar `MainActivity` para que seja uma subclasse de `AppCompactActivity`. Editar **MainActivity.cs** e adicione o seguinte `using` instruções: 

```csharp
using Android.Support.V7.App;
using Toolbar = Android.Support.V7.Widget.Toolbar;
```

Isso declara `Toolbar` para ser a versão de AppCompat de `Toolbar`. Em seguida, alterar a definição de classe `MainActivity`: 

```csharp
public class MainActivity : AppCompatActivity
```

Para definir a barra de ação para a versão de AppCompat do `Toolbar`, substitua a chamada para `SetActionBar` com `SetSupportActionBar`. Neste exemplo, o título também é alterado para indicar que a versão de AppCompat do `Toolbar` está sendo usado:

```csharp
SetSupportActionBar (toolbar);
SupportActionBar.Title = "My AppCompat Toolbar";
```

Por fim, altere o nível mínimo Android para o valor de pré-pirulito com suporte (por exemplo, API 19). 

Compile o aplicativo e executá-lo em um dispositivo de pirulito pré ou o emulador Android. Captura de tela a seguir mostra a versão de AppCompat do **ToolbarFun** em 4 Nexus executando KitKat (API 19): 

[![Captura de tela inteira do aplicativo em execução em um dispositivo KitKat, ambas as barras de ferramentas são mostradas](toolbar-compatibility-images/02-running-on-kitkat-sml.png)](toolbar-compatibility-images/02-running-on-kitkat.png#lightbox)

Quando a biblioteca de AppCompat é usada, temas não precisam ser alternado com base na versão do Android &ndash; a biblioteca de AppCompat torna possível fornecer uma experiência de usuário consistente em todas as versões com suporte do Android. 




## <a name="related-links"></a>Links relacionados

- [Barra de ferramentas de pirulito (exemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Barra de ferramentas de AppCompat (exemplo)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
