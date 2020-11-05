---
title: Adicionar reconhecedores de gestos de arrastar e soltar
description: Este artigo explica como reconhecer gestos de arrastar e soltar com o Xamarin.Forms .
ms.prod: xamarin
ms.assetid: 4CB2F270-908A-4A89-B852-70BC04066E8C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/27/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 30e77d4978e9a191c1ba19e80109f9854cc46f8f
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375233"
---
# <a name="add-drag-and-drop-gesture-recognizers"></a>Adicionar reconhecedores de gestos de arrastar e soltar

![API de pré-lançamento](~/media/shared/preview.png)

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/workingwithgestures-draganddropgesture/)

Um gesto de arrastar e soltar permite que os itens e seus pacotes de dados associados sejam arrastados de um local na tela para outro local usando um gesto contínuo. A operação de arrastar e soltar pode ocorrer em um único aplicativo ou pode ser iniciada em um aplicativo e terminar em outra.

> [!IMPORTANT]
> O Xamarin.Forms reconhecedor de gestos de arrastar e soltar é experimental e só pode ser usado definindo o `DragAndDrop_Experimental` sinalizador. Para obter mais informações, consulte [sinalizadores experimentais](~/xamarin-forms/internals/experimental-flags.md).
>
> Há suporte para o reconhecimento de gestos de arrastar e soltar no iOS, no Android e no Plataforma Universal do Windows (UWP). No entanto, no iOS, é necessária uma plataforma mínima do iOS 11.

A *fonte de arrastar* , que é o elemento no qual o gesto de arrastar é iniciado, pode fornecer dados a serem transferidos preenchendo um objeto de pacote de dados. Quando a fonte de arrastar é liberada, drop ocorre. O *destino de soltura* , que é o elemento sob a interface "arrastar" e, em seguida, processa o pacote de dados.

O processo para habilitar o recurso de arrastar e soltar em um aplicativo é o seguinte:

