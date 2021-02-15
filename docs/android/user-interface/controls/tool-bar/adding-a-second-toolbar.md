---
title: Adicionar uma segunda barra de ferramentas
ms.prod: xamarin
ms.assetid: FCE0AD27-8B6B-47C6-AD19-2B1C12E1BBBF
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: e6104a58c35b4daf8e204b3937022103d774615c
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457756"
---
# <a name="adding-a-second-toolbar"></a>Adicionar uma segunda barra de ferramentas

## <a name="overview"></a>Visão geral 

O `Toolbar` pode fazer mais do que substituir a barra de ação &ndash; que pode ser usada várias vezes em uma atividade, pode ser personalizada para posicionamento em qualquer lugar na tela e pode ser configurada para abranger apenas uma largura parcial da tela. Os exemplos a seguir ilustram como criar um segundo `Toolbar` e colocá-lo na parte inferior da tela. Isso `Toolbar` implementa os itens de menu **copiar**, **recortar**e **colar** . 

## <a name="define-the-second-toolbar"></a>Definir a segunda barra de ferramentas 

Edite o arquivo de layout **Main. axml** e substitua seu conteúdo por pelo seguinte XML:

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

Esse XML adiciona um segundo `Toolbar` à parte inferior da tela com um preenchimento vazio `ImageView` do meio da tela. A altura disso `Toolbar` é definida como a altura de uma barra de ação: 

```xml
android:minHeight="?android:attr/actionBarSize"
```

A cor do plano de fundo `Toolbar` é definida como uma cor de destaque que será definida em seguida:

```xml
android:background="?android:attr/colorAccent
```

Observe que isso `Toolbar` se baseia em um tema diferente (**ThemeOverlay. material. Dark. ActionBar**) do que o usado pelo `Toolbar` criado na [substituição da barra de ação](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md) &ndash; ele não está associado à janela da atividade décor ou ao tema usado no primeiro `Toolbar` .

Edite os **recursos/valores/styles.xml** e adicione a seguinte cor de destaque à definição de estilo: 

```xml
<item name="android:colorAccent">#C7A935</item>
```

Isso dá à barra de ferramentas inferior uma cor âmbar escura. Compilar e executar o aplicativo exibe uma segunda barra de ferramentas em branco na parte inferior da tela: 

