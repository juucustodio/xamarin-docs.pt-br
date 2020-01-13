---
title: Modos de exibição do xamarin. Forms
description: Modos de exibição do xamarin. Forms são os blocos de construção de interfaces de usuário móvel de plataforma cruzada. Este artigo lista as exibições que estão incluídas no xamarin. Forms.
ms.prod: xamarin
ms.assetid: AC070686-A423-4A98-8BB6-0B9F94C062CC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2019
ms.openlocfilehash: dcba15d049e5bab09142f0aeba237b2a790a5950
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728168"
---
# <a name="xamarinforms-views"></a>Modos de exibição do xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_Modos de exibição do xamarin. Forms são os blocos de construção de interfaces de usuário móvel de plataforma cruzada._

Modos de exibição são objetos de interface do usuário, como rótulos, botões e controles deslizantes que são normalmente conhecidos como *controles* ou *widgets* em outros ambientes de programação gráficas. As exibições compatíveis com o xamarin. Forms todos derivam de [ `View` ](xref:Xamarin.Forms.View) classe. Eles podem ser divididos em várias categorias:

## <a name="views-for-presentation"></a>Modos de exibição para apresentação

### <a name="label"></a>Rótulo

|     |     |
| --- | --- |
| [`Label`](xref:Xamarin.Forms.Label) Exibe as cadeias de caracteres de texto de linha única ou blocos de várias linhas de texto, com formatação constante ou variável. Defina a [ `Text` ](xref:Xamarin.Forms.Label.Text) propriedade como uma cadeia de caracteres de constante de formatação, ou um conjunto a [ `FormattedText` ](xref:Xamarin.Forms.Label.FormattedText) propriedade para um [ `FormattedString` ](xref:Xamarin.Forms.FormattedString) objeto variável formatação.<br /><br />[Documentação da API](xref:Xamarin.Forms.Label) / [guia](~/xamarin-forms/user-interface/text/label.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![Exemplo de rótulo](views-images/Label.png "Exemplo de rótulo")](views-images/Label-Large.png#lightbox "Exemplo de rótulo")<br /> [O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/LabelDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/LabelDemoPage.xaml) |
|     |     |

### <a name="image"></a>Image

|     |     |
| --- | --- |
| [`Image`](xref:Xamarin.Forms.Image) Exibe um bitmap. Bitmaps podem ser baixados pela Web, inseridos como recursos no projeto comum ou projetos de plataforma ou criado usando um .NET `Stream` objeto.<br /><br />[Documentação da API](xref:Xamarin.Forms.Image) / [guia](~/xamarin-forms/user-interface/images.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages) | [![Exemplo de imagem](views-images/Image.png "Exemplo de imagem")](views-images/Image-Large.png#lightbox "Exemplo de imagem")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageDemoPage.xaml) |
|     |     |

### <a name="boxview"></a>BoxView

|     |    |
| --- | ---|
| [`BoxView`](xref:Xamarin.Forms.BoxView) Exibe um retângulo sólido colorido pela [ `Color` ](xref:Xamarin.Forms.BoxView.Color) propriedade. `BoxView` tem uma solicitação de tamanho padrão de 40 x 40. Para outros tamanhos, atribuir a [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) e [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) propriedades.<br /><br />[Documentação da API](xref:Xamarin.Forms.BoxView) / [guia](~/xamarin-forms/user-interface/boxview.md) / [exemplo 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview), [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-textdecoration), [3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors/), [4 ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife), [5](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock), e [6](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock) | [![Exemplo de BoxView](views-images/BoxView.png "Exemplo de BoxView")](views-images/BoxView-Large.png#lightbox "Exemplo de BoxView")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/BoxViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/BoxViewDemoPage.xaml) |
|     |     |

### <a name="webview"></a>WebView

|     |     |
| --- | --- |
| [`WebView`](xref:Xamarin.Forms.WebView) Exibe o conteúdo, HTML ou páginas da Web com base em se a [ `Source` ](xref:Xamarin.Forms.WebView.Source) estiver definida como um [ `UriWebViewSource` ](xref:Xamarin.Forms.UrlWebViewSource) ou um [ `HtmlWebViewSource` ](xref:Xamarin.Forms.HtmlWebViewSource) objeto.<br /><br />[Documentação da API](xref:Xamarin.Forms.WebView) / [guia](~/xamarin-forms/user-interface/webview.md) / [exemplo 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview) e [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview) | [![Exemplo de WebView](views-images/WebView.png "Exemplo de WebView")](views-images/WebView-Large.png#lightbox "Exemplo de WebView")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/WebViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/WebViewDemoPage.xaml) |
|     |     |

### <a name="openglview"></a>OpenGLView

|     |     |
| --- | --- |
| [`OpenGLView`](xref:Xamarin.Forms.OpenGLView) exibe gráficos OpenGL em projetos do iOS e Android. Não há nenhum suporte para a plataforma Universal do Windows. Os projetos do iOS e Android exigem uma referência para o **OpenTK 1.0** assembly ou o **OpenTK** assembly versão 1.0.0.0. `OpenGLView` é mais fácil de usar em um projeto compartilhado; Se usado em uma biblioteca .NET Standard, uma dependência de serviço também será necessária (conforme mostrado no código de exemplo).<br /><br />Esse é o único recurso de gráficos que é criado no Xamarin. Forms, mas um aplicativo Xamarin. Forms também pode renderizar gráficos usando [`SkiaSharp`](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)ou [`UrhoSharp`](~/xamarin-forms/user-interface/graphics/urhosharp.md).<br /><br />[Documentação da API](xref:Xamarin.Forms.OpenGLView)<br /><br /> | [![Exemplo de OpenGLView](views-images/OpenGLView.png "Exemplo de OpenGLView")](views-images/OpenGLView-Large.png#lightbox "Exemplo de OpenGLView")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/OpenGLViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml) com [de lógica](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml.cs) |
|     |     |

### <a name="map"></a>Mapa

|     |     |
| --- | --- |
| [`Map`](xref:Xamarin.Forms.Maps.Map) Exibe um mapa. O pacote NuGet **Xamarin. Forms. Maps** deve ser instalado. Android e plataforma Universal do Windows exigem uma chave de autorização do mapa.<br /><br />[Documentação da API](xref:Xamarin.Forms.Maps.Map) / [guia](~/xamarin-forms/user-interface/map/index.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps/) | [![Exemplo de mapa](views-images/Map.png "Exemplo de mapa")](views-images/Map-Large.png#lightbox "Exemplo de mapa")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MapDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MapDemoPage.xaml) |
|     |     |

## <a name="views-that-initiate-commands"></a>Modos de exibição que iniciam comandos

### <a name="button"></a>Botão

|     |     |
| --- | --- |
| [`Button`](xref:Xamarin.Forms.Button) é um objeto retangular que exibe o texto, e que é disparado um [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) eventos quando ele é pressionado.<br /><br />[Documentação da API](xref:Xamarin.Forms.Button) / [guia](~/xamarin-forms/user-interface/button.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos/) | [![Exemplo de botão](views-images/Button.png "Exemplo de botão")](views-images/Button-Large.png#lightbox "Exemplo de botão")<br /> [O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ButtonDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml) com [de lógica](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml.cs) |
|     |     |

### <a name="imagebutton"></a>ImageButton

|     |     |
| --- | --- |
| `ImageButton` é o objeto um retangular que exibe uma imagem e que dispara um `Clicked` eventos quando ele é pressionado.<br /><br /> [Guia](~/xamarin-forms/user-interface/imagebutton.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) | [![Exemplo de ImageButton](views-images/ImageButton.png "Exemplo de ImageButton")](views-images/ImageButton-Large.png#lightbox "Exemplo de ImageButton")<br /> [O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageButtonDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml) com [de lógica](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml.cs) |
|     |     |

### <a name="refreshview"></a>RefreshView

|     |     |
| --- | --- |
| `RefreshView` é um controle de contêiner que fornece a funcionalidade de pull para atualização para conteúdo rolável. O `ICommand` definido pela propriedade `Command` é executado quando uma atualização é disparada e a propriedade `IsRefreshing` indica o estado atual do controle.<br /><br /> [Guia](~/xamarin-forms/user-interface/refreshview.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) | [![Exemplo de RefreshView](views-images/RefreshView.png "Exemplo de RefreshView")](views-images/RefreshView-Large.png#lightbox "Exemplo de RefreshView")<br /> [O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RefreshViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RefreshViewDemoPage.xaml) com [de lógica](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RefreshViewDemoPage.xaml.cs) |
|     |     |

### <a name="searchbar"></a>SearchBar

|     |     |
| --- | --- |
| [`SearchBar`](xref:Xamarin.Forms.SearchBar) Exibe uma área para o usuário digite uma cadeia de caracteres de texto e um botão (ou uma tecla do teclado) que sinaliza o aplicativo para realizar uma pesquisa. O [ `Text` ](xref:Xamarin.Forms.SearchBar.Text) propriedade fornece acesso ao texto e o [ `SearchButtonPressed` ](xref:Xamarin.Forms.SearchBar.SearchButtonPressed) evento indica que o botão foi pressionado.<br /><br />[Documentação da API](xref:Xamarin.Forms.SearchBar) / [guia](~/xamarin-forms/user-interface/searchbar.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/) | [![Exemplo de SearchBar](views-images/SearchBar.png "Exemplo de SearchBar")](views-images/SearchBar-Large.png#lightbox "Exemplo de SearchBar")<br /> [O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SearchBarDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml) com [de lógica](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml.cs) |
|     |     |

### <a name="swipeview"></a>SwipeView

|     |     |
| --- | --- |
| `SwipeView` é um controle de contêiner que envolve um item de conteúdo e fornece itens de menu de contexto que são revelados por um gesto de passar o dedo. Cada item de menu é representado por um `SwipeItem`, que tem uma propriedade `Command` que executa uma `ICommand` quando o item é tocado.<br /><br /> [Guia](~/xamarin-forms/user-interface/swipeview.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) | [![Exemplo de SwipeView](views-images/SwipeView.png "Exemplo de SwipeView")](views-images/SwipeView-Large.png#lightbox "Exemplo de SwipeView")<br /> [O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwipeViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwipeViewDemoPage.xaml) com [de lógica](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwipeViewDemoPage.xaml.cs) |
|     |     |

## <a name="views-for-setting-values"></a>Modos de exibição para definir valores

### <a name="checkbox"></a>CheckBox

|     |     |
| --- | --- |
| `CheckBox` permite ao usuário selecionar um valor booliano usando um tipo de botão que pode ser marcado ou vazio. A propriedade `IsChecked` é o estado da `CheckBox`e o evento `CheckedChanged` é acionado quando o estado é alterado.<br /><br />Documentação/ [guia](~/xamarin-forms/user-interface/checkbox.md) da API / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos) | [![Exemplo da caixa de seleção](views-images/CheckBox.png "Exemplo da caixa de seleção")](views-images/CheckBox-Large.png#lightbox "Exemplo da caixa de seleção")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CheckBoxDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CheckBoxDemoPage.xaml) |
|     |     |

### <a name="slider"></a>Controle Deslizante

|     |     |
| --- | --- |
| [`Slider`](xref:Xamarin.Forms.Slider) permite que o usuário selecione uma `double` valor de um intervalo contínuo especificado com o [ `Minimum` ](xref:Xamarin.Forms.Slider.Minimum) e [ `Maximum` ](xref:Xamarin.Forms.Slider.Maximum) propriedades.<br /><br />[Documentação da API](xref:Xamarin.Forms.Slider) / [guia](~/xamarin-forms/user-interface/slider.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) | [![Exemplo de Slider](views-images/Slider.png "Exemplo de Slider")](views-images/Slider-Large.png#lightbox "Exemplo de Slider")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SliderDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SliderDemoPage.xaml) |
|     |     |

### <a name="stepper"></a>Passador

|     |     |
| --- | --- |
| [`Stepper`](xref:Xamarin.Forms.Stepper) permite que o usuário selecione uma `double` valor de um intervalo de valores incrementais especificado com o [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum), [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum), e [ `Increment` ](xref:Xamarin.Forms.Stepper.Increment) propriedades.<br /><br />[Documentação da API](xref:Xamarin.Forms.Stepper)  / [guia](~/xamarin-forms/user-interface/stepper.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos) | [![Exemplo de stepper](views-images/Stepper.png "Exemplo de stepper")](views-images/Stepper-Large.png#lightbox "Exemplo de stepper")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StepperDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StepperDemoPage.xaml) |
|     |     |

### <a name="switch"></a>Alternar

|     |     |
| --- | --- |
| [`Switch`](xref:Xamarin.Forms.Switch) assume a forma de uma chave liga/desliga para permitir que o usuário selecione um valor booliano. O [ `IsToggled` ](xref:Xamarin.Forms.Switch.IsToggled) propriedade é o estado do comutador e o [ `Toggled` ](xref:Xamarin.Forms.Switch.Toggled) evento é acionado quando o estado é alterado.<br /><br />[Documentação da API](xref:Xamarin.Forms.Switch) / [guia](~/xamarin-forms/user-interface/switch.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/) | [![Exemplo de switch](views-images/Switch.png "Exemplo de switch")](views-images/Switch-Large.png#lightbox "Exemplo de switch")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchDemoPage.xaml) |
|     |     |

### <a name="datepicker"></a>DatePicker

|     |     |
| --- | --- |
| [`DatePicker`](xref:Xamarin.Forms.DatePicker) permite que o usuário selecione uma data com o seletor de datas da plataforma. Definir um intervalo de datas permitidos com o [ `MinimumDate` ](xref:Xamarin.Forms.DatePicker.MinimumDate) e [ `MaximumDate` ](xref:Xamarin.Forms.DatePicker.MaximumDate) propriedades. O [ `Date` ](xref:Xamarin.Forms.DatePicker.Date) propriedade é a data selecionada e o [ `DateSelected` ](xref:Xamarin.Forms.DatePicker.DateSelected) evento é disparado quando essa propriedade é alterado.<br /><br />[Documentação da API](xref:Xamarin.Forms.DatePicker) / [guia](~/xamarin-forms/user-interface/datepicker.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker) | [![Exemplo de DatePicker](views-images/DatePicker.png "Exemplo de DatePicker")](views-images/DatePicker-Large.png#lightbox "Exemplo de DatePicker")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/DatePickerDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/DatePickerDemoPage.xaml) |
|     |     |

### <a name="timepicker"></a>TimePicker

|     |     |
| --- | --- |
| [`TimePicker`](xref:Xamarin.Forms.TimePicker) permite que o usuário selecione uma hora com o seletor de tempo de plataforma. O [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) propriedade é o tempo selecionado. Um aplicativo pode monitorar as alterações na `Time` propriedade ao instalar um manipulador para o [ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged) eventos.<br /><br />[Documentação da API](xref:Xamarin.Forms.TimePicker) / [guia](~/xamarin-forms/user-interface/timepicker.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker) | [![Exemplo de timeseparar](views-images/TimePicker.png "Exemplo de timeseparar")](views-images/TimePicker-Large.png#lightbox "Exemplo de timeseparar")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TimePickerDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TimePickerDemoPage.xaml) |
|     |     |

## <a name="views-for-editing-text"></a>Modos de exibição de edição de texto

Essas duas classes derivam a [ `InputView` ](xref:Xamarin.Forms.InputView) classe, que define os [ `Keyboard` ](xref:Xamarin.Forms.InputView.Keyboard) propriedade.

### <a name="entry"></a>Entrada

|     |     |
| --- | --- |
| [`Entry`](xref:Xamarin.Forms.Entry) permite ao usuário inserir e editar uma única linha de texto. O texto está disponível como a [ `Text` ](xref:Xamarin.Forms.Entry.Text) propriedade e o [ `TextChanged` ](xref:Xamarin.Forms.Entry.TextChanged) e [ `Completed` ](xref:Xamarin.Forms.Entry.Completed) os eventos são disparados quando as alterações de texto ou o usuário sinaliza a conclusão, tocando a tecla enter.<br /><br />Use uma [ `Editor` ](#editor) para inserir e editar várias linhas de texto.<br /><br />[Documentação da API](xref:Xamarin.Forms.Entry) / [guia](~/xamarin-forms/user-interface/text/entry.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![Exemplo de entrada](views-images/Entry.png "Exemplo de entrada")](views-images/Entry-Large.png#lightbox "Exemplo de entrada")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryDemoPage.xaml) |
|     |     |

### <a name="editor"></a>Editor

|     |     |
| --- | --- |
| [`Editor`](xref:Xamarin.Forms.Editor) permite ao usuário inserir e editar várias linhas de texto. O texto está disponível como a [ `Text` ](xref:Xamarin.Forms.Editor.Text) propriedade e o [ `TextChanged` ](xref:Xamarin.Forms.Editor.TextChanged) e [ `Completed` ](xref:Xamarin.Forms.Editor.Completed) os eventos são disparados quando as alterações de texto ou o usuário sinaliza a conclusão.<br /><br />Use uma [ `Entry` ](#entry) modo de exibição para inserir e editar uma única linha de texto.<br /><br />[Documentação da API](xref:Xamarin.Forms.Editor) / [guia](~/xamarin-forms/user-interface/text/editor.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![Exemplo de entrada](views-images/Editor.png "Exemplo de editor")](views-images/Editor-Large.png#lightbox "Exemplo de editor")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EditorDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EditorDemoPage.xaml) |
|     |     |

## <a name="views-to-indicate-activity"></a>Modos de exibição para indicar a atividade

### <a name="activityindicator"></a>ActivityIndicator

|     |     |
| --- | --- |
| [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) usa uma animação para mostrar que o aplicativo está envolvido em uma atividade demorada sem fornecer nenhuma indicação de progresso. O [ `IsRunning` ](xref:Xamarin.Forms.ActivityIndicator.IsRunning) propriedade controla a animação.<br /><br />Se o progresso da atividade for conhecido, use uma [ `ProgressBar` ](#progressbar) em vez disso.<br /><br />[Documentação da API](xref:Xamarin.Forms.ActivityIndicator) / [guia](~/xamarin-forms/user-interface/activityindicator.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/) | [![Exemplo de ActivityIndicator](views-images/ActivityIndicator.png "Exemplo de ActivityIndicator")](views-images/ActivityIndicator-Large.png#lightbox "Exemplo de ActivityIndicator")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ActivityIndicatorDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ActivityIndicatorDemoPage.xaml) |
|     |     |

### <a name="progressbar"></a>ProgressBar

|     |     |
| --- | --- |
| [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) usa uma animação para mostrar que o aplicativo está em andamento através de uma atividade demorada. Defina as [ `Progress` ](xref:Xamarin.Forms.ProgressBar.Progress) propriedade para valores entre 0 e 1 para indicar o progresso.<br /><br />Se o progresso da atividade não for conhecido, use uma [ `ActivityIndicator` ](#activityindicator) em vez disso.<br /><br />[Documentação da API](xref:Xamarin.Forms.ProgressBar) / [guia](~/xamarin-forms/user-interface/progressbar.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/) | [![Exemplo de ProgressBar](views-images/ProgressBar.png "Exemplo de ProgressBar")](views-images/ProgressBar-Large.png#lightbox "Exemplo de ProgressBar")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ProgressBarDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml) com [de lógica](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml.cs) |
|     |     |

## <a name="views-that-display-collections"></a>Exibições que mostram coleções

### <a name="carouselview"></a>CarouselView

|     |     |
| --- | --- |
| [`CarouselView`](xref:Xamarin.Forms.CarouselView) exibe uma lista rolável de itens de dados. Defina a propriedade `ItemsSource` como uma coleção de objetos e defina a propriedade `ItemTemplate` como um objeto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) descrevendo como os itens devem ser formatados. O evento `CurrentItemChanged` sinaliza que o item exibido no momento foi alterado, que está disponível como a propriedade `CurrentItem`.<br /><br />[Guia](~/xamarin-forms/user-interface/carouselview/index.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/) | [![Exemplo de CarouselView](views-images/CarouselView.png "Exemplo de CarouselView")](views-images/CarouselView-Large.png#lightbox "Exemplo de CarouselView")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselViewDemoPage.xaml) |
|     |     |

### <a name="collectionview"></a>CollectionView

|     |     |
| --- | --- |
| [`CollectionView`](xref:Xamarin.Forms.CollectionView) exibe uma lista rolável de itens de dados selecionáveis, usando especificações de layout diferentes. Ele visa fornecer uma alternativa mais flexível e de alto desempenho para [`ListView`](xref:Xamarin.Forms.ListView). Defina a propriedade `ItemsSource` como uma coleção de objetos e defina a propriedade `ItemTemplate` como um objeto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) descrevendo como os itens devem ser formatados. O evento `SelectionChanged` sinaliza que uma seleção foi feita, que está disponível como a propriedade `SelectedItem`.<br /><br />[Guia](~/xamarin-forms/user-interface/collectionview/index.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/) | [![Exemplo de CollectionView](views-images/CollectionView.png "Exemplo de CollectionView")](views-images/CollectionView-Large.png#lightbox "Exemplo de CollectionView")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CollectionViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CollectionViewDemoPage.xaml) |
|     |     |

### <a name="indicatorview"></a>IndicatorView

|     |     |
| --- | --- |
| `IndicatorView` exibe os indicadores que representam o número de itens em um `CarouselView`. Defina a propriedade `ItemsSourceBy` para o objeto `CarouselView` para exibir os indicadores. <br /><br />[Guia](~/xamarin-forms/user-interface/indicatorview.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/) | [![Exemplo de IndicatorView](views-images/IndicatorView.png "Exemplo de IndicatorView")](views-images/IndicatorView-Large.png#lightbox "Exemplo de IndicatorView")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/IndicatorViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/IndicatorViewDemoPage.xaml) |
|     |     |

### <a name="listview"></a>ListView

|     |     |
| --- | --- |
| [`ListView`](xref:Xamarin.Forms.ListView) deriva [ `ItemsView` ](xref:Xamarin.Forms.ItemsView`1) e exibe uma lista rolável de itens de dados podem ser selecionados. Definir a [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propriedade a uma coleção de objetos e defina o [ `ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) propriedade para um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) objeto que descreve como os itens são a ser formatado. O [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) evento sinaliza que uma seleção foi feita, que está disponível como a [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) propriedade.<br /><br />[Documentação da API](xref:Xamarin.Forms.ListView) / [guia](~/xamarin-forms/user-interface/listview/index.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview/) | [![Exemplo de ListView](views-images/ListView.png "Exemplo de ListView")](views-images/ListView-Large.png#lightbox "Exemplo de ListView")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ListViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ListViewDemoPage.xaml) |
|     |     |

### <a name="picker"></a>Seletor

|     |     |
| --- | --- |
| [`Picker`](xref:Xamarin.Forms.Picker) Exibe um item selecionado em uma lista de cadeias de caracteres de texto e permite selecionar esse item quando o modo de exibição é tocado. Defina a [ `Items` ](xref:Xamarin.Forms.Picker.Items) propriedade a uma lista de cadeias de caracteres ou o [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) propriedade a uma coleção de objetos. O [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento é disparado quando um item é selecionado.<br /><br />O `Picker` exibe a lista de itens somente quando ele é selecionado. Use uma [ `ListView` ](#listview) ou [ `TableView` ](#tableview) para obter uma lista rolável que permanece na página.<br /><br />[Documentação da API](xref:Xamarin.Forms.Picker) / [guia](~/xamarin-forms/user-interface/picker/index.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo) | [![Exemplo de seletor](views-images/Picker.png "Exemplo de seletor")](views-images/Picker-Large.png#lightbox "Exemplo de seletor")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/PickerDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml) com [de lógica](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml.cs) |
|     |     |

### <a name="tableview"></a>TableView

|     |     |
| --- | --- |
| [`TableView`](xref:Xamarin.Forms.TableView) Exibe uma lista de linhas do tipo [ `Cell` ](xref:Xamarin.Forms.Cell) com cabeçalhos opcionais e subcabeçalhos. Defina as [ `Root` ](xref:Xamarin.Forms.TableView.Root) propriedade para um objeto do tipo [ `TableRoot` ](xref:Xamarin.Forms.TableRoot)e adicione [ `TableSection` ](xref:Xamarin.Forms.TableSection) objetos para que `TableRoot`. Cada `TableSection` é uma coleção de `Cell` objetos.<br /><br />[Documentação da API](xref:Xamarin.Forms.TableView) / [guia](~/xamarin-forms/user-interface/tableview.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview) | [![Exemplo de TableView](views-images/TableView.png "Exemplo de TableView")](views-images/TableView-Large.png#lightbox "Exemplo de TableView")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TableViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TableViewDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Links Relacionados

- [Exemplo de xamarin. Forms FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Exemplos do Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Documentação da API do Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
