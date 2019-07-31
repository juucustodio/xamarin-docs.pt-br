---
title: Compatibilidade da barra de ferramentas
ms.prod: xamarin
ms.assetid: A0798CA1-2C7D-43B6-9E91-4435CC7B6683
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: ce13532026e2c49cb733e5afc0f6abd41ff1abdd
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645030"
---
# <a name="toolbar-compatibility"></a>Compatibilidade da barra de ferramentas


## <a name="overview"></a>Visão geral

Esta seção explica como usar `Toolbar` versões do Android anteriores ao Android 5,0 pirulito. Se seu aplicativo não oferecer suporte a versões do Android anteriores ao Android 5,0, você poderá ignorar esta seção. 

Como `Toolbar` o faz parte da biblioteca de suporte do Android v7, ele pode ser usado em dispositivos que executam o Android 2,1 (API nível 7) e superior. No entanto, a [biblioteca de suporte do Android v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) NuGet deve ser instalada e o código modificado para `Toolbar` que ele use a implementação fornecida nesta biblioteca. Esta seção explica como instalar esse NuGet e modificar o aplicativo **ToolbarFun** da [adição de uma segunda barra de ferramentas](~/android/user-interface/controls/tool-bar/adding-a-second-toolbar.md) para que ela seja executada em versões do Android anteriores à pirulito 5,0.

Para modificar um aplicativo para usar a versão do AppCompat da barra de ferramentas: 

1.  Defina as versões mínima e de destino do Android para o aplicativo.

2.  Instale o pacote NuGet do AppCompat.

3.  Use um tema AppCompat em vez de um tema interno do Android.

4.  Modifique `MainActivity` para que ela seja `AppCompatActivity` subclasse em vez `Activity`de. 

Cada uma dessas etapas é explicada em detalhes nas seções a seguir.



## <a name="set-the-minimum-and-target-android-version"></a>Definir a versão mínima e de destino do Android

A estrutura de destino do aplicativo deve ser definida como a API nível 21 ou superior ou o aplicativo não será implantado corretamente. Se um erro como **nenhum identificador de recurso encontrado para o atributo ' tileModeX ' no pacote ' Android '** for visto durante a implantação do aplicativo, isso ocorre porque a estrutura de destino não está definida como **Android 5,0 (nível de API 21-pirulito)** ou superior. 

Defina o nível de estrutura de destino para a API nível 21 ou superior e defina as configurações de projeto no nível da API do Android para a versão mínima do Android para a qual o aplicativo oferece suporte. Para obter mais informações sobre como definir os níveis de API do Android, consulte Understanding [Android API Levels](~/android/app-fundamentals/android-api-levels.md). `ToolbarFun` No exemplo, a versão mínima do Android é definida como KitKat (nível de API 4,4). 


## <a name="install-the-appcompat-nuget-package"></a>Instalar o pacote NuGet do AppCompat

Em seguida, adicione o pacote de [biblioteca de suporte do Android v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) ao projeto. No Visual Studio, clique com o botão direito do mouse em **referências** e selecione **gerenciar pacotes NuGet...** . Clique em **procurar** e pesquise a **biblioteca de suporte do Android v7 AppCompat**. Selecione **Xamarin. Android. support. v7. AppCompat** e clique em **instalar**: 