[![Captura de tela do aplicativo com a segunda barra de ferramentas amarela na parte inferior do ecrã](adding-a-second-toolbar-images/01-second-toolbar-sml.png)](adding-a-second-toolbar-images/01-second-toolbar.png#lightbox)

## <a name="add-edit-menu-items"></a>Adicionar itens de menu de edição 

Esta seção explica como adicionar itens de menu de edição à parte inferior `Toolbar` . 

Para adicionar itens de menu a um secundário `Toolbar` : 

1. Adicione ícones de menu às `mipmap-` pastas do projeto de aplicativo (se necessário).

2. Defina o conteúdo dos itens de menu adicionando um arquivo de recurso de menu adicional para **recursos/menu**. 

3. No método da atividade `OnCreate` , localize o `Toolbar` (chamando `FindViewById` ) e aumente os `Toolbar` menus do.

4. Implemente um manipulador de cliques no `OnCreate` para os novos itens de menu. 

As seções a seguir demonstram esse processo em detalhes: os itens de menu **recortar**, **copiar**e **colar** são adicionados à parte inferior `Toolbar` . 

### <a name="define-the-edit-menu-resource"></a>Definir o recurso de menu Editar

No subdiretório de **recursos/menu** , crie um novo arquivo xml chamado **edit_menus.xml** e substitua o conteúdo pelo seguinte XML:

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

Esse XML cria os itens de menu **recortar**, **copiar**e **colar** (usando ícones que foram adicionados às `mipmap-` pastas ao [substituir a barra de ação](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md)).

### <a name="inflate-the-menus"></a>Inflar os menus

No final do `OnCreate` método no **MainActivity.cs**, adicione as seguintes linhas de código: 

```csharp
var editToolbar = FindViewById<Toolbar>(Resource.Id.edit_toolbar);
editToolbar.Title = "Editing";
editToolbar.InflateMenu (Resource.Menu.edit_menus);
editToolbar.MenuItemClick += (sender, e) => {
    Toast.MakeText(this, "Bottom toolbar tapped: " + e.Item.TitleFormatted, ToastLength.Short).Show();
};
```

Esse código localiza o `edit_toolbar` modo de exibição definido em **Main. axml**, define seu título como **editando**e inplana seus itens de menu (definidos em **edit_menus.xml**). Ele define um manipulador de cliques de menu que exibe um sistema de notificação para indicar qual ícone de edição foi tocado. 

Compile e execute o aplicativo. Quando o aplicativo for executado, o texto e os ícones adicionados acima aparecerão como mostrado aqui: 

[![Diagrama da barra de ferramentas inferior com ícones de recortar, copiar e colar](adding-a-second-toolbar-images/02-bottom-toolbar-sml.png)](adding-a-second-toolbar-images/02-bottom-toolbar.png#lightbox)

Tocar no ícone do menu **recortar** faz com que o seguinte sistema de notificação seja exibido: 

[![Captura de tela do sistema indicando que o ícone do menu recortar foi tocado](adding-a-second-toolbar-images/03-bottom-tapped-sml.png)](adding-a-second-toolbar-images/03-bottom-tapped.png#lightbox)

Tocar em itens de menu em qualquer barra de ferramentas exibe as notificações resultantes: 

[![Capturas de tela das notificações dos itens de menu salvar, copiar e colar que estão sendo tocadas](adding-a-second-toolbar-images/04-menu-action-sml.png)](adding-a-second-toolbar-images/04-menu-action.png#lightbox)

## <a name="the-up-button"></a>O botão para cima 

A maioria dos aplicativos Android depende do botão **voltar** para a navegação do aplicativo; pressionar o botão **voltar** leva o usuário para a tela anterior.
No entanto, você também pode querer fornecer um botão para **cima** que torna mais fácil para os usuários navegar "para cima" na tela principal do aplicativo. Quando o usuário seleciona o botão **para cima** , o usuário passa para um nível mais alto na hierarquia do aplicativo &ndash; , ou seja, o aplicativo faz o usuário voltar a várias atividades na pilha voltar, em vez de voltar à atividade visitada anteriormente. 

Para habilitar o botão para **cima** em uma segunda atividade que usa uma `Toolbar` como sua barra de ação, chame os `SetDisplayHomeAsUpEnabled` `SetHomeButtonEnabled` métodos e no método da segunda atividade `OnCreate` :

```csharp
SetActionBar (toolbar);
...
ActionBar.SetDisplayHomeAsUpEnabled (true);
ActionBar.SetHomeButtonEnabled (true);
```

O exemplo de código de [barra de ferramentas v7 de suporte](/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar) demonstra o botão **para cima** em ação. Este exemplo (que usa a biblioteca AppCompat descrita em Avançar) implementa uma segunda atividade que usa o botão de **cima** da barra de ferramentas para navegação hierárquica de volta para a atividade anterior. Neste exemplo, o `DetailActivity` botão página inicial habilita o botão **para cima** fazendo as seguintes `SupportActionBar` chamadas de método: 

```csharp
SetSupportActionBar (toolbar);
...
SupportActionBar.SetDisplayHomeAsUpEnabled (true);
SupportActionBar.SetHomeButtonEnabled (true);
```

Quando o usuário navega de `MainActivity` para `DetailActivity` , o `DetailActivity` exibe um botão para **cima** (seta apontando para a esquerda), conforme mostrado na captura de tela:

[![Exemplo de captura de tela de uma seta para a esquerda do botão para cima na barra de ferramentas](adding-a-second-toolbar-images/05-up-button-sml.png)](adding-a-second-toolbar-images/05-up-button.png#lightbox)

Tocar no botão **up** faz com que o aplicativo retorne `MainActivity` . Em um aplicativo mais complexo com vários níveis de hierarquia, tocar nesse botão retornaria o usuário para o próximo nível mais alto no aplicativo, e não para a tela anterior. 

## <a name="related-links"></a>Links Relacionados

- [Barra de ferramentas pirulito (exemplo)](/samples/xamarin/monodroid-samples/android50-toolbar)
- [Barra de ferramentas AppCompat (exemplo)](/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)