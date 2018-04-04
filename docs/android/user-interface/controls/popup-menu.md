---
title: Menu pop-up
ms.prod: xamarin
ms.assetid: 1C58E12B-4634-4691-BF59-D5A3F6B0E6F7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/18/2017
ms.openlocfilehash: e7fad84133ca712c531ab0d12a67db78103c7cdd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="popup-menu"></a>Menu pop-up

O `PopupMenu` classe adiciona suporte para exibir os menus pop-up que estão anexados a uma exibição específica. A ilustração a seguir mostra um menu pop-up exibido usando um botão, o segundo item realçado exatamente como ele é selecionado:

 [![Exemplo de um PopopMenu com três três itens](popup-menu-images/20-popupmenu.png)](popup-menu-images/20-popupmenu.png#lightbox)

Android 4 adicionou alguns novos recursos para `PopupMenu` que tornam mais fácil trabalhar com, ou seja:

-   **Inflar** &ndash; aumentar o método agora está disponível diretamente à classe de PopupMenu.
-   **DismissEvent** &ndash; PopupMenu a classe agora tem um DismissEvent.

Vamos examinar esses aprimoramentos. Neste exemplo, temos uma única atividade que contém um botão. Quando o usuário clica no botão, um menu pop-up é exibido conforme mostrado abaixo:

 [![Exemplo de aplicativo em execução no emulador com o botão e menu pop-up do item 3](popup-menu-images/06-popupmenu.png)](popup-menu-images/06-popupmenu.png#lightbox)


## <a name="creating-a-popup-menu"></a>Criar um Menu pop-up

Quando criamos uma instância do `PopupMenu`, precisamos transmitir seu construtor uma referência ao `Context`, bem como o modo de exibição para o qual o menu está anexado. Nesse caso, criamos o `PopupMenu` o manipulador de eventos para nosso botão, que é denominado `showPopupMenu`.
Esse botão também é o modo de exibição para o qual estamos anexará o `PopupMenu`, conforme mostrado no código a seguir:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
}
```

No Android 3, o código para aumentar o menu de um recurso XML necessário que você primeiro obter uma referência a um `MenuInflator`e, em seguida, chamar seu `Inflate` método com a ID de recurso do que continha o menu e a instância de menu para aumentar em XML. Essa abordagem ainda funciona no Android 4 e posterior, como o código a seguir mostra:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.MenuInflater.Inflate (Resource.Menu.popup_menu, menu.Menu);
};
```

A partir do Android 4 no entanto, agora você pode chamar `Inflate` diretamente na instância do `PopupMenu`. Isso torna o código mais conciso, conforme mostrado aqui:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);
    menu.Show ();
};
```

No código acima, após inflacionando menu simplesmente chamamos `menu.Show` para exibi-la na tela.


## <a name="handling-menu-events"></a>Manipulação de eventos de Menu

Quando o usuário seleciona um item de menu, o `MenuItemClick` evento será gerado e o menu será liberado. Toque em qualquer lugar fora o menu simplesmente será descartá-lo. Em ambos os casos, a partir do Android 4, quando o menu é descartado, seu `DismissEvent` será gerado. O código a seguir adiciona manipuladores de eventos para ambos os `MenuItemClick` e `DismissEvent` eventos:

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

- [PopupMenuDemo (exemplo)](https://developer.xamarin.com/samples/monodroid/PopupMenuDemo/)
- [Introdução ao sabor de sorvete Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
