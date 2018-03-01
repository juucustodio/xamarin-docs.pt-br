---
title: "Substituição da barra de ação"
ms.topic: article
ms.prod: xamarin
ms.assetid: 5341D28E-B203-478D-8464-6FAFDC3A4110
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 91d5612991c2297418cf7003c499c1a1bbfc7558
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="replacing-the-action-bar"></a>Substituição da barra de ação

<a name="overview" />

## <a name="overview"></a>Visão geral

Um dos mais comuns usa para o `Toolbar` é substituir a barra de ação padrão com um personalizado `Toolbar` (quando um novo projeto Android é criado, ele usa a barra de ação padrão). Porque o `Toolbar` fornece a capacidade de Adicionar marca logotipos, títulos, itens de menu, botões de navegação e exibições personalizadas até a seção da barra de aplicativo de uma atividade interface do usuário, ele oferece uma atualização significativa sobre a barra de ação padrão.

Para substituir a barra de ação padrão do aplicativo com um `Toolbar`: 

1.  Criar um novo tema personalizado e modificar propriedades do aplicativo para que ele usa esse novo tema. 

2.  Desabilitar o `windowActionBar` atributos no tema personalizado e habilitar o `windowNoTitle` atributo.

3.  Definir um layout para o `Toolbar`.

4.  Incluir o `Toolbar` layout na atividade de **Main.axml** arquivo de layout. 

5.  Adicione código para a atividade `OnCreate` método para localizar o `Toolbar` e chame `SetActionBar` para instalar o `ToolBar` como a barra de ação.

As seções a seguir explicam esse processo em detalhes. Um aplicativo simples é criado e sua barra de ação é substituída com um personalizado `Toolbar`. 


<a name="start_project" />

## <a name="start-an-app-project"></a>Iniciar um projeto de aplicativo

Criar um novo projeto Android chamado **ToolbarFun** (consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md) para obter mais informações sobre como criar um novo projeto Android). Depois que este projeto é criado, definir os níveis de API do Android mínimo e de destino **Android 5.0 (API nível 21 - pirulito)**. Para obter mais informações sobre configuração de níveis de versão do Android, consulte [Noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md). Quando o aplicativo é compilado e executado, ele exibe a barra de ação padrão como mostrado na captura de tela: 

[![Captura de tela da barra de ação padrão](replacing-the-action-bar-images/01-before-sml.png)](replacing-the-action-bar-images/01-before.png)


<a name="custom_theme" />

## <a name="create-a-custom-theme"></a>Crie um tema personalizado

Abra o **recursos/valores** directory e criar um novo arquivo chamado **Styles**. Substitua o seu conteúdo com o seguinte XML: 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<resources>
  <style name="MyTheme" parent="@android:style/Theme.Material.Light.DarkActionBar">
    <item name="android:windowNoTitle">true</item>
    <item name="android:windowActionBar">false</item>
    <item name="android:colorPrimary">#5A8622</item>
  </style>
</resources>
```

Esse XML define um novo tema personalizado chamado **MyTheme** que se baseia o **Theme.Material.Light.DarkActionBar** tema de pirulito. O `windowNoTitle` atributo é definido como `true` para ocultar a barra de título: 

```xml
<item name="android:windowNoTitle">true</item>
```

Para exibir a barra de ferramentas personalizada, o padrão `ActionBar` deve ser desabilitada: 

```xml
<item name="android:windowActionBar">false</item>
```

Um olive-green `colorPrimary` configuração é usada para a cor de plano de fundo da barra de ferramentas: 
 
```xml
<item name="android:colorPrimary">#5A8622</item>
```

Editar **Properties/AndroidManifest.xml** e adicione o seguinte `android:theme` de atributo para o `<application>` elemento para que o aplicativo usa o `MyTheme` tema personalizado: 

```xml
<application android:label="@string/app_name" android:theme="@style/MyTheme"></application>
```

Para obter mais informações sobre como aplicar um tema personalizado para um aplicativo, consulte [usando temas personalizados](~/android/user-interface/material-theme.md#customtheme). 


<a name="toolbar_layout" />

## <a name="define-a-toolbar-layout"></a>Definir um Layout de barra de ferramentas

No **recursos/layout** diretório, crie um novo arquivo chamado **toolbar.xml**. Substitua o seu conteúdo com o seguinte XML: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<Toolbar xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:minHeight="?android:attr/actionBarSize"
    android:background="?android:attr/colorPrimary"
    android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"/>
```

