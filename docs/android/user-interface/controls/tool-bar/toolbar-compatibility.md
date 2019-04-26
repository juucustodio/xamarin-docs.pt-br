---
title: Compatibilidade da barra de ferramentas
ms.prod: xamarin
ms.assetid: A0798CA1-2C7D-43B6-9E91-4435CC7B6683
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: 12c19cf1024b78e8be30b7c9f2652019e9854375
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61300327"
---
# <a name="toolbar-compatibility"></a>Compatibilidade da barra de ferramentas


## <a name="overview"></a>Visão geral

Esta seção explica como usar `Toolbar` em versões do Android anteriores ao Android 5.0 Lollipop. Se seu aplicativo não oferece suporte a versões do Android anteriores ao Android 5.0, você pode ignorar esta seção. 

Porque `Toolbar` faz parte da biblioteca de suporte do Android v7, ele pode ser usado em dispositivos que executam o Android 2.1 (API nível 7) e superior. No entanto, o [biblioteca de suporte ao Android v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) NuGet deve ser instalado e o código modificado para que ele use o `Toolbar` implementação fornecida nesta biblioteca. Esta seção explica como instalar este NuGet e modificar as **ToolbarFun** aplicativo da [adicionando uma segunda barra de ferramentas](~/android/user-interface/controls/tool-bar/adding-a-second-toolbar.md) para que ele seja executado em versões do Android anteriores à 5.0 Lollipop.

Para modificar um aplicativo para usar a versão AppCompat da barra de ferramentas: 

1.  Defina as versões mínima e o destino do Android para o aplicativo.

2.  Instale o pacote do AppCompat NuGet.

3.  Use um tema AppCompat em vez de um tema Android internos.

4.  Modifique `MainActivity` para que ele subclasses `AppCompatActivity` em vez de `Activity`. 

Cada uma dessas etapas é explicada em detalhes nas seções a seguir.



## <a name="set-the-minimum-and-target-android-version"></a>Definir o mínimo e a versão do Android de destino

Estrutura de destino do aplicativo deve ser definida como API nível 21 ou maior ou o aplicativo não será implantado corretamente. Se um erro como **nenhum identificador de recurso encontrado para o atributo 'tileModeX' no pacote 'android'** é visto durante a implantação do aplicativo, isso é porque a estrutura de destino não está definida como **Android 5.0 (API nível 21 - pirulito)**  ou maior. 

Definir a estrutura de destino de nível para nível de API 21 ou maior e definir as configurações de projeto de nível de API do Android para a versão mínima do Android que o aplicativo deve dar suporte. Para obter mais informações sobre como definir níveis de API do Android, consulte [Noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md). No `ToolbarFun` exemplo, a versão mínima do Android é definido como KitKat (API nível 4.4). 


## <a name="install-the-appcompat-nuget-package"></a>Instale o pacote AppCompat NuGet

Em seguida, adicione a [biblioteca de suporte ao Android v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) pacote ao projeto. No Visual Studio, clique com botão direito **referências** e selecione **gerenciar pacotes NuGet...** . Clique em **navegue** e pesquise **biblioteca de suporte ao Android v7 AppCompat**. Selecione **Xamarin.Android.Support.v7.AppCompat** e clique em **instalar**: 