1. Habilite a opção arrastar em um elemento adicionando um `DragGestureRecognizer` objeto à sua `GestureRecognizers` coleção e definindo a `DragGestureRecognizer.CanDrag` propriedade como `true` . Para obter mais informações, consulte [habilitar arrastar](#enable-drag).
1. adicional Crie um pacote de dados. Xamarin.Forms popula automaticamente o pacote de dados para controles de imagem e texto, mas para outro conteúdo, você precisará construir seu próprio pacote de dados. Para obter mais informações, consulte [criar um pacote de dados](#build-a-data-package).
1. Habilite Drop em um elemento adicionando um `DropGestureRecognizer` objeto sua `GestureRecognizers` coleção e definindo a `DropGestureRecognizer.AllowDrop` propriedade como `true` . Para obter mais informações, consulte [habilitar drop](#enable-drop).
1. adicional Manipule o `DropGestureRecognizer.DragOver` evento para indicar o tipo de operação permitido pelo destino de soltura. Para obter mais informações, consulte [manipular o evento DragOver](#handle-the-dragover-event).
1. adicional Processe o pacote de dados para receber o conteúdo Descartado. Xamarin.Forms o recuperará automaticamente dados de imagem e texto do pacote de dados, mas para outro conteúdo, você precisará processar o pacote de dados. Para obter mais informações, consulte [processar o pacote de dados](#process-the-data-package).

> [!NOTE]
> Não há suporte para arrastar itens para e de um [`CollectionView`](xref:Xamarin.Forms.CollectionView) no momento.

## <a name="enable-drag"></a>Habilitar arrastar

No Xamarin.Forms , o reconhecimento de gestos de arrastar é fornecido pela `DragGestureRecognizer` classe. Essa classe define as seguintes propriedades:

- `CanDrag`, do tipo `bool` , que indica se o elemento ao qual o reconhecedor de gestos está anexado pode ser uma fonte de arrastar. O valor padrão dessa propriedade é `false`.
- `DragStartingCommand`, do tipo `ICommand` , que é executado quando um gesto de arrastar é reconhecido pela primeira vez.
- `DragStartingCommandParameter`, do tipo `object`, que é o parâmetro passado para `DragStartingCommand`.
- `DropCompletedCommand`, do tipo `ICommand` , que é executado quando a fonte de arrastar é descartada.
- `DropCompletedCommandParameter`, do tipo `object`, que é o parâmetro passado para `DropCompletedCommand`.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

A `DragGestureRecognizer` classe também define `DragStarting` e `DropCompleted` eventos. Quando um `DragGestureRecognizer` objeto detecta um gesto de arrastar, ele executa o `DragStartingCommand` e invoca o `DragStarting` evento. Em seguida, quando o `DragGestureRecognizer` objeto detecta a conclusão de um gesto de soltar, ele executa o `DropCompletedCommand` e invoca o `DropCompleted` evento.

O `DragStartingEventArgs` objeto que acompanha o `DragStarting` evento define as seguintes propriedades:

- `Handled`, do tipo `bool` , indica se o manipulador de eventos tratou o evento ou se Xamarin.Forms deve continuar seu próprio processamento.
- `Cancel`, do tipo `bool` , indica se o evento deve ser cancelado.
- `Data`, do tipo `DataPackage` , indica o pacote de dados que acompanha a fonte de arrastar. Trata-se de uma propriedade somente leitura.

O `DropCompletedEventArgs` objeto que acompanha o `DropCompleted` evento tem uma propriedade somente leitura `DropResult` , do tipo `DataPackageOperation` . Para obter mais informações sobre a `DataPackageOperation` enumeração, consulte [manipular o evento DragOver](#handle-the-dragover-event).

O exemplo de XAML a seguir mostra um `DragGestureRecognizer` anexado a um [`Image`](xref:Xamarin.Forms.Image) :

```xaml
<Image Source="monkeyface.png">
    <Image.GestureRecognizers>
        <DragGestureRecognizer CanDrag="True" />
    </Image.GestureRecognizers>
</Image>
```

Neste exemplo, um gesto de arrastar pode ser iniciado no [`Image`](xref:Xamarin.Forms.Image) .

> [!TIP]
> No iOS, Android e UWP, um gesto de arrastar é iniciado com um pressionamento longo seguido de um arrastar.

Para obter um exemplo de como usar `DragGestureRecognizer` comandos, consulte o [exemplo](/samples/xamarin/xamarin-forms-samples/workingwithgestures-draganddropgesture/).

## <a name="build-a-data-package"></a>Criar um pacote de dados

Xamarin.Forms o criará automaticamente um pacote de dados para você, quando um arrastar for iniciado, para os seguintes controles:

- Controles de texto. Os valores de texto podem ser arrastados dos objetos,,,,,, [`CheckBox`](xref:Xamarin.Forms.CheckBox) [`DatePicker`](xref:Xamarin.Forms.DatePicker) [`Editor`](xref:Xamarin.Forms.Editor) [`Entry`](xref:Xamarin.Forms.Entry) [`Label`](xref:Xamarin.Forms.Label) [`RadioButton`](xref:Xamarin.Forms.RadioButton) [`Switch`](xref:Xamarin.Forms.Switch) e [`TimePicker`](xref:Xamarin.Forms.TimePicker) .
- Controles de imagem. As imagens podem ser arrastadas de [`Button`](xref:Xamarin.Forms.Button) [`Image`](xref:Xamarin.Forms.Image) controles, e [`ImageButton`](xref:Xamarin.Forms.ImageButton) .

A tabela a seguir mostra as propriedades que são lidas e qualquer conversão que seja tentada quando um arrastar é iniciado em um controle de texto:

| Control | Propriedade | Conversão |
| --- | --- | --- |
| `CheckBox` | `IsChecked` | `bool` convertido em um `string`. |
| `DatePicker` | `Date` | `DateTime` convertido em um `string`. |
| `Editor` | `Text` ||
| `Entry` | `Text` ||
| `Label` | `Text` ||
| `RadioButton` | `IsChecked` | `bool` convertido em um `string`. |
| `Switch` | `IsToggled` | `bool` convertido em um `string`. |
| `TimePicker` | `Time` | `TimeSpan` convertido em um `string`. |

Para conteúdo que não seja texto e imagens, você mesmo precisará criar um pacote de dados.

Os pacotes de dados são representados pela `DataPackage` classe, que define as seguintes propriedades:

- `Properties`, do tipo `DataPackagePropertySet` , que é uma coleção de propriedades que compõe os dados contidos no `DataPackage` . Esta propriedade é uma propriedade somente leitura.
- `Image`, do tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource) , que é a imagem contida no `DataPackage` .
- `Text`, do tipo `string` , que é o texto contido no `DataPackage` .
- `View`, do tipo `DataPackageView` , que é uma versão somente leitura do `DataPackage` .

A `DataPackagePropertySet` classe representa um conjunto de propriedades armazenado como um `Dictionary<string,object>` . Para obter informações sobre a `DataPackageView` classe, consulte [processar o pacote de dados](#process-the-data-package).

### <a name="store-image-or-text-data"></a>Armazenar dados de imagem ou texto

Dados de imagem ou texto podem ser associados a uma fonte de arrastar, armazenando os dados na `DataPackage.Image` `DataPackage.Text` propriedade ou. Isso pode ser feito no manipulador para o `DragStarting` evento.

O exemplo de XAML a seguir mostra um `DragGestureRecognizer` que registra um manipulador para o `DragStarting` evento:

```xaml
<Path Stroke="Black"
      StrokeThickness="4">
    <Path.GestureRecognizers>
        <DragGestureRecognizer CanDrag="True"
                               DragStarting="OnDragStarting" />
    </Path.GestureRecognizers>
    <Path.Data>
        <!-- PathGeometry goes here -->
    </Path.Data>
</Path>
```

Neste exemplo, o `DragGestureRecognizer` é anexado a um `Path` objeto. O `DragStarting` evento é acionado quando um gesto de arrastar é detectado no `Path` , que executa o `OnDragStarting` manipulador de eventos:

```csharp
void OnDragStarting(object sender, DragStartingEventArgs e)
{
    e.Data.Text = "My text data goes here";
}
```

O `DragStartingEventArgs` objeto que acompanha o `DragStarting` evento tem uma `Data` propriedade, do tipo `DataPackage` . Neste exemplo, a `Text` Propriedade do `DataPackage` objeto é definida como um `string` . O `DataPackage` pode ser acessado ao soltar para recuperar o `string` .

### <a name="store-data-in-the-property-bag"></a>Armazenar dados no recipiente de propriedades

Todos os dados, incluindo imagens e texto, podem ser associados a uma fonte de arrastar, armazenando os dados na `DataPackage.Properties` coleção. Isso pode ser feito no manipulador para o `DragStarting` evento.

O exemplo de XAML a seguir mostra um `DragGestureRecognizer` que registra um manipulador para o `DragStarting` evento:

```xaml
<Rectangle Stroke="Red"
           Fill="DarkBlue"
           StrokeThickness="4"
           HeightRequest="200"
           WidthRequest="200">
    <Rectangle.GestureRecognizers>
        <DragGestureRecognizer CanDrag="True"
                               DragStarting="OnDragStarting" />
    </Rectangle.GestureRecognizers>
</Rectangle>
```

Neste exemplo, o `DragGestureRecognizer` é anexado a um `Rectangle` objeto. O `DragStarting` evento é acionado quando um gesto de arrastar é detectado no `Rectangle` , que executa o `OnDragStarting` manipulador de eventos:

```csharp
void OnDragStarting(object sender, DragStartingEventArgs e)
{
    Shape shape = (sender as Element).Parent as Shape;
    e.Data.Properties.Add("Square", new Square(shape.Width, shape.Height));
}
```

O `DragStartingEventArgs` objeto que acompanha o `DragStarting` evento tem uma `Data` propriedade, do tipo `DataPackage` . A `Properties` coleção do `DataPackage` objeto, que é uma `Dictionary<string, object>` coleção, pode ser modificada para armazenar os dados necessários. Neste exemplo, o `Properties` dicionário é modificado para armazenar um `Square` objeto, que representa o tamanho do `Rectangle` , em relação a uma chave "quadrada".

## <a name="enable-drop"></a>Habilitar drop

No Xamarin.Forms , o reconhecimento de gestos drop é fornecido pela `DropGestureRecognizer` classe. Essa classe define as seguintes propriedades:

- `AllowDrop`, do tipo `bool` , que indica se o elemento ao qual o reconhecedor de gestos está anexado pode ser um destino de soltura. O valor padrão dessa propriedade é `false`.
- `DragOverCommand`, do tipo `ICommand` , que é executado quando a fonte de arrastar é arrastada sobre o destino de soltar.
- `DragOverCommandParameter`, do tipo `object`, que é o parâmetro passado para `DragOverCommand`.
- `DropCommand`, do tipo `ICommand` , que é executado quando a origem de arrastar é descartada sobre o destino de soltura.
- `DropCommandParameter`, do tipo `object`, que é o parâmetro passado para `DropCommand`.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

A `DropGestureRecognizer` classe também define `DragOver` e `Drop` eventos. Quando um `DropGestureRecognizer` reconhece uma origem de arrastar sobre o destino de soltura, ele executa o `DragOverCommand` e invoca o `DragOver` evento. Em seguida, quando o `DropGestureRecognizer` reconhece um gesto de soltar sobre o destino de soltura, ele executa o `DropCommand` e invoca o `Drop` evento.

A `DragEventArgs` classe que acompanha o `DragOver` evento define as seguintes propriedades:

- `Data`, do tipo `DataPackage` , que contém os dados associados à fonte de arrastar. Esta propriedade é somente para leitura.
- `AcceptedOperation`, do tipo `DataPackageOperation` , que especifica quais operações são permitidas pelo destino de soltura.

Para obter informações sobre a `DataPackageOperation` enumeração, consulte [manipular o evento DragOver](#handle-the-dragover-event).

A `DropEventArgs` classe que acompanha o `Drop` evento define as seguintes propriedades:

- `Data`, do tipo `DataPackageView` , que é uma versão somente leitura do pacote de dados.
- `Handled`, do tipo `bool` , indica se o manipulador de eventos tratou o evento ou se Xamarin.Forms deve continuar seu próprio processamento.

O exemplo de XAML a seguir mostra um `DropGestureRecognizer` anexado a um [`Image`](xref:Xamarin.Forms.Image) :

```xaml
<Image BackgroundColor="Silver"
       HeightRequest="300"
       WidthRequest="250">
    <Image.GestureRecognizers>
        <DropGestureRecognizer AllowDrop="True" />
    </Image.GestureRecognizers>
</Image>
```

Neste exemplo, quando uma fonte de arrastar é descartada no [`Image`](xref:Xamarin.Forms.Image) destino de soltar, a fonte de arrastar será copiada para o destino de soltura, desde que a fonte de arrastar seja um [`ImageSource`](xref:Xamarin.Forms.ImageSource) . Isso ocorre porque o Xamarin.Forms copia automaticamente imagens arrastadas e texto para destinos de destino compatíveis.

Para obter um exemplo de como usar `DropGestureRecognizer` comandos, consulte o [exemplo](/samples/xamarin/xamarin-forms-samples/workingwithgestures-draganddropgesture/).

## <a name="handle-the-dragover-event"></a>Manipular o evento DragOver

O `DropGestureRecognizer.DragOver` evento pode ser manipulado opcionalmente para indicar que tipo de operações são permitidas pelo destino de soltura. Isso pode ser feito definindo a `AcceptedOperation` propriedade, do tipo `DataPackageOperation` , do `DragEventArgs` objeto que acompanha o `DragOver` evento.

A enumeração `DataPackageOperation` define os seguintes membros:

- `None`, indica que nenhuma ação será executada.
- `Copy`, indica que o conteúdo de origem de arrastar será copiado para o destino de soltura.

> [!IMPORTANT]
> Quando um `DragEventArgs` objeto é criado, a `AcceptedOperation` propriedade assume como padrão `DataPackageOperation.Copy` .

O exemplo de XAML a seguir mostra um `DropGestureRecognizer` que registra um manipulador para o `DragOver` evento:

```xaml
<Image BackgroundColor="Silver"
       HeightRequest="300"
       WidthRequest="250">
    <Image.GestureRecognizers>
        <DropGestureRecognizer AllowDrop="True"
                               DragOver="OnDragOver" />
    </Image.GestureRecognizers>
</Image>
```

Neste exemplo, o `DropGestureRecognizer` é anexado a um [`Image`](xref:Xamarin.Forms.Image) objeto. O `DragOver` evento é acionado quando uma fonte de arrastar é arrastada sobre o destino de soltura, mas não foi descartada, o que executa o `OnDragOver` manipulador de eventos:

```csharp
void OnDragOver(object sender, DragEventArgs e)
{
    e.AcceptedOperation = DataPackageOperation.None;
}
```

Neste exemplo, a `AcceptedOperation` Propriedade do `DragEventArgs` objeto é definida como `DataPackageOperation.None` . Isso garante que nenhuma ação seja tomada quando uma origem de arrastar for descartada sobre o destino de soltura.

## <a name="process-the-data-package"></a>Processar o pacote de dados

O `Drop` evento é acionado quando uma fonte de arrastar é liberada em um destino de soltar. Quando isso ocorrer, o Xamarin.Forms tentará automaticamente recuperar dados do pacote de dados quando uma fonte de arrastar for descartada nos seguintes controles:

- Controles de texto. Os valores de texto podem ser descartados nos [`CheckBox`](xref:Xamarin.Forms.CheckBox) objetos,,,,,, e [`DatePicker`](xref:Xamarin.Forms.DatePicker) [`Editor`](xref:Xamarin.Forms.Editor) [`Entry`](xref:Xamarin.Forms.Entry) [`Label`](xref:Xamarin.Forms.Label) [`RadioButton`](xref:Xamarin.Forms.RadioButton) [`Switch`](xref:Xamarin.Forms.Switch) [`TimePicker`](xref:Xamarin.Forms.TimePicker) .
- Controles de imagem. As imagens podem ser removidas em [`Button`](xref:Xamarin.Forms.Button) [`Image`](xref:Xamarin.Forms.Image) controles, e [`ImageButton`](xref:Xamarin.Forms.ImageButton) .

A tabela a seguir mostra as propriedades que são definidas e qualquer conversão que seja tentada quando uma fonte "arrastar" baseada em texto é descartada em um controle de texto:

| Control | Propriedade | Conversão |
| --- | --- | --- |
| `CheckBox` | `IsChecked` | `string` é convertido em um `bool` . |
| `DatePicker` | `Date` | `string` é convertido em um `DateTime` . |
| `Editor` | `Text` ||
| `Entry` | `Text` ||
| `Label` | `Text` ||
| `RadioButton` | `IsChecked` | `string` é convertido em um `bool` . |
| `Switch` | `IsToggled` | `string` é convertido em um `bool` . |
| `TimePicker` | `Time` | `string` é convertido em um `TimeSpan` . |

Para conteúdo que não seja texto e imagens, você mesmo precisará processar o pacote de dados.

A `DropEventArgs` classe que acompanha o `Drop` evento define uma `Data` propriedade, do tipo `DataPackageView` . Essa propriedade representa uma versão somente leitura do pacote de dados.

### <a name="retrieve-image-or-text-data"></a>Recuperar dados de imagem ou texto

Dados de imagem ou texto podem ser recuperados de um pacote de dados no manipulador para o `Drop` evento, usando métodos definidos na `DataPackageView` classe.

A `DataPackageView` classe inclui `GetImageAsync` os `GetTextAsync` métodos e. O `GetImageAsync` método recupera uma imagem do pacote de dados, que foi armazenado na `DataPackage.Image` propriedade e retorna `Task<ImageSource>` . Da mesma forma, o `GetTextAsync` método recupera texto do pacote de dados, que foi armazenado na `DataPackage.Text` propriedade e retorna `Task<string>` .

O exemplo a seguir mostra um `Drop` manipulador de eventos que recupera o texto do pacote de dados para um `Path` :

```csharp
async void OnDrop(object sender, DropEventArgs e)
{
    string text = await e.Data.GetTextAsync();

    // Perform logic to take action based on the text value.
}
```

Neste exemplo, os dados de texto são recuperados do pacote de dados usando o `GetTextAsync` método. Uma ação com base no valor de texto pode ser executada.

### <a name="retrieve-data-from-the-property-bag"></a>Recuperar dados do recipiente de propriedades

Todos os dados podem ser recuperados de um pacote de dados no manipulador para o `Drop` evento, acessando a `Properties` coleção do pacote de dados.

A `DataPackageView` classe define uma `Properties` propriedade, do tipo `DataPackagePropertySetView` . A `DataPackagePropertySetView` classe representa um recipiente de propriedades somente leitura armazenado como um `Dictionary<string, object>` .

O exemplo a seguir mostra um `Drop` manipulador de eventos que recupera dados do recipiente de propriedades de um pacote de dados para um `Rectangle` :

```csharp
void OnDrop(object sender, DropEventArgs e)
{
    Square square = (Square)e.Data.Properties["Square"];

    // Perform logic to take action based on retrieved value.
}
```

Neste exemplo, o `Square` objeto é recuperado do recipiente de propriedades do pacote de dados, especificando a chave de dicionário "Square". Uma ação com base no valor recuperado pode ser realizada.

## <a name="related-links"></a>Links relacionados

- [Gesto de arrastar e soltar (exemplo)](/samples/xamarin/xamarin-forms-samples/workingwithgestures-draganddropgesture/)