---
title: Menu pop-up
description: Como adicionar um menu pop-up que é ancorado a um modo de exibição específico.
ms.prod: xamarin
ms.assetid: 1C58E12B-4634-4691-BF59-D5A3F6B0E6F7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/31/2018
ms.openlocfilehash: 1e74c8b7745936f6e9a8890fd26acafe2f2fb6d5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113879"
---
# <a name="popup-menu"></a>Menu pop-up

O [PopupMenu](https://developer.xamarin.com/api/type/Android.Widget.PopupMenu/) (também chamado de um _menu de atalho_) é um menu que é ancorado a um modo de exibição específico. No exemplo a seguir, uma única atividade contém um botão. Quando o usuário toca no botão, é exibido um menu pop-up de três itens:

[![Exemplo de um aplicativo com um botão e três itens de menu pop-up](popup-menu-images/01-app-example-sml.png)](popup-menu-images/01-app-example.png#lightbox)


## <a name="creating-a-popup-menu"></a>Criando um Menu pop-up

A primeira etapa é criar um arquivo de recurso de menu para o menu e colocá-lo no **menu derecursos/**. Por exemplo, o XML a seguir é o código para o menu de três itens é exibido na captura de tela anterior **Resources/menu/popup_menu.xml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/item1"
          android:title="item 1" />
    <item android:id="@+id/item1"
          android:title="item 2" />
    <item android:id="@+id/item1"
          android:title="item 3" />
</menu>
```

Em seguida, crie uma instância de `PopupMenu` e ancorava-o em seu modo de exibição. Quando você cria uma instância do `PopupMenu`, você passa ao construtor uma referência para o `Context` , bem como o modo de exibição para o qual o menu será anexado. Como resultado, o menu pop-up é ancorado a essa exibição durante sua construção.

No exemplo a seguir, o `PopupMenu` é criado no manipulador de eventos de clique do botão (que é denominado `showPopupMenu`). Esse botão também é o modo de exibição para o qual o `PopupMenu` é ancorado, conforme mostrado no exemplo de código a seguir:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
};
```

Por fim, no menu pop-up deve ser *inflado* com o recurso de menu que foi criado anteriormente. No exemplo a seguir, a chamada para o menu [inflar](https://developer.xamarin.com/api/member/Android.Views.LayoutInflater.Inflate/p/System.Int32/Android.Views.ViewGroup/) método é adicionado e seu [Mostrar](https://developer.xamarin.com/api/member/Android.Widget.PopupMenu.Show%28%29/) método é chamado para exibi-lo:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);
    menu.Show ();
};
```


## <a name="handling-menu-events"></a>Manipulação de eventos de Menu

Quando o usuário seleciona um item de menu, o [MenuItemClick](https://developer.xamarin.com/api/event/Android.Widget.PopupMenu.MenuItemClick/) clique evento será gerado e o menu será liberado. Tocando em qualquer lugar fora do menu simplesmente será descartá-la. Em ambos os casos, quando o menu é descartado, sua [DismissEvent](https://developer.xamarin.com/api/member/Android.Widget.PopupMenu.Dismiss%28%29/) será gerado. O código a seguir adiciona manipuladores de eventos para ambos os `MenuItemClick` e `DismissEvent` eventos:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);

    menu.MenuItemClick += (s1, arg1) => {
        Console.WriteLine ("{0} selected", arg1.Item.TitleFormatted);
    };

    menu.DismissEvent += (s2, arg2) => {
        Console.WriteLine ("menu dismissed");
    };
    menu.Show ();
};
```



## <a name="related-links"></a>Links relacionados

- [PopupMenuDemo (amostra)](https://developer.xamarin.com/samples/monodroid/PopupMenuDemo/)
