---
title: Compatibilidade da barra de ferramentas
ms.prod: xamarin
ms.assetid: A0798CA1-2C7D-43B6-9E91-4435CC7B6683
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: 8d5f0b06a804e9b9af7bb15111c70e4505c79fd0
ms.sourcegitcommit: 40a56bbc1e038a9181101580ad18a4584edb5ab0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98025507"
---
# <a name="toolbar-compatibility"></a>Compatibilidade da barra de ferramentas

## <a name="overview"></a>Visão geral

Esta seção explica como usar `Toolbar` versões do Android anteriores ao android 5,0 pirulito. Se seu aplicativo não oferecer suporte a versões do Android anteriores ao Android 5,0, você poderá ignorar esta seção. 

Como `Toolbar` o faz parte da biblioteca de suporte do Android v7, ele pode ser usado em dispositivos que executam o android 2,1 (API nível 7) e superior. No entanto, a [biblioteca de suporte do Android v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) NuGet deve ser instalada e o código modificado para que ele use a `Toolbar` implementação fornecida nesta biblioteca. Esta seção explica como instalar esse NuGet e modificar o aplicativo **ToolbarFun** da [adição de uma segunda barra de ferramentas](~/android/user-interface/controls/tool-bar/adding-a-second-toolbar.md) para que ela seja executada em versões do Android anteriores à pirulito 5,0.

Para modificar um aplicativo para usar a versão do AppCompat da barra de ferramentas: 

1. Defina as versões mínima e de destino do Android para o aplicativo.

2. Instale o pacote NuGet do AppCompat.

3. Use um tema AppCompat em vez de um tema interno do Android.

4. Modifique `MainActivity` para que ela seja subclasse `AppCompatActivity` em vez de `Activity` . 

Cada uma dessas etapas é explicada em detalhes nas seções a seguir.

## <a name="set-the-minimum-and-target-android-version"></a>Definir a versão mínima e de destino do Android

A estrutura de destino do aplicativo deve ser definida como a API nível 21 ou superior ou o aplicativo não será implantado corretamente. Se um erro como **nenhum identificador de recurso encontrado para o atributo ' tileModeX ' no pacote ' Android '** for visto durante a implantação do aplicativo, isso ocorre porque a estrutura de destino não está definida como **Android 5,0 (nível de API 21-pirulito)** ou superior. 

Defina o nível de estrutura de destino para a API nível 21 ou superior e defina as configurações de projeto no nível da API do Android para a versão mínima do Android para a qual o aplicativo oferece suporte. Para obter mais informações sobre como definir os níveis de API do Android, consulte [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md). No `ToolbarFun` exemplo, a versão mínima do Android é definida como KitKat (nível de API 4,4). 

## <a name="install-the-appcompat-nuget-package"></a>Instalar o pacote NuGet do AppCompat

Em seguida, adicione o pacote de [biblioteca de suporte do Android v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) ao projeto. No Visual Studio, clique com o botão direito do mouse em **referências** e selecione **gerenciar pacotes NuGet...**. Clique em **procurar** e pesquise a **biblioteca de suporte do Android v7 AppCompat**. Selecione **Xamarin. Android. support. v7. AppCompat** e clique em **instalar**: 

