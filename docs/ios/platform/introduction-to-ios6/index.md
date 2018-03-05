---
title: "Introdução ao iOS 6"
description: iOS 6 inclui uma variedade de novas tecnologias para desenvolver aplicativos que xamarin 6 oferece aos desenvolvedores do c#.
ms.topic: article
ms.prod: xamarin
ms.assetid: 242DA7E3-8FD8-5F20-285D-603259CA622D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 0d08b2ff5131996984dbe41862ad1d6aac33b3d0
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-ios-6"></a>Introdução ao iOS 6

_iOS 6 inclui uma variedade de novas tecnologias para desenvolver aplicativos que xamarin 6 oferece aos desenvolvedores do c#._

[ ![](images/ios6-large.jpg "O logotipo de iOS 6")](images/ios6-large.jpg)

Com iOS 6 e xamarin 6, os desenvolvedores agora têm uma grande quantidade de recursos à sua disposição para criar aplicativos do iOS, incluindo aqueles que iPhone 5 do destino.
Este documento lista alguns dos novos recursos mais interessantes que estão disponíveis e links para artigos para cada tópico. Além disso toca em algumas alterações que serão importantes que os desenvolvedores se movem para iOS 6 e a nova resolução do iPhone 5.


## <a name="introduction-to-collection-viewsiosuser-interfacecontrolsuicollectionviewmd"></a>[Introdução às exibições de coleção](~/ios/user-interface/controls/uicollectionview.md)

Exibições de coleção permitem que o conteúdo a ser exibido usando layouts arbitrários. Eles permitem criar facilmente layouts de grade fora da caixa, e dar suporte a layouts personalizados também. Para obter mais informações consulte, o [Introdução às exibições de coleção](~/ios/user-interface/controls/uicollectionview.md) [](~/ios/user-interface/controls/uicollectionview.md)guia.


## <a name="introduction-to-pass-kitiosplatformpasskitmd"></a>[Introdução ao passar Kit](~/ios/platform/passkit.md)

A estrutura de passar Kit permite que os aplicativos a interagir com passa digitais que é gerenciadas no aplicativo de senha. Para obter mais informações consulte, o [Introdução ao guia de passar Kit](~/ios/platform/passkit.md).


##  <a name="introduction-to-event-kitiosplatformeventkitmd"></a>[Introdução ao Kit de evento](~/ios/platform/eventkit.md)

A estrutura EventKit fornece uma maneira de acessar os calendários, calendário de eventos e dados lembretes que armazena o banco de dados do calendário. Acesso a calendários e calendário de eventos está disponível desde o iOS 4, mas iOS 6 agora expõe o acesso aos dados de lembretes. Para obter mais informações, consulte o [,](~/ios/platform/eventkit.md) [ntroduction para EventKit](~/ios/platform/eventkit.md) guia.


##  <a name="introduction-to-the-social-frameworkiosplatformsocial-frameworkmd"></a>[Introdução ao Framework Social](~/ios/platform/social-framework.md)

A estrutura Social fornece uma API unificada para interagir com redes sociais, incluindo o Facebook e Twitter, bem como SinaWeibo para os usuários na China. Para obter mais informações consulte, o [Introdução ao Framework Social](~/ios/platform/social-framework.md) guia.


##  <a name="changes-to-store-kitchanges-to-storekitmd"></a>[Alterações no armazenamento de Kit](changes-to-storekit.md)

Apple apresentou dois novos recursos no Kit de armazenamento: comprar e baixar iTunes ou conteúdo de loja de aplicativos a partir do seu aplicativo e hospeda os arquivos de conteúdo para compras no aplicativo!. Para obter mais informações consulte, o [alterações Store Kit](changes-to-storekit.md) guia.


## <a name="other-changes"></a>Outras alterações


### <a name="viewwillunload-and-viewdidunload-deprecated"></a>ViewWillUnload e ViewDidUnload preterido

O `ViewWillUnload` e `ViewDidUnload` métodos de `UIViewController` não são chamados em iOS 6. Nas versões anteriores do iOS, esses métodos podem ter sido usados por aplicativos para salvar o estado anterior descarrega um modo de exibição e o código de limpeza respectivamente.

Por exemplo, o Visual Studio para Mac criaria um método chamado `ReleaseDesignerOutlets`, conforme mostrado abaixo, que deve ser chamado de `ViewDidUnload`:

