---
title: Modos de exibição do xamarin. Forms
description: Modos de exibição do xamarin. Forms são os blocos de construção de interfaces de usuário móvel de plataforma cruzada. Este artigo lista as exibições que estão incluídas no xamarin. Forms.
ms.prod: xamarin
ms.assetid: AC070686-A423-4A98-8BB6-0B9F94C062CC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/21/2019
ms.openlocfilehash: 5b2e58901d4a850863f68b26ce41e1aa4e8daee4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61358732"
---
# <a name="xamarinforms-views"></a>Modos de exibição do xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/FormsGallery/)

_Modos de exibição do xamarin. Forms são os blocos de construção de interfaces de usuário móvel de plataforma cruzada._

Modos de exibição são objetos de interface do usuário, como rótulos, botões e controles deslizantes que são normalmente conhecidos como *controles* ou *widgets* em outros ambientes de programação gráficas. As exibições compatíveis com o xamarin. Forms todos derivam de [ `View` ](xref:Xamarin.Forms.View) classe. Eles podem ser divididos em várias categorias:

## <a name="views-for-presentation"></a>Modos de exibição para apresentação

### <a name="label"></a>Rotular

|     |     |
| --- | --- |
| [`Label`](xref:Xamarin.Forms.Label) Exibe as cadeias de caracteres de texto de linha única ou blocos de várias linhas de texto, com formatação constante ou variável. Defina a [ `Text` ](xref:Xamarin.Forms.Label.Text) propriedade como uma cadeia de caracteres de constante de formatação, ou um conjunto a [ `FormattedText` ](xref:Xamarin.Forms.Label.FormattedText) propriedade para um [ `FormattedString` ](xref:Xamarin.Forms.FormattedString) objeto variável formatação.<br /><br />[Documentação da API](xref:Xamarin.Forms.Label) / [guia](~/xamarin-forms/user-interface/text/label.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text/) | [![Exemplo de rótulo](views-images/Label.png "exemplo de rótulo")](views-images/Label-Large.png#lightbox "exemplo de rótulo")<br /> [O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/LabelDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/LabelDemoPage.xaml) |
|     |     |

### <a name="image"></a>Image

|     |     |
| --- | --- |
| [`Image`](xref:Xamarin.Forms.Image) Exibe um bitmap. Bitmaps podem ser baixados pela Web, inseridos como recursos no projeto comum ou projetos de plataforma ou criado usando um .NET `Stream` objeto.<br /><br />[Documentação da API](xref:Xamarin.Forms.Image) / [guia](~/xamarin-forms/user-interface/images.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithImages/) | [![Exemplo de imagem](views-images/Image.png "imagem de exemplo")](views-images/Image-Large.png#lightbox "imagem de exemplo")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageDemoPage.xaml) |
|     |     |

### <a name="boxview"></a>BoxView

|     |    |
| --- | ---|
| [`BoxView`](xref:Xamarin.Forms.BoxView) Exibe um retângulo sólido colorido pela [ `Color` ](xref:Xamarin.Forms.BoxView.Color) propriedade. `BoxView` tem uma solicitação de tamanho padrão de 40 x 40. Para outros tamanhos, atribuir a [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) e [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) propriedades.<br /><br />[Documentação da API](xref:Xamarin.Forms.BoxView) / [guia](~/xamarin-forms/user-interface/boxview.md) / [exemplo 1](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BasicBoxView), [2](https://developer.xamarin.com/samples/xamarin-forms/BoxView/TextDecoration), [3](https://developer.xamarin.com/samples/xamarin-forms/BoxView/ColorListBox), [4 ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife), [5](https://developer.xamarin.com/samples/xamarin-forms/BoxView/DotMatrixClock), e [6](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock) | [![Exemplo de BoxView](views-images/BoxView.png "exemplo BoxView")](views-images/BoxView-Large.png#lightbox "BoxView exemplo")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/BoxViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/BoxViewDemoPage.xaml) |
|     |     |

### <a name="webview"></a>WebView

|     |     |
| --- | --- |
| [`WebView`](xref:Xamarin.Forms.WebView) Exibe o conteúdo, HTML ou páginas da Web com base em se a [ `Source` ](xref:Xamarin.Forms.WebView.Source) estiver definida como um [ `UriWebViewSource` ](xref:Xamarin.Forms.UrlWebViewSource) ou um [ `HtmlWebViewSource` ](xref:Xamarin.Forms.HtmlWebViewSource) objeto.<br /><br />[Documentação da API](xref:Xamarin.Forms.WebView) / [guia](~/xamarin-forms/user-interface/webview.md) / [exemplo 1](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithWebview/) e [2](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/WebView) | [![Exemplo de WebView](views-images/WebView.png "exemplo WebView")](views-images/WebView-Large.png#lightbox "exemplo WebView")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/WebViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/WebViewDemoPage.xaml) |
|     |     |

### <a name="openglview"></a>OpenGLView

|     |     |
| --- | --- |
| [`OpenGLView`](xref:Xamarin.Forms.OpenGLView) exibe gráficos OpenGL em projetos do iOS e Android. Não há nenhum suporte para a plataforma Universal do Windows. Os projetos do iOS e Android exigem uma referência para o **OpenTK 1.0** assembly ou o **OpenTK** assembly versão 1.0.0.0. `OpenGLView` é mais fácil de usar em um projeto compartilhado; Se usado em uma biblioteca .NET Standard, uma dependência de serviço também será necessária (conforme mostrado no código de exemplo).<br /><br />Esse é o recurso de apenas gráficos que é incorporado ao xamarin. Forms, mas um aplicativo xamarin. Forms também pode renderizar elementos gráficos usando [ `CocosSharp` ](~/xamarin-forms/user-interface/graphics/cocossharp.md), [ `SkiaSharp` ](~/xamarin-forms/user-interface/graphics/skiasharp/index.md), ou [ `UrhoSharp` ](~/xamarin-forms/user-interface/graphics/urhosharp.md).<br /><br />[Documentação da API](xref:Xamarin.Forms.OpenGLView)<br /><br /> | [![Exemplo de OpenGLView](views-images/OpenGLView.png "exemplo OpenGLView")](views-images/OpenGLView-Large.png#lightbox "OpenGLView exemplo")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/OpenGLViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml) com [de lógica](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml.cs) |
|     |     |

### <a name="map"></a>Mapa

|     |     |
| --- | --- |
| [`Map`](xref:Xamarin.Forms.Maps.Map) Exibe um mapa. O **Xamarin.Forms.Maps** pacote do Nuget deve ser instalado. Android e plataforma Universal do Windows exigem uma chave de autorização do mapa.<br /><br />[Documentação da API](xref:Xamarin.Forms.Maps.Map) / [guia](~/xamarin-forms/user-interface/map.md) / [exemplo](https://developer.xamarin.com/samples/WorkingWithMaps/) | [![Exemplo do mapa](views-images/Map.png "mapear exemplo")](views-images/Map-Large.png#lightbox "mapear de exemplo")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MapDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MapDemoPage.xaml) |
|     |     |

## <a name="views-that-initiate-commands"></a>Modos de exibição que iniciam comandos

### <a name="button"></a>Botão

|     |     |
| --- | --- |
| [`Button`](xref:Xamarin.Forms.Button) é um objeto retangular que exibe o texto, e que é disparado um [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) eventos quando ele é pressionado.<br /><br />[Documentação da API](xref:Xamarin.Forms.Button) / [guia](~/xamarin-forms/user-interface/button.md) / [exemplo](https://developer.xamarin.com/samples/UserInterface/ButtonDemos/) | [![Exemplo de botão](views-images/Button.png "botão exemplo")](views-images/Button-Large.png#lightbox "exemplo de botão")<br /> [O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ButtonDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml) com [de lógica](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml.cs) |
|     |     |

### <a name="imagebutton"></a>ImageButton

|     |     |
| --- | --- |
| `ImageButton` é o objeto um retangular que exibe uma imagem e que dispara um `Clicked` eventos quando ele é pressionado.<br /><br /> [Guia](~/xamarin-forms/user-interface/imagebutton.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/) | [![Exemplo de ImageButton](views-images/ImageButton.png "exemplo ImageButton")](views-images/ImageButton-Large.png#lightbox "ImageButton exemplo")<br /> [O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageButtonDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml) com [de lógica](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml.cs) |
|     |     |

### <a name="searchbar"></a>SearchBar

|     |     |
| --- | --- |
| [`SearchBar`](xref:Xamarin.Forms.SearchBar) Exibe uma área para o usuário digite uma cadeia de caracteres de texto e um botão (ou uma tecla do teclado) que sinaliza o aplicativo para realizar uma pesquisa. O [ `Text` ](xref:Xamarin.Forms.SearchBar.Text) propriedade fornece acesso ao texto e o [ `SearchButtonPressed` ](xref:Xamarin.Forms.SearchBar.SearchButtonPressed) evento indica que o botão foi pressionado.<br /><br />[Documentação da API](xref:Xamarin.Forms.SearchBar) | [![Exemplo de SearchBar](views-images/SearchBar.png "exemplo SearchBar")](views-images/SearchBar-Large.png#lightbox "SearchBar exemplo")<br /> [O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SearchBarDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml) com [de lógica](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml.cs) |
|     |     |

## <a name="views-for-setting-values"></a>Modos de exibição para definir valores

### <a name="slider"></a>Controle deslizante

|     |     |
| --- | --- |
| [`Slider`](xref:Xamarin.Forms.Slider) permite que o usuário selecione uma `double` valor de um intervalo contínuo especificado com o [ `Minimum` ](xref:Xamarin.Forms.Slider.Minimum) e [ `Maximum` ](xref:Xamarin.Forms.Slider.Maximum) propriedades.<br /><br />[Documentação da API](xref:Xamarin.Forms.Slider) / [guia](~/xamarin-forms/user-interface/slider.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos) | [![Exemplo de controle deslizante](views-images/Slider.png "exemplo de controle deslizante")](views-images/Slider-Large.png#lightbox "exemplo de controle deslizante")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SliderDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SliderDemoPage.xaml) |
|     |     |

### <a name="stepper"></a>Escalonador

|     |     |
| --- | --- |
| [`Stepper`](xref:Xamarin.Forms.Stepper) permite que o usuário selecione uma `double` valor de um intervalo de valores incrementais especificado com o [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum), [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum), e [ `Increment` ](xref:Xamarin.Forms.Stepper.Increment) propriedades.<br /><br />[Documentação da API](xref:Xamarin.Forms.Stepper)  / [guia](~/xamarin-forms/user-interface/stepper.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/StepperDemos) | [![Exemplo de escalonador](views-images/Stepper.png "exemplo escalonador")](views-images/Stepper-Large.png#lightbox "escalonador exemplo")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StepperDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StepperDemoPage.xaml) |
|     |     |

### <a name="switch"></a>Alternar

|     |     |
| --- | --- |
| [`Switch`](xref:Xamarin.Forms.Switch) assume a forma de uma chave liga/desliga para permitir que o usuário selecione um valor booliano. O [ `IsToggled` ](xref:Xamarin.Forms.Switch.IsToggled) propriedade é o estado do comutador e o [ `Toggled` ](xref:Xamarin.Forms.Switch.Toggled) evento é acionado quando o estado é alterado.<br /><br />[Documentação da API](xref:Xamarin.Forms.Switch) | [![Alternar de exemplo](views-images/Switch.png "alternar exemplo")](views-images/Switch-Large.png#lightbox "alternar de exemplo")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchDemoPage.xaml) |
|     |     |

### <a name="datepicker"></a>DatePicker

|     |     |
| --- | --- |
| [`DatePicker`](xref:Xamarin.Forms.DatePicker) permite que o usuário selecione uma data com o seletor de datas da plataforma. Definir um intervalo de datas permitidos com o [ `MinimumDate` ](xref:Xamarin.Forms.DatePicker.MinimumDate) e [ `MaximumDate` ](xref:Xamarin.Forms.DatePicker.MaximumDate) propriedades. O [ `Date` ](xref:Xamarin.Forms.DatePicker.Date) propriedade é a data selecionada e o [ `DateSelected` ](xref:Xamarin.Forms.DatePicker.DateSelected) evento é disparado quando essa propriedade é alterado.<br /><br />[Documentação da API](xref:Xamarin.Forms.DatePicker) / [guia](~/xamarin-forms/user-interface/datepicker.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/DatePicker) | [![Exemplo de DatePicker](views-images/DatePicker.png "exemplo de DatePicker")](views-images/DatePicker-Large.png#lightbox "exemplo de DatePicker")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/DatePickerDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/DatePickerDemoPage.xaml) |
|     |     |

### <a name="timepicker"></a>TimePicker

|     |     |
| --- | --- |
| [`TimePicker`](xref:Xamarin.Forms.TimePicker) permite que o usuário selecione uma hora com o seletor de tempo de plataforma. O [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) propriedade é o tempo selecionado. Um aplicativo pode monitorar as alterações na `Time` propriedade ao instalar um manipulador para o [ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged) eventos.<br /><br />[Documentação da API](xref:Xamarin.Forms.TimePicker) / [guia](~/xamarin-forms/user-interface/timepicker.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TimePicker) | [![Exemplo de TimePicker](views-images/TimePicker.png "exemplo TimePicker")](views-images/TimePicker-Large.png#lightbox "TimePicker exemplo")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TimePickerDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TimePickerDemoPage.xaml) |
|     |     |

## <a name="views-for-editing-text"></a>Modos de exibição de edição de texto

Essas duas classes derivam a [ `InputView` ](xref:Xamarin.Forms.InputView) classe, que define os [ `Keyboard` ](xref:Xamarin.Forms.InputView.Keyboard) propriedade.

<a name="entry" />

### <a name="entry"></a>Entrada

|     |     |
| --- | --- |
| [`Entry`](xref:Xamarin.Forms.Entry) permite ao usuário inserir e editar uma única linha de texto. O texto está disponível como a [ `Text` ](xref:Xamarin.Forms.Entry.Text) propriedade e o [ `TextChanged` ](xref:Xamarin.Forms.Entry.TextChanged) e [ `Completed` ](xref:Xamarin.Forms.Entry.Completed) os eventos são disparados quando as alterações de texto ou o usuário sinaliza a conclusão, tocando a tecla enter.<br /><br />Use uma [ `Editor` ](#editor) para inserir e editar várias linhas de texto.<br /><br />[Documentação da API](xref:Xamarin.Forms.Entry) / [guia](~/xamarin-forms/user-interface/text/entry.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text) | [![Exemplo de entrada](views-images/Entry.png "entrada de exemplo")](views-images/Entry-Large.png#lightbox "exemplo de entrada")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryDemoPage.xaml) |
|     |     |

<a name="editor" />

### <a name="editor"></a>Editor

|     |     |
| --- | --- |
| [`Editor`](xref:Xamarin.Forms.Editor) permite ao usuário inserir e editar várias linhas de texto. O texto está disponível como a [ `Text` ](xref:Xamarin.Forms.Editor.Text) propriedade e o [ `TextChanged` ](xref:Xamarin.Forms.Editor.TextChanged) e [ `Completed` ](xref:Xamarin.Forms.Editor.Completed) os eventos são disparados quando as alterações de texto ou o usuário sinaliza a conclusão.<br /><br />Use uma [ `Entry` ](#entry) modo de exibição para inserir e editar uma única linha de texto.<br /><br />[Documentação da API](xref:Xamarin.Forms.Editor) / [guia](~/xamarin-forms/user-interface/text/editor.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text) | [![Exemplo de entrada](views-images/Editor.png "Editor de exemplo")](views-images/Editor-Large.png#lightbox "Editor de exemplo")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EditorDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EditorDemoPage.xaml) |
|     |     |

## <a name="views-to-indicate-activity"></a>Modos de exibição para indicar a atividade

<a name="activityindicator" />

### <a name="activityindicator"></a>ActivityIndicator

|     |     |
| --- | --- |
| [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) usa uma animação para mostrar que o aplicativo está envolvido em uma atividade demorada sem fornecer nenhuma indicação de progresso. O [ `IsRunning` ](xref:Xamarin.Forms.ActivityIndicator.IsRunning) propriedade controla a animação.<br /><br />Se o progresso da atividade for conhecido, use uma [ `ProgressBar` ](#progressbar) em vez disso.<br /><br />[Documentação da API](xref:Xamarin.Forms.ActivityIndicator) | [![Exemplo de ActivityIndicator](views-images/ActivityIndicator.png "exemplo ActivityIndicator")](views-images/ActivityIndicator-Large.png#lightbox "ActivityIndicator exemplo")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ActivityIndicatorDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ActivityIndicatorDemoPage.xaml) |
|     |     |

<a name="progressbar" />

### <a name="progressbar"></a>ProgressBar

|     |     |
| --- | --- |
| [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) usa uma animação para mostrar que o aplicativo está em andamento através de uma atividade demorada. Defina as [ `Progress` ](xref:Xamarin.Forms.ProgressBar.Progress) propriedade para valores entre 0 e 1 para indicar o progresso.<br /><br />Se o progresso da atividade não for conhecido, use uma [ `ActivityIndicator` ](#activityindicator) em vez disso.<br /><br />[Documentação da API](xref:Xamarin.Forms.ProgressBar) | [![Exemplo de ProgressBar](views-images/ProgressBar.png "exemplo ProgressBar")](views-images/ProgressBar-Large.png#lightbox "exemplo ProgressBar")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ProgressBarDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml) com [de lógica](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml.cs) |
|     |     |

## <a name="views-that-display-collections"></a>Exibições que mostram coleções

### <a name="collectionview"></a>CollectionView

|     |     |
| --- | --- |
| `CollectionView` Exibe uma lista rolável de itens selecionáveis de dados, usando as especificações de layout diferente. Tem como objetivo fornecer uma mais flexível e alternativa de alto desempenho para o [ `ListView` ](xref:Xamarin.Forms.ListView). Definir a `ItemsSource` propriedade a uma coleção de objetos e defina o `ItemTemplate` propriedade para um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) objeto que descreve como os itens devem ser formatados. O `SelectionChanged` evento sinaliza que uma seleção foi feita, que está disponível como a `SelectedItem` propriedade.<br /><br />[Guia](~/xamarin-forms/user-interface/collectionview/index.md) / [exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/) | [![Exemplo de CollectionView](views-images/CollectionView.png "exemplo CollectionView")](views-images/CollectionView-Large.png#lightbox "CollectionView de exemplo")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/forms40/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CollectionViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/forms40/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CollectionViewDemoPage.xaml) |
|     |     |

<a name="listView" />

### <a name="listview"></a>ListView

|     |     |
| --- | --- |
| [`ListView`](xref:Xamarin.Forms.ListView) deriva [ `ItemsView` ](xref:Xamarin.Forms.ItemsView`1) e exibe uma lista rolável de itens de dados podem ser selecionados. Definir a [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propriedade a uma coleção de objetos e defina o [ `ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) propriedade para um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) objeto que descreve como os itens são a ser formatado. O [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) evento sinaliza que uma seleção foi feita, que está disponível como a [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) propriedade.<br /><br />[Documentação da API](xref:Xamarin.Forms.ListView) / [guia](~/xamarin-forms/user-interface/listview/index.md) / [exemplo](https://developer.xamarin.com/samples/WorkingWithListview) | [![exemplo ListView](views-images/ListView.png "Exemplo de ListView")](views-images/ListView-Large.png#lightbox "exemplo ListView")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ListViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ListViewDemoPage.xaml) |
|     |     |

### <a name="picker"></a>Seletor

|     |     |
| --- | --- |
| [`Picker`](xref:Xamarin.Forms.Picker) Exibe um item selecionado em uma lista de cadeias de caracteres de texto e permite selecionar esse item quando o modo de exibição é tocado. Defina a [ `Items` ](xref:Xamarin.Forms.Picker.Items) propriedade a uma lista de cadeias de caracteres ou o [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) propriedade a uma coleção de objetos. O [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento é disparado quando um item é selecionado.<br /><br />O `Picker` exibe a lista de itens somente quando ele é selecionado. Use uma [ `ListView` ](#listView) ou [ `TableView` ](#tableView) para obter uma lista rolável que permanece na página.<br /><br />[Documentação da API](xref:Xamarin.Forms.Picker) / [guia](~/xamarin-forms/user-interface/picker/index.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PickerDemo/) | [![Exemplo de seletor](views-images/Picker.png "exemplo seletor")](views-images/Picker-Large.png#lightbox "selecionador de exemplo")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/PickerDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml) com [de lógica](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml.cs) |
|     |     |

<a name="tableView" />

### <a name="tableview"></a>Modo de tabela

|     |     |
| --- | --- |
| [`TableView`](xref:Xamarin.Forms.TableView) Exibe uma lista de linhas do tipo [ `Cell` ](xref:Xamarin.Forms.Cell) com cabeçalhos opcionais e subcabeçalhos. Defina as [ `Root` ](xref:Xamarin.Forms.TableView.Root) propriedade para um objeto do tipo [ `TableRoot` ](xref:Xamarin.Forms.TableRoot)e adicione [ `TableSection` ](xref:Xamarin.Forms.TableSection) objetos para que `TableRoot`. Cada `TableSection` é uma coleção de `Cell` objetos.<br /><br />[Documentação da API](xref:Xamarin.Forms.TableView) / [guia](~/xamarin-forms/user-interface/tableview.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TableView) | [![Exemplo de modo de tabela](views-images/TableView.png "modo de tabela de exemplo")](views-images/TableView-Large.png#lightbox "exemplo de modo de tabela")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TableViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TableViewDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Links relacionados

- [Exemplo de xamarin. Forms FormsGallery](https://developer.xamarin.com/samples/FormsGallery/)
- [Amostras do Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Documentação da API do Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