Esse XML define personalizado `Toolbar` que substitui a barra de ação padrão. A altura mínima do `Toolbar` é definido como o tamanho da barra de ação que ele substitui: 

```csharp
android:minHeight="?android:attr/actionBarSize"
```

A cor de fundo de `Toolbar` é definido como a cor olive-green definida anteriormente na **Styles**:

```csharp
android:background="?android:attr/colorPrimary"
```

Começando com pirulito, o `android:theme` atributo pode ser usado para definir o estilo de uma exibição individual. O `ThemeOverlay.Material` temas introduzidos no pirulito possibilitam a sobreposição padrão `Theme.Material` temas, substituindo os atributos relevantes para torná-las claro ou escuro. Neste exemplo, o `Toolbar` usa um tema escuro para que seu conteúdo é de cor clara: 

```csharp
android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
```

Essa configuração é usada para que os itens de menu Compare com a cor de plano de fundo mais escura.


<a name="include_layout" />

## <a name="include-the-toolbar-layout"></a>Incluir o Layout da barra de ferramentas

Edite o arquivo de layout **Resources/layout/Main.axml** e substitua o seu conteúdo com o seguinte XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <include
        android:id="@+id/toolbar"
        layout="@layout/toolbar" />
    <Button
        android:id="@+id/MyButton"
        android:layout_below="@+id/toolbar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Hello World, Click Me!" />
</RelativeLayout>
```

Esse layout inclui o `Toolbar` definido em **toolbar.xml** e usa um `RelativeLayout` para especificar que o `Toolbar` será colocado na parte superior da interface do usuário (acima do botão). 


<a name="activate_toolbar" />

## <a name="find-and-activate-the-toolbar"></a>Localizar e ativar a barra de ferramentas

Editar **MainActivity.cs** e adicione o seguinte usando a instrução:

```csharp
using Android.Views;
```

Além disso, adicione as seguintes linhas de código ao final do `OnCreate` método:

```csharp
var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
SetActionBar(toolbar);
ActionBar.Title = "My Toolbar";
```

Esse código localiza o `Toolbar` e chamadas `SetActionBar` para que o `Toolbar` terá características de barra de ação padrão. O título da barra de ferramentas é alterado para **minha barra de ferramentas**. Como mostrado no exemplo de código, o `ToolBar` pode ser referenciado diretamente como uma barra de ação. Compilar e executar esse aplicativo &ndash; o personalizado `Toolbar` é exibido no lugar da barra de ação padrão: 

[![Captura de tela da barra de ferramentas personalizada com o esquema de cores verde](replacing-the-action-bar-images/02-after-sml.png)](replacing-the-action-bar-images/02-after.png)

Observe que o `Toolbar` estilo independentemente do `Theme.Material.Light.DarkActionBar` tema aplicado para o restante do aplicativo. 


<a name="main_menus" />
 
## <a name="add-menu-items"></a>Adicionar itens de Menu 

Nesta seção, menus são adicionados para o `Toolbar`. A área superior direita do `ToolBar` é reservado para itens de menu &ndash; cada item de menu (também chamado de um *item de ação*) pode executar uma ação dentro da atividade atual ou pode executar uma ação em nome do aplicativo inteiro. 

Para adicionar menus para o `Toolbar`: 

1.  Adicionar ícones de menu (se necessário) para o `mipmap-` pastas de projeto de aplicativo. Google fornece um conjunto de ícones de menu livre sobre o [ícones Material](https://design.google.com/icons/) página. 

2.  Definir o conteúdo dos itens do menu adicionando um novo arquivo de recurso de menu em **recursos/menu**. 

3.  Implementar o `OnCreateOptionsMenu` método da atividade &ndash; esse método inflação os itens de menu. 

4.  Implementar o `OnOptionsItemSelected` método da atividade &ndash; esse método executa uma ação quando um item de menu é tocado. 

As seções a seguir demonstram esse processo detalhadamente adicionando **editar** e **salvar** itens de menu para o personalizado `Toolbar`. 


<a name="menu_icons" />

### <a name="install-menu-icons"></a>Instalar os ícones de Menu

Continuar com a `ToolbarFun` aplicativo de exemplo, adicionar ícones de menu ao projeto de aplicativo. Baixar [icons.zip de barra de ferramentas](https://github.com/xamarin/monodroid-samples/blob/master/Supportv7/AppCompat/Toolbar/Resources/toolbar-icons.zip?raw=true) e descompacte-o. Copie o conteúdo a *mipmap -* pastas para o projeto *mipmap -* pastas sob **ToolbarFun/recursos** e incluir cada arquivo de ícone adicionado no projeto.

<a name="menu_resource" />

### <a name="define-a-menu-resource"></a>Definir um recurso de Menu

Criar um novo **menu** subdiretório em **recursos**. No **menu** subdiretório, crie um novo arquivo de recurso de menu chamado **top_menus.xml** e substitua o seu conteúdo com o seguinte XML: 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item
       android:id="@+id/menu_edit"
       android:icon="@mipmap/ic_action_content_create"
       android:showAsAction="ifRoom"
       android:title="Edit" />
  <item
       android:id="@+id/menu_save"
       android:icon="@mipmap/ic_action_content_save"
       android:showAsAction="ifRoom"
       android:title="Save" />
  <item
       android:id="@+id/menu_preferences"
       android:showAsAction="never"
       android:title="Preferences" />
</menu>
```

