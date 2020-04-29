---
title: Exibições do Xamarin. Forms
description: As exibições do Xamarin. Forms são os blocos de construção de interfaces de usuário móvel de plataforma cruzada. Este artigo lista as exibições incluídas no Xamarin. Forms.
ms.prod: xamarin
ms.assetid: AC070686-A423-4A98-8BB6-0B9F94C062CC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/16/2020
ms.openlocfilehash: 4164941e4ed8d484699e52eece86085f1c761c91
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82516923"
---
# <a name="xamarinforms-views"></a>Exibições do Xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_As exibições do Xamarin. Forms são os blocos de construção de interfaces de usuário móvel de plataforma cruzada._

As exibições são objetos de interface do usuário, como rótulos, botões e controles deslizantes que normalmente são conhecidos como *controles* ou *widgets* em outros ambientes de programação gráfica. As exibições suportadas pelo Xamarin. Forms derivam da [`View`](xref:Xamarin.Forms.View) classe. Eles podem ser divididos em várias categorias:

## <a name="views-for-presentation"></a>Exibições para apresentação

### <a name="boxview"></a>BoxView

|     |    |
| --- | ---|
| [`BoxView`](xref:Xamarin.Forms.BoxView)exibe um retângulo sólido colorido pela [`Color`](xref:Xamarin.Forms.BoxView.Color) propriedade. `BoxView`tem uma solicitação de tamanho padrão de 40x40. Para outros tamanhos, atribua as [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) propriedades [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) e.<br /><br />[API Documentation](xref:Xamarin.Forms.BoxView) / [Guide](~/xamarin-forms/user-interface/boxview.md)Guia / de documentação da API[exemplo 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview), [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-textdecoration), [3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors/), [4](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife), [5](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock)e [6](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock) | [![Exemplo de BoxView](views-images/BoxView.png "Exemplo de BoxView")](views-images/BoxView-Large.png#lightbox "Exemplo de BoxView")<br />[Código C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/BoxViewDemoPage.cs) / página[XAML página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/BoxViewDemoPage.xaml) |
|     |     |

### <a name="expander"></a>Expansor

|     |     |
| --- | --- |
| `Expander`fornece um contêiner expansível para hospedar qualquer conteúdo e é composto por um cabeçalho e um conteúdo. Defina a `Header` Propriedade como a [`View`](xref:Xamarin.Forms.View) que será exibida como o cabeçalho e a `Content` propriedade para a [`View`](xref:Xamarin.Forms.View) que será exibida quando o cabeçalho for expandido por um toque.<br /><br />[Guide](~/xamarin-forms/user-interface/expander.md) / [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-expanderdemos) de guia | [![Exemplo de expansor](views-images/Expander.png "Exemplo de expansor")](views-images/Expander-Large.png#lightbox "Exemplo de expansor")<br /> [Código C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ExpanderDemoPage.cs) / página[XAML página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ExpanderDemoPage.xaml) |
|     |     |

### <a name="label"></a>Rotular

|     |     |
| --- | --- |
| [`Label`](xref:Xamarin.Forms.Label)exibe cadeias de texto de linha única ou blocos de texto de várias linhas, com a formatação constante ou variável. Defina a [`Text`](xref:Xamarin.Forms.Label.Text) Propriedade como uma cadeia de caracteres para formatação constante ou defina [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) a propriedade como [`FormattedString`](xref:Xamarin.Forms.FormattedString) um objeto para formatação de variável.<br /><br />[API Documentation](xref:Xamarin.Forms.Label) / [Guide](~/xamarin-forms/user-interface/text/label.md) / [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) de guia de documentação da API | [![Exemplo de rótulo](views-images/Label.png "Exemplo de rótulo")](views-images/Label-Large.png#lightbox "Exemplo de rótulo")<br /> [Código C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/LabelDemoPage.cs) / página[XAML página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/LabelDemoPage.xaml) |
|     |     |

### <a name="image"></a>Image

|     |     |
| --- | --- |
| [`Image`](xref:Xamarin.Forms.Image)exibe um bitmap. Os bitmaps podem ser baixados pela Web, inseridos como recursos no projeto comum ou projetos de plataforma, ou criados com `Stream` o uso de um objeto .net.<br /><br />[API Documentation](xref:Xamarin.Forms.Image) / [Guide](~/xamarin-forms/user-interface/images.md) / [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages) de guia de documentação da API | [![Exemplo de imagem](views-images/Image.png "Exemplo de imagem")](views-images/Image-Large.png#lightbox "Exemplo de imagem")<br />[Código C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageDemoPage.cs) / página[XAML página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageDemoPage.xaml) |
|     |     |

### <a name="map"></a>Mapeamento

|     |     |
| --- | --- |
| [`Map`](xref:Xamarin.Forms.Maps.Map)exibe um mapa. O pacote NuGet **Xamarin. Forms. Maps** deve ser instalado. O Android e o Plataforma Universal do Windows exigem uma chave de autorização de mapa.<br /><br />[API Documentation](xref:Xamarin.Forms.Maps.Map) / [Guide](~/xamarin-forms/user-interface/map/index.md) / [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps/) de guia de documentação da API | [![Exemplo de mapa](views-images/Map.png "Exemplo de mapa")](views-images/Map-Large.png#lightbox "Exemplo de mapa")<br />[Código C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MapDemoPage.cs) / página[XAML página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MapDemoPage.xaml) |
|     |     |

### <a name="mediaelement"></a>MediaElement

|     |     |
| --- | --- |
| [`MediaElement`](xref:Xamarin.Forms.MediaElement)reproduz vídeo ou áudio. A mídia pode ser reproduzida a partir de uma URL ou de um arquivo local, [`Source`](xref:Xamarin.Forms.MediaElement.Source) com base em se a [`UriMediaSource`](xref:Xamarin.Forms.UriMediaSource) Propriedade está [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource)definida como um ou um.<br /><br />[API Documentation](xref:Xamarin.Forms.MediaElement) / [Guide](~/xamarin-forms/user-interface/mediaelement.md) / [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-mediaelementdemos) de guia de documentação da API | [![Exemplo de MediaElement](views-images/MediaElement.png "Exemplo de MediaElement")](views-images/MediaElement-Large.png#lightbox "Exemplo de MediaElement")<br />[Código C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MediaElementDemoPage.cs) / página[XAML página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MediaElementDemoPage.xaml) |
|     |     |

### <a name="openglview"></a>OpenGLView

|     |     |
| --- | --- |
| [`OpenGLView`](xref:Xamarin.Forms.OpenGLView)exibe gráficos OpenGL em projetos iOS e Android. Não há suporte para o Plataforma Universal do Windows. Os projetos iOS e Android exigem uma referência ao assembly **OpenTK-1,0** ou ao assembly da versão 1.0.0.0 do **OpenTK** . `OpenGLView`é mais fácil de usar em um projeto compartilhado; Se usado em uma biblioteca de .NET Standard, um serviço de dependência também será necessário (conforme mostrado no código de exemplo).<br /><br />Esse é o único recurso de gráficos que é criado no Xamarin. Forms, mas um aplicativo Xamarin. Forms também pode renderizar [`SkiaSharp`](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)gráficos usando [`UrhoSharp`](~/xamarin-forms/user-interface/graphics/urhosharp.md), ou.<br /><br />[Documentação da API](xref:Xamarin.Forms.OpenGLView)<br /><br /> | [![Exemplo de OpenGLView](views-images/OpenGLView.png "Exemplo de OpenGLView")](views-images/OpenGLView-Large.png#lightbox "Exemplo de OpenGLView")<br />[Código C# para esta página XAML de página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/OpenGLViewDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml) com [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml.cs) |
|     |     |

### <a name="webview"></a>Exibição da Web

|     |     |
| --- | --- |
| [`WebView`](xref:Xamarin.Forms.WebView)exibe páginas da Web ou conteúdo HTML, com base em [`Source`](xref:Xamarin.Forms.WebView.Source) se a propriedade está definida [`UriWebViewSource`](xref:Xamarin.Forms.UrlWebViewSource) como um [`HtmlWebViewSource`](xref:Xamarin.Forms.HtmlWebViewSource) ou um objeto.<br /><br />[API Documentation](xref:Xamarin.Forms.WebView) / [Guide](~/xamarin-forms/user-interface/webview.md)Guia / de documentação da API[exemplo 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview) e [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview) | [![Exemplo de WebView](views-images/WebView.png "Exemplo de WebView")](views-images/WebView-Large.png#lightbox "Exemplo de WebView")<br />[Código C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/WebViewDemoPage.cs) / página[XAML página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/WebViewDemoPage.xaml) |
|     |     |

## <a name="views-that-initiate-commands"></a>Exibições que iniciam comandos

### <a name="button"></a>Botão

|     |     |
| --- | --- |
| [`Button`](xref:Xamarin.Forms.Button)é um objeto retangular que exibe texto e que dispara um [`Clicked`](xref:Xamarin.Forms.Button.Clicked) evento quando ele é pressionado.<br /><br />[API Documentation](xref:Xamarin.Forms.Button) / [Guide](~/xamarin-forms/user-interface/button.md) / [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos/) de guia de documentação da API | [![Exemplo de botão](views-images/Button.png "Exemplo de botão")](views-images/Button-Large.png#lightbox "Exemplo de botão")<br /> [Código C# para esta página XAML de página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ButtonDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml) com [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml.cs) |
|     |     |

### <a name="imagebutton"></a>ImageButton

|     |     |
| --- | --- |
| `ImageButton`é um objeto retangular que exibe uma imagem e que dispara um `Clicked` evento quando ele é pressionado.<br /><br /> [Guide](~/xamarin-forms/user-interface/imagebutton.md) / [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) de guia | [![Exemplo de ImageButton](views-images/ImageButton.png "Exemplo de ImageButton")](views-images/ImageButton-Large.png#lightbox "Exemplo de ImageButton")<br /> [Código C# para esta página XAML de página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageButtonDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml) com [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml.cs) |
|     |     |

### <a name="radiobutton"></a>RadioButton

|     |     |
| --- | --- |
| `RadioButton`permite a seleção de uma opção de um conjunto e dispara um `CheckedChanged` evento quando a seleção ocorre.<br /><br />[Guide](~/xamarin-forms/user-interface/radiobutton.md) / [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/) de guia | [![Exemplo de RadioButton](views-images/RadioButton.png "Exemplo de RadioButton")](views-images/RadioButton-Large.png#lightbox "Exemplo de RadioButton")<br /> [Código C# para esta página XAML de página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RadioButtonDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RadioButtonDemoPage.xaml) com [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RadioButtonDemoPage.xaml.cs) |
|     |     |

### <a name="refreshview"></a>RefreshView

|     |     |
| --- | --- |
| `RefreshView`é um controle de contêiner que fornece a funcionalidade de pull para atualização para conteúdo rolável. O `ICommand` definido pela `Command` propriedade é executado quando uma atualização é disparada e a `IsRefreshing` propriedade indica o estado atual do controle.<br /><br /> [Guide](~/xamarin-forms/user-interface/refreshview.md) / [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) de guia | [![Exemplo de RefreshView](views-images/RefreshView.png "Exemplo de RefreshView")](views-images/RefreshView-Large.png#lightbox "Exemplo de RefreshView")<br /> [Código C# para esta página XAML de página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RefreshViewDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RefreshViewDemoPage.xaml) com [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RefreshViewDemoPage.xaml.cs) |
|     |     |

### <a name="searchbar"></a>SearchBar

|     |     |
| --- | --- |
| [`SearchBar`](xref:Xamarin.Forms.SearchBar)exibe uma área para o usuário digitar uma cadeia de texto e um botão (ou uma tecla de teclado) que sinaliza o aplicativo para executar uma pesquisa. A [`Text`](xref:Xamarin.Forms.InputView.Text) propriedade fornece acesso ao texto e o [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed) evento indica que o botão foi pressionado.<br /><br />[API Documentation](xref:Xamarin.Forms.SearchBar) / [Guide](~/xamarin-forms/user-interface/searchbar.md) / [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/) de guia de documentação da API | [![Exemplo de SearchBar](views-images/SearchBar.png "Exemplo de SearchBar")](views-images/SearchBar-Large.png#lightbox "Exemplo de SearchBar")<br /> [Código C# para esta página XAML de página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SearchBarDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml) com [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml.cs) |
|     |     |

### <a name="swipeview"></a>SwipeView

|     |     |
| --- | --- |
| `SwipeView`é um controle de contêiner que envolve um item de conteúdo e fornece itens de menu de contexto que são revelados por um gesto de passar o dedo. Cada item de menu é representado por `SwipeItem`um, que tem `Command` uma propriedade que executa um `ICommand` quando o item é tocado.<br /><br /> [Guide](~/xamarin-forms/user-interface/swipeview.md) / [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) de guia | [![Exemplo de SwipeView](views-images/SwipeView.png "Exemplo de SwipeView")](views-images/SwipeView-Large.png#lightbox "Exemplo de SwipeView")<br /> [Código C# para esta página XAML de página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwipeViewDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwipeViewDemoPage.xaml) com [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwipeViewDemoPage.xaml.cs) |
|     |     |

## <a name="views-for-setting-values"></a>Exibições para definir valores

### <a name="checkbox"></a>CheckBox

|     |     |
| --- | --- |
| `CheckBox`permite que o usuário selecione um valor booliano usando um tipo de botão que pode ser marcado ou vazio. A `IsChecked` propriedade é o estado do `CheckBox`e o `CheckedChanged` evento é acionado quando o estado é alterado.<br /><br />Documentação da API/[exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos) de [guia](~/xamarin-forms/user-interface/checkbox.md) /  | [![Exemplo da caixa de seleção](views-images/CheckBox.png "Exemplo da caixa de seleção")](views-images/CheckBox-Large.png#lightbox "Exemplo da caixa de seleção")<br />[Código C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CheckBoxPage.cs) / página[XAML página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CheckBoxPage.xaml) |
|     |     |

### <a name="slider"></a>Controle deslizante

|     |     |
| --- | --- |
| [`Slider`](xref:Xamarin.Forms.Slider)permite que o usuário selecione um `double` valor de um intervalo contínuo especificado com as [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) propriedades [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) e.<br /><br />[API Documentation](xref:Xamarin.Forms.Slider) / [Guide](~/xamarin-forms/user-interface/slider.md) / [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) de guia de documentação da API | [![Exemplo de Slider](views-images/Slider.png "Exemplo de Slider")](views-images/Slider-Large.png#lightbox "Exemplo de Slider")<br />[Código C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SliderDemoPage.cs) / página[XAML página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SliderDemoPage.xaml) |
|     |     |

### <a name="stepper"></a>Passador

|     |     |
| --- | --- |
| [`Stepper`](xref:Xamarin.Forms.Stepper)permite que o usuário selecione um `double` valor de um intervalo de valores incrementais especificado com [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum)as [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum)Propriedades, [`Increment`](xref:Xamarin.Forms.Stepper.Increment) e.<br /><br />[API Documentation](xref:Xamarin.Forms.Stepper)  / [Guide](~/xamarin-forms/user-interface/stepper.md) / [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos) de guia de documentação da API | [![Exemplo de stepper](views-images/Stepper.png "Exemplo de stepper")](views-images/Stepper-Large.png#lightbox "Exemplo de stepper")<br />[Código C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StepperDemoPage.cs) / página[XAML página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StepperDemoPage.xaml) |
|     |     |

### <a name="switch"></a>Opção

|     |     |
| --- | --- |
| [`Switch`](xref:Xamarin.Forms.Switch)assume a forma de uma opção liga/desliga para permitir que o usuário selecione um valor booliano. A [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) propriedade é o estado da opção e o [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) evento é acionado quando o estado é alterado.<br /><br />[API Documentation](xref:Xamarin.Forms.Switch) / [Guide](~/xamarin-forms/user-interface/switch.md) / [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/) de guia de documentação da API | [![Exemplo de switch](views-images/Switch.png "Exemplo de switch")](views-images/Switch-Large.png#lightbox "Exemplo de switch")<br />[Código C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchDemoPage.cs) / página[XAML página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchDemoPage.xaml) |
|     |     |

### <a name="datepicker"></a>DatePicker

|     |     |
| --- | --- |
| [`DatePicker`](xref:Xamarin.Forms.DatePicker)permite que o usuário selecione uma data com o seletor de data da plataforma. Defina um intervalo de datas permitidas com as [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) propriedades [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) e. A [`Date`](xref:Xamarin.Forms.DatePicker.Date) propriedade é a data selecionada e o [`DateSelected`](xref:Xamarin.Forms.DatePicker.DateSelected) evento é acionado quando essa propriedade é alterada.<br /><br />[API Documentation](xref:Xamarin.Forms.DatePicker) / [Guide](~/xamarin-forms/user-interface/datepicker.md) / [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker) de guia de documentação da API | [![Exemplo de DatePicker](views-images/DatePicker.png "Exemplo de DatePicker")](views-images/DatePicker-Large.png#lightbox "Exemplo de DatePicker")<br />[Código C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/DatePickerDemoPage.cs) / página[XAML página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/DatePickerDemoPage.xaml) |
|     |     |

### <a name="timepicker"></a>TimePicker

|     |     |
| --- | --- |
| [`TimePicker`](xref:Xamarin.Forms.TimePicker)permite que o usuário selecione uma hora com o seletor de tempo de plataforma. A [`Time`](xref:Xamarin.Forms.TimePicker.Time) propriedade é a hora selecionada. Um aplicativo pode monitorar alterações na `Time` Propriedade instalando um manipulador para o [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) evento.<br /><br />[API Documentation](xref:Xamarin.Forms.TimePicker) / [Guide](~/xamarin-forms/user-interface/timepicker.md) / [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker) de guia de documentação da API | [![Exemplo de timeseparar](views-images/TimePicker.png "Exemplo de timeseparar")](views-images/TimePicker-Large.png#lightbox "Exemplo de timeseparar")<br />[Código C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TimePickerDemoPage.cs) / página[XAML página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TimePickerDemoPage.xaml) |
|     |     |

## <a name="views-for-editing-text"></a>Exibições para edição de texto

Essas duas classes derivam da [`InputView`](xref:Xamarin.Forms.InputView) classe, que define a [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) propriedade.

### <a name="entry"></a>Entrada

|     |     |
| --- | --- |
| [`Entry`](xref:Xamarin.Forms.Entry)permite que o usuário insira e edite uma única linha de texto. O texto está disponível como a [`Text`](xref:Xamarin.Forms.InputView.Text) Propriedade e os [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) eventos e [`Completed`](xref:Xamarin.Forms.Entry.Completed) são acionados quando o texto é alterado ou o usuário sinaliza a conclusão tocando na tecla Enter.<br /><br />Use um [`Editor`](#editor) para inserir e editar várias linhas de texto.<br /><br />[API Documentation](xref:Xamarin.Forms.Entry) / [Guide](~/xamarin-forms/user-interface/text/entry.md) / [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) de guia de documentação da API | [![Exemplo de entrada](views-images/Entry.png "Exemplo de entrada")](views-images/Entry-Large.png#lightbox "Exemplo de entrada")<br />[Código C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryDemoPage.cs) / página[XAML página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryDemoPage.xaml) |
|     |     |

### <a name="editor"></a>Editor

|     |     |
| --- | --- |
| [`Editor`](xref:Xamarin.Forms.Editor)permite que o usuário insira e edite várias linhas de texto. O texto está disponível como a [`Text`](xref:Xamarin.Forms.InputView.Text) Propriedade, e os [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) eventos [`Completed`](xref:Xamarin.Forms.Editor.Completed) e são acionados quando o texto é alterado ou o usuário sinaliza a conclusão.<br /><br />Use uma [`Entry`](#entry) exibição para inserir e editar uma única linha de texto.<br /><br />[API Documentation](xref:Xamarin.Forms.Editor) / [Guide](~/xamarin-forms/user-interface/text/editor.md) / [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) de guia de documentação da API | [![Exemplo de entrada](views-images/Editor.png "Exemplo de editor")](views-images/Editor-Large.png#lightbox "Exemplo de editor")<br />[Código C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EditorDemoPage.cs) / página[XAML página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EditorDemoPage.xaml) |
|     |     |

## <a name="views-to-indicate-activity"></a>Exibições para indicar atividade

### <a name="activityindicator"></a>ActivityIndicator

|     |     |
| --- | --- |
| [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)usa uma animação para mostrar que o aplicativo está envolvido em uma atividade demorada sem dar qualquer indicação de progresso. A [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) propriedade controla a animação.<br /><br />Se o progresso da atividade for conhecido, use um [`ProgressBar`](#progressbar) em vez disso.<br /><br />[API Documentation](xref:Xamarin.Forms.ActivityIndicator) / [Guide](~/xamarin-forms/user-interface/activityindicator.md) / [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/) de guia de documentação da API | [![Exemplo de ActivityIndicator](views-images/ActivityIndicator.png "Exemplo de ActivityIndicator")](views-images/ActivityIndicator-Large.png#lightbox "Exemplo de ActivityIndicator")<br />[Código C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ActivityIndicatorDemoPage.cs) / página[XAML página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ActivityIndicatorDemoPage.xaml) |
|     |     |

### <a name="progressbar"></a>ProgressBar

|     |     |
| --- | --- |
| [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)usa uma animação para mostrar que o aplicativo está progredindo por meio de uma atividade demorada. Defina a [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress) Propriedade como valores entre 0 e 1 para indicar o progresso.<br /><br />Se o progresso da atividade não for conhecido, use um [`ActivityIndicator`](#activityindicator) em vez disso.<br /><br />[API Documentation](xref:Xamarin.Forms.ProgressBar) / [Guide](~/xamarin-forms/user-interface/progressbar.md) / [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/) de guia de documentação da API | [![Exemplo de ProgressBar](views-images/ProgressBar.png "Exemplo de ProgressBar")](views-images/ProgressBar-Large.png#lightbox "Exemplo de ProgressBar")<br />[Código C# para esta página XAML de página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ProgressBarDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml) com [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml.cs) |
|     |     |

## <a name="views-that-display-collections"></a>Exibições que mostram coleções

### <a name="carouselview"></a>CarouselView

|     |     |
| --- | --- |
| [`CarouselView`](xref:Xamarin.Forms.CarouselView)exibe uma lista rolável de itens de dados. Defina a `ItemsSource` Propriedade como uma coleção de objetos e defina a `ItemTemplate` Propriedade como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objeto que descreve como os itens devem ser formatados. O `CurrentItemChanged` evento sinaliza que o item exibido no momento foi alterado, que está disponível como `CurrentItem` a propriedade.<br /><br />[Guide](~/xamarin-forms/user-interface/carouselview/index.md) / [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/) de guia | [![Exemplo de CarouselView](views-images/CarouselView.png "Exemplo de CarouselView")](views-images/CarouselView-Large.png#lightbox "Exemplo de CarouselView")<br />[Código C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselViewDemoPage.cs) / página[XAML página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselViewDemoPage.xaml) |
|     |     |

### <a name="collectionview"></a>CollectionView

|     |     |
| --- | --- |
| [`CollectionView`](xref:Xamarin.Forms.CollectionView)exibe uma lista rolável de itens de dados selecionáveis, usando especificações de layout diferentes. Ele visa fornecer uma alternativa mais flexível e de alto desempenho para [`ListView`](xref:Xamarin.Forms.ListView)o. Defina a `ItemsSource` Propriedade como uma coleção de objetos e defina a `ItemTemplate` Propriedade como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objeto que descreve como os itens devem ser formatados. O `SelectionChanged` evento sinaliza que uma seleção foi feita, que está disponível como a `SelectedItem` propriedade.<br /><br />[Guide](~/xamarin-forms/user-interface/collectionview/index.md) / [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/) de guia | [![Exemplo de CollectionView](views-images/CollectionView.png "Exemplo de CollectionView")](views-images/CollectionView-Large.png#lightbox "Exemplo de CollectionView")<br />[Código C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CollectionViewDemoPage.cs) / página[XAML página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CollectionViewDemoPage.xaml) |
|     |     |

### <a name="indicatorview"></a>IndicatorView

|     |     |
| --- | --- |
| `IndicatorView`exibe os indicadores que representam o número de itens em `CarouselView`um. Defina a `CarouselView.IndicatorView` Propriedade como o `IndicatorView` objeto para exibir os indicadores para `CarouselView`o. <br /><br />[Guide](~/xamarin-forms/user-interface/indicatorview.md) / [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/) de guia | [![Exemplo de IndicatorView](views-images/IndicatorView.png "Exemplo de IndicatorView")](views-images/IndicatorView-Large.png#lightbox "Exemplo de IndicatorView")<br />[Código C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/IndicatorViewDemoPage.cs) / página[XAML página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/IndicatorViewDemoPage.xaml) |
|     |     |

### <a name="listview"></a>ListView

|     |     |
| --- | --- |
| [`ListView`](xref:Xamarin.Forms.ListView)deriva de [`ItemsView`](xref:Xamarin.Forms.ItemsView`1) e exibe uma lista rolável de itens de dados selecionáveis. Defina a [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) Propriedade como uma coleção de objetos e defina a [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) Propriedade como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objeto que descreve como os itens devem ser formatados. O [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento sinaliza que uma seleção foi feita, que está disponível como a [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) propriedade.<br /><br />[API Documentation](xref:Xamarin.Forms.ListView) / [Guide](~/xamarin-forms/user-interface/listview/index.md) / [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview/) de guia de documentação da API | [![Exemplo de ListView](views-images/ListView.png "Exemplo de ListView")](views-images/ListView-Large.png#lightbox "Exemplo de ListView")<br />[Código C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ListViewDemoPage.cs) / página[XAML página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ListViewDemoPage.xaml) |
|     |     |

### <a name="picker"></a>Seletor

|     |     |
| --- | --- |
| [`Picker`](xref:Xamarin.Forms.Picker)exibe um item selecionado de uma lista de cadeias de caracteres de texto e permite selecionar esse item quando a exibição é tocada. Defina a [`Items`](xref:Xamarin.Forms.Picker.Items) Propriedade como uma lista de cadeias de caracteres [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) ou a propriedade para uma coleção de objetos. O [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento é acionado quando um item é selecionado.<br /><br />O `Picker` exibe a lista de itens somente quando ele está selecionado. Use um [`ListView`](#listview) ou [`TableView`](#tableview) para uma lista rolável que permaneça na página.<br /><br />[API Documentation](xref:Xamarin.Forms.Picker) / [Guide](~/xamarin-forms/user-interface/picker/index.md) / [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo) de guia de documentação da API | [![Exemplo de seletor](views-images/Picker.png "Exemplo de seletor")](views-images/Picker-Large.png#lightbox "Exemplo de seletor")<br />[Código C# para esta página XAML de página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/PickerDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml) com [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml.cs) |
|     |     |

### <a name="tableview"></a>TableView

|     |     |
| --- | --- |
| [`TableView`](xref:Xamarin.Forms.TableView)exibe uma lista de linhas do tipo [`Cell`](xref:Xamarin.Forms.Cell) com cabeçalhos e subtítulos opcionais. Defina a [`Root`](xref:Xamarin.Forms.TableView.Root) Propriedade como um objeto do tipo [`TableRoot`](xref:Xamarin.Forms.TableRoot)e adicione [`TableSection`](xref:Xamarin.Forms.TableSection) objetos a ela `TableRoot`. Cada `TableSection` uma é uma coleção `Cell` de objetos.<br /><br />[API Documentation](xref:Xamarin.Forms.TableView) / [Guide](~/xamarin-forms/user-interface/tableview.md) / [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview) de guia de documentação da API | [![Exemplo de TableView](views-images/TableView.png "Exemplo de TableView")](views-images/TableView-Large.png#lightbox "Exemplo de TableView")<br />[Código C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TableViewFormDemoPage.cs) / página[XAML página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TableViewFormDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Links relacionados

- [Exemplo de FormsGallery do Xamarin. Forms](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Exemplos do Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Documentação da API do Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
