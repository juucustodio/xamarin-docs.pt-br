---
title: Dicas para atualizar o código para a API unificada
ms.prod: xamarin
ms.assetid: 8DD34D21-342C-48E9-97AA-1B649DD8B61F
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: b23a84c990eb2418e94b414cc9750b3060c572ad
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="tips-for-updating-code-to-the-unified-api"></a>Dicas para atualizar o código para a API unificada

Usando a ferramenta de migração automatizada do Visual Studio para Mac converterá iOS e projetos de Mac para referenciar o API unificada (Xamarin.iOS.dll ou Xamarin.Mac.dll) e fazer muita as alterações de código necessárias para você (consulte o [versão Xamarin Studio Notas de seção 'Clássico para a ferramenta de migração de código API unificada'](http://developer.xamarin.com/releases/studio/xamarin.studio_5.7/xamarin.studio_5.7/) para obter detalhes específicos).

## <a name="nsinvalidargumentexception-could-not-find-storyboard-error"></a>Não foi possível encontrar NSInvalidArgumentException storyboard erro

Há um [bug](https://bugzilla.xamarin.com/show_bug.cgi?id=25569) na versão atual do Visual Studio para Mac que pode ocorrer depois de usar a ferramenta de migração automatizada para converter seu projeto para as APIs unificado. Após a atualização, se você receber uma mensagem de erro no formato:

```console
Objective-C exception thrown. Name: NSInvalidArgumentException Reason: Could not find a storyboard named 'xxx' in bundle NSBundle...

```

Você pode fazer o seguinte para resolver esse problema, localize o arquivo de destino de compilação a seguir:

```console
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/2.1/Xamarin.iOS.Common.targets
```

Nesse arquivo, você precisa localizar a declaração de destino a seguir:

```xml
<Target Name="_CopyContentToBundle"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

E adicione o `DependsOnTargets="_CollectBundleResources"` de atributos a ele. Assim:

```xml
<Target Name="_CopyContentToBundle"
        DependsOnTargets="_CollectBundleResources"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

Salve o arquivo, reinicie o Visual Studio para Mac e fazer uma limpeza e recriação de seu projeto. Para corrigir esse problema deve ser liberada ao Xamarin em breve.

## <a name="useful-tips"></a>Dicas úteis

Depois de usar a ferramenta de migração, você ainda pode receber alguns erros de compilador necessidade de intervenção manual.
Algumas coisas que talvez precisem ser corrigidos manualmente incluem:

* Comparando `enum`s podem exigir um `(int)` cast.

* `NSDictionary.IntValue` agora retorna um `nint`, há um `Int32Value` que pode ser usado em vez disso.

* `nfloat` e `nint` tipos não podem ser marcados como `const`;   `static readonly nint` é uma alternativa razoável.

* Coisas que costumavam ser diretamente no `MonoTouch.` namespace agora são geralmente no `ObjCRuntime.` namespace (por exemplo: `MonoTouch.Constants.Version` agora é `ObjCRuntime.Constants.Version`).

* Código que serializa objetos falhar ao tentar serializar `nint` e `nfloat` tipos. Certifique-se de verificar que seu código de serialização funciona conforme o esperado após a migração.

* Às vezes, a ferramenta automatizada erros de código dentro de `#if #else` diretivas de compilador condicional. Nesse caso, será necessário fazer as correções manualmente (consulte os erros comuns abaixo).

* Métodos manualmente exportados usando `[Export]` não pode ser corrigido automaticamente com a ferramenta de migração, por exemplo, no snippert código que você deve atualizar manualmente o tipo de retorno para `nfloat`:

    ```csharp
    [Export("tableView:heightForRowAtIndexPath:")]
    public nfloat HeightForRow(UITableView tableView, NSIndexPath indexPath)
    ```

 * A API unificada não oferece uma conversão implícita entre NSDate e DateTime .NET porque ele não é uma conversão sem perdas. Para evitar erros relacionados a `DateTimeKind.Unspecified` converter .NET `DateTime` local ou UTC antes da conversão para `NSDate`.

 * Métodos de categoria Objective-C agora são gerados como métodos de extensão na API unificada. Por exemplo, o código que usava `UIView.DrawString` agora seria referência `NSString.DrawString` na API unificada.

 * Codificar usando classes AVFoundation com `VideoSettings` devem mudar para usar o `WeakVideoSettings` propriedade. Isso requer um `Dictionary`, que está disponível como uma propriedade nas classes de configurações, por exemplo:

    ```csharp
    vidrec.WeakVideoSettings = new AVVideoSettings() { ... }.Dictionary;
    ```

 * O NSObject `.ctor(IntPtr)` foi alterada construtor de público para protegido ([para evitar o uso impróprio](~/cross-platform/macios/unified/index.md#NSObject_ctor)).

 * `NSAction` foi [substituído](~/cross-platform/macios/unified/index.md#NSAction) com o .NET starndard `Action`. Alguns delegados simples (parâmetro único) também foram substituídos por `Action<T>`.

Por fim, consulte o [as diferenças de API de unificado clássico v](http://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) para pesquisar alterações às APIs no seu código. Pesquisando [essa página](http://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) ajudará a localizar APIs clássico e o que eles já foram atualizados para.

**Observação:** o `MonoTouch.Dialog` namespace permanece o mesmo após a migração. Se seu código usa **MonoTouch.Dialog** você deve continuar a usar esse namespace - *não* alterar `MonoTouch.Dialog` para `Dialog`!

## <a name="common-compiler-errors"></a>Erros do compilador comum

Outros exemplos de erros comuns estão listados abaixo, junto com a solução:

**Erro CS0012: O tipo 'MonoTouch.UIKit.UIView' está definido em um assembly que não é referenciado.**

Correção: Isso geralmente significa que o projeto referencia um componente ou o pacote NuGet que não foram criado com a API unificada. Você deve excluir e adicionar novamente todos os componentes e NuGet pacotes. Se isso não corrigir o erro, a biblioteca externa pode ainda não suporta a API unificada.

**Erro MT0034: Não é possível incluir 'monotouch.dll' e 'Xamarin.iOS.dll' no mesmo projeto xamarin - 'Xamarin.iOS.dll' é referenciado explicitamente, enquanto a 'monotouch.dll' é referenciado por ' Xamarin.Mobile, Version = 0.6.3.0, Culture = neutral, PublicKeyToken = null'.**

Correção: Excluir o componente que está causando o erro e adicione novamente ao projeto.

**Erro CS0234: O nome do namespace ou tipo 'Base' não existe no namespace 'MonoTouch'. Está faltando uma referência de assembly?**

Correção: A ferramenta de migração automatizada no Visual Studio para Mac *devem* Atualizar tudo `MonoTouch.Foundation` referências a `Foundation`, no entanto, em alguns casos eles precisam ser atualizadas manualmente. Erros semelhantes podem ser exibidos para os outros namespaces contidos anteriormente no `MonoTouch`, como `UIKit`.

**Erro CS0266: Não é possível converter implicitamente o tipo 'double' em 'System.float'**

Correção: alterar o tipo e convertido em `nfloat`. Esse erro também poderá ocorrer para os outros tipos de equivalentes de 64 bits (como `nint`,)

```csharp
nfloat scale = (nfloat)Math.Min(rect.Width, rect.Height);
```

**Erro CS0266: Não é possível converter implicitamente o tipo 'CoreGraphics.CGRect' para 'System.Drawing.RectangleF'. Existe uma conversão explícita (há uma conversão?)**

Correção: Alterar instâncias `RectangleF` para `CGRect`, `SizeF` para `CGSize`, e `PointF` para `CGPoint`. O namespace `using System.Drawing;` deve ser substituído pelo `using CoreGraphics;` (se ainda não estiver presente).

**Erro CS1502: A melhor correspondência de método para sobrecarregado ' CoreGraphics.CGContext.SetLineDash (System.nfloat, System.nfloat[])' tem alguns argumentos inválidos**

Correção: Alterar o tipo de matriz para `nfloat[]` e explicitamente conversão `Math.PI`.

```csharp
grphc.SetLineDash (0, new nfloat[] { 0, 3 * (nfloat)Math.PI });
```

**Erro CS0115: 'WordsTableSource.RowsInSection (UIKit.UITableView, int)' está marcado como uma substituição, mas nenhum método adequado encontrado para substituição**

Correção: Alterar os tipos de valor e o parâmetro de retorno para `nint`. Isso geralmente ocorre nas substituições de método, como aqueles em `UITableViewSource`, incluindo `RowsInSection`, `NumberOfSections`, `GetHeightForRow`, `TitleForHeader`, `GetViewForHeader`, etc.

```csharp
public override nint RowsInSection (UITableView tableview, nint section) {
```

**Erro CS0508: `WordsTableSource.NumberOfSections(UIKit.UITableView)': return type must be 'System.nint' to match overridden member `UIKit.UITableViewSource.NumberOfSections(UIKit.UITableView)'**

Correção: Quando o tipo de retorno é alterado para `nint`, converter o valor de retorno para `nint`.

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return (nint)navItems.Count;
}
```

**Erro CS1061: O tipo 'CoreGraphics.CGPath' não contém uma definição para 'AddElipseInRect'**

Correção: A correção ortográfica para `AddEllipseInRect`. Outras alterações de nome incluem:

* Altere 'Color.Black' para `NSColor.Black`.
* Alterar MapKit 'AddAnnotation' para `AddAnnotations`.
* Alterar AVFoundation 'DataUsingEncoding' para `Encode`.
* Alterar AVFoundation 'AVMetadataObject.TypeQRCode' para `AVMetadataObjectType.QRCode`.
* Alterar AVFoundation 'VideoSettings' para `WeakVideoSettings`.
* Alterar PopViewControllerAnimated para `PopViewController`.
* Alterar CoreGraphics 'CGBitmapContext.SetRGBFillColor' para `SetFillColor`.

**Erro CS0546: não é possível substituir porque 'MapKit.MKAnnotation.Coordinate' não tem um acessador set substituível (CS0546)**

Durante a criação de uma anotação personalizada por subclasses MKAnnotation o campo de coordenada não possui método setter, somente um getter.

[Fix](https://forums.xamarin.com/discussion/comment/109505/#Comment_109505):

* Adicionar um campo para controlar a coordenada
* retornar a este campo no getter da propriedade de coordenada
* Substitua o método SetCoordinate e defina o campo
* Chamar SetCoordinate em seu construtor com o parâmetro de coordenada passado.

Ela deve ser semelhante ao seguinte:

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

- [Atualizar aplicativos](~/cross-platform/macios/unified/updating-apps.md)
- [Atualizando aplicativos do iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Atualizando aplicativos do Mac](~/cross-platform/macios/unified/updating-mac-apps.md)
- [Atualizar aplicativos xamarin. Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [Atualizando associações](~/cross-platform/macios/unified/update-binding.md)
- [Trabalhar com tipos nativos em aplicativos de plataforma cruzada](~/cross-platform/macios/native-types-cross-platform.md)
- [Diferenças de API unificada do vs clássico](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
