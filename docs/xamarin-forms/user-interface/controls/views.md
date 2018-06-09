---
title: Modos de exibição do xamarin. Forms
description: Xamarin. Forms exibições são os blocos de construção de interfaces de usuário móvel de plataforma cruzada. Este artigo lista as exibições que estão incluídas no xamarin. Forms.
ms.prod: xamarin
ms.assetid: AC070686-A423-4A98-8BB6-0B9F94C062CC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 414df933716c7ce5d9422e8a2b9be89ba7eff3a9
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243512"
---
# <a name="xamarinforms-views"></a>Modos de exibição do xamarin. Forms

_Xamarin. Forms exibições são os blocos de construção de interfaces de usuário móvel de plataforma cruzada._

Modos de exibição são objetos de interface do usuário, como rótulos, botões e controles deslizantes que são normalmente conhecidos como *controles* ou *widgets* em outros ambientes de programação gráficas. As exibições com suporte de xamarin. Forms todos derivam de [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) classe. Eles podem ser divididos em várias categorias:

## <a name="views-for-presentation"></a>Exibições de apresentação

### <a name="label"></a>Rotular

|     |     |
| --- | --- |
| [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) Exibe as cadeias de caracteres de texto de linha única ou multilinhas blocos de texto com formatação constante ou variável. Definir o [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) propriedade como uma cadeia de caracteres de constante formatação ou conjunto o [ `FormattedText` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FormattedText/) propriedade para um [ `FormattedString` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FormattedString/) objeto variável formatação.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) / [guia](~/xamarin-forms/user-interface/text/label.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text/) | [![Exemplo de rótulo](views-images/Label.png "exemplo de rótulo")](views-images/Label-Large.png#lightbox "exemplo de rótulo")<br /> [O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/LabelDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/LabelDemoPage.xaml) |
|     |     |

### <a name="image"></a>Image

|     |     |
| --- | --- |
| [`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) Exibe um bitmap. Bitmaps pode ser baixado na Web, inseridos como recursos no projeto comum ou projetos de plataforma ou criados usando .NET `Stream` objeto.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) / [guia](~/xamarin-forms/user-interface/images.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithImages/) | [![Exemplo de imagem](views-images/Image.png "imagem exemplo")](views-images/Image-Large.png#lightbox "a imagem de exemplo")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageDemoPage.xaml) |
|     |     |

### <a name="boxview"></a>BoxView

|     |    |
| --- | ---|
| [`BoxView`](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) Exibe um retângulo sólido colorido pelo [ `Color` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BoxView.Color/) propriedade. `BoxView` tem uma solicitação de tamanho padrão de 40 x 40. Para outros tamanhos, atribuir o [ `WidthRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/) e [ `HeightRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/) propriedades.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) / [guia](~/xamarin-forms/user-interface/boxview.md) / [exemplo 1](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BasicBoxView), [2](https://developer.xamarin.com/samples/xamarin-forms/BoxView/TextDecoration), [3](https://developer.xamarin.com/samples/xamarin-forms/BoxView/ColorListBox), [4 ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife), [5](https://developer.xamarin.com/samples/xamarin-forms/BoxView/DotMatrixClock), e [6](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock) | [![Exemplo de BoxView](views-images/BoxView.png "BoxView exemplo")](views-images/BoxView-Large.png#lightbox "BoxView exemplo")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/BoxViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/BoxViewDemoPage.xaml) |
|     |     |

### <a name="webview"></a>WebView

|     |     |
| --- | --- |
| [`WebView`](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) Exibe o conteúdo, HTML ou páginas da Web com base em se o [ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebView.Source/) está definida como um [ `UriWebViewSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.UrlWebViewSource/) ou um [ `HtmlWebViewSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.HtmlWebViewSource/) objeto.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) / [guia](~/xamarin-forms/user-interface/webview.md) / [exemplo 1](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithWebview/) e [2](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/WebView) | [![Exemplo de WebView](views-images/WebView.png "WebView exemplo")](views-images/WebView-Large.png#lightbox "exemplo WebView")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/WebViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/WebViewDemoPage.xaml) |
|     |     |

### <a name="openglview"></a>OpenGLView

|     |     |
| --- | --- |
| [`OpenGLView`](https://developer.xamarin.com/api/type/Xamarin.Forms.OpenGLView/) exibe gráficos OpenGL em projetos Android e iOS. Não há nenhum suporte para a plataforma Universal do Windows. Projetos Android e iOS exigem uma referência para o **OpenTK 1.0** assembly ou o **OpenTK** assembly versão 1.0.0.0. `OpenGLView` é mais fácil de usar em um projeto compartilhado; Se usada em uma biblioteca .NET padrão, uma dependência de serviço também será necessária (conforme mostrado no código de exemplo).<br /><br />Esse é o recurso apenas gráficos incorporado xamarin. Forms, mas um aplicativo xamarin. Forms também pode renderizar elementos gráficos usando [ `CocosSharp` ](~/xamarin-forms/user-interface/graphics/cocossharp.md), [ `SkiaSharp` ](~/xamarin-forms/user-interface/graphics/skiasharp/index.md), ou [ `UrhoSharp` ](~/xamarin-forms/user-interface/graphics/urhosharp.md).<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.OpenGLView/)<br /><br /> | [![Exemplo de OpenGLView](views-images/OpenGLView.png "OpenGLView exemplo")](views-images/OpenGLView-Large.png#lightbox "OpenGLView exemplo")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/OpenGLViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml) com [por trás do código](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml.cs) |
|     |     |

### <a name="map"></a>Mapa

|     |     |
| --- | --- |
| [`Map`](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) Exibe um mapa. O **Xamarin.Forms.Maps** pacote Nuget deve ser instalado. Android e plataforma Universal do Windows exigem uma chave de autorização do mapa.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) / [guia](~/xamarin-forms/user-interface/map.md) / [exemplo](https://developer.xamarin.com/samples/WorkingWithMaps/) | [![Exemplo de mapa](views-images/Map.png "mapear exemplo")](views-images/Map-Large.png#lightbox "exemplo do mapa")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MapDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MapDemoPage.xaml) |
|     |     |

## <a name="views-that-initiate-commands"></a>Modos de exibição que iniciam comandos

### <a name="button"></a>Botão

|     |     |
| --- | --- |
| [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) é um objeto retangular que exibe o texto, e que aciona um [ `Clicked` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Button.Clicked/) eventos quando é pressionado.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) / [guia](~/xamarin-forms/user-interface/button.md) / [exemplo](https://developer.xamarin.com/samples/UserInterface/ButtonDemos/) | [![Exemplo de botão](views-images/Button.png "botão exemplo")](views-images/Button-Large.png#lightbox "exemplo de botão")<br /> [O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ButtonDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml) com [por trás do código](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml.cs) |
|     |     |

### <a name="searchbar"></a>SearchBar

|     |     |
| --- | --- |
| [`SearchBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) Exibe uma área para o usuário para o tipo de uma cadeia de caracteres de texto e um botão (ou uma chave de teclado) que sinaliza o aplicativo para realizar uma pesquisa. O [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.Text/) propriedade fornece acesso para o texto e o [ `SearchButtonPressed` ](https://developer.xamarin.com/api/event/Xamarin.Forms.SearchBar.SearchButtonPressed/) evento indica que o botão foi pressionado.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) | [![Exemplo de SearchBar](views-images/SearchBar.png "SearchBar exemplo")](views-images/SearchBar-Large.png#lightbox "SearchBar exemplo")<br /> [O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SearchBarDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml) com [por trás do código](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml.cs) |
|     |     |

## <a name="views-for-setting-values"></a>Modos de exibição para definir valores

### <a name="slider"></a>Controle deslizante

|     |     |
| --- | --- |
| [`Slider`](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) permite que o usuário selecione um `double` valor de um intervalo contínuo especificado com o [ `Minimum` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Minimum/) e [ `Maximum` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Maximum/) propriedades.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) / [guia](~/xamarin-forms/user-interface/slider.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos) | [![Exemplo de controle deslizante](views-images/Slider.png "exemplo de controle deslizante")](views-images/Slider-Large.png#lightbox "exemplo de controle deslizante")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SliderDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SliderDemoPage.xaml) |
|     |     |

### <a name="stepper"></a>Seletor

|     |     |
| --- | --- |
| [`Stepper`](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/) permite que o usuário selecione um `double` valor de um intervalo de valores com incremento especificado com o [ `Minimum` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Stepper.Minimum/), [ `Maximum` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Stepper.Maximum/), e [ `Increment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Stepper.Increment/) propriedades.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/) | [![Exemplo de seletor](views-images/Stepper.png "exemplo seletor")](views-images/Stepper-Large.png#lightbox "seletor de exemplo")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StepperDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StepperDemoPage.xaml) |
|     |     |

### <a name="switch"></a>Alternar

|     |     |
| --- | --- |
| [`Switch`](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/) assume a forma de um comutador ligado/desligado para permitir que o usuário selecione um valor booliano. O [ `IsToggled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Switch.IsToggled/) propriedade é o estado do comutador e o [ `Toggled` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Switch.Toggled/) evento é acionado quando o estado é alterado.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/) | [![Exemplo de switch](views-images/Switch.png "alternar exemplo")](views-images/Switch-Large.png#lightbox "alternar de exemplo")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchDemoPage.xaml) |
|     |     |

### <a name="datepicker"></a>DatePicker

|     |     |
| --- | --- |
| [`DatePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/) permite que o usuário selecione uma data com o seletor de data de plataforma. Definir um intervalo de datas permitidos com o [ `MinimumDate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MinimumDate/) e [ `MaximumDate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MaximumDate/) propriedades. O [ `Date` ](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.Date/) propriedade é a data selecionada e o [ `DateSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.DatePicker.DateSelected/) evento é acionado quando essa propriedade é alterado.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/) / [guia](~/xamarin-forms/user-interface/datepicker.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/DatePicker) | [![Exemplo de DatePicker](views-images/DatePicker.png "exemplo de DatePicker")](views-images/DatePicker-Large.png#lightbox "DatePicker exemplo")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/DatePickerDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/DatePickerDemoPage.xaml) |
|     |     |

### <a name="timepicker"></a>TimePicker

|     |     |
| --- | --- |
| [`TimePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/) permite que o usuário selecione uma hora com o seletor de tempo de plataforma. O [ `Time` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TimePicker.Time/) é de propriedade de tempo selecionado. Um aplicativo pode monitorar as alterações no `Time` propriedade ao instalar um manipulador para o [ `PropertyChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.BindableObject.PropertyChanged/) eventos.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/) | [![Exemplo de TimePicker](views-images/TimePicker.png "TimePicker exemplo")](views-images/TimePicker-Large.png#lightbox "TimePicker exemplo")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TimePickerDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TimePickerDemoPage.xaml) |
|     |     |

## <a name="views-for-editing-text"></a>Modos de exibição de edição de texto

Essas duas classes derivam o [ `InputView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.InputView/) classe, que define o [ `Keyboard` ](https://developer.xamarin.com/api/property/Xamarin.Forms.InputView.Keyboard/) propriedade.

<a name="entry" />

### <a name="entry"></a>Entrada

|     |     |
| --- | --- |
| [`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) permite ao usuário digitar e editar uma única linha de texto. O texto está disponível como a [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Text/) propriedade e o [ `TextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/) e [ `Completed` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.Completed/) os eventos são disparados quando as alterações de texto ou o usuário sinaliza a conclusão tocando a tecla enter.<br /><br />Use um [ `Editor` ](#editor) para inserir e editar várias linhas de texto.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) / [guia](~/xamarin-forms/user-interface/text/entry.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text) | [![Exemplo de entrada](views-images/Entry.png "exemplo de entrada")](views-images/Entry-Large.png#lightbox "exemplo de entrada")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryDemoPage.xaml) |
|     |     |

<a name="editor" />

### <a name="editor"></a>Editor

|     |     |
| --- | --- |
| [`Editor`](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/) permite ao usuário digitar e editar várias linhas de texto. O texto está disponível como a [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Editor.Text/) propriedade e o [ `TextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Editor.TextChanged/) e [ `Completed` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Editor.Completed/) os eventos são disparados quando as alterações de texto ou o usuário sinaliza a conclusão.<br /><br />Use um [ `Entry` ](#entry) exibição para inserir e editar uma única linha de texto.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/) / [guia](~/xamarin-forms/user-interface/text/editor.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text) | [![Exemplo de entrada](views-images/Editor.png "Editor exemplo")](views-images/Editor-Large.png#lightbox "Editor de exemplo")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EditorDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EditorDemoPage.xaml) |
|     |     |

## <a name="views-to-indicate-activity"></a>Modos de exibição para indicar a atividade

<a name="activityindicator" />

### <a name="activityindicator"></a>ActivityIndicator

|     |     |
| --- | --- |
| [`ActivityIndicator`](https://developer.xamarin.com/api/type/Xamarin.Forms.ActivityIndicator/) usa uma animação para mostrar que o aplicativo está envolvido em uma atividade longa sem dar nenhuma indicação de progresso. O [ `IsRunning` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ActivityIndicator.IsRunning/) propriedade controla a animação.<br /><br />Se o progresso da atividade é conhecido, use um [ `ProgressBar` ](#progressbar) em vez disso.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.ActivityIndicator/) | [![Exemplo de ActivityIndicator](views-images/ActivityIndicator.png "ActivityIndicator exemplo")](views-images/ActivityIndicator-Large.png#lightbox "ActivityIndicator exemplo")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ActivityIndicatorDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ActivityIndicatorDemoPage.xaml) |
|     |     |

<a name="progressbar" />

### <a name="progressbar"></a>ProgressBar

|     |     |
| --- | --- |
| [`ProgressBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.ProgressBar/) usa uma animação para mostrar que o aplicativo está em andamento por meio de uma atividade longa. Definir o [ `Progress` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ProgressBar.Progress/) propriedade para valores entre 0 e 1 para indicar o progresso.<br /><br />Se o progresso da atividade não é conhecido, use um [ `ActivityIndicator` ](#activityindicator) em vez disso.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.ProgressBar/) | [![Exemplo de ProgressBar](views-images/ProgressBar.png "ProgressBar exemplo")](views-images/ProgressBar-Large.png#lightbox "exemplo ProgressBar")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ProgressBarDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml) com [por trás do código](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml.cs) |
|     |     |

## <a name="views-that-display-collections"></a>Exibições que mostram coleções

### <a name="picker"></a>Seletor

|     |     |
| --- | --- |
| [`Picker`](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) Exibe um item selecionado em uma lista de cadeias de caracteres de texto e permite selecionar o item quando o modo de exibição é tocado. Definir o [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) propriedade a uma lista de cadeias de caracteres, ou o [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) propriedade a uma coleção de objetos. O [ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/) evento é acionado quando um item é selecionado.<br /><br />O `Picker` exibe a lista de itens apenas quando ele está selecionado. Use um [ `ListView` ](#listView) ou [ `TableView` ](#tableView) para obter uma lista rolável que permanece na página.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) / [guia](~/xamarin-forms/user-interface/picker/index.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PickerDemo/) | [![Exemplo de seletor](views-images/Picker.png "exemplo seletor")](views-images/Picker-Large.png#lightbox "seletor de exemplo")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/PickerDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml) com [por trás do código](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml.cs) |
|     |     |

<a name="listView" />

### <a name="listview"></a>ListView

|     |     |
| --- | --- |
| [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) deriva [ `ItemsView[Cell]` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/) e exibe uma lista de rolagem de itens de dados podem ser selecionados. Definir o [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/) propriedade a uma coleção de objetos e defina o [ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/) propriedade para um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) objeto que descreve como os itens são a ser formatado. O [ `ItemSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/) evento sinaliza que uma seleção foi feita, que está disponível como a [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SelectedItem/) propriedade.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) / [guia](~/xamarin-forms/user-interface/listview/index.md) / [exemplo](https://developer.xamarin.com/samples/WorkingWithListview) | [![Exemplo de ListView](views-images/ListView.png "exemplo ListView")](views-images/ListView-Large.png#lightbox "exemplo ListView")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ListViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ListViewDemoPage.xaml) |
|     |     |

<a name="tableView" />

### <a name="tableview"></a>Modo de tabela

|     |     |
| --- | --- |
| [`TableView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/) Exibe uma lista de linhas do tipo [ `Cell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/) com cabeçalhos opcionais e subcabeçalhos. Definir o [ `Root` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TableView.Root/) propriedade para um objeto do tipo [ `TableRoot` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableRoot/)e adicione [ `TableSection` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableSection/) objetos para que `TableRoot`. Cada `TableSection` é uma coleção de `Cell` objetos.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/) / [guia](~/xamarin-forms/user-interface/tableview.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TableView) | [![Exemplo de modo de tabela](views-images/TableView.png "exemplo de modo de tabela")](views-images/TableView-Large.png#lightbox "exemplo de modo de tabela")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TableViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TableViewDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Links relacionados

- [Introdução ao Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Exemplo de FormsGallery xamarin. Forms](https://developer.xamarin.com/samples/FormsGallery/)
- [Amostras do Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Documentação da API de xamarin. Forms](https://developer.xamarin.com/api/root/Xamarin.Forms/)
