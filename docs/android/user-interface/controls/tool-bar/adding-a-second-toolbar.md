---
title: Adicionar uma segunda barra de ferramentas
ms.prod: xamarin
ms.assetid: FCE0AD27-8B6B-47C6-AD19-2B1C12E1BBBF
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: fc95c05c1945464cd9cac8565d8a11ff1b4c7e1d
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2018
ms.locfileid: "51527281"
---
# <a name="adding-a-second-toolbar"></a>Adicionar uma segunda barra de ferramentas


## <a name="overview"></a>Visão geral 

O `Toolbar` pode fazer mais do que substituir a barra de ação &ndash; ele pode ser usado várias vezes dentro de uma atividade, ele pode ser personalizado para o posicionamento em qualquer lugar na tela e ele pode ser configurado para abranger apenas uma parcial largura da tela. Os exemplos a seguir ilustram como criar um segundo `Toolbar` e coloque-o na parte inferior da tela. Isso `Toolbar` implementa **cópia**, **Recortar**, e **colar** itens de menu. 


## <a name="define-the-second-toolbar"></a>Definir a segunda barra de ferramentas 

Edite o arquivo de layout **Main. axml** e substitua seu conteúdo com o XML a seguir:

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

Esse XML adiciona uma segunda `Toolbar` na parte inferior da tela com um vazio `ImageView` preenchendo o meio da tela. A altura desta `Toolbar` é definido como a altura de uma barra de ações: 

```xml
android:minHeight="?android:attr/actionBarSize"
```

A cor do plano de fundo desse `Toolbar` é definido como uma cor de ênfase será definida em seguida:

```xml
android:background="?android:attr/colorAccent
```

Observe que este `Toolbar` baseia-se em um tema diferente (**ThemeOverlay.Material.Dark.ActionBar**) diferente daquela usada pelo `Toolbar` criados no [substituindo a barra de ação](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md) &ndash;ele não está associado a decoração de janela de atividade ou o tema usado no primeiro `Toolbar`.

Edite **Resources/values/styles.xml** e adicione a seguinte cor de destaque para a definição de estilo: 

```xml
<item name="android:colorAccent">#C7A935</item>
```

Isso dá a barra de ferramentas inferior âmbar escura. Criar e executar o aplicativo exibe uma barra de ferramentas de segundo em branco na parte inferior da tela: 