[![Pacote de captura de tela de V7 Appcompat selecionado em Gerenciar pacotes NuGet](toolbar-compatibility-images/01-appcompat-nuget-sml.png)](toolbar-compatibility-images/01-appcompat-nuget.png#lightbox)

Quando este NuGet está instalado, vários outros pacotes do NuGet também são instalados se ainda não existir (por exemplo, **Xamarin.Android.Support.Animated.Vector.Drawable**, **Xamarin.Android.Support.v4**, e **Xamarin.Android.Support.Vector.Drawable**). Para obter mais informações sobre como instalar pacotes do NuGet, consulte [passo a passo: Incluindo um NuGet em seu projeto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough). 


## <a name="use-an-appcompat-theme-and-toolbar"></a>Usar um tema AppCompat e a barra de ferramentas

A biblioteca AppCompat vem com vários `Theme.AppCompat` temas que podem ser usados em qualquer versão do Android com suporte pela biblioteca AppCompat. O `ToolbarFun` tema do aplicativo de exemplo é derivado de `Theme.Material.Light.DarkActionBar`, que não está disponível em versões anteriores de pirulito. Portanto, `ToolbarFun` devem ser adaptados para uso a contraparte AppCompat para este tema `Theme.AppCompat.Light.DarkActionBar`. Além disso, porque `Toolbar` é não está disponível em versões do Android anteriores ao Lollipop, devemos usar a versão AppCompat do `Toolbar`. Portanto, devem usar os layouts `android.support.v7.widget.Toolbar` em vez de `Toolbar`. 


### <a name="update-layouts"></a>Layouts de atualização

Edite **Resources/layout/Main.axml** e substitua o `Toolbar` elemento com o XML a seguir: 

```xml
<android.support.v7.widget.Toolbar
    android:id="@+id/edit_toolbar"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorAccent"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

Edite **Resources/layout/toolbar.xml** e substitua seu conteúdo pelo seguinte XML: 

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

Observe que o `?attr` valores não têm o prefixo `android:` (Lembre-se de que o `?` notação faz referência a um recurso no tema atual). Se `?android:attr` ainda foram usados aqui, o Android faria referência o valor do atributo da plataforma em execução no momento, em vez da biblioteca de AppCompat. Como este exemplo usa o `actionBarSize` definidas pela biblioteca AppCompat, o `android:` prefixo é descartado. Da mesma forma, `@android:style` é alterado para `@style` para que o `android:theme` atributo é definido como um tema na biblioteca AppCompat &ndash; o `ThemeOverlay.AppCompat.Dark.ActionBar` tema é usado aqui em vez de `ThemeOverlay.Material.Dark.ActionBar`. 


### <a name="update-the-style"></a>Atualizar o estilo

Edite **Resources/values/styles.xml** e substitua seu conteúdo pelo seguinte XML: 

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

Os nomes de item e o tema do pai neste exemplo não são prefixados com `android:` porque estamos usando a biblioteca AppCompat. Além disso, o tema do pai é alterado para a versão AppCompat do `Light.DarkActionBar`. 



### <a name="update-menus"></a>Menus de atualização

Para dar suporte a versões anteriores do Android, a biblioteca de AppCompat usa atributos personalizados que refletem os atributos do `android:` namespace. No entanto, alguns atributos (como o `showAsAction` atributo usado em de `<menu>` marca) não existem no Android framework em dispositivos mais antigos &ndash; `showAsAction` foi introduzido em 11 de API do Android, mas não está disponível no Android 7 API. Por esse motivo, um namespace personalizado deve ser usado para prefixar todos os atributos definidos pela biblioteca de suporte. Nos arquivos de recurso de menu, um namespace chamado `local` é definido para prefixar o `showAsAction` atributo. 

Edite **Resources/menu/top_menus.xml** e substitua seu conteúdo pelo seguinte XML:

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

O `local` namespace é adicionado com esta linha:

```xml
xmlns:local="http://schemas.android.com/apk/res-auto">
```

O `showAsAction` atributo seja prefixado com isso `local:` namespace em vez de `android:` 

```csharp
local:showAsAction="ifRoom"
```

Da mesma forma, edite **Resources/menu/edit_menus.xml** e substitua seu conteúdo pelo seguinte XML:

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

Como essa opção namespace fornecem suporte para o `showAsAction` atributo em versões do Android anteriores a 11 de nível de API? O atributo personalizado `showAsAction` e todos os seus valores possíveis são incluídos no aplicativo quando o AppCompat NuGet está instalado. 


## <a name="subclass-appcompatactivity"></a>Subclasse AppCompatActivity

A etapa final na conversão é modificar `MainActivity` para que ele seja uma subclasse de `AppCompactActivity`. Edite **MainActivity.cs** e adicione o seguinte `using` instruções: 

```csharp
using Android.Support.V7.App;
using Toolbar = Android.Support.V7.Widget.Toolbar;
```

Isso declara `Toolbar` como a versão AppCompat do `Toolbar`. Em seguida, altere a definição de classe `MainActivity`: 

```csharp
public class MainActivity : AppCompatActivity
```

Para definir a barra de ação à versão do AppCompat `Toolbar`, substitua a chamada para `SetActionBar` com `SetSupportActionBar`. Neste exemplo, o título também é alterado para indicar que a versão AppCompat do `Toolbar` está sendo usada:

```csharp
SetSupportActionBar (toolbar);
SupportActionBar.Title = "My AppCompat Toolbar";
```

Por fim, altere o nível mínimo de Android para o valor de pirulito de pré-lançamento que deve ter suporte (por exemplo, a API 19). 

Compile o aplicativo e executá-lo em um dispositivo de pirulito pré ou o emulador do Android. Captura de tela a seguir mostra a versão de AppCompat do **ToolbarFun** em um Nexus 4 executando KitKat (API 19): 

[![Captura de tela inteira do aplicativo em execução em um dispositivo KitKat, ambas as barras de ferramentas são mostradas](toolbar-compatibility-images/02-running-on-kitkat-sml.png)](toolbar-compatibility-images/02-running-on-kitkat.png#lightbox)

Quando a biblioteca AppCompat é usada, temas não precisa ser alternado com base na versão do Android &ndash; a biblioteca AppCompat torna possível fornecer uma experiência de usuário consistente em todas as versões com suporte do Android. 




## <a name="related-links"></a>Links relacionados

- [Barra de ferramentas de pirulito (amostra)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Barra de ferramentas AppCompat (amostra)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