[![Captura de tela do pacote do v7 AppCompat selecionado em gerenciar pacotes NuGet](toolbar-compatibility-images/01-appcompat-nuget-sml.png)](toolbar-compatibility-images/01-appcompat-nuget.png#lightbox)

Quando esse NuGet é instalado, vários outros pacotes do NuGet também são instalados, caso ainda não estejam presentes (como **Xamarin. Android. support. animada. Vector. estilododesenho**, **xamarin. Android. support. v4** e **Xamarin. Android. support. Vector. estilododesenho**). Para obter mais informações sobre a instalação de pacotes do NuGet, consulte [Walkthrough: incluindo um NuGet em seu projeto](/visualstudio/mac/nuget-walkthrough). 

## <a name="use-an-appcompat-theme-and-toolbar"></a>Usar um tema e uma barra de ferramentas do AppCompat

A biblioteca AppCompat vem com vários `Theme.AppCompat` temas que podem ser usados em qualquer versão do Android com suporte na biblioteca AppCompat. O `ToolbarFun` tema do aplicativo de exemplo é derivado de `Theme.Material.Light.DarkActionBar` , que não está disponível em versões do Android anteriores à pirulito. Portanto, `ToolbarFun` deve ser adaptado para usar o equivalente de AppCompat para esse tema, `Theme.AppCompat.Light.DarkActionBar` . Além disso, como `Toolbar` o não está disponível em versões do Android anteriores à pirulito, devemos usar a versão AppCompat do `Toolbar` . Portanto, os layouts devem ser usados `android.support.v7.widget.Toolbar` em vez de `Toolbar` . 

### <a name="update-layouts"></a>Atualizar layouts

Edite **Resources/layout/Main. axml** e substitua o `Toolbar` elemento pelo seguinte XML: 

```xml
<android.support.v7.widget.Toolbar
    android:id="@+id/edit_toolbar"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorAccent"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

Edite **recursos/layout/toolbar.xml** e substitua seu conteúdo pelo seguinte XML: 

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

Observe que os `?attr` valores não são mais prefixados com `android:` (Lembre-se de que a `?` notação faz referência a um recurso no tema atual). Se `?android:attr` ainda foram usados aqui, o Android referenciaria o valor do atributo da plataforma em execução no momento, em vez da biblioteca AppCompat. Como este exemplo usa o `actionBarSize` definido pela biblioteca AppCompat, o `android:` prefixo é Descartado. Da mesma forma, `@android:style` é alterado para `@style` que o `android:theme` atributo seja definido como um tema na biblioteca AppCompat &ndash; em `ThemeOverlay.AppCompat.Dark.ActionBar` que o tema é usado aqui em vez de `ThemeOverlay.Material.Dark.ActionBar` . 

### <a name="update-the-style"></a>Atualizar o estilo

Edite **recursos/valores/styles.xml** e substitua seu conteúdo pelo seguinte XML: 

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

Os nomes de item e o tema pai neste exemplo não são mais prefixados com `android:` porque estamos usando a biblioteca AppCompat. Além disso, o tema pai é alterado para a versão AppCompat do `Light.DarkActionBar` . 

### <a name="update-menus"></a>Atualizar menus

Para dar suporte a versões anteriores do Android, a biblioteca AppCompat usa atributos personalizados que espelham os atributos do `android:` namespace. No entanto, alguns atributos (como o `showAsAction` atributo usado na `<menu>` marca) não existem na estrutura do Android em dispositivos antigos &ndash; `showAsAction` foram introduzidos na API do Android 11, mas não estão disponíveis no Android API 7. Por esse motivo, um namespace personalizado deve ser usado para prefixar todos os atributos definidos pela biblioteca de suporte. Nos arquivos de recurso de menu, um namespace chamado `local` é definido para a prefixação do `showAsAction` atributo. 

Edite **recursos/menu/top_menus.xml** e substitua seu conteúdo pelo seguinte XML:

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

O `showAsAction` atributo é precedido por esse `local:` namespace em vez de `android:` 

```csharp
local:showAsAction="ifRoom"
```

Da mesma forma, edite **recursos/menu/edit_menus.xml** e substitua seu conteúdo pelo seguinte XML:

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

Como essa opção de namespace fornece suporte para o `showAsAction` atributo em versões do Android antes do nível de API 11? O atributo personalizado `showAsAction` e todos os seus valores possíveis são incluídos no aplicativo quando o NuGet do AppCompat é instalado. 

## <a name="subclass-appcompatactivity"></a>AppCompatActivity de subclasse

A etapa final da conversão é modificar `MainActivity` de forma que ela seja uma subclasse de `AppCompatActivity` . Edite **MainActivity.cs** e adicione as seguintes `using` instruções: 

```csharp
using Android.Support.V7.App;
using Toolbar = Android.Support.V7.Widget.Toolbar;
```

Isso declara `Toolbar` ser a versão AppCompat do `Toolbar` . Em seguida, altere a definição de classe de `MainActivity` : 

```csharp
public class MainActivity : AppCompatActivity
```

Para definir a barra de ação para a versão AppCompat do `Toolbar` , substitua a chamada para `SetActionBar` por `SetSupportActionBar` . Neste exemplo, o título também é alterado para indicar que a versão de AppCompat do `Toolbar` está sendo usada:

```csharp
SetSupportActionBar (toolbar);
SupportActionBar.Title = "My AppCompat Toolbar";
```

Por fim, altere o nível mínimo do Android para o valor de pré-autenticação que deve ser suportado (por exemplo, API 19). 

Compile o aplicativo e execute-o em um dispositivo de pré-pirulito ou um emulador do Android. A captura de tela a seguir mostra a versão AppCompat do **ToolbarFun** em um Nexus 4 executando KITKAT (API 19): 

[![Captura de tela completa do aplicativo em execução em um dispositivo KitKat, ambas as barras de ferramentas são mostradas](toolbar-compatibility-images/02-running-on-kitkat-sml.png)](toolbar-compatibility-images/02-running-on-kitkat.png#lightbox)

Quando a biblioteca AppCompat é usada, os temas não precisam ser alternados com base na versão do Android &ndash; a biblioteca AppCompat torna possível fornecer uma experiência de usuário consistente em todas as versões do Android com suporte. 

## <a name="related-links"></a>Links Relacionados

- [Barra de ferramentas pirulito (exemplo)](/samples/xamarin/monodroid-samples/android50-toolbar)
- [Barra de ferramentas AppCompat (exemplo)](/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)
