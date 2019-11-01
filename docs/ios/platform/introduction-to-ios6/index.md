---
title: Introdução ao iOS 6
description: Este documento contém links para guias que descrevem os recursos introduzidos no iOS 6. As exibições de coleção, PassKit, a estrutura social e as alterações em StoreKit são todas discutidas.
ms.prod: xamarin
ms.assetid: 242DA7E3-8FD8-5F20-285D-603259CA622D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: d30789a33eb8023f4ce77b4a7c2445fabca0f2db
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031829"
---
# <a name="introduction-to-ios-6"></a>Introdução ao iOS 6

_o iOS 6 inclui uma variedade de novas tecnologias para o desenvolvimento de aplicativos, que o Xamarin. C# Ios 6 traz para os desenvolvedores._

[![](images/ios6-large.jpg "The iOS 6 logo")](images/ios6-large.jpg#lightbox)

Com o iOS 6 e o Xamarin. iOS 6, os desenvolvedores agora têm uma infinidade de capacidade à sua disposição para criar aplicativos iOS, incluindo aqueles destinados ao iPhone 5.
Este documento lista alguns dos novos recursos mais interessantes que estão disponíveis e links para artigos para cada tópico. Além disso, ele toca em algumas alterações que serão importantes à medida que os desenvolvedores mudarem para o iOS 6 e a nova resolução do iPhone 5.

## <a name="introduction-to-collection-viewsiosuser-interfacecontrolsuicollectionviewmd"></a>[Introdução às exibições de coleção](~/ios/user-interface/controls/uicollectionview.md)

Exibições de coleção permitem que o conteúdo seja exibido usando layouts arbitrários. Eles permitem criar facilmente layouts de grade prontos para uso, além de dar suporte a layouts personalizados. Para obter mais informações consulte, o guia [Introdução às exibições de coleção](~/ios/user-interface/controls/uicollectionview.md) [](~/ios/user-interface/controls/uicollectionview.md).

## <a name="introduction-to-passkitiosplatformpasskitmd"></a>[Introdução ao PassKit](~/ios/platform/passkit.md)

A estrutura PassKit permite que os aplicativos interajam com as passagens digitais que são gerenciadas no aplicativo Passbook. Para obter mais informações, consulte a [introdução ao guia do kit de passagem](~/ios/platform/passkit.md).

## <a name="introduction-to-eventkitiosplatformeventkitmd"></a>[Introdução ao kit](~/ios/platform/eventkit.md)

A estrutura Kit fornece uma maneira de acessar os dados de calendários, eventos de calendário e lembretes que o banco de dados de calendário armazena. O acesso aos calendários e eventos de calendário está disponível desde o iOS 4, mas o iOS 6 agora expõe o acesso aos dados de lembretes. Para obter mais informações, consulte o guia [I](~/ios/platform/eventkit.md) [ntroduction to kit](~/ios/platform/eventkit.md) .

## <a name="introduction-to-the-social-frameworkiosplatformsocial-frameworkmd"></a>[Introdução à estrutura social](~/ios/platform/social-framework.md)

A estrutura social fornece uma API unificada para interagir com redes sociais, incluindo o Twitter e o Facebook, bem como SinaWeibo para usuários na China. Para obter mais informações, consulte a [introdução ao guia da estrutura social](~/ios/platform/social-framework.md) .

## <a name="changes-to-storekitchanges-to-storekitmd"></a>[Alterações no StoreKit](changes-to-storekit.md)

A Apple introduziu dois novos recursos no kit de armazenamento: comprando e baixando o iTunes ou o conteúdo da loja de aplicativos de dentro de seu aplicativo e hospedando seus arquivos de conteúdo para compras no aplicativo!. Para obter mais informações, consulte as [alterações no](changes-to-storekit.md) guia do kit de armazenamento.

## <a name="other-changes"></a>Outras alterações

### <a name="viewwillunload-and-viewdidunload-deprecated"></a>ViewWillUnload e ViewDidUnload preteridos

Os métodos `ViewWillUnload` e `ViewDidUnload` de `UIViewController` não são mais chamados no iOS 6. Em versões anteriores do iOS, esses métodos podem ter sido usados por aplicativos para salvar o estado antes de uma exibição ser descarregada e código de limpeza, respectivamente.

Por exemplo, Visual Studio para Mac criaria um método chamado `ReleaseDesignerOutlets`, mostrado abaixo, que seria chamado a partir de `ViewDidUnload`:

```csharp
void ReleaseDesignerOutlets ()
{
    if (myOutlet != null) {
        myOutlet.Dispose ();
        myOutlet = null;
    }
}
```

No entanto, no iOS 6, não é mais necessário chamar `ReleaseDesignerOutlets`.   

Para o código de limpeza, os aplicativos iOS 6 devem usar `DidReceiveMemoryWarning`. No entanto, o código que chama `Dispose` deve ser usado com moderação e apenas para objetos com uso intensivo de memória, conforme mostrado abaixo:

```csharp
if (myImageView != null){
    if (myImageView.Superview == null){
        myImageView.Dispose();
        myImageView = null;
    }
}
```

Novamente, a chamada de `Dispose` como acima deve ser necessária raramente. Em geral, a maioria dos aplicativos deve fazer é remover manipuladores de eventos.

Para o caso de salvamento de estado, os aplicativos podem executá-lo em `ViewWillDisappear` e `ViewDidDisappear` em vez de `ViewWillUnload`.

### <a name="iphone-5-resolution"></a>Resolução do iPhone 5

os dispositivos iPhone 5 têm uma resolução 640x1136. Os aplicativos direcionados a versões anteriores do iOS aparecerão letterboxed quando executados em um iPhone 5, conforme mostrado abaixo:

 [![](images/01-letterboxed.png "Applications that targeted previous versions of iOS will appear letterboxed when run on an iPhone 5")](images/01-letterboxed.png#lightbox)

Para que o aplicativo apareça em tela inteira no iPhone 5, basta adicionar uma imagem chamada `Default-568h@2x.png` com uma resolução de 640x1136. A captura de tela a seguir mostra o aplicativo em execução depois que essa imagem é incluída:

 [![](images/02-fullscreen.png "This screenshot shows the application running after this image has been included")](images/02-fullscreen.png#lightbox)

### <a name="subclassing-uinavigationbar"></a>UINavigationBar de subclasse

No iOS 6 `UINavigationBar` pode ser subclasse. Isso permite o controle adicional da aparência do `UINavigationBar`. Por exemplo, os aplicativos podem subclasses para adicionar subexibições, animar essas exibições e modificar os limites do `UINavigationBar`.

O código a seguir mostra um exemplo de uma `UINavigationBar` de subclasse que adiciona uma `UIImageView`:

```csharp
public class CustomNavBar : UINavigationBar
{
    UIImageView iv;
    public CustomNavBar (IntPtr h) : base(h)
    {
        iv = new UIImageView (UIImage.FromFile ("monkey.png"));
        iv.Frame = new CGRect (75, 0, 30, 39);
    }
    public override void Draw (RectangleF rect)
    {
        base.Draw (rect);
        TintColor = UIColor.Purple;
        AddSubview (iv);
    }
}
```

Para adicionar um `UINavigationBar` de subclasse a um `UINavigationController`, use o Construtor `UINavigationController` que usa o tipo de `UINavigationBar` e `UIToolbar`, conforme mostrado abaixo:

```csharp
navController = new UINavigationController (typeof(CustomNavBar), typeof(UIToolbar));
```

Usar essa `UINavigationBar` subclasse resulta na exibição da imagem, conforme mostrado na seguinte captura de tela:

 [![](images/03-navbar.png "Using this UINavigationBar subclass results in the image view being displayed as shown in this screenshot")](images/03-navbar.png#lightbox)

### <a name="interface-orientation"></a>Orientação da interface

Antes do iOS 6, os aplicativos podiam substituir `ShouldAutorotateToInterfaceOrientation`, retornando true para todas as orientações com suporte no controlador específico. Por exemplo, o código a seguir seria usado para dar suporte apenas a retrato:

```csharp
public override bool ShouldAutorotateToInterfaceOrientation (UIInterfaceOrientation toInterfaceOrientation)
    {
        return (toInterfaceOrientation == UIInterfaceOrientation.Portrait);
    }
```

No iOS 6 `ShouldAutorotateToInterfaceOrientation` é preterida.
Em vez disso, os aplicativos podem substituir `GetSupportedInterfaceOrientations` no controlador de exibição raiz, conforme mostrado abaixo:

```csharp
public override UIInterfaceOrientationMask GetSupportedInterfaceOrientations ()
    {
        return UIInterfaceOrientationMask.Portrait;
    }
```

No iPad, esse padrão será para todas as quatro orientações se `GetSupportedInterfaceOrientation` não for implementada. No iPhone e no iPod Touch, o padrão é todas as orientações, exceto `PortraitUpsideDown`.
