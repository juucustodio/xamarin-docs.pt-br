---
title: Adicionando AppCompat e design de material
description: Este artigo explica como converter aplicativos Android Xamarin. Forms existentes para usar AppCompat e design de material.
ms.prod: xamarin
ms.assetid: 045FBCDF-4D45-48BB-9911-BD3938C87D58
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2017
ms.openlocfilehash: e8c05000275b65c18ddb6b628091da0fac462278
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73005458"
---
# <a name="adding-appcompat-and-material-design"></a>Adicionando AppCompat e design de material

_Siga estas etapas para converter aplicativos Android Xamarin. Forms existentes para usar AppCompat e design de material_

<!-- source https://gist.github.com/jassmith/a3b2a543f99126782936
https://blog.xamarin.com/material-design-for-your-xamarin-forms-android-apps/ -->

## <a name="overview"></a>Visão Geral

Estas instruções explicam como atualizar seus aplicativos Android Xamarin. Forms existentes para usar a biblioteca AppCompat e habilitar o design de material na versão do Android de seus aplicativos Xamarin. Forms.

### <a name="1-update-xamarinforms"></a>1. atualizar Xamarin. Forms

Verifique se a solução está usando o Xamarin. Forms 2,0 ou mais recente. Atualize o pacote do NuGet do Xamarin. Forms para 2,0, se necessário.

### <a name="2-check-android-version"></a>2. Verifique a versão do Android

Verifique se a estrutura de destino do projeto Android é Android 6,0 (marshmallow). Verifique as **Opções de > do projeto do Android > criar > configurações gerais** para garantir que a estrutura corrent esteja selecionada:

 ![](appcompat-images/target-android-6-sml.png "Android General Build Configuration")

### <a name="3-add-new-themes-to-support-material-design"></a>3. adicionar novos temas ao design de material de suporte

Crie os três arquivos a seguir em seu projeto Android e cole o conteúdo abaixo. O Google fornece um [Guia de estilo](https://www.google.com/design/spec/style/color.html#color-color-palette) e um gerador de paleta de [cores](https://www.materialpalette.com/) para ajudá-lo a escolher um esquema de cores alternativo para aquele especificado.

**Recursos/valores/cores. xml**

```xml
<resources>
  <color name="primary">#2196F3</color>
  <color name="primaryDark">#1976D2</color>
  <color name="accent">#FFC107</color>
  <color name="window_background">#F5F5F5</color>
</resources>
```

**Recursos/valores/Style. xml**

```xml
<resources>
  <style name="MyTheme" parent="MyTheme.Base">
  </style>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light.NoActionBar">
    <item name="colorPrimary">@color/primary</item>
    <item name="colorPrimaryDark">@color/primaryDark</item>
    <item name="colorAccent">@color/accent</item>
    <item name="android:windowBackground">@color/window_background</item>
    <item name="windowActionModeOverlay">true</item>
  </style>
</resources>
```

Um estilo adicional deve ser incluído na pasta **Values-v21** para aplicar propriedades específicas ao ser executado em Android pirulito e mais recente.

**Resources/Values-v21/Style. xml**

```xml
<resources>
  <style name="MyTheme" parent="MyTheme.Base">
    <!--If you are using MasterDetailPage you will want to set these, else you can leave them out-->
    <!--<item name="android:windowDrawsSystemBarBackgrounds">true</item>
    <item name="android:statusBarColor">@android:color/transparent</item>-->
  </style>
</resources>
```

### <a name="4-update-androidmanifestxml"></a>4. atualizar AndroidManifest. xml

Para garantir que essas novas informações de tema sejam usadas, defina o tema no arquivo **AndroidManifest** adicionando `android:theme="@style/MyTheme"` (Deixe o restante do XML como ele estava).

**Propriedades/AndroidManifest. xml**

```xml
...
<application android:label="AppName" android:icon="@drawable/icon"
  android:theme="@style/MyTheme">
...
```

### <a name="5-provide-toolbar-and-tab-layouts"></a>5. fornecer layouts de barra de ferramentas e guias

Crie os arquivos **Tabbar. axml** e **Toolbar. axml** no diretório de **recursos/layout** e cole o conteúdo abaixo:

**Recursos/layout/Tabbar. axml**

```xml
<android.support.design.widget.TabLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/sliding_tabs"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    app:tabIndicatorColor="@android:color/white"
    app:tabGravity="fill"
    app:tabMode="fixed" />
```

Algumas propriedades para as guias foram definidas, incluindo a gravidade da guia para `fill` e modo como `fixed`.
Se você tiver muitas guias, convém mudar isso para rolável – Leia a [documentação do TabLayout](https://developer.android.com/reference/android/support/design/widget/TabLayout.html) do Android para saber mais.

**Recursos/layout/barra de ferramentas. axml**

```xml
<android.support.v7.widget.Toolbar
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="?attr/actionBarSize"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    app:popupTheme="@style/ThemeOverlay.AppCompat.Light"
    app:layout_scrollFlags="scroll|enterAlways" />
```

Nesses arquivos, estamos criando um tema específico para a barra de ferramentas que pode variar para seu aplicativo.
Consulte a postagem do blog da [barra de ferramentas Hello](https://blog.xamarin.com/android-tips-hello-toolbar-goodbye-action-bar/) para saber mais.

### <a name="6-update-the-mainactivity"></a>6. atualizar o `MainActivity`

Em aplicativos Xamarin. Forms existentes, a classe **MainActivity.cs** herdará de `FormsApplicationActivity`. Isso deve ser substituído por `FormsAppCompatActivity` para habilitar a nova funcionalidade.

**MainActivity.cs**

```csharp
public class MainActivity : FormsAppCompatActivity  // was FormsApplicationActivity
```

Por fim, "Conecte" os novos layouts da etapa 5 no método `OnCreate`, conforme mostrado aqui:

```csharp
protected override void OnCreate(Bundle bundle)
{
  // set the layout resources first
  FormsAppCompatActivity.ToolbarResource = Resource.Layout.Toolbar;
  FormsAppCompatActivity.TabLayoutResource = Resource.Layout.Tabbar;

  // then call base.OnCreate and the Xamarin.Forms methods
  base.OnCreate(bundle);
  Forms.Init(this, bundle);
  LoadApplication(new App());
}
```