Esse XML cria três itens de menu:

-   Um **editar** item de menu que usa o `ic_action_content_create.png` ícone (um lápis). 

-   Um **salvar** item de menu que usa o `ic_action_content_save.png` ícone (um disquete). 

-   Um **preferências** item de menu que não tem um ícone.

O `showAsAction` atributos do **editar** e **salvar** itens de menu são definidos como `ifRoom` &ndash; essa configuração faz com que esses itens de menu apareçam no `Toolbar` se houver espaço suficiente para exibi-las. O **preferências** conjuntos de item de menu `showAsAction` para `never` &ndash; isso faz com que o **preferências** menu apareçam no *estouro* menu (três pontos verticais). 

<a name="on_create_options_menu" />

### <a name="implement-oncreateoptionsmenu"></a>Implementar OnCreateOptionsMenu

Adicione o seguinte método para **MainActivity.cs**:

```csharp
public override bool OnCreateOptionsMenu(IMenu menu)
{
    MenuInflater.Inflate(Resource.Menu.top_menus, menu);
    return base.OnCreateOptionsMenu(menu);
}
```

Chamadas Android a `OnCreateOptionsMenu` método para que o aplicativo pode especificar o recurso de menu para uma atividade. Nesse método, o **top_menus.xml** recurso é aumentado em transmitido `menu`. Este código faz com que o novo **editar**, **salvar**, e **preferências** itens de menu apareçam no `Toolbar`. 


<a name="on_options_item_selected" />

### <a name="implement-onoptionsitemselected"></a>Implement OnOptionsItemSelected

Adicione o seguinte método para **MainActivity.cs**:

```csharp
public override bool OnOptionsItemSelected(IMenuItem item)
{
    Toast.MakeText(this, "Action selected: " + item.TitleFormatted,
        ToastLength.Short).Show();
    return base.OnOptionsItemSelected(item);
}
```

Quando um usuário toca em um item de menu, Android chama o `OnOptionsItemSelected` método e passa no item de menu que foi selecionado. Neste exemplo, a implementação apenas exibe uma notificação do sistema para indicar qual item de menu foi tocado. 

Compilar e executar `ToolbarFun` para ver os novos itens de menu na barra de ferramentas. O `Toolbar` agora exibe três ícones de menu, como mostrado na captura de tela: 

[![Diagrama ilustrando locais de editar, salvar e itens de menu de estouro](replacing-the-action-bar-images/04-menu-items-sml.png)](replacing-the-action-bar-images/04-menu-items.png)

Quando um usuário toques a **editar** item de menu, uma notificação do sistema é exibido para indicar que o `OnOptionsItemSelected` método foi chamado: 

[![Captura de tela de torrada exibida quando o item de edição é tocado](replacing-the-action-bar-images/05-toast-displayed-sml.png)](replacing-the-action-bar-images/05-toast-displayed.png)

Quando um usuário toca o menu de estouro, o **preferências** item de menu é exibido. Normalmente, menos comuns ações devem ser colocadas no menu de estouro &ndash; Este exemplo usa o menu de estouro de **preferências** porque ele não é usado com frequência como **editar** e  **Salvar**: 

[![Item de menu de captura de tela de preferências que aparece no menu de estouro](replacing-the-action-bar-images/06-preferences-sml.png)](replacing-the-action-bar-images/06-preferences.png)

Para obter mais informações sobre menus Android, consulte o desenvolvedor Android [Menus](https://developer.android.com/guide/topics/ui/menus.html) tópico. 
 



## <a name="related-links"></a>Links relacionados

- [Barra de ferramentas de pirulito (exemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Barra de ferramentas de AppCompat (exemplo)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
