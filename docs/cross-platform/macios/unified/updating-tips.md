---
title: Dicas para atualizar o código para a API unificada
description: Este documento discute erros comuns e várias dicas úteis ao atualizar um aplicativo para usar o API Unificada do Xamarin.
ms.prod: xamarin
ms.assetid: 8DD34D21-342C-48E9-97AA-1B649DD8B61F
ms.date: 03/29/2017
author: davidortinau
ms.author: daortin
ms.openlocfilehash: ee207ffc83f887e9c86c650b6f93fc2ff9f5b69c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014987"
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

E adicione o atributo `DependsOnTargets="_CollectBundleResources"` a ele. Assim:

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

- A comparação de `enum`s pode exigir uma conversão `(int)`.

- `NSDictionary.IntValue` agora retorna um `nint`, há um `Int32Value` que pode ser usado em vez disso.

- os tipos `nfloat` e `nint` não podem ser marcados como `const`; `static readonly nint` é uma alternativa razoável.

- As coisas que costumavam estar diretamente no namespace de `MonoTouch.` agora estão geralmente no namespace `ObjCRuntime.` (por exemplo: `MonoTouch.Constants.Version` agora é `ObjCRuntime.Constants.Version`).

- O código que serializa objetos pode ser interrompido ao tentar serializar `nint` e `nfloat` tipos. Certifique-se de verificar se o código de serialização funciona conforme o esperado após a migração.

- Às vezes, a ferramenta automatizada perde o código dentro `#if #else` Diretivas condicionais do compilador. Nesse caso, você precisará fazer as correções manualmente (consulte os erros comuns abaixo).

- Os métodos exportados manualmente usando `[Export]` podem não ser corrigidos automaticamente pela ferramenta de migração, por exemplo, neste código snippert você deve atualizar manualmente o tipo de retorno para `nfloat`:

  ```csharp
  [Export("tableView:heightForRowAtIndexPath:")]
  public nfloat HeightForRow(UITableView tableView, NSIndexPath indexPath)
  ```

- O API Unificada não fornece uma conversão implícita entre NSDate e DateTime .NET porque não é uma conversão sem perdas. Para evitar erros relacionados a `DateTimeKind.Unspecified` converter o .NET `DateTime` em local ou UTC antes da conversão para `NSDate`.

- Os métodos de categoria Objective-C agora são gerados como métodos de extensão no API Unificada. Por exemplo, o código usado anteriormente `UIView.DrawString` referenciaria `NSString.DrawString` no API Unificada.

- O código que usa classes AVFoundation com `VideoSettings` deve ser alterado para usar a propriedade `WeakVideoSettings`. Isso requer uma `Dictionary`, que está disponível como uma propriedade nas classes de configurações, por exemplo:

  ```csharp
  vidrec.WeakVideoSettings = new AVVideoSettings() { ... }.Dictionary;
  ```

