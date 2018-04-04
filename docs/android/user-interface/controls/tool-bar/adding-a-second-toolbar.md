---
title: Adicionando uma segunda barra de ferramentas
ms.prod: xamarin
ms.assetid: FCE0AD27-8B6B-47C6-AD19-2B1C12E1BBBF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: f2255ab5ac7e153266093877a1c52bfc08927a09
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="adding-a-second-toolbar"></a>Adicionando uma segunda barra de ferramentas


## <a name="overview"></a>Visão geral 

O `Toolbar` pode fazer mais do que substituir a barra de ação &ndash; ele pode ser usado várias vezes em uma atividade, ele pode ser a ser personalizado para posicionamento em qualquer lugar na tela, e pode ser configurada para abranger apenas uma parcial largura da tela. Os exemplos a seguir ilustram como criar um segundo `Toolbar` e colocá-lo na parte inferior da tela. Isso `Toolbar` implementa **cópia**, **Recortar**, e **colar** itens de menu. 


## <a name="define-the-second-toolbar"></a>Definir a segunda barra de ferramentas 

Edite o arquivo de layout **Main.axml** e substituir seu conteúdo com o seguinte XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <include
        android:id="@+id/toolbar"
        layout="@layout/toolbar" />
    <LinearLayout
        android:orientation="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:id="@+id/main_content"
        android:layout_below="@id/toolbar">
      <ImageView
          android:layout_width="fill_parent"
          android:layout_height="0dp"
          android:layout_weight="1" />
      <Toolbar
          android:id="@+id/edit_toolbar"
          android:minHeight="?android:attr/actionBarSize"
          android:background="?android:attr/colorAccent"
          android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
          android:layout_width="match_parent"
          android:layout_height="wrap_content" />
    </LinearLayout>
</RelativeLayout>
```

Esse XML adiciona uma segunda `Toolbar` até a parte inferior da tela com vazio `ImageView` preenchendo o meio da tela. A altura deste `Toolbar` é definido como a altura de uma barra de ação: 

```xml
android:minHeight="?android:attr/actionBarSize"
```

A cor de plano de fundo deste `Toolbar` é definido como uma cor de Destaque que será definida em seguida:

```xml
android:background="?android:attr/colorAccent
```

Observe que esse `Toolbar` baseia-se um tema diferente (**ThemeOverlay.Material.Dark.ActionBar**) daquele usado pelo `Toolbar` criado no [substituindo a barra de ação](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md) &ndash;ele não está associado a casa de janela da atividade ou o tema usado na primeira `Toolbar`.

Editar **Resources/values/styles.xml** e adicionar a cor de destaque a seguir para a definição de estilo: 

```xml
<item name="android:colorAccent">#C7A935</item>
```

Isso oferece a barra de ferramentas inferior âmbar escura. Criando e executando o aplicativo exibe uma barra de ferramentas de segundo em branco na parte inferior da tela: 

[![Captura de tela do aplicativo com a segunda barra de ferramentas amarela na parte inferior da tela](adding-a-second-toolbar-images/01-second-toolbar-sml.png)](adding-a-second-toolbar-images/01-second-toolbar.png#lightbox)


 
## <a name="add-edit-menu-items"></a>Adicionar itens de Menu Editar 

Esta seção explica como adicionar editar itens de menu na parte inferior `Toolbar`. 

Para adicionar itens de menu para um secundário `Toolbar`: 

1.  Adicionar ícones de menu para o `mipmap-` pastas de projeto de aplicativo (se necessário).

2.  Definir o conteúdo dos itens do menu, adicionando um arquivo de recurso de menu adicionais para **recursos/menu**. 

3.  A atividade `OnCreate` método, localizar o `Toolbar` (chamando `FindViewById`) e aumentar o `Toolbar`do menus.

4.  Implementar um manipulador de clique em `OnCreate` para os novos itens de menu. 

As seções a seguir demonstram esse processo em detalhes: **Recortar**, **cópia**, e **colar** itens de menu são adicionados à parte inferior `Toolbar`. 



### <a name="define-the-edit-menu-resource"></a>Definir o recurso de Menu Editar

No **recursos/menu** subdiretório, criar um novo arquivo XML chamado **edit_menus.xml** e substitua o conteúdo XML a seguir:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item
       android:id="@+id/menu_cut"
       android:icon="@mipmap/ic_menu_cut_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Cut" />
  <item
       android:id="@+id/menu_copy"
       android:icon="@mipmap/ic_menu_copy_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Copy" />
  <item
       android:id="@+id/menu_paste"
       android:icon="@mipmap/ic_menu_paste_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Paste" />
</menu>
```