[![Captura de tela do pacote do v7 AppCompat selecionado em gerenciar pacotes NuGet](toolbar-compatibility-images/01-appcompat-nuget-sml.png)](toolbar-compatibility-images/01-appcompat-nuget.png#lightbox)

Quando esse NuGet é instalado, vários outros pacotes NuGet também são instalados, se ainda não estiverem presentes (como **Xamarin. Android. support. animada. Vector. estilododesenho**, **xamarin. Android. support. v4**e  **Xamarin. Android. support. Vector. Estilododesenho**). Para obter mais informações sobre a instalação de pacotes [do NuGet, consulte Walkthrough: Incluindo um NuGet em seu projeto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough). 


## <a name="use-an-appcompat-theme-and-toolbar"></a>Usar um tema e uma barra de ferramentas do AppCompat

A biblioteca AppCompat vem com vários `Theme.AppCompat` temas que podem ser usados em qualquer versão do Android com suporte na biblioteca AppCompat. O `ToolbarFun` tema do aplicativo de exemplo é `Theme.Material.Light.DarkActionBar`derivado de, que não está disponível em versões do Android anteriores à pirulito. Portanto, `ToolbarFun` deve ser adaptado para usar o equivalente de AppCompat para esse `Theme.AppCompat.Light.DarkActionBar`tema,. Além disso, `Toolbar` como o não está disponível em versões do Android anteriores à pirulito, devemos usar a versão AppCompat `Toolbar`do. Portanto, os layouts devem `android.support.v7.widget.Toolbar` ser usados `Toolbar`em vez de. 


### <a name="update-layouts"></a>Atualizar layouts

Edite Resources **/layout/Main. axml** e substitua o `Toolbar` elemento pelo seguinte XML: 

```xml
<android.support.v7.widget.Toolbar
    android:id="@+id/edit_toolbar"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorAccent"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

Edite Resources **/layout/Toolbar. xml** e substitua seu conteúdo pelo XML a seguir: 

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

Observe que os `?attr` valores não são mais prefixados `android:` com (lembre- `?` se de que a notação faz referência a um recurso no tema atual). Se `?android:attr` ainda foram usados aqui, o Android referenciaria o valor do atributo da plataforma em execução no momento, em vez da biblioteca AppCompat. Como este exemplo usa o `actionBarSize` definido pela biblioteca AppCompat, o `android:` prefixo é Descartado. Da mesma `@android:style` forma, é `@style` alterado para que `android:theme` o atributo seja definido como um tema na biblioteca &ndash; AppCompat em `ThemeOverlay.AppCompat.Dark.ActionBar` que o tema é usado aqui `ThemeOverlay.Material.Dark.ActionBar`em vez de. 


### <a name="update-the-style"></a>Atualizar o estilo

Edite Resources **/Values/Styles. xml** e substitua seu conteúdo pelo seguinte XML: 

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

Os nomes de item e o tema pai neste exemplo não são mais prefixados com `android:` porque estamos usando a biblioteca AppCompat. Além disso, o tema pai é alterado para a versão AppCompat `Light.DarkActionBar`do. 



### <a name="update-menus"></a>Atualizar menus

Para dar suporte a versões anteriores do Android, a biblioteca AppCompat usa atributos personalizados que espelham os `android:` atributos do namespace. No entanto, alguns atributos (como `showAsAction` o atributo usado `<menu>` na marca) não existem na estrutura do Android em dispositivos &ndash; `showAsAction` antigos foram introduzidos na API do Android 11, mas não estão disponíveis no Android API 7. Por esse motivo, um namespace personalizado deve ser usado para prefixar todos os atributos definidos pela biblioteca de suporte. Nos arquivos de recurso de menu, um namespace `local` chamado é definido para a prefixação do `showAsAction` atributo. 

Edite os **recursos/menu/top_menus. xml** e substitua seu conteúdo pelo seguinte XML:

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

O `showAsAction` atributo é precedido por `local:` esse namespace em vez de`android:` 

```csharp
local:showAsAction="ifRoom"
```

Da mesma forma, edite os **recursos/menu/edit_menus. xml** e substitua seu conteúdo pelo XML a seguir:

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

Como essa opção de namespace fornece suporte para o `showAsAction` atributo em versões do Android antes do nível de API 11? O atributo `showAsAction` personalizado e todos os seus valores possíveis são incluídos no aplicativo quando o NuGet do AppCompat é instalado. 


## <a name="subclass-appcompatactivity"></a>AppCompatActivity de subclasse

A etapa final da conversão é modificar `MainActivity` de forma que ela seja uma subclasse de. `AppCompactActivity` Edite **MainActivity.cs** e adicione as `using` seguintes instruções: 

```csharp
using Android.Support.V7.App;
using Toolbar = Android.Support.V7.Widget.Toolbar;
```

Isso declara `Toolbar` ser a versão AppCompat do `Toolbar`. Em seguida, altere a definição de `MainActivity`classe de: 

```csharp
public class MainActivity : AppCompatActivity
```

Para definir a barra de ação para a versão AppCompat `Toolbar`do, substitua a chamada `SetActionBar` para `SetSupportActionBar`por. Neste exemplo, o título também é alterado para indicar que a versão de AppCompat do `Toolbar` está sendo usada:

```csharp
SetSupportActionBar (toolbar);
SupportActionBar.Title = "My AppCompat Toolbar";
```

Por fim, altere o nível mínimo do Android para o valor de pré-autenticação que deve ser suportado (por exemplo, API 19). 

Compile o aplicativo e execute-o em um dispositivo de pré-pirulito ou um emulador do Android. A captura de tela a seguir mostra a versão AppCompat do **ToolbarFun** em um Nexus 4 executando KITKAT (API 19): 

[![Captura de tela completa do aplicativo em execução em um dispositivo KitKat, ambas as barras de ferramentas são mostradas](toolbar-compatibility-images/02-running-on-kitkat-sml.png)](toolbar-compatibility-images/02-running-on-kitkat.png#lightbox)

Quando a biblioteca AppCompat é usada, os temas não precisam ser alternados com base na versão &ndash; do Android a biblioteca AppCompat torna possível fornecer uma experiência de usuário consistente em todas as versões do Android com suporte. 




## <a name="related-links"></a>Links relacionados

- [Barra de ferramentas pirulito (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-toolbar)
- [Barra de ferramentas AppCompat (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)