- O construtor de `.ctor(IntPtr)` NSObject foi alterado de Public para Protected ([para impedir o uso impróprio](~/cross-platform/macios/unified/overview.md#NSObject_ctor)).

- `NSAction` foi [substituído](~/cross-platform/macios/unified/overview.md#NSAction) pelo `Action`.NET padrão. Alguns delegados simples (parâmetro único) também foram substituídos por `Action<T>`.

Por fim, consulte as [diferenças clássicas do v API unificada](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md) para pesquisar alterações em APIs em seu código. Pesquisar [esta página](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md) ajudará a encontrar APIs clássicas e para que elas foram atualizadas.

> [!NOTE]
> O namespace `MonoTouch.Dialog` permanece o mesmo após a migração. Se seu código usa **MonoTouch. caixa de diálogo** você deve continuar a usar esse namespace- *não altere `MonoTouch.Dialog`* para `Dialog`!

## <a name="common-compiler-errors"></a>Erros comuns do compilador

Outros exemplos de erros comuns estão listados abaixo, juntamente com a solução:

**Erro CS0012: o tipo ' MonoTouch. UIKit. UIView ' está definido em um assembly que não é referenciado.**

Correção: isso geralmente significa que o projeto faz referência a um componente ou pacote NuGet que não foi criado com o API Unificada. Você deve excluir e adicionar novamente todos os componentes e pacotes NuGet. Se isso não corrigir o erro, talvez a biblioteca externa ainda não ofereça suporte à API Unificada.

**Erro MT0034: não é possível incluir ' MonoTouch. dll ' e ' Xamarin. iOS. dll ' no mesmo projeto Xamarin. iOS-' Xamarin. iOS. dll ' é referenciado explicitamente, enquanto ' MonoTouch. dll ' é referenciado por ' Xamarin. Mobile, Version = 0.6.3.0, Culture = neutral, PublicKeyToken = null '.**

Correção: exclua o componente que está causando esse erro e adicione novamente ao projeto.

**Erro CS0234: o nome de namespace ou tipo ' Foundation ' não existe no namespace ' MonoTouch '. Está faltando uma referência de assembly?**

Correção: a ferramenta de migração automatizada no Visual Studio para Mac *deve* atualizar todas as referências de `MonoTouch.Foundation` para `Foundation`, no entanto, em algumas instâncias, elas precisarão ser atualizadas manualmente. Erros semelhantes podem aparecer para os outros namespaces contidos anteriormente no `MonoTouch`, como `UIKit`.

**Erro CS0266: não é possível converter implicitamente o tipo ' Double ' em ' System. float '**

Correção: alteração de tipo e conversão para `nfloat`. Esse erro também pode ocorrer para os outros tipos com equivalentes de 64 bits (como `nint`,)

```csharp
nfloat scale = (nfloat)Math.Min(rect.Width, rect.Height);
```

**Erro CS0266: não é possível converter implicitamente o tipo ' CoreGraphics. CGRect ' em ' System. Drawing. RectangleF '. Existe uma conversão explícita (você está sem um elenco?)**

Correção: altere as instâncias para `RectangleF` para `CGRect`, `SizeF` para `CGSize`e `PointF` para `CGPoint`. O namespace `using System.Drawing;` deve ser substituído por `using CoreGraphics;` (se ainda não estiver presente).

**erro CS1502: o melhor método sobrecarregado corresponde a ' CoreGraphics. CGContext. SetLineDash (System. nfloat, System. nfloat []) ' tem alguns argumentos inválidos**

Correção: altere o tipo de matriz para `nfloat[]` e converta explicitamente `Math.PI`.

```csharp
grphc.SetLineDash (0, new nfloat[] { 0, 3 * (nfloat)Math.PI });
```

**Erro CS0115: ' WordsTableSource. RowsInSection (UIKit. UITableView, int) ' está marcado como uma substituição, mas nenhum método adequado foi encontrado para substituir**

Correção: altere o valor de retorno e os tipos de parâmetro para `nint`. Isso geralmente ocorre em substituições de método, como aquelas em `UITableViewSource`, incluindo `RowsInSection`, `NumberOfSections`, `GetHeightForRow`, `TitleForHeader`, `GetViewForHeader`, etc.

```csharp
public override nint RowsInSection (UITableView tableview, nint section) {
```

**Erro CS0508: `WordsTableSource.NumberOfSections(UIKit.UITableView)`: o tipo de retorno deve ser ' System. Nint ' para corresponder ao membro substituído `UIKit.UITableViewSource.NumberOfSections(UIKit.UITableView)`**

Correção: quando o tipo de retorno é alterado para `nint`, converta o valor de retorno para `nint`.

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return (nint)navItems.Count;
}
```

**Erro CS1061: o tipo ' CoreGraphics. CGPath ' não contém uma definição para ' AddElipseInRect '**

Correção: correção ortográfica para `AddEllipseInRect`. Outras alterações de nome incluem:

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
