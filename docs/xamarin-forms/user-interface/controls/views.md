---
title: Exibições do Xamarin. Forms
description: As exibições do Xamarin. Forms são os blocos de construção de interfaces de usuário móvel de plataforma cruzada. Este artigo lista as exibições incluídas no Xamarin. Forms.
ms.prod: xamarin
ms.assetid: AC070686-A423-4A98-8BB6-0B9F94C062CC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/11/2019
ms.openlocfilehash: bc46d9ee083cca7eacc354846eec6e582070bb9e
ms.sourcegitcommit: e71474f91639bb43159b22f5d534325c3270ba93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72749829"
---
# <a name="xamarinforms-views"></a>Exibições do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_As exibições do Xamarin. Forms são os blocos de construção de interfaces de usuário móvel de plataforma cruzada._

As exibições são objetos de interface do usuário, como rótulos, botões e controles deslizantes que normalmente são conhecidos como *controles* ou *widgets* em outros ambientes de programação gráfica. As exibições suportadas pelo Xamarin. Forms derivam da classe [`View`](xref:Xamarin.Forms.View) . Eles podem ser divididos em várias categorias:

## <a name="views-for-presentation"></a>Exibições para apresentação

### <a name="label"></a>Rotular

|     |     |
| --- | --- |
| [`Label`](xref:Xamarin.Forms.Label) exibe cadeias de texto de linha única ou blocos de texto de várias linhas, com a formatação constante ou variável. Defina a propriedade [`Text`](xref:Xamarin.Forms.Label.Text) como uma cadeia de caracteres para formatação constante ou defina a propriedade [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) como um objeto [`FormattedString`](xref:Xamarin.Forms.FormattedString) para formatação de variável.<br /><br />[Documentação da API](xref:Xamarin.Forms.Label)  / [guia](~/xamarin-forms/user-interface/text/label.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![Exemplo de rótulo](views-images/Label.png "Exemplo de rótulo")](views-images/Label-Large.png#lightbox "Exemplo de rótulo")<br /> código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/LabelDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/LabelDemoPage.cs) |
|     |     |

### <a name="image"></a>Image

|     |     |
| --- | --- |
| [`Image`](xref:Xamarin.Forms.Image) exibe um bitmap. Os bitmaps podem ser baixados pela Web, inseridos como recursos no projeto comum ou projetos de plataforma, ou criados com o uso de um objeto .NET `Stream`.<br /><br />[Documentação da API](xref:Xamarin.Forms.Image)  / [guia](~/xamarin-forms/user-interface/images.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages) | [![Exemplo de imagem](views-images/Image.png "Exemplo de imagem")](views-images/Image-Large.png#lightbox "Exemplo de imagem")<br />código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageDemoPage.cs) |
|     |     |

### <a name="boxview"></a>BoxView

|     |    |
| --- | ---|
| [`BoxView`](xref:Xamarin.Forms.BoxView) exibe um retângulo sólido colorido pela propriedade [`Color`](xref:Xamarin.Forms.BoxView.Color) . `BoxView` tem uma solicitação de tamanho padrão de 40x40. Para outros tamanhos, atribua as propriedades [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) .<br /><br />[Documentação da API](xref:Xamarin.Forms.BoxView)  / [guia](~/xamarin-forms/user-interface/boxview.md)  / [amostra 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview), [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-textdecoration), [3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors/), [4](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife), [5](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock)e [6](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock) | [![Exemplo de BoxView](views-images/BoxView.png "Exemplo de BoxView")](views-images/BoxView-Large.png#lightbox "Exemplo de BoxView")<br />código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/BoxViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/BoxViewDemoPage.cs) |
|     |     |

### <a name="webview"></a>WebView

|     |     |
| --- | --- |
| [`WebView`](xref:Xamarin.Forms.WebView) exibe páginas da Web ou conteúdo HTML, com base em se a propriedade [`Source`](xref:Xamarin.Forms.WebView.Source) está definida como um [`UriWebViewSource`](xref:Xamarin.Forms.UrlWebViewSource) ou um objeto [`HtmlWebViewSource`](xref:Xamarin.Forms.HtmlWebViewSource) .<br /><br />[Documentação da API](xref:Xamarin.Forms.WebView)  / [guia](~/xamarin-forms/user-interface/webview.md)  / [amostra 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview) e [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview) | [![Exemplo de WebView](views-images/WebView.png "Exemplo de WebView")](views-images/WebView-Large.png#lightbox "Exemplo de WebView")<br />código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/WebViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/WebViewDemoPage.cs) |
|     |     |

### <a name="openglview"></a>OpenGLView

|     |     |
| --- | --- |
| [`OpenGLView`](xref:Xamarin.Forms.OpenGLView) exibe gráficos OpenGL em projetos Ios e Android. Não há suporte para o Plataforma Universal do Windows. Os projetos iOS e Android exigem uma referência ao assembly **OpenTK-1,0** ou ao assembly da versão 1.0.0.0 do **OpenTK** . `OpenGLView` é mais fácil de usar em um projeto compartilhado; Se usado em uma biblioteca de .NET Standard, um serviço de dependência também será necessário (conforme mostrado no código de exemplo).<br /><br />Esse é o único recurso de gráficos que é criado no Xamarin. Forms, mas um aplicativo Xamarin. Forms também pode renderizar gráficos usando [`SkiaSharp`](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)ou [`UrhoSharp`](~/xamarin-forms/user-interface/graphics/urhosharp.md).<br /><br />[Documentação da API](xref:Xamarin.Forms.OpenGLView)<br /><br /> | [![Exemplo de OpenGLView](views-images/OpenGLView.png "Exemplo de OpenGLView")](views-images/OpenGLView-Large.png#lightbox "Exemplo de OpenGLView")<br />código para esta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml) com [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/OpenGLViewDemoPage.cs) |
|     |     |

### <a name="map"></a>Mapa

|     |     |
| --- | --- |
| [`Map`](xref:Xamarin.Forms.Maps.Map) exibe um mapa. O pacote NuGet **Xamarin. Forms. Maps** deve ser instalado. O Android e o Plataforma Universal do Windows exigem uma chave de autorização de mapa.<br /><br />[Documentação da API](xref:Xamarin.Forms.Maps.Map)  / [guia](~/xamarin-forms/user-interface/map/index.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps/) | [![Exemplo de mapa](views-images/Map.png "Exemplo de mapa")](views-images/Map-Large.png#lightbox "Exemplo de mapa")<br />código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MapDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MapDemoPage.cs) |
|     |     |

## <a name="views-that-initiate-commands"></a>Exibições que iniciam comandos

### <a name="button"></a>Botão

|     |     |
| --- | --- |
| [`Button`](xref:Xamarin.Forms.Button) é um objeto retangular que exibe texto e que dispara um evento de [`Clicked`](xref:Xamarin.Forms.Button.Clicked) quando ele é pressionado.<br /><br />[Documentação da API](xref:Xamarin.Forms.Button)  / [guia](~/xamarin-forms/user-interface/button.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos/) | [![Exemplo de botão](views-images/Button.png "Exemplo de botão")](views-images/Button-Large.png#lightbox "Exemplo de botão")<br /> código para esta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml) com [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ButtonDemoPage.cs) |
|     |     |

### <a name="imagebutton"></a>ImageButton

|     |     |
| --- | --- |
| `ImageButton` é um objeto retangular que exibe uma imagem e que dispara um evento de `Clicked` quando ele é pressionado.<br /><br /> [Guia](~/xamarin-forms/user-interface/imagebutton.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) | [![Exemplo de ImageButton](views-images/ImageButton.png "Exemplo de ImageButton")](views-images/ImageButton-Large.png#lightbox "Exemplo de ImageButton")<br /> código para esta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml) com [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageButtonDemoPage.cs) |
|     |     |

### <a name="refreshview"></a>RefreshView

|     |     |
| --- | --- |
| `RefreshView` é um controle de contêiner que fornece a funcionalidade de pull para atualização para conteúdo rolável. O `ICommand` definido pela propriedade `Command` é executado quando uma atualização é disparada e a propriedade `IsRefreshing` indica o estado atual do controle.<br /><br /> [Guia](~/xamarin-forms/user-interface/refreshview.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) | [![Exemplo de RefreshView](views-images/RefreshView.png "Exemplo de RefreshView")](views-images/RefreshView-Large.png#lightbox "RefreshView Example")<br /> código para esta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RefreshViewDemoPage.xaml) com [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RefreshViewDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RefreshViewDemoPage.cs) |
|     |     |

### <a name="searchbar"></a>SearchBar

|     |     |
| --- | --- |
| [`SearchBar`](xref:Xamarin.Forms.SearchBar) exibe uma área para o usuário digitar uma cadeia de texto e um botão (ou uma tecla de teclado) que sinaliza o aplicativo para executar uma pesquisa. A propriedade [`Text`](xref:Xamarin.Forms.SearchBar.Text) fornece acesso ao texto e o evento [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed) indica que o botão foi pressionado.<br /><br />[Documentação da API](xref:Xamarin.Forms.SearchBar)  / [guia](~/xamarin-forms/user-interface/searchbar.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/) | [![Exemplo de SearchBar](views-images/SearchBar.png "Exemplo de SearchBar")](views-images/SearchBar-Large.png#lightbox "Exemplo de SearchBar")<br /> código para esta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml) com [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SearchBarDemoPage.cs) |
|     |     |

## <a name="views-for-setting-values"></a>Exibições para definir valores

### <a name="checkbox"></a>CheckBox

|     |     |
| --- | --- |
| `CheckBox` permite ao usuário selecionar um valor booliano usando um tipo de botão que pode ser marcado ou vazio. A propriedade `IsChecked` é o estado da `CheckBox` e o evento `CheckedChanged` é acionado quando o estado é alterado.<br /><br />Documentação/ [guia](~/xamarin-forms/user-interface/checkbox.md) da API  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos) | [![Exemplo da caixa de seleção](views-images/CheckBox.png "Exemplo da caixa de seleção")](views-images/CheckBox-Large.png#lightbox "Exemplo da caixa de seleção")<br />código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CheckBoxDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CheckBoxDemoPage.cs) |
|     |     |

### <a name="slider"></a>Controle deslizante

|     |     |
| --- | --- |
| [`Slider`](xref:Xamarin.Forms.Slider) permite que o usuário selecione um valor de `double` de um intervalo contínuo especificado com as propriedades [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) e [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) .<br /><br />[Documentação da API](xref:Xamarin.Forms.Slider)  / [guia](~/xamarin-forms/user-interface/slider.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) | [![Exemplo de Slider](views-images/Slider.png "Exemplo de Slider")](views-images/Slider-Large.png#lightbox "Exemplo de Slider")<br />código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SliderDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SliderDemoPage.cs) |
|     |     |

### <a name="stepper"></a>Passador

|     |     |
| --- | --- |
| [`Stepper`](xref:Xamarin.Forms.Stepper) permite que o usuário selecione um valor de `double` de um intervalo de valores incrementais especificado com as propriedades [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum), [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum)e [`Increment`](xref:Xamarin.Forms.Stepper.Increment) .<br /><br />[Documentação da API](xref:Xamarin.Forms.Stepper)   / [guia](~/xamarin-forms/user-interface/stepper.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos) | [![Exemplo de stepper](views-images/Stepper.png "Exemplo de stepper")](views-images/Stepper-Large.png#lightbox "Exemplo de stepper")<br />código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StepperDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StepperDemoPage.cs) |
|     |     |

### <a name="switch"></a>Alternar

|     |     |
| --- | --- |
| [`Switch`](xref:Xamarin.Forms.Switch) assume a forma de uma opção liga/desliga para permitir que o usuário selecione um valor booliano. A propriedade [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) é o estado da opção e o evento [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) é acionado quando o estado é alterado.<br /><br />[Documentação da API](xref:Xamarin.Forms.Switch)  / [guia](~/xamarin-forms/user-interface/switch.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/) | [![Exemplo de switch](views-images/Switch.png "Exemplo de switch")](views-images/Switch-Large.png#lightbox "Exemplo de switch")<br />código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchDemoPage.cs) |
|     |     |

### <a name="datepicker"></a>DatePicker

|     |     |
| --- | --- |
| [`DatePicker`](xref:Xamarin.Forms.DatePicker) permite que o usuário selecione uma data com o seletor de data da plataforma. Defina um intervalo de datas permitidas com as propriedades [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) e [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) . A propriedade [`Date`](xref:Xamarin.Forms.DatePicker.Date) é a data selecionada e o evento [`DateSelected`](xref:Xamarin.Forms.DatePicker.DateSelected) é acionado quando essa propriedade é alterada.<br /><br />[Documentação da API](xref:Xamarin.Forms.DatePicker)  / [guia](~/xamarin-forms/user-interface/datepicker.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker) | [![Exemplo de DatePicker](views-images/DatePicker.png "Exemplo de DatePicker")](views-images/DatePicker-Large.png#lightbox "Exemplo de DatePicker")<br />código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/DatePickerDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/DatePickerDemoPage.cs) |
|     |     |

### <a name="timepicker"></a>TimePicker

|     |     |
| --- | --- |
| [`TimePicker`](xref:Xamarin.Forms.TimePicker) permite que o usuário selecione uma hora com o seletor de tempo de plataforma. A propriedade [`Time`](xref:Xamarin.Forms.TimePicker.Time) é a hora selecionada. Um aplicativo pode monitorar alterações na propriedade `Time` instalando um manipulador para o evento [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) .<br /><br />[Documentação da API](xref:Xamarin.Forms.TimePicker)  / [guia](~/xamarin-forms/user-interface/timepicker.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker) | [![Exemplo de timeseparar](views-images/TimePicker.png "Exemplo de timeseparar")](views-images/TimePicker-Large.png#lightbox "Exemplo de timeseparar")<br />código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TimePickerDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TimePickerDemoPage.cs) |
|     |     |

## <a name="views-for-editing-text"></a>Exibições para edição de texto

Essas duas classes derivam da classe [`InputView`](xref:Xamarin.Forms.InputView) , que define a propriedade [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) .

<a name="entry" />

### <a name="entry"></a>Entrada

|     |     |
| --- | --- |
| [`Entry`](xref:Xamarin.Forms.Entry) permite que o usuário insira e edite uma única linha de texto. O texto está disponível como a propriedade [`Text`](xref:Xamarin.Forms.Entry.Text) e os eventos [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) e [`Completed`](xref:Xamarin.Forms.Entry.Completed) são acionados quando o texto é alterado ou o usuário sinaliza a conclusão tocando na tecla Enter.<br /><br />Use um [`Editor`](#editor) para inserir e editar várias linhas de texto.<br /><br />[Documentação da API](xref:Xamarin.Forms.Entry)  / [guia](~/xamarin-forms/user-interface/text/entry.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![Exemplo de entrada](views-images/Entry.png "Exemplo de entrada")](views-images/Entry-Large.png#lightbox "Exemplo de entrada")<br />código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryDemoPage.cs) |
|     |     |

<a name="editor" />

### <a name="editor"></a>Editor

|     |     |
| --- | --- |
| [`Editor`](xref:Xamarin.Forms.Editor) permite que o usuário insira e edite várias linhas de texto. O texto está disponível como a propriedade [`Text`](xref:Xamarin.Forms.Editor.Text) , e os eventos [`TextChanged`](xref:Xamarin.Forms.Editor.TextChanged) e [`Completed`](xref:Xamarin.Forms.Editor.Completed) são acionados quando o texto é alterado ou o usuário sinaliza a conclusão.<br /><br />Use uma exibição [`Entry`](#entry) para inserir e editar uma única linha de texto.<br /><br />[Documentação da API](xref:Xamarin.Forms.Editor)  / [guia](~/xamarin-forms/user-interface/text/editor.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![Exemplo de entrada](views-images/Editor.png "Exemplo de editor")](views-images/Editor-Large.png#lightbox "Exemplo de editor")<br />código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EditorDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EditorDemoPage.cs) |
|     |     |

## <a name="views-to-indicate-activity"></a>Exibições para indicar atividade

<a name="activityindicator" />

### <a name="activityindicator"></a>ActivityIndicator

|     |     |
| --- | --- |
| [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) usa uma animação para mostrar que o aplicativo está envolvido em uma atividade demorada sem dar qualquer indicação de progresso. A propriedade [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) controla a animação.<br /><br />Se o progresso da atividade for conhecido, use um [`ProgressBar`](#progressbar) em vez disso.<br /><br />[Documentação da API](xref:Xamarin.Forms.ActivityIndicator)  / [guia](~/xamarin-forms/user-interface/activityindicator.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/) | [![Exemplo de ActivityIndicator](views-images/ActivityIndicator.png "Exemplo de ActivityIndicator")](views-images/ActivityIndicator-Large.png#lightbox "Exemplo de ActivityIndicator")<br />código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ActivityIndicatorDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ActivityIndicatorDemoPage.cs) |
|     |     |

<a name="progressbar" />

### <a name="progressbar"></a>ProgressBar

|     |     |
| --- | --- |
| [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) usa uma animação para mostrar que o aplicativo está progredindo por uma atividade demorada. Defina a propriedade [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress) como valores entre 0 e 1 para indicar o progresso.<br /><br />Se o progresso da atividade não for conhecido, use um [`ActivityIndicator`](#activityindicator) em vez disso.<br /><br />[Documentação da API](xref:Xamarin.Forms.ProgressBar)  / [guia](~/xamarin-forms/user-interface/progressbar.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/) | [![Exemplo de ProgressBar](views-images/ProgressBar.png "Exemplo de ProgressBar")](views-images/ProgressBar-Large.png#lightbox "Exemplo de ProgressBar")<br />código para esta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml) com [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ProgressBarDemoPage.cs) |
|     |     |

## <a name="views-that-display-collections"></a>Exibições que exibem coleções

### <a name="carouselview"></a>CarouselView

|     |     |
| --- | --- |
| [`CarouselView`](xref:Xamarin.Forms.CarouselView) exibe uma lista rolável de itens de dados. Defina a propriedade `ItemsSource` como uma coleção de objetos e defina a propriedade `ItemTemplate` como um objeto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) descrevendo como os itens devem ser formatados. O evento `CurrentItemChanged` sinaliza que o item exibido no momento foi alterado, que está disponível como a propriedade `CurrentItem`.<br /><br />[Guia](~/xamarin-forms/user-interface/carouselview/index.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/) | [![Exemplo de CarouselView](views-images/CarouselView.png "Exemplo de CarouselView")](views-images/CarouselView-Large.png#lightbox "Exemplo de CarouselView")<br />código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselViewDemoPage.cs) |
|     |     |

### <a name="collectionview"></a>CollectionView

|     |     |
| --- | --- |
| [`CollectionView`](xref:Xamarin.Forms.CollectionView) exibe uma lista rolável de itens de dados selecionáveis, usando especificações de layout diferentes. Ele visa fornecer uma alternativa mais flexível e de alto desempenho para [`ListView`](xref:Xamarin.Forms.ListView). Defina a propriedade `ItemsSource` como uma coleção de objetos e defina a propriedade `ItemTemplate` como um objeto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) descrevendo como os itens devem ser formatados. O evento `SelectionChanged` sinaliza que uma seleção foi feita, que está disponível como a propriedade `SelectedItem`.<br /><br />[Guia](~/xamarin-forms/user-interface/collectionview/index.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/) | [![Exemplo de CollectionView](views-images/CollectionView.png "Exemplo de CollectionView")](views-images/CollectionView-Large.png#lightbox "Exemplo de CollectionView")<br />código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CollectionViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CollectionViewDemoPage.cs) |
|     |     |

<a name="listView" />

### <a name="listview"></a>ListView

|     |     |
| --- | --- |
| [`ListView`](xref:Xamarin.Forms.ListView) deriva de [`ItemsView`](xref:Xamarin.Forms.ItemsView`1) e exibe uma lista rolável de itens de dados selecionáveis. Defina a propriedade [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) como uma coleção de objetos e defina a propriedade [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) como um objeto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) descrevendo como os itens devem ser formatados. O evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) sinaliza que uma seleção foi feita, que está disponível como a propriedade [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) .<br /><br />[Documentação da API](xref:Xamarin.Forms.ListView)  / [guia](~/xamarin-forms/user-interface/listview/index.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview/) | [![Exemplo de ListView](views-images/ListView.png "Exemplo de ListView")](views-images/ListView-Large.png#lightbox "Exemplo de ListView")<br />código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ListViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ListViewDemoPage.cs) |
|     |     |

### <a name="picker"></a>Seletor

|     |     |
| --- | --- |
| [`Picker`](xref:Xamarin.Forms.Picker) exibe um item selecionado de uma lista de cadeias de caracteres de texto e permite selecionar esse item quando a exibição é tocada. Defina a propriedade [`Items`](xref:Xamarin.Forms.Picker.Items) como uma lista de cadeias de caracteres ou a propriedade [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) como uma coleção de objetos. O evento [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) é acionado quando um item é selecionado.<br /><br />O `Picker` exibe a lista de itens somente quando ele está selecionado. Use um [`ListView`](#listView) ou [`TableView`](#tableView) para uma lista rolável que permaneça na página.<br /><br />[Documentação da API](xref:Xamarin.Forms.Picker)  / [guia](~/xamarin-forms/user-interface/picker/index.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo) | [![Exemplo de seletor](views-images/Picker.png "Exemplo de seletor")](views-images/Picker-Large.png#lightbox "Exemplo de seletor")<br />código para esta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml) com [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/PickerDemoPage.cs) |
|     |     |

<a name="tableView" />

### <a name="tableview"></a>TableView

|     |     |
| --- | --- |
| [`TableView`](xref:Xamarin.Forms.TableView) exibe uma lista de linhas do tipo [`Cell`](xref:Xamarin.Forms.Cell) com cabeçalhos e subtítulos opcionais. Defina a propriedade [`Root`](xref:Xamarin.Forms.TableView.Root) como um objeto do tipo [`TableRoot`](xref:Xamarin.Forms.TableRoot)e adicione [`TableSection`](xref:Xamarin.Forms.TableSection) objetos a esse `TableRoot`. Cada `TableSection` é uma coleção de objetos `Cell`.<br /><br />[Documentação da API](xref:Xamarin.Forms.TableView)  / [guia](~/xamarin-forms/user-interface/tableview.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview) | [![Exemplo de TableView](views-images/TableView.png "Exemplo de TableView")](views-images/TableView-Large.png#lightbox "Exemplo de TableView")<br />código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TableViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TableViewDemoPage.cs) |
|     |     |

## <a name="related-links"></a>Links relacionados

- [Exemplo de FormsGallery do Xamarin. Forms](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Amostras do Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Documentação da API do Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