Esse XML cria o **Recortar**, **cópia**, e **colar** itens de menu (usando ícones que foram adicionados para o `mipmap-` pastas no [substituindo a barra de ação ](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md)).



### <a name="inflate-the-menus"></a>Aumentar os Menus

No final do `OnCreate` método **MainActivity.cs**, adicione as seguintes linhas de código: 

```csharp
var editToolbar = FindViewById<Toolbar>(Resource.Id.edit_toolbar);
editToolbar.Title = "Editing";
editToolbar.InflateMenu (Resource.Menu.edit_menus);
editToolbar.MenuItemClick += (sender, e) => {
    Toast.MakeText(this, "Bottom toolbar tapped: " + e.Item.TitleFormatted, ToastLength.Short).Show();
};
```

Esse código localiza o `edit_toolbar` exibição definida no **Main.axml**, define seu título para **edição**e inflação seus itens de menu (definido em **edit_menus.xml**). Define um menu de clique manipulador que exibe uma notificação do sistema para indicar qual ícone de edição foi tocado. 

Compile e execute o aplicativo. Quando o aplicativo é executado, o texto e ícones adicionados acima serão exibidos conforme mostrado aqui: 

[![Diagrama da parte inferior da barra de ferramentas com ícones de recortar, copiar e colar](adding-a-second-toolbar-images/02-bottom-toolbar-sml.png)](adding-a-second-toolbar-images/02-bottom-toolbar.png#lightbox)

Tocar o **Recortar** ícone do menu faz a torrada seguinte a ser exibido: 

[![Captura de tela de notificação do sistema que indica que o ícone do menu Recortar foi tocado](adding-a-second-toolbar-images/03-bottom-tapped-sml.png)](adding-a-second-toolbar-images/03-bottom-tapped.png#lightbox)

Ao tocar em itens de menu ou barra de ferramentas exibe as notificações do sistema resultantes: 

[![Capturas de tela de notificações do sistema para salvar a cópia e colar itens de menu sendo tocados](adding-a-second-toolbar-images/04-menu-action-sml.png)](adding-a-second-toolbar-images/04-menu-action.png#lightbox)



## <a name="the-up-button"></a>O botão de cima 

Contam com a maioria dos aplicativos do Android a **novamente** botão de navegação do aplicativo; pressionando o **novamente** botão leva o usuário para a tela anterior.
No entanto, você talvez queira fornecer um **backup** botão que torna mais fácil para os usuários navegarem "até" à tela principal do aplicativo. Quando o usuário seleciona o **backup** botão, o usuário move-se para um nível mais alto na hierarquia de aplicativo &ndash; ou seja, o aplicativo será exibida ao usuário a várias atividades na pilha voltar em vez de produzirão novamente para o visitados anteriormente Atividade. 

Para habilitar o **backup** botão em uma segunda atividade que usa um `Toolbar` como sua barra de ação, chame o `SetDisplayHomeAsUpEnabled` e `SetHomeButtonEnabled` métodos em que a segunda atividade `OnCreate` método:

```csharp
SetActionBar (toolbar);
...
ActionBar.SetDisplayHomeAsUpEnabled (true);
ActionBar.SetHomeButtonEnabled (true);
```

O [dar suporte a barra de ferramentas v7](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/) código de exemplo demonstra o **backup** botão em ação. Este exemplo (que usa a biblioteca de AppCompat descrita a seguir) implementa uma segunda atividade que usa a barra de ferramentas **backup** botão de Navegação hierárquica para a atividade anterior. Neste exemplo, o `DetailActivity` botão página inicial permite que o **backup** botão fazendo o seguinte `SupportActionBar` chamadas de método: 

```csharp
SetSupportActionBar (toolbar);
...
SupportActionBar.SetDisplayHomeAsUpEnabled (true);
SupportActionBar.SetHomeButtonEnabled (true);
```

Quando o usuário navega a partir de `MainActivity` para `DetailActivity`, o `DetailActivity` exibe um **backup** botão (seta à esquerda), conforme mostrado na captura de tela:

[![Captura de tela exemplo de uma seta à esquerda do botão Up na barra de ferramentas](adding-a-second-toolbar-images/05-up-button-sml.png)](adding-a-second-toolbar-images/05-up-button.png#lightbox)

Tocar isso **backup** botão faz com que o aplicativo retornar ao `MainActivity`. Em um aplicativo mais complexo com vários níveis de hierarquia, você tocar neste botão retornaria o usuário para o próximo nível mais alto no aplicativo em vez da tela anterior. 



## <a name="related-links"></a>Links relacionados

- [Barra de ferramentas de pirulito (exemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Barra de ferramentas de AppCompat (exemplo)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