```csharp
void ReleaseDesignerOutlets ()
{
    if (myOutlet != null) {
        myOutlet.Dispose ();
        myOutlet = null;
    }
}
```

No entanto, no iOS 6, ele não é mais necessário chamar `ReleaseDesignerOutlets`.   
   
   
   
Para o código de limpeza, devem usar aplicativos iOS 6 `DidReceiveMemoryWarning`. No entanto, código que chama `Dispose` deve ser usado com moderação e somente de memória intensiva objetos conforme mostrado abaixo:

```csharp
if (myImageView != null){
    if (myImageView.Superview == null){
        myImageView.Dispose();
        myImageView = null;
    }
}
```

Novamente, chamando `Dispose` como acima raramente será necessária. Em geral, a maioria dos aplicativos devem fazer é remover manipuladores de eventos.

No caso de salvar o estado, aplicativos podem executar isso em `ViewWillDisappear` e `ViewDidDisappear` em vez de `ViewWillUnload`.


### <a name="iphone-5-resolution"></a>iPhone 5 resolução

dispositivos iPhone 5 tem uma resolução de 640 x 1136. Aplicativos que destinados a versões anteriores do iOS aparecerão letterboxed quando executado em um iPhone 5, conforme mostrado abaixo:

 [ ![](images/01-letterboxed.png "Aplicativos que destinados a versões anteriores do iOS aparecerão letterboxed quando executado em um iPhone 5")](images/01-letterboxed.png)

Para que o aplicativo seja exibido em tela inteira no iPhone 5, basta adicionar uma imagem chamada `Default-568h@2x.png` com uma resolução de 640 x 1136. Captura de tela a seguir mostra o aplicativo em execução depois que esta imagem foi incluída:

 [ ![](images/02-fullscreen.png "Esta captura de tela mostra o aplicativo em execução depois que esta imagem foi incluída")](images/02-fullscreen.png)

### <a name="subclassing-uinavigationbar"></a>Subclasses UINavigationBar

No iOS 6 `UINavigationBar` pode ser uma subclasse. Isso permite o controle adicional de aparência do `UINavigationBar`. Por exemplo, os aplicativos podem subclasse para adicionar sub-visualizações, animar esses modos de exibição e modificar os limites do `UINavigationBar`.

O código a seguir mostra um exemplo de uma subclasse `UINavigationBar` que adiciona um `UIImageView`:

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

Para adicionar uma subclasse `UINavigationBar` para um `UINavigationController`, use o `UINavigationController` construtor que usa o tipo do `UINavigationBar` e `UIToolbar`, conforme mostrado abaixo:

```csharp
navController = new UINavigationController (typeof(CustomNavBar), typeof(UIToolbar));
```

Usando esse `UINavigationBar` subclasse resulta na exibição da imagem que está sendo exibida conforme mostrado na seguinte captura de tela:

 [ ![](images/03-navbar.png "Usando essa resultados de subclasse de UINavigationBar na exibição da imagem que está sendo exibida conforme mostrado nesta captura de tela")](images/03-navbar.png)

### <a name="interface-orientation"></a>Orientação de interface

Antes de iOS 6 aplicativos podem substituir `ShouldAutorotateToInterfaceOrientation`, retornando true para qualquer orientações controlador específico com suporte. Por exemplo, o código a seguir deve ser usado para dar suporte a apenas Retrato:

```csharp
public override bool ShouldAutorotateToInterfaceOrientation (UIInterfaceOrientation toInterfaceOrientation)
    {
        return (toInterfaceOrientation == UIInterfaceOrientation.Portrait);
    }
```

No iOS 6 `ShouldAutorotateToInterfaceOrientation` foi preterido.
Em vez disso, os aplicativos podem substituir `GetSupportedInterfaceOrientations` no controlador de exibição de raiz, conforme mostrado abaixo:

```csharp
public override UIInterfaceOrientationMask GetSupportedInterfaceOrientations ()
    {
        return UIInterfaceOrientationMask.Portrait;
    }
```

No iPad, o padrão é todas as quatro orientações se `GetSupportedInterfaceOrientation` não está implementado. No iPhone e iPod Touch, o padrão é todas as orientações exceto `PortraitUpsideDown`.