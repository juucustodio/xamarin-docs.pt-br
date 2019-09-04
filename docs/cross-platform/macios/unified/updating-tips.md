---
title: Dicas para atualizar o código para a API unificada
description: Este documento discute erros comuns e várias dicas úteis ao atualizar um aplicativo para usar o API Unificada do Xamarin.
ms.prod: xamarin
ms.assetid: 8DD34D21-342C-48E9-97AA-1B649DD8B61F
ms.date: 03/29/2017
author: asb3993
ms.author: amburns
ms.openlocfilehash: 844730d2ace717b951df2d80b2add6d1094fe997
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70226104"
---
# <a name="tips-for-updating-code-to-the-unified-api"></a>Dicas para atualizar o código para a API unificada

Ao atualizar as soluções anteriores do Xamarin para a API Unificada, os erros a seguir podem ser encontrados.

## <a name="nsinvalidargumentexception-could-not-find-storyboard-error"></a>NSInvalidArgumentException não pôde encontrar o erro de storyboard

Há um [bug](https://bugzilla.xamarin.com/show_bug.cgi?id=25569) na versão atual do Visual Studio para Mac que pode ocorrer depois de usar a ferramenta de migração automatizada para converter seu projeto para as APIs unificadas. Após a atualização, se você receber uma mensagem de erro no formato:

```console
Objective-C exception thrown. Name: NSInvalidArgumentException Reason: Could not find a storyboard named 'xxx' in bundle NSBundle...
```

Você pode fazer o seguinte para resolver esse problema, localize o seguinte arquivo de destino de compilação:

```console
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/2.1/Xamarin.iOS.Common.targets
```

Neste arquivo, você precisa encontrar a seguinte declaração de destino:

```xml
<Target Name="_CopyContentToBundle"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

E adicione o `DependsOnTargets="_CollectBundleResources"` atributo a ele. Assim:

```xml
<Target Name="_CopyContentToBundle"
        DependsOnTargets="_CollectBundleResources"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

Salve o arquivo, reinicialize Visual Studio para Mac e faça uma recompilação de & limpa do seu projeto. Uma correção para esse problema deve ser lançada pelo Xamarin em breve.

## <a name="useful-tips"></a>Dicas úteis

Depois de usar a ferramenta de migração, você ainda poderá obter alguns erros de compilador que exigem intervenção manual.
Algumas coisas que podem precisar ser corrigidas manualmente incluem:

- Comparar `enum`s pode exigir uma `(int)` conversão.

- `NSDictionary.IntValue`agora retorna um `nint`, há um `Int32Value` que pode ser usado em seu lugar.

- `nfloat`os `nint` tipos e não podem `const`ser marcados; `static readonly nint` é uma alternativa razoável.

- As coisas que costumavam estar diretamente no `MonoTouch.` namespace agora estão geralmente `ObjCRuntime.` no namespace (por exemplo: `MonoTouch.Constants.Version` é agora `ObjCRuntime.Constants.Version`).

- O código que serializa objetos pode ser interrompido ao tentar serializar `nint` e `nfloat` digitar. Certifique-se de verificar se o código de serialização funciona conforme o esperado após a migração.

- Às vezes, a ferramenta automatizada `#if #else` perde o código dentro das diretivas de compilador condicional. Nesse caso, você precisará fazer as correções manualmente (consulte os erros comuns abaixo).

- Os métodos exportados manualmente usando `[Export]` o não podem ser corrigidos automaticamente pela ferramenta de migração, por exemplo, neste código snippert você deve atualizar manualmente o tipo de retorno para: `nfloat`

  ```csharp
  [Export("tableView:heightForRowAtIndexPath:")]
  public nfloat HeightForRow(UITableView tableView, NSIndexPath indexPath)
  ```

- O API Unificada não fornece uma conversão implícita entre NSDate e DateTime .NET porque não é uma conversão sem perdas. Para evitar erros relacionados à `DateTimeKind.Unspecified` conversão do .NET `DateTime` em local ou UTC antes da conversão `NSDate`para.

- Os métodos de categoria Objective-C agora são gerados como métodos de extensão no API Unificada. Por exemplo, o código usado `UIView.DrawString` anteriormente agora faria referência `NSString.DrawString` no API unificada.

- O código usando classes AVFoundation `VideoSettings` com deve ser alterado para `WeakVideoSettings` usar a propriedade. Isso requer um `Dictionary`, que está disponível como uma propriedade nas classes de configurações, por exemplo:

  ```csharp
  vidrec.WeakVideoSettings = new AVVideoSettings() { ... }.Dictionary;
  ```

- O construtor `.ctor(IntPtr)` NSObject foi alterado de Public para Protected ([para impedir o uso impróprio](~/cross-platform/macios/unified/overview.md#NSObject_ctor)).

- `NSAction`foi [substituído](~/cross-platform/macios/unified/overview.md#NSAction) pelo .NET `Action`padrão. Alguns delegados simples (parâmetro único) também foram substituídos por `Action<T>`.

Por fim, consulte as [diferenças clássicas do v API unificada](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md) para pesquisar alterações em APIs em seu código. Pesquisar [esta página](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md) ajudará a encontrar APIs clássicas e para que elas foram atualizadas.

> [!NOTE]
> O `MonoTouch.Dialog` namespace permanece o mesmo após a migração. Se seu código usa **MonoTouch. caixa de diálogo** você deve continuar a usar esse namespace -não `MonoTouch.Dialog` Altere `Dialog`para!

## <a name="common-compiler-errors"></a>Erros comuns do compilador

Outros exemplos de erros comuns estão listados abaixo, juntamente com a solução:

**Erro CS0012: O tipo ' MonoTouch. UIKit. UIView ' é definido em um assembly que não é referenciado.**

Soluciona Isso geralmente significa que o projeto faz referência a um componente ou pacote NuGet que não foi criado com o API Unificada. Você deve excluir e adicionar novamente todos os componentes e pacotes NuGet. Se isso não corrigir o erro, talvez a biblioteca externa ainda não ofereça suporte à API Unificada.

**Error MT0034: Não é possível incluir ' MonoTouch. dll ' e ' Xamarin. iOS. dll ' no mesmo projeto Xamarin. iOS-' Xamarin. iOS. dll ' é referenciado explicitamente, enquanto ' MonoTouch. dll ' é referenciado por ' Xamarin. Mobile, Version = 0.6.3.0, Culture = neutral, PublicKeyToken = null '.**

Soluciona Exclua o componente que está causando esse erro e adicione-o novamente ao projeto.

**Erro CS0234: O nome de tipo ou namespace ' Foundation ' não existe no namespace ' MonoTouch '. Está faltando uma referência de assembly?**

Soluciona A ferramenta de migração automatizada no Visual Studio para Mac deve `MonoTouch.Foundation` atualizar todas `Foundation`as referências para, no entanto, em algumas instâncias, elas precisarão ser atualizadas manualmente. Erros semelhantes podem aparecer para os outros namespaces anteriormente contidos no `MonoTouch`, `UIKit`como.

**Erro CS0266: Não é possível converter implicitamente o tipo ' Double ' em ' System. float '**

Correção: altere o tipo e converta para `nfloat`. Esse erro também pode ocorrer para os outros tipos com equivalentes de 64 bits ( `nint`como,)

```csharp
nfloat scale = (nfloat)Math.Min(rect.Width, rect.Height);
```

**Erro CS0266: Não é possível converter implicitamente o tipo ' CoreGraphics. CGRect ' em ' System. Drawing. RectangleF '. Existe uma conversão explícita (você está sem um elenco?)**

Soluciona Altere as instâncias `RectangleF` para `CGRect`para `SizeF` , `CGSize`para e `PointF` para .`CGPoint` O namespace `using System.Drawing;` deve ser substituído por `using CoreGraphics;` (se ainda não estiver presente).

**erro CS1502: A melhor correspondência do método sobrecarregado para ' CoreGraphics. CGContext. SetLineDash (System. nfloat, System. nfloat []) ' tem alguns argumentos inválidos**

Soluciona Altere o tipo de `nfloat[]` matriz para e `Math.PI`converta explicitamente.

```csharp
grphc.SetLineDash (0, new nfloat[] { 0, 3 * (nfloat)Math.PI });
```

**Erro CS0115: ' WordsTableSource. RowsInSection (UIKit. UITableView, int) ' está marcado como uma substituição, mas nenhum método adequado foi encontrado para substituir**

Soluciona Altere o valor de retorno e os tipos `nint`de parâmetro para. Isso geralmente ocorre em substituições de método, como `UITableViewSource`as de `RowsInSection`, `NumberOfSections`incluindo `GetHeightForRow`, `TitleForHeader`, `GetViewForHeader`,, etc.

```csharp
public override nint RowsInSection (UITableView tableview, nint section) {
```

**Erro CS0508: `WordsTableSource.NumberOfSections(UIKit.UITableView)`: o tipo de retorno deve ser ' System. Nint ' para corresponder ao membro substituído`UIKit.UITableViewSource.NumberOfSections(UIKit.UITableView)`**

Soluciona Quando o tipo de retorno é alterado `nint`para, converta o valor `nint`de retorno para.

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return (nint)navItems.Count;
}
```

**Erro CS1061: O tipo ' CoreGraphics. CGPath ' não contém uma definição para ' AddElipseInRect '**

Soluciona Corrija a ortografia `AddEllipseInRect`para. Outras alterações de nome incluem:

- Altere ' Color. Black ' para `NSColor.Black`.
- Altere MapKit ' AddAnnotation ' para `AddAnnotations`.
- Altere AVFoundation ' DataUsingEncoding ' para `Encode`.
- Altere AVFoundation ' AVMetadataObject. TypeQRCode ' para `AVMetadataObjectType.QRCode`.
- Altere AVFoundation ' VideoSettings ' para `WeakVideoSettings`.
- Altere PopViewControllerAnimated para `PopViewController`.
- Altere CoreGraphics ' CGBitmapContext. SetRGBFillColor ' para `SetFillColor`.

**Erro CS0546: não é possível substituir porque ' MapKit. MKAnnotation. Coordinate ' não tem um acessador set substituível (CS0546)**

Ao criar uma anotação personalizada por meio de subclasses MKAnnotation, o campo de coordenadas não tem setter, apenas um getter.

[Correção](https://forums.xamarin.com/discussion/comment/109505/#Comment_109505):

- Adicionar um campo para acompanhar a coordenada
- retornar este campo no getter da propriedade Coordinate
- Substituir o método setcoordinate e definir seu campo
- Chamar setcoordinate em seu construtor com o parâmetro de coordenado passado

Ele deve ser semelhante ao seguinte:

```csharp
class BasicPinAnnotation : MKAnnotation
{
    private CLLocationCoordinate2D _coordinate;

    public override CLLocationCoordinate2D Coordinate
    {
        get
        {
            return _coordinate;
        }
    }

    public override void SetCoordinate(CLLocationCoordinate2D value)
    {
        _coordinate = value;
    }

    public BasicPinAnnotation (CLLocationCoordinate2D coordinate)
    {
        SetCoordinate(coordinate);
    }
}
```

## <a name="related-links"></a>Links relacionados

- [Atualizando aplicativos](~/cross-platform/macios/unified/updating-apps.md)
- [Atualizando aplicativos iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Atualizando aplicativos do Mac](~/cross-platform/macios/unified/updating-mac-apps.md)
- [Atualizando aplicativos do Xamarin. Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [Atualizando associações](~/cross-platform/macios/unified/update-binding.md)
- [Trabalhar com tipos nativos em aplicativos de plataforma cruzada](~/cross-platform/macios/native-types-cross-platform.md)
- [Diferenças clássicas do vs API Unificada](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
