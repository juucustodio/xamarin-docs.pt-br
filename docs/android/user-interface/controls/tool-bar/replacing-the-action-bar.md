---
title: Substituição da barra de ações
ms.prod: xamarin
ms.assetid: 5341D28E-B203-478D-8464-6FAFDC3A4110
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/27/2018
ms.openlocfilehash: 1339833c9971c70ccb855dc340b12eb60bf22350
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457660"
---
# <a name="replacing-the-action-bar"></a>Substituição da barra de ações

## <a name="overview"></a>Visão geral

Um dos usos mais comuns para o `Toolbar` é substituir a barra de ação padrão por um personalizado `Toolbar` (quando um novo projeto Android é criado, ele usa a barra de ação padrão). Como o `Toolbar` fornece a capacidade de adicionar logotipos com marca, títulos, itens de menu, botões de navegação e até mesmo exibições personalizadas à seção da barra de aplicativos da interface do usuário de uma atividade, ele oferece uma atualização significativa sobre a barra de ação padrão.

Para substituir a barra de ação padrão de um aplicativo por um `Toolbar` : 

1. Crie um novo tema personalizado e modifique as propriedades do aplicativo para que ele use esse novo tema. 

2. Desabilite o `windowActionBar` atributo no tema personalizado e habilite o `windowNoTitle` atributo.

3. Defina um layout para o `Toolbar` .

4. Inclua o `Toolbar` layout no arquivo de layout **principal. Axml** da atividade. 

5. Adicione código ao método da atividade `OnCreate` para localizar o `Toolbar` e a chamada `SetActionBar` para instalar o `ToolBar` como a barra de ação.

As seções a seguir explicam esse processo em detalhes. Um aplicativo simples é criado e sua barra de ação é substituída por um personalizado `Toolbar` . 

## <a name="start-an-app-project"></a>Iniciar um projeto de aplicativo

Crie um novo projeto Android chamado **ToolbarFun** (consulte [Olá, Android](~/android/get-started/hello-android/hello-android-quickstart.md) para obter mais informações sobre como criar um novo projeto Android). Depois que esse projeto for criado, defina os níveis de destino e mínimo da API do Android para **android 5,0 (API nível 21-pirulito)** ou posterior. Para obter mais informações sobre como definir os níveis de versão do Android, consulte [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md). Quando o aplicativo é criado e executado, ele exibe a barra de ação padrão como visto nesta captura de tela:

