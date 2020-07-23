---
title: Personalizando a aparência de uma tabela no Xamarin. iOS
description: Este documento descreve como personalizar a aparência de uma tabela no Xamarin. iOS. Ele aborda estilos de célula, acessórios, separadores de células e layouts de células personalizadas.
ms.prod: xamarin
ms.assetid: 8A83DE38-0028-CB61-66F9-0FB9DE552286
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: f65b4f8f97059858067df8c847bc9ed181c8cc4c
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86932672"
---
# <a name="customizing-a-tables-appearance-in-xamarinios"></a>Personalizando a aparência de uma tabela no Xamarin. iOS

A maneira mais simples de alterar a aparência de uma tabela é usar um estilo de célula diferente. Você pode alterar qual estilo de célula é usado ao criar cada célula no `UITableViewSource` `GetCell` método.

## <a name="cell-styles"></a>Estilos de célula

Há quatro estilos internos:

- **Padrão** – dá suporte a um `UIImageView` .
- **Subtítulo** – dá suporte a um `UIImageView` subtítulo e.
- **Value1** – subtítulo alinhado à direita, dá suporte a um `UIImageView` .
- **Value2** – o título é alinhado à direita e o subtítulo é alinhado à esquerda (mas nenhuma imagem).

Essas capturas de tela mostram como cada estilo é exibido:

 [![Essas capturas de tela mostram como cada estilo aparece](customizing-table-appearance-images/image7.png)](customizing-table-appearance-images/image7.png#lightbox)

O **CellDefaultTable** de exemplo contém o código para produzir essas telas. O estilo de célula é definido no `UITableViewCell` Construtor, desta forma:

```csharp
cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Subtitle, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Value1, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Value2, cellIdentifier);
```

[As propriedades com suporte](xref:UIKit.UITableViewCell) do estilo de célula podem ser definidas:

```csharp
cell.TextLabel.Text = tableItems[indexPath.Row].Heading;
cell.DetailTextLabel.Text = tableItems[indexPath.Row].SubHeading;
cell.ImageView.Image = UIImage.FromFile("Images/" + tableItems[indexPath.Row].ImageName); // don't use for Value2
```

## <a name="accessories"></a>Acessórios

As células podem ter os seguintes acessórios adicionados à direita da exibição:

- **Marca** de seleção – pode ser usada para indicar várias seleções em uma tabela.
- **DetailButton** – responde ao toque independentemente do restante da célula, permitindo que ele execute uma função diferente para tocar a própria célula (como abrir um pop-up ou uma nova janela que não faz parte de uma `UINavigationController` pilha).
- **DisclosureIndicator** – normalmente usado para indicar que tocar na célula abrirá outra exibição.
- **DetailDisclosureButton** – uma combinação de `DetailButton` e `DisclosureIndicator` .

Isso é o que eles parecem:

 [![Acessórios de exemplo](customizing-table-appearance-images/image8.png)](customizing-table-appearance-images/image8.png#lightbox)

Para exibir um desses acessórios, você pode definir a `Accessory` propriedade no `GetCell` método:

```csharp
cell.Accessory = UITableViewCellAccessory.Checkmark;
//cell.Accessory = UITableViewCellAccessory.DisclosureIndicator;
//cell.Accessory = UITableViewCellAccessory.DetailDisclosureButton; // implement AccessoryButtonTapped
//cell.Accessory = UITableViewCellAccessory.None; // to clear the accessory
```

Quando o `DetailButton` ou `DetailDisclosureButton` for mostrado, você também deverá substituir o `AccessoryButtonTapped` para executar alguma ação quando ele for tocado.

```csharp
public override void AccessoryButtonTapped (UITableView tableView, NSIndexPath indexPath)
{
    UIAlertController okAlertController = UIAlertController.Create ("DetailDisclosureButton Touched", tableItems[indexPath.Row].Heading, UIAlertControllerStyle.Alert);
    okAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
    owner.PresentViewController (okAlertController, true, null);

    tableView.DeselectRow (indexPath, true);
}
```

O exemplo de **CellAccessoryTable** mostra um exemplo usando acessórios.

## <a name="cell-separators"></a>Separadores de células

Separadores de células são células de tabela usadas para separar a tabela. As propriedades são definidas na tabela.

```csharp
TableView.SeparatorColor = UIColor.Blue;
TableView.SeparatorStyle = UITableViewCellSeparatorStyle.DoubleLineEtched;
```

Também é possível adicionar um efeito de desfoque ou Vibrancy ao separador:

```csharp
// blur effect
TableView.SeparatorEffect =
    UIBlurEffect.FromStyle(UIBlurEffectStyle.Dark);

//vibrancy effect
var effect = UIBlurEffect.FromStyle(UIBlurEffectStyle.Light);
TableView.SeparatorEffect = UIVibrancyEffect.FromBlurEffect(effect);
```

O separador também pode ter uma margem interna:

```csharp
TableView.SeparatorInset.InsetRect(new CGRect(4, 4, 150, 2));
```

## <a name="creating-custom-cell-layouts"></a>Criando layouts de célula personalizados

Para alterar o estilo visual de uma tabela, você precisa fornecer células personalizadas para exibição. A célula personalizada pode ter cores e layouts de controle diferentes.

O exemplo CellCustomTable implementa uma `UITableViewCell` subclasse que define um layout personalizado de `UILabel` s e um `UIImage` com fontes e cores diferentes. As células resultantes têm a seguinte aparência:

 [![Layouts de células personalizadas](customizing-table-appearance-images/image9.png)](customizing-table-appearance-images/image9.png#lightbox)

A classe de célula Personalizada consiste em apenas três métodos:

- **Construtor** – cria os controles da interface do usuário e define as propriedades de estilo personalizado (por exemplo, face da fonte, tamanho e cores).
- **UpdateCell** – um método para `UITableView.GetCell` usar para definir as propriedades da célula.
- **LayoutSubviews** – defina o local dos controles da interface do usuário. No exemplo, cada célula tem o mesmo layout, mas uma célula mais complexa (particularmente aquelas com tamanhos variados) pode precisar de diferentes posições de layout dependendo do conteúdo que está sendo exibido.

O código de exemplo completo no **CellCustomTable > CustomVegeCell.cs** segue:

```csharp
public class CustomVegeCell : UITableViewCell  {
    UILabel headingLabel, subheadingLabel;
    UIImageView imageView;
    public CustomVegeCell (NSString cellId) : base (UITableViewCellStyle.Default, cellId)
    {
        SelectionStyle = UITableViewCellSelectionStyle.Gray;
        ContentView.BackgroundColor = UIColor.FromRGB (218, 255, 127);
        imageView = new UIImageView();
        headingLabel = new UILabel () {
            Font = UIFont.FromName("Cochin-BoldItalic", 22f),
            TextColor = UIColor.FromRGB (127, 51, 0),
            BackgroundColor = UIColor.Clear
        };
        subheadingLabel = new UILabel () {
            Font = UIFont.FromName("AmericanTypewriter", 12f),
            TextColor = UIColor.FromRGB (38, 127, 0),
            TextAlignment = UITextAlignment.Center,
            BackgroundColor = UIColor.Clear
        };
        ContentView.AddSubviews(new UIView[] {headingLabel, subheadingLabel, imageView});

    }
    public void UpdateCell (string caption, string subtitle, UIImage image)
    {
        imageView.Image = image;
        headingLabel.Text = caption;
        subheadingLabel.Text = subtitle;
    }
    public override void LayoutSubviews ()
    {
        base.LayoutSubviews ();
        imageView.Frame = new CGRect (ContentView.Bounds.Width - 63, 5, 33, 33);
        headingLabel.Frame = new CGRect (5, 4, ContentView.Bounds.Width - 63, 25);
        subheadingLabel.Frame = new CGRect (100, 18, 100, 20);
    }
}
```

O `GetCell` método do `UITableViewSource` precisa ser modificado para criar a célula Personalizada:

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (cellIdentifier) as CustomVegeCell;
    if (cell == null)
        cell = new CustomVegeCell (cellIdentifier);
    cell.UpdateCell (tableItems[indexPath.Row].Heading
            , tableItems[indexPath.Row].SubHeading
            , UIImage.FromFile ("Images/" + tableItems[indexPath.Row].ImageName) );
    return cell;
}
```

## <a name="related-links"></a>Links Relacionados

- [WorkingWithTables (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
