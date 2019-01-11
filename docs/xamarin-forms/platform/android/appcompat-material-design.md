---
title: Adicionando AppCompat e Design de Material
description: Este artigo explica como converter aplicativos existentes do Android do xamarin. Forms para usar AppCompat e Design de Material.
ms.prod: xamarin
ms.assetid: 045FBCDF-4D45-48BB-9911-BD3938C87D58
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2017
ms.openlocfilehash: c2eed44a7c684b91ceed4493a83ff3b4e1578b5f
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209311"
---
# <a name="adding-appcompat-and-material-design"></a>Adicionando AppCompat e Design de Material

_Siga estas etapas para converter aplicativos existentes do Android do xamarin. Forms para usar AppCompat e Design de Material_

<!-- source https://gist.github.com/jassmith/a3b2a543f99126782936
https://blog.xamarin.com/material-design-for-your-xamarin-forms-android-apps/ -->

## <a name="overview"></a>Visão geral

Estas instruções explicam como atualizar seus aplicativos existentes do Android do xamarin. Forms para usar a biblioteca AppCompat e habilitar o Design de Material na versão Android de seus aplicativos xamarin. Forms.

### <a name="1-update-xamarinforms"></a>1. Atualizar o xamarin. Forms

Certifique-se de que a solução está usando o xamarin. Forms 2.0 ou mais recente. Atualize o pacote Nuget do xamarin. Forms para 2.0, se necessário.

### <a name="2-check-android-version"></a>2. Verificar a versão do Android

Verifique se a que estrutura de destino do projeto Android é o Android 6.0 (Marshmallow). Verifique a **projeto Android > Opções > Build > geral** configurações para garantir que o framework corrent está selecionada:

 ![](appcompat-images/target-android-6-sml.png "Configuração de Build do Android geral")

### <a name="3-add-new-themes-to-support-material-design"></a>3. Adicionar novos temas para dar suporte a Design de Material

Crie três arquivos a seguir em seu projeto Android e cole no conteúdo abaixo. Google fornece um [guia de estilo](http://www.google.com/design/spec/style/color.html#color-color-palette) e uma [gerador da paleta de cores](http://www.materialpalette.com/) para ajudá-lo a escolher um esquema de cores alternativas ao especificado.

**Resources/values/colors.xml**

```xml
<resources>
  <color name="primary">#2196F3</color>
  <color name="primaryDark">#1976D2</color>
  <color name="accent">#FFC107</color>
  <color name="window_background">#F5F5F5</color>
</resources>
```

**Resources/values/style.xml**

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

Um estilo adicionais deve ser incluído na **v21 valores** pasta para aplicar as propriedades específicas ao executar no Android Lollipop e mais recentes.

**Resources/values-v21/style.xml**

```xml
<resources>
  <style name="MyTheme" parent="MyTheme.Base">
    <!--If you are using MasterDetailPage you will want to set these, else you can leave them out-->
    <!--<item name="android:windowDrawsSystemBarBackgrounds">true</item>
    <item name="android:statusBarColor">@android:color/transparent</item>-->
  </style>
</resources>
```

### <a name="4-update-androidmanifestxml"></a>4. Atualizar androidmanifest. XML

Para garantir que esse novo tema informações são usado, definido tema na **AndroidManifest** arquivo adicionando `android:theme="@style/MyTheme"` (deixe o restante do XML como ele era).

**Properties/AndroidManifest.xml**

```xml
...
<application android:label="AppName" android:icon="@drawable/icon"
  android:theme="@style/MyTheme">
...
```

### <a name="5-provide-toolbar-and-tab-layouts"></a>5. Forneça os layouts de barra de ferramentas e o guia

Crie **Tabbar.axml** e **Toolbar.axml** arquivos no **recursos/layout** diretório e colar em seu conteúdo de abaixo:

**Resources/layout/Tabbar.axml**

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

Algumas propriedades para as guias foram definidas incluindo a gravidade da guia para `fill` e o modo para `fixed`.
Se você tiver várias guias você talvez queira mudar isso para rolável - ler por meio do Android [TabLayout documentação](http://developer.android.com/reference/android/support/design/widget/TabLayout.html) para saber mais.

**Resources/layout/Toolbar.axml**

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

Esses arquivos, estamos criando um tema específico para a barra de ferramentas que pode variar para seu aplicativo.
Consulte a [barra de ferramentas Hello](https://blog.xamarin.com/android-tips-hello-toolbar-goodbye-action-bar/) postagem de blog para saber mais.


### <a name="6-update-the-mainactivity"></a>6. Atualização do `MainActivity`

Em aplicativos do xamarin. Forms existentes a **MainActivity.cs** classe herda `FormsApplicationActivity`. Isso deve ser substituído por `FormsAppCompatActivity` para habilitar a nova funcionalidade.

**MainActivity.cs**

```csharp
public class MainActivity : FormsAppCompatActivity  // was FormsApplicationActivity
```

Por fim, "conectar" novos layouts da etapa 5 no `OnCreate` método, conforme mostrado aqui:

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