[![Captura de tela da barra de ação padrão](replacing-the-action-bar-images/01-before-sml.png)](replacing-the-action-bar-images/01-before.png#lightbox)

## <a name="create-a-custom-theme"></a>Criar um tema personalizado

Abra o diretório de **recursos/valores** e crie um novo arquivo chamado **styles.xml**. Substitua seu conteúdo pelo seguinte XML: 

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

Esse XML define um novo tema personalizado chamado **Mythemes** , com base no tema **Theme. material. Light. DarkActionBar** na pirulito. O `windowNoTitle` atributo é definido como `true` para ocultar a barra de título: 

```xml
<item name="android:windowNoTitle">true</item>
```

Para exibir a barra de ferramentas personalizada, o padrão `ActionBar` deve ser desabilitado: 

```xml
<item name="android:windowActionBar">false</item>
```

Uma configuração verde-oliva `colorPrimary` é usada para a cor do plano de fundo da barra de ferramentas: 

```xml
<item name="android:colorPrimary">#5A8622</item>
```

## <a name="apply-the-custom-theme"></a>Aplicar o tema personalizado

Edite **Propriedades/AndroidManifest.xml** e adicione o seguinte `android:theme` atributo ao `<application>` elemento para que o aplicativo use o `MyTheme` tema personalizado: 

```xml
<application android:label="@string/app_name" android:theme="@style/MyTheme"></application>
```

Para obter mais informações sobre como aplicar um tema personalizado a um aplicativo, consulte [usando temas personalizados](~/android/user-interface/material-theme.md#customtheme). 

## <a name="define-a-toolbar-layout"></a>Definir um layout de barra de ferramentas

No diretório de **recursos/layout** , crie um novo arquivo chamado **toolbar.xml**. Substitua seu conteúdo pelo seguinte XML: 

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

Esse XML define o personalizado `Toolbar` que substitui a barra de ação padrão. A altura mínima do `Toolbar` é definida como o tamanho da barra de ação que ele substitui: 

```csharp
android:minHeight="?android:attr/actionBarSize"
```

A cor do plano de fundo do `Toolbar` é definida como a cor verde-oliva definida anteriormente no **styles.xml**:

```csharp
android:background="?android:attr/colorPrimary"
```

A partir da pirulito, o `android:theme` atributo pode ser usado para estilizar uma exibição individual. Os `ThemeOverlay.Material` temas introduzidos na pirulito possibilitam a sobreposição dos `Theme.Material` temas padrão, substituindo os atributos relevantes para torná-los leves ou escuros. Neste exemplo, o `Toolbar` usa um tema escuro para que seu conteúdo fique claro em cores: 

```csharp
android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
```

Essa configuração é usada para que os itens de menu contrastem com a cor de fundo mais escura.

## <a name="include-the-toolbar-layout"></a>Incluir o layout da barra de ferramentas

Edite o arquivo de layout **Resources/layout/Main. axml** e substitua seu conteúdo pelo XML a seguir:

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

Esse layout inclui o `Toolbar` definido em **toolbar.xml** e usa um `RelativeLayout` para especificar que o `Toolbar` deve ser colocado na parte superior da interface do usuário (acima do botão). 

## <a name="find-and-activate-the-toolbar"></a>Localizar e ativar a barra de ferramentas

Edite **MainActivity.cs** e adicione a seguinte instrução Using:

```csharp
using Android.Views;
```

Além disso, adicione as seguintes linhas de código ao final do `OnCreate` método:

```csharp
var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
SetActionBar(toolbar);
ActionBar.Title = "My Toolbar";
```

Esse código localiza as `Toolbar` chamadas e `SetActionBar` para que o `Toolbar` assuma as características da barra de ação padrão. O título da barra de ferramentas é alterado para **minha barra de ferramentas**. Como visto neste exemplo de código, o `ToolBar` pode ser referenciado diretamente como uma barra de ação. Compilar e executar este aplicativo &ndash; o personalizado `Toolbar` é exibido no lugar da barra de ação padrão: 

[![Captura de tela da barra de ferramentas personalizada com esquema de cores verde](replacing-the-action-bar-images/02-after-sml.png)](replacing-the-action-bar-images/02-after.png#lightbox)

Observe que o `Toolbar` é estilizado de forma independente do `Theme.Material.Light.DarkActionBar` tema que é aplicado ao restante do aplicativo. 

Se ocorrer uma exceção durante a execução do aplicativo, consulte a seção [solução de problemas](#troubleshooting) abaixo.

## <a name="add-menu-items"></a>Adicionar itens de menu 

Nesta seção, os menus são adicionados ao `Toolbar` . A área superior direita do `ToolBar` é reservada para itens de menu &ndash; cada item de menu (também chamado de *item de ação*) pode executar uma ação dentro da atividade atual ou pode executar uma ação em nome de todo o aplicativo. 

Para adicionar menus ao `Toolbar` : 

1. Adicione ícones de menu (se necessário) às `mipmap-` pastas do projeto de aplicativo. O Google fornece um conjunto de ícones de menu livres na página [ícones de material](https://design.google.com/icons/) . 

2. Defina o conteúdo dos itens de menu adicionando um novo arquivo de recurso de menu em **recursos/menu**. 

3. Implementar o `OnCreateOptionsMenu` método da atividade &ndash;  esse método inplana os itens de menu. 

4. Implementar o `OnOptionsItemSelected` método da atividade  &ndash; esse método executa uma ação quando um item de menu é tocado. 

As seções a seguir demonstram esse processo em detalhes adicionando os itens de menu **Editar** e **salvar** ao personalizado `Toolbar` . 

### <a name="install-menu-icons"></a>Ícones do menu instalar

Continuando com o `ToolbarFun` aplicativo de exemplo, adicione ícones de menu ao projeto de aplicativo. Baixe os [ícones da barra de ferramentas](https://github.com/xamarin/monodroid-samples/blob/master/Supportv7/AppCompat/Toolbar/Resources/toolbar-icons-plus.zip?raw=true), descompacte e copie o conteúdo das *mipmap* extraídas para o projeto *mipmap-* Folders em **ToolbarFun/recursos** e inclua cada arquivo de ícone adicionado no projeto.

### <a name="define-a-menu-resource"></a>Definir um recurso de menu

Crie um novo subdiretório de **menu** em **recursos**. No subdiretório do **menu** , crie um novo arquivo de recurso de menu chamado **top_menus.xml** e substitua seu conteúdo pelo XML a seguir: 

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

- Um item de menu de **edição** que usa o `ic_action_content_create.png` ícone (um lápis). 

- Um item de menu **salvar** que usa o `ic_action_content_save.png` ícone (um disquete). 

- Um item de menu de **preferências** que não tem um ícone.

Os `showAsAction` atributos dos itens de menu **Editar** e **salvar** são definidos para `ifRoom` &ndash; essa configuração faz com que esses itens de menu sejam exibidos no `Toolbar` se houver espaço suficiente para serem exibidos. O item de menu de **preferências** define `showAsAction` como `never` &ndash; isso faz com que o menu de **preferências** apareça no menu de *estouro* (três pontos verticais). 

### <a name="implement-oncreateoptionsmenu"></a>Implementar OnCreateOptionsMenu

Adicione o seguinte método a **MainActivity.cs**:

```csharp
public override bool OnCreateOptionsMenu(IMenu menu)
{
    MenuInflater.Inflate(Resource.Menu.top_menus, menu);
    return base.OnCreateOptionsMenu(menu);
}
```

O Android chama o `OnCreateOptionsMenu` método para que o aplicativo possa especificar o recurso de menu para uma atividade. Nesse método, o recurso de **top_menus.xml** é informado no passado `menu` . Esse código faz com que os novos itens de menu **Editar**, **salvar**e **preferências** apareçam no `Toolbar` . 

### <a name="implement-onoptionsitemselected"></a>Implementar OnOptionsItemSelected

Adicione o seguinte método a **MainActivity.cs**:

```csharp
public override bool OnOptionsItemSelected(IMenuItem item)
{
    Toast.MakeText(this, "Action selected: " + item.TitleFormatted,
        ToastLength.Short).Show();
    return base.OnOptionsItemSelected(item);
}
```

Quando um usuário toca em um item de menu, o Android chama o `OnOptionsItemSelected` método e passa o item de menu que foi selecionado. Neste exemplo, a implementação apenas exibe um sistema de notificação para indicar qual item de menu foi tocado. 

Crie e execute `ToolbarFun` para ver os novos itens de menu na barra de ferramentas. O `Toolbar` agora exibe três ícones de menu como mostrado nesta captura de tela: 

[![Diagrama ilustrando os locais dos itens de menu Editar, salvar e estouro](replacing-the-action-bar-images/04-menu-items-sml.png)](replacing-the-action-bar-images/04-menu-items.png#lightbox)

Quando um usuário toca no item de menu **Editar** , um sistema de notificação é exibido para indicar que o `OnOptionsItemSelected` método foi chamado: 

[![Captura de tela do sistema exibido quando o item de edição é tocado](replacing-the-action-bar-images/05-toast-displayed-sml.png)](replacing-the-action-bar-images/05-toast-displayed.png#lightbox)

Quando um usuário toca no menu de estouro, o item de menu **preferências** é exibido. Normalmente, as ações menos comuns devem ser colocadas no menu de estouro &ndash; . Este exemplo usa o menu de estouro para **preferências** porque ele não é usado com tanta frequência como **Editar** e **salvar**: 

[![Captura de tela do item de menu de preferências que aparece no menu de estouro](replacing-the-action-bar-images/06-preferences-sml.png)](replacing-the-action-bar-images/06-preferences.png#lightbox)

Para obter mais informações sobre os menus do Android, consulte o tópico [menus](https://developer.android.com/guide/topics/ui/menus.html) para desenvolvedores do Android. 

## <a name="troubleshooting"></a>Solução de problemas

As dicas a seguir podem ajudar a depurar problemas que podem ocorrer ao substituir a barra de ação por uma barra de ferramentas.

### <a name="activity-already-has-an-action-bar"></a>A atividade já tem uma barra de ação

Se o aplicativo não estiver configurado corretamente para usar um tema personalizado, conforme explicado em [aplicar o tema personalizado](#apply-the-custom-theme), a seguinte exceção poderá ocorrer durante a execução do aplicativo:

![Erro que pode ocorrer quando o tema personalizado não é usado](replacing-the-action-bar-images/03-theme-not-defined.png)

Além disso, uma mensagem de erro como a seguinte pode ser produzida: _Java. lang. IllegalStateException: essa atividade já tem uma barra de ação fornecida pela janela décor._ 

Para corrigir esse erro, verifique se o `android:theme` atributo do tema personalizado foi adicionado a `<application>` (em **Propriedades/AndroidManifest.xml**) conforme descrito anteriormente em [aplicar o tema personalizado](#apply-the-custom-theme). Além disso, esse erro pode ser causado se o `Toolbar` layout ou o tema personalizado não estiver configurado corretamente.

## <a name="related-links"></a>Links Relacionados

- [Barra de ferramentas pirulito (exemplo)](/samples/xamarin/monodroid-samples/android50-toolbar)
- [Barra de ferramentas AppCompat (exemplo)](/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)