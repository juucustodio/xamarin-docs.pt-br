---
title: Dicas para atualizar o código para a API unificada
description: Este documento aborda os erros comuns e várias dicas úteis ao atualizar um aplicativo para usar a API de unificada do Xamarin.
ms.prod: xamarin
ms.assetid: 8DD34D21-342C-48E9-97AA-1B649DD8B61F
ms.date: 03/29/2017
author: asb3993
ms.author: amburns
ms.openlocfilehash: a5083e1d31377caece1b8fb4faf33b6e3ff88202
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61211814"
---
# <a name="tips-for-updating-code-to-the-unified-api"></a>Dicas para atualizar o código para a API unificada

Ao atualizar soluções antigas do Xamarin para a API unificada, os erros a seguir podem ser encontrados.

## <a name="nsinvalidargumentexception-could-not-find-storyboard-error"></a>Não foi possível encontrar NSInvalidArgumentException storyboard erro

Há um [bug](https://bugzilla.xamarin.com/show_bug.cgi?id=25569) na versão atual do Visual Studio para Mac que pode ocorrer depois de usar a ferramenta de migração automatizada para converter o projeto para as APIs de Unificação. Após a atualização, se você receber uma mensagem de erro no formulário:

```console
Objective-C exception thrown. Name: NSInvalidArgumentException Reason: Could not find a storyboard named 'xxx' in bundle NSBundle...
```

Você pode fazer o seguinte para resolver esse problema, localize o seguinte arquivo de destino de compilação:

```console
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/2.1/Xamarin.iOS.Common.targets
```

Nesse arquivo, você precisará encontrar a seguinte declaração de destino:

```xml
<Target Name="_CopyContentToBundle"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

E adicione o `DependsOnTargets="_CollectBundleResources"` de atributo a ele. Assim:

```xml
<Target Name="_CopyContentToBundle"
        DependsOnTargets="_CollectBundleResources"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

Salve o arquivo, reinicie o Visual Studio para Mac e fazer uma limpeza e recriação de seu projeto. Para corrigir esse problema deve ser lançada em breve pelo Xamarin.

## <a name="useful-tips"></a>Dicas úteis

Depois de usar a ferramenta de migração, você ainda poderá obter alguns erros de compilador necessidade de intervenção manual.
Algumas coisas que talvez precisem ser corrigidos manualmente incluem:

* Comparando `enum`s pode exigir um `(int)` cast.

* `NSDictionary.IntValue` agora retorna um `nint`, há um `Int32Value` que pode ser usado em vez disso.

* `nfloat` e `nint` tipos não podem ser marcados `const`;   `static readonly nint` é uma alternativa razoável.

* Coisas que costumavam ser diretamente na `MonoTouch.` namespace agora estão geralmente na `ObjCRuntime.` namespace (por exemplo,: `MonoTouch.Constants.Version` agora está `ObjCRuntime.Constants.Version`).

* Código que serializa objetos falhar ao tentar serializar `nint` e `nfloat` tipos. Certifique-se de verificar que seu código de serialização funciona conforme o esperado após a migração.

* Às vezes, a ferramenta automatizada erros de código dentro de `#if #else` diretivas de compilador condicional. Nesse caso, você precisará fazer as correções manualmente (consulte os erros comuns abaixo).

* Métodos exportados manualmente usando `[Export]` não pode ser corrigido automaticamente pela ferramenta de migração, por exemplo, no snippert código que você deve atualizar manualmente o tipo de retorno para `nfloat`:

    ```csharp
    [Export("tableView:heightForRowAtIndexPath:")]
    public nfloat HeightForRow(UITableView tableView, NSIndexPath indexPath)
    ```

 * A API unificada do não fornece uma conversão implícita entre NSDate e .NET DateTime porque ele não é uma conversão sem perdas. Para evitar erros relacionados à `DateTimeKind.Unspecified` converter o .NET `DateTime` será local ou UTC antes de realizar a conversão em `NSDate`.

 * Métodos de categoria de Objective-C agora são gerados como métodos de extensão na API unificada. Por exemplo, o código que usava `UIView.DrawString` agora fará referência `NSString.DrawString` na API unificada.

 * O código usando as classes de AVFoundation com `VideoSettings` deve ser alterado para usar o `WeakVideoSettings` propriedade. Isso requer um `Dictionary`, que está disponível como uma propriedade nas classes de configurações, por exemplo:

    ```csharp
    vidrec.WeakVideoSettings = new AVVideoSettings() { ... }.Dictionary;
    ```

 * O NSObject `.ctor(IntPtr)` construtor tiver sido alterada de pública para protegido ([para evitar o uso impróprio](~/cross-platform/macios/unified/overview.md#NSObject_ctor)).

 * `NSAction` tem sido [substituído](~/cross-platform/macios/unified/overview.md#NSAction) com o .NET starndard `Action`. Alguns delegados simples (único parâmetro) também foram substituídos por `Action<T>`.

Por fim, consulte o [diferenças de API unificada do clássico v](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) para pesquisar alterações às APIs em seu código. Pesquisando [nesta página](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) o ajudará a localizar APIs clássicas e o que eles já foi atualizados para.

**Observação:** o `MonoTouch.Dialog` namespace permanece o mesmo após a migração. Se seu código usa **MonoTouch** deverá continuar a usar esse namespace – fazer *não* alterar `MonoTouch.Dialog` para `Dialog`!

## <a name="common-compiler-errors"></a>Erros do compilador comum

Outros exemplos de erros comuns estão listados abaixo, junto com a solução:

**Erro CS0012: O tipo 'MonoTouch.UIKit.UIView' é definido em um assembly que não é referenciado.**

Correção: Isso geralmente significa que o projeto referencia um componente ou pacote do NuGet que não foi criado com a API unificada. Você deve excluir e adicionar novamente todos os componentes e NuGet pacotes. Se isso não corrigir o erro, a biblioteca externa pode ainda não suporta a API unificada.

**Error MT0034: Não é possível incluir 'MonoTouch. dll' e 'Xamarin' no mesmo projeto xamarin. IOS - 'Xamarin' é referenciada explicitamente, enquanto 'MonoTouch. dll' é referenciado por ' Mobile, versão = 0.6.3.0, Culture = neutral, PublicKeyToken = null'.**

Correção: Excluir o componente que está causando o erro e adicione novamente ao projeto.

**Erro CS0234: O nome do namespace ou tipo 'Base' não existe no namespace 'MonoTouch'. Está faltando uma referência de assembly?**

Correção: A ferramenta de migração automatizada no Visual Studio para Mac *devem* Atualizar tudo `MonoTouch.Foundation` faz referência às `Foundation`, no entanto, em alguns casos eles precisam ser atualizadas manualmente. Erros semelhantes podem aparecer para outros namespaces contidos anteriormente no `MonoTouch`, tais como `UIKit`.

**Erro CS0266: Não é possível converter implicitamente o tipo 'double' para 'System.float'**

Correção: altere o tipo e convertido em `nfloat`. Esse erro também pode ocorrer para outros tipos com equivalentes de 64 bits (como `nint`,)

```csharp
nfloat scale = (nfloat)Math.Min(rect.Width, rect.Height);
```

**Erro CS0266: Não é possível converter implicitamente o tipo 'CoreGraphics.CGRect' para 'System.Drawing.RectangleF'. Existe uma conversão explícita (uma conversão estão faltando?)**

Correção: Alterar instâncias a serem `RectangleF` à `CGRect`, `SizeF` à `CGSize`, e `PointF` para `CGPoint`. O namespace `using System.Drawing;` deve ser substituído pelo `using CoreGraphics;` (se ainda não estiver presente).

**Erro CS1502: O melhor sobrecarregado correspondência de método para ' CoreGraphics.CGContext.SetLineDash (System.nfloat, System.nfloat[])' tem alguns argumentos inválidos**

Correção: Alterar o tipo de matriz para `nfloat[]` e converta explicitamente `Math.PI`.

```csharp
grphc.SetLineDash (0, new nfloat[] { 0, 3 * (nfloat)Math.PI });
```

**Erro CS0115: 'WordsTableSource.RowsInSection (UIKit.UITableView, int)' está marcado como uma substituição, mas nenhum método adequado foi encontrado para substituição**

Correção: Alterar os tipos de parâmetro e valor de retorno para `nint`. Isso geralmente ocorre em substituições de método, como aqueles em `UITableViewSource`, incluindo `RowsInSection`, `NumberOfSections`, `GetHeightForRow`, `TitleForHeader`, `GetViewForHeader`, etc.

```csharp
public override nint RowsInSection (UITableView tableview, nint section) {
```

**Erro CS0508: `WordsTableSource.NumberOfSections(UIKit.UITableView)': return type must be 'System.nint' to match overridden member `UIKit.UITableViewSource.NumberOfSections(UIKit.UITableView)'**

Correção: Quando o tipo de retorno é alterado para `nint`, converta o valor de retorno para `nint`.

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return (nint)navItems.Count;
}
```

**Erro CS1061: O tipo 'CoreGraphics.CGPath' não contém uma definição para 'AddElipseInRect'**

Correção: Corrija a grafia para `AddEllipseInRect`. Outras alterações de nome incluem:

* Alterar 'Color.Black' para `NSColor.Black`.
* Alterar MapKit 'AddAnnotation' para `AddAnnotations`.
* Alterar AVFoundation 'DataUsingEncoding' para `Encode`.
* Alterar AVFoundation 'AVMetadataObject.TypeQRCode' para `AVMetadataObjectType.QRCode`.
* Alterar AVFoundation 'VideoSettings' para `WeakVideoSettings`.
* Alterar PopViewControllerAnimated para `PopViewController`.
* Alterar CoreGraphics 'CGBitmapContext.SetRGBFillColor' para `SetFillColor`.

**Erro CS0546: não é possível substituir porque 'MapKit.MKAnnotation.Coordinate' não tem um acessador set substituível (CS0546)**

Durante a criação de uma anotação personalizada através de subclasses MKAnnotation o campo de coordenadas não tem nenhum setter, apenas um getter.

[Corrigir](https://forums.xamarin.com/discussion/comment/109505/#Comment_109505):

* Adicionar um campo para controlar a coordenada
* Esse campo de retorno getter da propriedade coordenada
* Substitua o método SetCoordinate e defina o campo
* Chamar SetCoordinate em seu construtor com o parâmetro de coordenadas passado

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

- [Atualização de aplicativos](~/cross-platform/macios/unified/updating-apps.md)
- [Atualizando aplicativos do iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Atualizando aplicativos do Mac](~/cross-platform/macios/unified/updating-mac-apps.md)
- [Atualizando aplicativos xamarin. Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [Atualizando associações](~/cross-platform/macios/unified/update-binding.md)
- [Trabalhar com tipos nativos em aplicativos de plataforma cruzada](~/cross-platform/macios/native-types-cross-platform.md)
- [Diferenças de API unificada do vs clássico](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
