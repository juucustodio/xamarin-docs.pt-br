---
title: Substituindo a barra de ação
ms.prod: xamarin
ms.assetid: 5341D28E-B203-478D-8464-6FAFDC3A4110
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/27/2018
ms.openlocfilehash: 9e9fa1e2651661670f89baac7fcd438b3d14bfb3
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108217"
---
# <a name="replacing-the-action-bar"></a>Substituindo a barra de ação

## <a name="overview"></a>Visão geral

Um dos mais comuns usa para o `Toolbar` é substituir a barra de ação padrão por um personalizado `Toolbar` (quando um novo projeto Android é criado, ele usa a barra de ação padrão). Porque o `Toolbar` fornece a capacidade de Adicionar marca logotipos, títulos, itens de menu, botões de navegação e modos de exibição personalizados, mesmo para a seção da barra de aplicativo de uma atividade interface do usuário, ele oferece uma atualização significativa sobre a barra de ação padrão.

Para substituir a barra de ação padrão do aplicativo com um `Toolbar`: 

1.  Criar um novo tema personalizado e modificar as propriedades do aplicativo para que ele usa esse novo tema. 

2.  Desabilitar a `windowActionBar` de atributos no tema personalizado e habilitar o `windowNoTitle` atributo.

3.  Definir um layout para o `Toolbar`.

4.  Incluir o `Toolbar` layout na atividade de **Main. axml** arquivo de layout. 

5.  Adicione código para a atividade `OnCreate` método para localizar o `Toolbar` e chame `SetActionBar` para instalar o `ToolBar` como a barra de ação.

As seções a seguir explicam esse processo em detalhes. Um aplicativo simples é criado e sua barra de ação é substituída com um personalizado `Toolbar`. 



## <a name="start-an-app-project"></a>Iniciar um projeto de aplicativo

Criar um novo projeto Android chamado **ToolbarFun** (consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md) para obter mais informações sobre como criar um novo projeto Android). Depois que este projeto é criado, defina os níveis de API do Android de destino e mínimas **Android 5.0 (API nível 21 - pirulito)** ou posterior. Para obter mais informações sobre configuração de níveis de versão do Android, consulte [Noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md). Quando o aplicativo é compilado e executado, ele exibe a barra de ação padrão, conforme mostrado nesta captura de tela:

