---
title: "Barra de navegação"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6023DB7E-9E72-4B90-A96A-11BC297B8A3D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/01/2017
ms.openlocfilehash: fe76c93afc149553e44b5e8fa29a21767becf5c5
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="navigation-bar"></a>Barra de navegação

Android 4 introduziu um novo recurso de interface de usuário de sistema chamado um *barra de navegação*, que fornece controles de navegação em dispositivos que não incluem os botões de hardware para **início**, **novamente** , e **Menu**.
Captura de tela a seguir mostra a barra de navegação de um dispositivo do Nexus primo:

 [![Exemplo de uma barra de navegação do Android](navigation-bar-images/19-navbar.png)](navigation-bar-images/19-navbar.png#lightbox)

Vários sinalizadores novo estão disponíveis que controlam a visibilidade da barra de navegação e seus controles, bem como a visibilidade da barra de sistema que foi introduzido no Android 3. Os sinalizadores são definidos no `Android.View.View` classe e estão listadas abaixo:

-   `SystemUiFlagVisible` &ndash; Barra de navegação torna visível. 
-   `SystemUiFlagLowProfile` &ndash; DIMS controles na barra de navegação. 
-   `SystemUiFlagHideNavigation` &ndash; Oculta a barra de navegação. 


Esses sinalizadores podem ser aplicados a qualquer exibição na hierarquia do modo de exibição, definindo o `SystemUiVisibility` propriedade. Se vários modos de exibição têm esta propriedade definida, o sistema combina com uma operação OR e os aplica desde que a janela na qual os sinalizadores são definidos retém o foco. Quando você remove uma exibição, os sinalizadores que definido também serão removidos.

O exemplo a seguir mostra um aplicativo simples, onde os botões altera o `SystemUiVisibility`:

 [![Capturas de tela demonstra visíveis, baixo perfil e SystemUiVisibility oculto](navigation-bar-images/18-systemuivisibility.png)](navigation-bar-images/18-systemuivisibility.png#lightbox)

O código para alterar o `SystemUiVisibility` define a propriedade em uma `TextView` de cada botão Clique manipulador de eventos, conforme mostrado abaixo:

```csharp
var tv = FindViewById<TextView> (Resource.Id.systemUiFlagTextView);
var lowProfileButton = FindViewById<Button>(Resource.Id.lowProfileButton);
var hideNavButton = FindViewById<Button> (Resource.Id.hideNavigation);
var visibleButton = FindViewById<Button> (Resource.Id.visibleButton);
           
lowProfileButton.Click += delegate {
    tv.SystemUiVisibility =
        (StatusBarVisibility)View.SystemUiFlagLowProfile;
};
           
hideNavButton.Click += delegate {
    tv.SystemUiVisibility =
       (StatusBarVisibility)View.SystemUiFlagHideNavigation;        
};
           
visibleButton.Click += delegate {
    tv.SystemUiVisibility = (StatusBarVisibility)View.SystemUiFlagVisible;
}
```

Além disso, um `SystemUiVisibility` alterar gera um `SystemUiVisibilityChange` eventos. Assim como a configuração de `SystemUiVisibility` propriedade, um manipulador para o `SystemUiVisibilityChange` evento pode ser registrado para qualquer exibição na hierarquia. Por exemplo, o código abaixo usa o `TextView` instância a ser registrada para o evento:

```csharp
tv.SystemUiVisibilityChange +=
  delegate(object sender, View.SystemUiVisibilityChangeEventArgs e) {
        tv.Text = String.Format ("Visibility = {0}", e.Visibility);
  };
```



## <a name="related-links"></a>Links relacionados

- [SystemUIVisibilityDemo (sample)](https://developer.xamarin.com/samples/monodroid/SystemUIVisibilityDemo/)
- [Introdução ao sabor de sorvete Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
