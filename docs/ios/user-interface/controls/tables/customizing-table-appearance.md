---
title: "Personalizando a aparência de uma tabela"
ms.topic: article
ms.prod: xamarin
ms.assetid: 8A83DE38-0028-CB61-66F9-0FB9DE552286
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 12314e6ec5951a5a094ed8b0a5536b450853c5fb
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="customizing-a-tables-appearance"></a>Personalizando a aparência de uma tabela

A maneira mais simples para alterar a aparência de uma tabela é usar um estilo de célula diferente. Você pode alterar o estilo de célula é usado durante a criação de cada célula de `UITableViewSource`do `GetCell` método.

## <a name="cell-styles"></a>Estilos de célula

Há quatro estilos internos:

-  **Padrão** – dá suporte a um `UIImageView`.
-  **Subtítulo** – dá suporte a um `UIImageView` e subtítulo.
-  **Value1** – direito subtítulo alinhado, dá suporte a um `UIImageView`.
-  **Value2** – título é alinhado à direita e o subtítulo é alinhado à esquerda (mas não há imagem).


Essas capturas de tela mostram como cada estilo aparece:

 [ ![](customizing-table-appearance-images/image7.png "Essas capturas de tela mostram como cada estilo aparece")](customizing-table-appearance-images/image7.png)

O exemplo **CellDefaultTable** contém o código para gerar essas telas. O estilo de célula é definido `UITableViewCell` construtor, como este:

```csharp
cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Subtitle, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Value1, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Value2, cellIdentifier);
```

[Suporte para propriedades](http://developer.xamarin.com/api/type/UIKit.UITableViewCell/) da célula de estilo, em seguida, pode ser definido:

```csharp
cell.TextLabel.Text = tableItems[indexPath.Row].Heading;
cell.DetailTextLabel.Text = tableItems[indexPath.Row].SubHeading;
cell.ImageView.Image = UIImage.FromFile("Images/" + tableItems[indexPath.Row].ImageName); // don't use for Value2
```

## <a name="accessories"></a>Acessórios

Células podem ter os seguintes Acessórios adicionados à direita da exibição:

-   **Marca de seleção** – pode ser usado para indicar a seleção múltipla em uma tabela.
-   **DetailButton** – responde tocar independentemente do restante da célula, permitindo que ele execute uma função diferente ao tocar a própria célula (como abrir uma janela pop-up ou uma nova janela que não seja parte de um `UINavigationController` pilha).
-   **DisclosureIndicator** – normalmente usada para indicar que tocar a célula será aberto outro modo de exibição.
-   **DetailDisclosureButton** – uma combinação da `DetailButton` e `DisclosureIndicator`.


Esta é sua aparência:

 [ ![](customizing-table-appearance-images/image8.png "Acessórios de exemplo")](customizing-table-appearance-images/image8.png)

Para exibir uma esses acessórios que você pode definir o `Accessory` propriedade o `GetCell` método:

```csharp
cell.Accessory = UITableViewCellAccessory.Checkmark;
//cell.Accessory = UITableViewCellAccessory.DisclosureIndicator;
//cell.Accessory = UITableViewCellAccessory.DetailDisclosureButton; // implement AccessoryButtonTapped
//cell.Accessory = UITableViewCellAccessory.None; // to clear the accessory
```

Quando o `DetailButton` ou `DetailDisclosureButton` são mostrados, você também deve substituir o `AccessoryButtonTapped` para executar alguma ação quando ele é alterado.

```csharp
public override void AccessoryButtonTapped (UITableView tableView, NSIndexPath indexPath)
{
    UIAlertController okAlertController = UIAlertController.Create ("DetailDisclosureButton Touched", tableItems[indexPath.Row].Heading, UIAlertControllerStyle.Alert);
    okAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
    owner.PresentViewController (okAlertController, true, null);

    tableView.DeselectRow (indexPath, true);
}
```

O exemplo **CellAccessoryTable** mostra um exemplo de uso de Acessórios.

## <a name="cell-separators"></a>Separadores de célula

Separadores de célula são células de tabela usadas para separar a tabela. As propriedades são definidas na tabela.

```csharp
TableView.SeparatorColor = UIColor.Blue;
TableView.SeparatorStyle = UITableViewCellSeparatorStyle.DoubleLineEtched;
```

Também é possível adicionar um efeito de desfoque ou valorize para o separador:

```csharp
// blur effect
TableView.SeparatorEffect =
    UIBlurEffect.FromStyle(UIBlurEffectStyle.Dark);

//vibrancy effect
var effect = UIBlurEffect.FromStyle(UIBlurEffectStyle.Light);
TableView.SeparatorEffect = UIVibrancyEffect.FromBlurEffect(effect);
```

O separador também pode ter uma inserção:

```csharp
TableView.SeparatorInset.InsetRect(new CGRect(4, 4, 150, 2));
```

## <a name="creating-custom-cell-layouts"></a>A criação de Layouts de célula personalizado

Para alterar o estilo visual de uma tabela, você precisa fornecer células personalizadas para exibir. A célula personalizada pode ter diferentes layouts de controle e de cores.

O exemplo CellCustomTable implementa um `UITableViewCell` subclasse que define um layout personalizado de `UILabel`s e um `UIImage` com diferentes fontes e cores. As células resultantes tem esta aparência:

 [ ![](customizing-table-appearance-images/image9.png "Layouts de célula personalizado")](customizing-table-appearance-images/image9.png)

A classe de célula personalizado consiste em apenas três métodos:

-   **Construtor** – cria os controles de interface do usuário e define as propriedades de estilo personalizada (por exemplo. tipo de fonte, tamanho e cores).
-   **UpdateCell** – um método para `UITableView.GetCell` a ser usado para definir as propriedades da célula.
-   **LayoutSubviews** – defina o local dos controles da interface do usuário. No exemplo, todas as células têm o mesmo layout, mas uma célula mais complexa (especialmente aqueles com tamanhos variados) talvez seja necessário posições do layout diferente dependendo do conteúdo que está sendo exibido.


O código de exemplo completo em **CellCustomTable > CustomVegeCell.cs** segue:

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

O `GetCell` método o `UITableViewSource` precisa ser modificada para criar a célula personalizada:

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



## <a name="related-links"></a>Links relacionados

- [WorkingWithTables (exemplo)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