[![Captura de tela do aplicativo com a segunda barra de ferramentas amarela na parte inferior da tela](adding-a-second-toolbar-images/01-second-toolbar-sml.png)](adding-a-second-toolbar-images/01-second-toolbar.png#lightbox)


 
## <a name="add-edit-menu-items"></a>Adicionar itens de Menu Editar 

Esta seção explica como adicionar editar itens de menu na parte inferior `Toolbar`. 

Para adicionar itens de menu para um secundário `Toolbar`: 

1.  Adicionar ícones de menu para o `mipmap-` pastas do projeto de aplicativo (se necessário).

2.  Definir o conteúdo dos itens de menu, adicionando um arquivo de recurso de menu adicionais à **menu derecursos/**. 

3.  Na atividade de `OnCreate` método, localizar o `Toolbar` (chamando `FindViewById`) e aumentar a `Toolbar`do menus.

4.  Implementar um manipulador click no `OnCreate` para os novos itens de menu. 

As seções a seguir demonstram esse processo em detalhes: **Recortar**, **cópia**, e **colar** itens de menu são adicionados à parte inferior `Toolbar`. 



### <a name="define-the-edit-menu-resource"></a>Definir o recurso de Menu Editar

No **/menu de recursos** subdiretório, crie um novo arquivo XML chamado **edit_menus.xml** e substitua o conteúdo com o seguinte XML:

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

Este XML cria o **Recortar**, **cópia**, e **colar** itens de menu (usando os ícones que foram adicionados para o `mipmap-` pastas no [substituindo a barra de ação ](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md)).



### <a name="inflate-the-menus"></a>Aumentar os Menus

No final de `OnCreate` método no **MainActivity.cs**, adicione as seguintes linhas de código: 

```csharp
var editToolbar = FindViewById<Toolbar>(Resource.Id.edit_toolbar);
editToolbar.Title = "Editing";
editToolbar.InflateMenu (Resource.Menu.edit_menus);
editToolbar.MenuItemClick += (sender, e) => {
    Toast.MakeText(this, "Bottom toolbar tapped: " + e.Item.TitleFormatted, ToastLength.Short).Show();
};
```

Esse código localiza os `edit_toolbar` exibição definida no **Main. axml**, define seu título para **edição**e irá aumentar seus itens de menu (definidos no **edit_menus.xml**). Ele define um menu que exibe uma notificação do sistema para indicar qual ícone de edição foi tocado manipulador de clique. 

Compile e execute o aplicativo. Quando o aplicativo é executado, o texto e os ícones adicionados acima serão exibida conforme mostrado aqui: 

[![Diagrama da parte inferior da barra de ferramentas com ícones de recortar, copiar e colar](adding-a-second-toolbar-images/02-bottom-toolbar-sml.png)](adding-a-second-toolbar-images/02-bottom-toolbar.png#lightbox)

Tocar o **Recortar** ícone do menu faz com que a seguinte notificação do sistema a ser exibido: 

[![Captura de tela de notificação do sistema que indica que o ícone do menu Recortar foi tocado](adding-a-second-toolbar-images/03-bottom-tapped-sml.png)](adding-a-second-toolbar-images/03-bottom-tapped.png#lightbox)

Tocando em itens de menu em qualquer barra de ferramentas exibe as notificações do sistema resultantes: 

[![Capturas de tela de notificações do sistema para salvar, copiar e colar itens de menu sendo tocados](adding-a-second-toolbar-images/04-menu-action-sml.png)](adding-a-second-toolbar-images/04-menu-action.png#lightbox)



## <a name="the-up-button"></a>Botão para cima 

Contam com a maioria dos aplicativos do Android a **volta** botão para navegação de aplicativo; pressionando o **volta** botão leva o usuário para a tela anterior.
No entanto, você talvez queira fornecer um **backup** botão que torna mais fácil para os usuários navegarem "até" à tela principal do aplicativo. Quando o usuário seleciona o **para cima** botão, o usuário move para um nível mais alto na hierarquia de aplicativo &ndash; ou seja, o aplicativo retira o usuário voltar várias atividades em pilha voltar em vez de retirada novamente para o anteriormente visitado Atividade. 

Para habilitar o **para cima** botão em uma segunda atividade que usa um `Toolbar` como sua barra de ação, chame o `SetDisplayHomeAsUpEnabled` e `SetHomeButtonEnabled` métodos em que a segunda atividade `OnCreate` método:

```csharp
SetActionBar (toolbar);
...
ActionBar.SetDisplayHomeAsUpEnabled (true);
ActionBar.SetHomeButtonEnabled (true);
```

O [dar suporte a barra de ferramentas v7](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/) código de exemplo demonstra o **backup** botão em ação. Este exemplo (que usa a biblioteca de AppCompat descrita a seguir) implementa uma segunda atividade que usa a barra de ferramentas **backup** botão Navegação hierárquica volta para a atividade anterior. Neste exemplo, o `DetailActivity` botão Início permite que o **backup** botão fazendo o seguinte `SupportActionBar` chamadas de método: 

```csharp
SetSupportActionBar (toolbar);
...
SupportActionBar.SetDisplayHomeAsUpEnabled (true);
SupportActionBar.SetHomeButtonEnabled (true);
```

Quando o usuário navega `MainActivity` para `DetailActivity`, o `DetailActivity` exibe um **backup** botão (apontador seta para a esquerda), conforme mostrado na captura de tela:

[![Captura de tela exemplo de uma seta à esquerda do botão Up na barra de ferramentas](adding-a-second-toolbar-images/05-up-button-sml.png)](adding-a-second-toolbar-images/05-up-button.png#lightbox)

Tocando isso **para cima** botão faz com que o aplicativo retornar ao `MainActivity`. Em um aplicativo mais complexo com vários níveis da hierarquia, tocar neste botão retornaria o usuário para o próximo nível mais alto no aplicativo em vez da tela anterior. 



## <a name="related-links"></a>Links relacionados

- [Barra de ferramentas de pirulito (amostra)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Barra de ferramentas AppCompat (amostra)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