[![Captura de tela da barra de ação padrão](replacing-the-action-bar-images/01-before-sml.png)](replacing-the-action-bar-images/01-before.png#lightbox)



## <a name="create-a-custom-theme"></a>Criar um tema personalizado

Abra o **recursos/valores** directory e criar um novo arquivo chamado **Styles**. Substitua seu conteúdo pelo seguinte XML: 

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

Esse XML define um novo tema personalizado chamado **MyTheme** que se baseia o **Theme.Material.Light.DarkActionBar** tema no pirulito. O `windowNoTitle` atributo é definido como `true` para ocultar a barra de título: 

```xml
<item name="android:windowNoTitle">true</item>
```

Para exibir a barra de ferramentas personalizada, o padrão `ActionBar` deve ser desabilitada: 

```xml
<item name="android:windowActionBar">false</item>
```

Um olive-green `colorPrimary` configuração é usada para a cor do plano de fundo da barra de ferramentas: 
 
```xml
<item name="android:colorPrimary">#5A8622</item>
```

## <a name="apply-the-custom-theme"></a>Aplicar o tema personalizado

Edite **androidmanifest** e adicione o seguinte `android:theme` de atributo para o `<application>` elemento para que o aplicativo usa o `MyTheme` tema personalizado: 

```xml
<application android:label="@string/app_name" android:theme="@style/MyTheme"></application>
```

Para obter mais informações sobre como aplicar um tema personalizado para um aplicativo, consulte [temas personalizados usando](~/android/user-interface/material-theme.md#customtheme). 



## <a name="define-a-toolbar-layout"></a>Definir um Layout da barra de ferramentas

No **recursos/layout** diretório, crie um novo arquivo chamado **toolbar.xml**. Substitua seu conteúdo pelo seguinte XML: 

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

Esse XML define personalizado `Toolbar` que substitui a barra de ação padrão. A altura mínima da `Toolbar` é definido como o tamanho da barra de ação que ele substitui: 

```csharp
android:minHeight="?android:attr/actionBarSize"
```

A cor de fundo a `Toolbar` é definido como a cor olive-green definida anteriormente na **Styles**:

```csharp
android:background="?android:attr/colorPrimary"
```

Começando com o pirulito, o `android:theme` atributo pode ser usado para definir o estilo de uma exibição individual. O `ThemeOverlay.Material` temas introduzidos no pirulito tornam possível o padrão de sobreposição `Theme.Material` temas, substituindo os atributos relevantes para torná-las claro ou escuro. Neste exemplo, o `Toolbar` usa um tema escuro, para que seu conteúdo é a cor clara: 

```csharp
android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
```

Essa configuração é usada para que os itens de menu Compare com a cor de plano de fundo mais escura.



## <a name="include-the-toolbar-layout"></a>Incluir o Layout da barra de ferramentas

Edite o arquivo de layout **Resources/layout/Main.axml** e substitua seu conteúdo pelo seguinte XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <include
        android:id="@+id/toolbar"
        layout="@layout/toolbar" />
</RelativeLayout>
```

Esse layout inclui o `Toolbar` definidos no **toolbar.xml** e usa um `RelativeLayout` para especificar que o `Toolbar` será colocado na parte superior da interface do usuário (acima do botão). 



## <a name="find-and-activate-the-toolbar"></a>Localizar e ativar a barra de ferramentas

Edite **MainActivity.cs** e adicione a seguinte instrução using:

```csharp
using Android.Views;
```

Além disso, adicione as seguintes linhas de código ao final do `OnCreate` método:

```csharp
var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
SetActionBar(toolbar);
ActionBar.Title = "My Toolbar";
```

Esse código encontra os `Toolbar` e chama `SetActionBar` para que o `Toolbar` entrarão em características de barra de ação padrão. O título da barra de ferramentas é alterado para **minha barra de ferramentas**. Como visto neste exemplo de código, o `ToolBar` pode ser referenciado diretamente como uma barra de ação. Compilar e executar este aplicativo &ndash; personalizada `Toolbar` é exibido no lugar da barra de ação padrão: 

[![Captura de tela da barra de ferramentas personalizada com o esquema de cores verde](replacing-the-action-bar-images/02-after-sml.png)](replacing-the-action-bar-images/02-after.png#lightbox)

Observe que o `Toolbar` tem o estilo de forma independente do `Theme.Material.Light.DarkActionBar` tema que é aplicado ao restante do aplicativo. 

Se ocorrer uma exceção ao executar o aplicativo, consulte o [solução de problemas](#troubleshooting) seção abaixo.

 
## <a name="add-menu-items"></a>Adicionar itens de Menu 

Nesta seção, os menus são adicionados para o `Toolbar`. A área superior direita dos `ToolBar` é reservado para itens de menu &ndash; cada item de menu (também chamado de um *item de ação*) pode executar uma ação dentro da atividade atual ou ele pode executar uma ação em nome do aplicativo inteiro. 

Para adicionar menus para a `Toolbar`: 

1.  Adicionar ícones do menu (se necessário) para o `mipmap-` pastas de projeto do aplicativo. Google fornece um conjunto de ícones do menu gratuito sobre o [ícones Material](https://design.google.com/icons/) página. 

2.  Definir o conteúdo dos itens de menu, adicionando um novo arquivo de recurso de menu sob **menu derecursos/**. 

3.  Implemente a `OnCreateOptionsMenu` método da atividade &ndash; esse método irá aumentar os itens de menu. 

4.  Implemente a `OnOptionsItemSelected` método da atividade &ndash; esse método executa uma ação quando um item de menu é tocado. 

As seções a seguir demonstram esse processo em detalhes adicionando **edite** e **salve** itens de menu para o personalizado `Toolbar`. 



### <a name="install-menu-icons"></a>Instalar os ícones do Menu

Continuando com o `ToolbarFun` aplicativo de exemplo, adicionar ícones de menu ao projeto de aplicativo. Baixe [ícones da barra de ferramentas](https://github.com/xamarin/monodroid-samples/blob/master/Supportv7/AppCompat/Toolbar/Resources/toolbar-icons-plus.zip?raw=true), descompacte e copie o conteúdo da extraída *mipmap -* pastas ao projeto *mipmap -* pastas em **ToolbarFun / Recursos** e inclua cada arquivo de ícone adicionado no projeto.


### <a name="define-a-menu-resource"></a>Definir um recurso de Menu

Criar um novo **menus** subdiretório **recursos**. No **menus** subdiretório, crie um novo arquivo de recurso de menu chamado **top_menus.xml** e substitua seu conteúdo pelo seguinte XML: 

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

Este XML cria três itens de menu:

-   Uma **edite** item de menu que usa o `ic_action_content_create.png` ícone (um lápis). 

-   Um **salve** item de menu que usa o `ic_action_content_save.png` ícone (um disquete). 

-   Um **preferências** item de menu que não tem um ícone.

O `showAsAction` atributos do **editar** e **salvar** itens de menu são definidos como `ifRoom` &ndash; essa configuração faz com que esses itens de menu apareça no `Toolbar` se houver espaço suficiente para que sejam exibidos. O **preferências** conjuntos de itens de menu `showAsAction` ao `never` &ndash; isso faz com que o **preferências** menu apareça no *estouro* menu (três pontos verticais). 


### <a name="implement-oncreateoptionsmenu"></a>Implementar OnCreateOptionsMenu

Adicione o seguinte método à **MainActivity.cs**:

```csharp
public override bool OnCreateOptionsMenu(IMenu menu)
{
    MenuInflater.Inflate(Resource.Menu.top_menus, menu);
    return base.OnCreateOptionsMenu(menu);
}
```

Android chama o `OnCreateOptionsMenu` método para que o aplicativo pode especificar o recurso de menu para uma atividade. Nesse método, o **top_menus.xml** recurso está inflado passado `menu`. Esse código faz com que o novo **editar**, **salve**, e **preferências** itens de menu apareça no `Toolbar`. 



### <a name="implement-onoptionsitemselected"></a>Implementar OnOptionsItemSelected

Adicione o seguinte método à **MainActivity.cs**:

```csharp
public override bool OnOptionsItemSelected(IMenuItem item)
{
    Toast.MakeText(this, "Action selected: " + item.TitleFormatted,
        ToastLength.Short).Show();
    return base.OnOptionsItemSelected(item);
}
```

Quando um usuário toca um item de menu, o Android chama o `OnOptionsItemSelected` e passa no item de menu que foi selecionado. Neste exemplo, a implementação apenas exibe uma notificação do sistema para indicar qual item de menu foi tocada. 

Compilar e executar `ToolbarFun` para ver os novos itens de menu na barra de ferramentas. O `Toolbar` agora exibe três ícones de menu, como visto nesta captura de tela: 

[![Diagrama ilustrando locais de editar, salvar e itens de menu de estouro](replacing-the-action-bar-images/04-menu-items-sml.png)](replacing-the-action-bar-images/04-menu-items.png#lightbox)

Quando um usuário toca a **edite** item de menu, uma notificação do sistema será exibida para indicar que o `OnOptionsItemSelected` método foi chamado: 

[![Captura de tela de notificação do sistema exibida quando o item de edição é tocado](replacing-the-action-bar-images/05-toast-displayed-sml.png)](replacing-the-action-bar-images/05-toast-displayed.png#lightbox)

Quando um usuário toca o menu de estouro, o **preferências** item de menu é exibido. Normalmente, menos comuns ações devem ser colocadas no menu de estouro &ndash; Este exemplo usa o menu de estouro, para **preferências** porque ele não é usado com tanta frequência como **editar** e  **Salvar**: 

[![Item de menu de captura de tela de preferências que aparece no menu de estouro](replacing-the-action-bar-images/06-preferences-sml.png)](replacing-the-action-bar-images/06-preferences.png#lightbox)

Para obter mais informações sobre menus Android, consulte o desenvolvedor do Android [Menus](https://developer.android.com/guide/topics/ui/menus.html) tópico. 
 

## <a name="troubleshooting"></a>Solução de problemas

As dicas a seguir podem ajudar a depurar problemas que podem ocorrer ao substituir a barra de ação com uma barra de ferramentas.

### <a name="activity-already-has-an-action-bar"></a>Atividade já tem uma barra de ação

Se o aplicativo não está configurado corretamente para usar um tema personalizado, conforme explicado em [aplicar o tema personalizado](#apply-the-custom-theme), a seguinte exceção pode ocorrer durante a execução do aplicativo:

![Erro pode ocorrer quando o tema personalizado não é usado.](replacing-the-action-bar-images/03-theme-not-defined.png)

Além disso, uma mensagem de erro, como a seguir pode ser produzidos: _Java.Lang.IllegalStateException: esta atividade já tem uma barra de ação fornecida pela decoração de janela._ 

Para corrigir esse erro, verifique se o `android:theme` de atributo para o tema personalizado é adicionado ao `<application>` (no **androidmanifest**) conforme descrito anteriormente em [aplicar o tema personalizado](#apply-the-custom-theme). Além disso, esse erro pode ser causado se o `Toolbar` layout ou tema personalizado não está configurado corretamente.


## <a name="related-links"></a>Links relacionados

- [Barra de ferramentas de pirulito (amostra)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Barra de ferramentas AppCompat (amostra)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
