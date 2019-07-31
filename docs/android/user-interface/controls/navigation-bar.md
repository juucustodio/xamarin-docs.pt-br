---
title: Barra de navegação do Xamarin. Android
ms.prod: xamarin
ms.assetid: 6023DB7E-9E72-4B90-A96A-11BC297B8A3D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/01/2017
ms.openlocfilehash: c017ab96bdd78a41b31a3305021c5bc1f8e472d0
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644920"
---
# <a name="xamarinandroid-navigation-bar"></a>Barra de navegação do Xamarin. Android

O Android 4 introduziu um novo recurso de interface do usuário do sistema chamado *barra de navegação*, que fornece controles de navegação em dispositivos que não incluem botões de hardware para **página inicial**, **voltar**e **menu**.
A captura de tela a seguir mostra a barra de navegação de um dispositivo principal Nexus:

 [![Exemplo de uma barra de navegação do Android](navigation-bar-images/19-navbar.png)](navigation-bar-images/19-navbar.png#lightbox)

Vários novos sinalizadores estão disponíveis que controlam a visibilidade da barra de navegação e de seus controles, bem como a visibilidade da barra do sistema que foi introduzida no Android 3. Os sinalizadores são definidos na `Android.View.View` classe e são listados abaixo:

-   `SystemUiFlagVisible`&ndash; Torna a barra de navegação visível. 
-   `SystemUiFlagLowProfile`&ndash; Escurece os controles na barra de navegação. 
-   `SystemUiFlagHideNavigation`&ndash; Oculta a barra de navegação. 


Esses sinalizadores podem ser aplicados a qualquer exibição na hierarquia de exibição, definindo a `SystemUiVisibility` propriedade. Se várias exibições tiverem essa propriedade definida, o sistema as combinará com uma operação ou e as aplicará, desde que a janela na qual os sinalizadores estejam definidos Mantenha o foco. Quando você remove uma exibição, todos os sinalizadores definidos também serão removidos.

O exemplo a seguir mostra um aplicativo simples em que clicar em qualquer um dos `SystemUiVisibility`botões altera:

 [![Capturas de tela que demonstram SystemUiVisibility visíveis, de baixo perfil e ocultos](navigation-bar-images/18-systemuivisibility.png)](navigation-bar-images/18-systemuivisibility.png#lightbox)

O código para alterar o `SystemUiVisibility` define a propriedade em um `TextView` manipulador de eventos de clique de cada botão, como mostrado abaixo:

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

Além disso, `SystemUiVisibility` uma alteração gera `SystemUiVisibilityChange` um evento. Assim como a configuração `SystemUiVisibility` da propriedade, um manipulador para `SystemUiVisibilityChange` o evento pode ser registrado para qualquer exibição na hierarquia. Por exemplo, o código a seguir usa `TextView` a instância para se registrar no evento:

```csharp
tv.SystemUiVisibilityChange +=
  delegate(object sender, View.SystemUiVisibilityChangeEventArgs e) {
        tv.Text = String.Format ("Visibility = {0}", e.Visibility);
  };
```



## <a name="related-links"></a>Links relacionados

- [SystemUIVisibilityDemo (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/systemuivisibilitydemo)
- [Introdução ao sanduíche de sorvete](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma Android 4,0](https://developer.android.com/sdk/android-4.0.html)
