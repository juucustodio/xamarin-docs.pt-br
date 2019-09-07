---
title: Menu pop-up
description: Como adicionar um menu pop-up que está ancorado a uma exibição específica.
ms.prod: xamarin
ms.assetid: 1C58E12B-4634-4691-BF59-D5A3F6B0E6F7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/31/2018
ms.openlocfilehash: 8a808e6ea49338de5b6bd1618fc2227e6cf5f0b1
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764895"
---
# <a name="xamarinandroid-popup-menu"></a>Menu pop-up do Xamarin. Android

O [PopupMenu](xref:Android.Widget.PopupMenu) (também chamado de _menu de atalho_) é um menu que é ancorado a uma determinada exibição. No exemplo a seguir, uma única atividade contém um botão. Quando o usuário toca no botão, um menu pop-up de três itens é exibido:

[![Exemplo de um aplicativo com um botão e um menu pop-up de três itens](popup-menu-images/01-app-example-sml.png)](popup-menu-images/01-app-example.png#lightbox)

## <a name="creating-a-popup-menu"></a>Criando um menu pop-up

A primeira etapa é criar um arquivo de recurso de menu para o menu e colocá-lo em **recursos/menu**. Por exemplo, o XML a seguir é o código para o menu de três itens exibido na captura de tela anterior, **Resources/menu/popup_menu. xml**:

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

Em seguida, crie uma instância `PopupMenu` do e-a em sua exibição. Quando você cria uma instância do `PopupMenu`, passa seu Construtor uma referência para o `Context` , bem como a exibição à qual o menu será anexado. Como resultado, o menu pop-up é ancorado a esta exibição durante sua construção.

No exemplo a seguir, o `PopupMenu` é criado no manipulador de eventos de clique para o botão (que é `showPopupMenu`denominado). Esse botão também é a exibição à qual o `PopupMenu` é ancorado, conforme mostrado no exemplo de código a seguir:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
};
```

Por fim, o menu pop-up deve ser *replanado* com o recurso de menu que foi criado anteriormente. No exemplo a seguir, a chamada para o método [inflar](xref:Android.Views.LayoutInflater.Inflate*) do menu é adicionada e seu método [show](xref:Android.Widget.PopupMenu.Show) é chamado para exibi-lo:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);
    menu.Show ();
};
```

## <a name="handling-menu-events"></a>Manipulando eventos de menu

Quando o usuário seleciona um item de menu, o evento de clique [MenuItemClick](xref:Android.Widget.PopupMenu.MenuItemClick) será gerado e o menu será Descartado. Tocar em qualquer lugar fora do menu simplesmente irá descartá-lo. Em ambos os casos, quando o menu for descartado, seu [DismissEvent](xref:Android.Widget.PopupMenu.Dismiss) será gerado. O código a seguir adiciona manipuladores de eventos para `MenuItemClick` os `DismissEvent` eventos e:

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

- [PopupMenuDemo (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/popupmenudemo)
