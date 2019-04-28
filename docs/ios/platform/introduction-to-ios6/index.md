---
title: Introdução ao iOS 6
description: 'Este documento leva a guias que descrevem os recursos introduzidos no iOS 6. Exibições de coleção, PassKit, a estrutura Social, e alterações no StoreKit são discutidas.'
ms.prod: xamarin
ms.assetid: 242DA7E3-8FD8-5F20-285D-603259CA622D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
---

# <a name="introduction-to-ios-6"></a>Introdução ao iOS 6

_iOS 6 inclui uma variedade de novas tecnologias para o desenvolvimento de aplicativos, que traz o xamarin. IOS 6 para C# os desenvolvedores._

[ ![](images/ios6-large.jpg "O logotipo do iOS 6")](images/ios6-large.jpg#lightbox)

Com o iOS 6 e xamarin. IOS 6, os desenvolvedores agora têm uma grande quantidade de capacidade à sua disposição para criar aplicativos iOS, incluindo aqueles que destino iPhone 5.
Este documento lista alguns dos novos recursos mais interessantes que estão disponíveis e links para artigos de cada tópico. Além disso, ele aborda algumas alterações que serão importantes à medida que os desenvolvedores movem para o iOS 6 e a nova resolução de iPhone 5.


## <a name="introduction-to-collection-viewsiosuser-interfacecontrolsuicollectionviewmd"></a>[Introdução às exibições de coleção](~/ios/user-interface/controls/uicollectionview.md)

Exibições de coleção permitem que o conteúdo a ser exibido usando layouts arbitrários. Eles permitem facilmente criar layouts de grade para uso imediato, e dar suporte a layouts personalizados também. Para obter mais informações consulte, o [Introdução às exibições de coleção](~/ios/user-interface/controls/uicollectionview.md) [](~/ios/user-interface/controls/uicollectionview.md)guia.


## <a name="introduction-to-passkitiosplatformpasskitmd"></a>[Introdução ao PassKit](~/ios/platform/passkit.md)

PassKit framework permite que os aplicativos interagir com passos de digitais que são gerenciados no aplicativo Passbook. Para obter mais informações consulte, o [Introdução ao guia do Kit de passar](~/ios/platform/passkit.md).


##  <a name="introduction-to-eventkitiosplatformeventkitmd"></a>[Introdução ao Kit de eventos](~/ios/platform/eventkit.md)

O framework do Kit de eventos fornece uma maneira de acessar os calendários, eventos de calendário e dados de lembretes que armazena o banco de dados de calendário. Acesso ao calendário e calendários de eventos está disponível desde o iOS 4, mas o iOS 6 agora expõe o acesso aos dados de lembretes. Para obter mais informações, consulte o [eu](~/ios/platform/eventkit.md) [Introdução ao Kit de eventos](~/ios/platform/eventkit.md) guia.


##  <a name="introduction-to-the-social-frameworkiosplatformsocial-frameworkmd"></a>[Introdução ao Framework Social](~/ios/platform/social-framework.md)

A estrutura Social fornece uma API unificada para interagir com redes sociais, incluindo o Facebook e Twitter, bem como SinaWeibo para os usuários na China. Para obter mais informações consulte, o [Introdução ao Framework Social](~/ios/platform/social-framework.md) guia.


##  <a name="changes-to-storekitchanges-to-storekitmd"></a>[Alterações no StoreKit](changes-to-storekit.md)

Apple introduziu dois novos recursos do Store Kit: comprar e baixar iTunes ou o conteúdo da App Store de dentro de seu aplicativo e hospedando seus arquivos de conteúdo para compras no aplicativo!. Para obter mais informações consulte, o [muda para Store Kit](changes-to-storekit.md) guia.


## <a name="other-changes"></a>Outras alterações


### <a name="viewwillunload-and-viewdidunload-deprecated"></a>ViewWillUnload e ViewDidUnload preterido

O `ViewWillUnload` e `ViewDidUnload` métodos de `UIViewController` não são chamados no iOS 6. Nas versões anteriores do iOS, esses métodos podem ter sido usados por aplicativos para salvar o estado antes de um modo de exibição descarrega e código de limpeza, respectivamente.

Por exemplo, o Visual Studio para Mac criaria um método chamado `ReleaseDesignerOutlets`, conforme mostrado abaixo, que, em seguida, ser chamado de `ViewDidUnload`:

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
   
   
   
Para o código de limpeza, iOS 6 aplicativos devem usar `DidReceiveMemoryWarning`. No entanto, o código que chama `Dispose` devem ser usados com moderação e somente para objetos de memória intensiva conforme mostrado abaixo:

```csharp
if (myImageView != null){
    if (myImageView.Superview == null){
        myImageView.Dispose();
        myImageView = null;
    }
}
```

Mais uma vez, chamar `Dispose` como acima, raramente deve ser necessária. Em geral, a maioria dos aplicativos devem fazer é remover manipuladores de eventos.

No caso de salvar o estado, aplicativos podem executar isso em `ViewWillDisappear` e `ViewDidDisappear` em vez de `ViewWillUnload`.


### <a name="iphone-5-resolution"></a>iPhone 5 resolução

dispositivos iPhone 5 tem uma resolução de 640 x 1136. Aplicativos destinados a versões anteriores do iOS serão exibidos letterboxed quando executado em um iPhone 5, conforme mostrado abaixo:

 [![](images/01-letterboxed.png "Aplicativos destinados a versões anteriores do iOS serão exibidos letterboxed quando executado em um iPhone 5")](images/01-letterboxed.png#lightbox)

Para que o aplicativo seja exibido em tela inteira no iPhone 5, basta adicionar uma imagem chamada `Default-568h@2x.png` ter uma resolução de 640 x 1136. Captura de tela a seguir mostra o aplicativo em execução depois que essa imagem foi incluída:

 [![](images/02-fullscreen.png "Esta captura de tela mostra o aplicativo em execução depois que essa imagem foi incluída")](images/02-fullscreen.png#lightbox)

### <a name="subclassing-uinavigationbar"></a>Criando subclasses UINavigationBar

No iOS 6 `UINavigationBar` pode ser uma subclasse. Isso permite que o controle adicional da aparência e funcionalidade do `UINavigationBar`. Por exemplo, os aplicativos podem subclasse para adicionar as subexibições, animar essas exibições e modificar os limites do `UINavigationBar`.

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

Para adicionar uma subclasse `UINavigationBar` para um `UINavigationController`, use o `UINavigationController` construtor que usa o tipo dos `UINavigationBar` e `UIToolbar`, conforme mostrado abaixo:

```csharp
navController = new UINavigationController (typeof(CustomNavBar), typeof(UIToolbar));
```

Usando este `UINavigationBar` subclasse resulta na exibição de imagem que está sendo exibida conforme mostrado na seguinte captura de tela:

 [![](images/03-navbar.png "Usando essa resulta da subclasse UINavigationBar na exibição de imagem que está sendo exibida conforme mostrado nesta captura de tela")](images/03-navbar.png#lightbox)

### <a name="interface-orientation"></a>Orientação de interface

Antes do iOS 6 aplicativos poderia substituir `ShouldAutorotateToInterfaceOrientation`, retornando true para qualquer orientações controlador específico tem suporte. Por exemplo, o código a seguir poderia ser usado para dar suporte a apenas Retrato:

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

No iPad, este assumirá como padrão para todas as quatro orientações se `GetSupportedInterfaceOrientation` não está implementado. No iPhone e iPod Touch, o padrão é todas as orientações, exceto `PortraitUpsideDown`.
