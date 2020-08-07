---
title: Controles XAML
description: Todas as exibições definidas em Xamarin.Forms podem ser referenciadas de arquivos XAML.
ms.topic: article
ms.prod: xamarin
ms.assetid: 639BD392-1496-41BB-BB09-7652273AC9D8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/09/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 94c451305f04294924b3f7115c4f2b3a7a6bad07
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918668"
---
# <a name="xaml-controls"></a>Controles XAML

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

As exibições são objetos de interface do usuário, como rótulos, botões e controles deslizantes que normalmente são conhecidos como *controles* ou *widgets* em outros ambientes de programação gráfica. As exibições com suporte de Xamarin.Forms todos derivam da [`View`](xref:Xamarin.Forms.View) classe.

Todas as exibições definidas em Xamarin.Forms podem ser referenciadas de arquivos XAML.

## <a name="views-for-presentation"></a>Exibições para apresentação

| Visualizar | Exemplo |
| --- | --- |
| <h3>BoxView</h3>Exibe um retângulo de uma cor específica.<p align="center">![Captura de tela de um BoxView](xaml-controls-images/BoxView.png "BoxView")</p>[API](xref:Xamarin.Forms.BoxView)  /  do [Guia](~/xamarin-forms/user-interface/boxview.md) | <p valign="center"><pre>&lt;BoxView Color="Accent"<br />         WidthRequest="150"<br />         HeightRequest="150"<br />         HorizontalOptions="Center"&gt;</pre></p> |
| <h3>Elipse</h3>Exibe uma elipse ou um círculo.<p align="center">![Captura de tela de uma elipse](xaml-controls-images/Ellipse.png "Elipse")</p>[API](xref:Xamarin.Forms.Shapes.Ellipse)  /  do [Guia](~/xamarin-forms/user-interface/shapes/ellipse.md) | <p valign="center"><pre>&lt;Ellipse Fill="Red"<br />         WidthRequest="150"<br />         HeightRequest="50"<br />         HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>Expansor</h3>Fornece um contêiner expansível para hospedar qualquer conteúdo.<p align="center">![Captura de tela de um expansor](xaml-controls-images/Expander.png "Expansor")</p>[Guia](~/xamarin-forms/user-interface/expander.md) | <pre>&lt;Expander&gt;<br />    &lt;Expander.Header&gt;<br />        &lt;Label Text="Baboon" /&gt;<br />    &lt;/Expander.Header&gt;<br />    &lt;Image Source="Baboon.png"<br />           Aspect="AspectFill" /&gt;<br />&lt;/Expander&gt;</pre></p> |
| <h3>Imagem</h3>Exibe um bitmap.<p align="center">![Captura de tela de uma imagem](xaml-controls-images/Image.png "Image")</p>[API](xref:Xamarin.Forms.Image)  /  do [Guia](~/xamarin-forms/user-interface/images.md) | <pre>&lt;Image Source="https://aka.ms/campus.jpg"<br />       Aspect="AspectFit"<br />       HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>Rótulo</h3>Exibe uma ou mais linhas de texto.<p align="center">![Captura de tela de um rótulo](xaml-controls-images/Label.png "Rotular")</p>[API](xref:Xamarin.Forms.Label)  /  do [Guia](~/xamarin-forms/user-interface/text/label.md) | <p valign="center"><pre>&lt;Label Text="Hello, Xamarin.Forms!"<br />       FontSize="Large"<br />       FontAttributes="Italic"<br />       HorizontalTextAlignment="Center" /&gt;</pre></p> |
| <h3>Linha</h3>Exibir uma linha.<p align="center">![Captura de tela de uma linha](xaml-controls-images/Line.png "Linha")</p>[API](xref:Xamarin.Forms.Shapes.Line)  /  do [Guia](~/xamarin-forms/user-interface/shapes/line.md) | <p valign="center"><pre>&lt;Line X1="40"<br />      Y1="0"<br />      X2="0"<br />      Y2="120"<br />      Stroke="Red"<br />      HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>Mapeamento</h3>Exibe um mapa.<p align="center">![Captura de tela de um mapa](xaml-controls-images/Map.png "Mapeamento")</p>[API](xref:Xamarin.Forms.Maps.Map)  /  do [Guia](~/xamarin-forms/user-interface/map/index.md) | <p valign="center"><pre>&lt;maps:Map ItemsSource="{Binding Locations}" /&gt;</pre></p> |
| <h3>MediaElement</h3>Reproduzir vídeo ou áudio.<p align="center">![Captura de tela de um MediaElement](xaml-controls-images/MediaElement.png "MediaELement")</p>[API](xref:Xamarin.Forms.MediaElement)  /  do [Guia](~/xamarin-forms/user-interface/mediaelement.md) | <p valign="center"><pre>&lt;MediaElement Source="https://sec.ch9.ms/ch9/XamarinShow_mid.mp4"<br />              AutoPlay="True"<br />              ShowsPlaybackControls="True" /&gt;</pre></p> |
| <h3>Caminho</h3>Exibir curvas e formas complexas.<p align="center">![Captura de tela de um caminho](xaml-controls-images/Path.png "Caminho")</p>[API](xref:Xamarin.Forms.Shapes.Path)  /  do [Guia](~/xamarin-forms/user-interface/shapes/path.md) | <p valign="center"><pre>&lt;Path Stroke="Black"<br />      Aspect="Uniform"<br />      HorizontalOptions="Center"<br />      HeightRequest="100"<br />      WidthRequest="100"<br />      Data="M13.9,16.2<br />            L32,16.2 32,31.9 13.9,30.1Z<br />            M0,16.2<br />            L11.9,16.2 11.9,29.9 0,28.6Z<br />            M11.9,2<br />            L11.9,14.2 0,14.2 0,3.3Z<br />            M32,0<br />            L32,14.2 13.9,14.2 13.9,1.8Z" /&gt;</pre></p> |
| <h3>Polygon</h3>Exibir um polígono.<p align="center">![Captura de tela de um polígono](xaml-controls-images/Polygon.png "Polygon")</p>[API](xref:Xamarin.Forms.Shapes.Polygon)  /  do [Guia](~/xamarin-forms/user-interface/shapes/polygon.md) | <p valign="center"><pre>&lt;Polygon Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96,<br/>                 50 96, 48 192, 150 200 144 48"<br />         Fill="Blue"<br />         Stroke="Red"<br />         StrokeThickness="3"<br />         HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>Linha poligonal</h3>Exibe uma série de linhas retas conectadas.<p align="center">![Captura de tela de uma polilinha](xaml-controls-images/Polyline.png "Linha poligonal")</p>[API](xref:Xamarin.Forms.Shapes.Polyline)  /  do [Guia](~/xamarin-forms/user-interface/shapes/Polyline.md) | <p valign="center"><pre>&lt;Polyline Points="0,0 10,30, 15,0 18,60 23,30 35,30 40,0<br />                  43,60 48,30 100,30"<br />          Stroke="Red"<br />          HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>Retângulo</h3>Exibir um retângulo ou quadrado.<p align="center">![Captura de tela de um retângulo](xaml-controls-images/Rectangle.png "Retângulo")</p>[API](xref:Xamarin.Forms.Shapes.Rectangle)  /  do [Guia](~/xamarin-forms/user-interface/shapes/rectangle.md) | <p valign="center"><pre>&lt;Rectangle Fill="Red"<br />           WidthRequest="150"<br />           HeightRequest="50"<br />           HorizontalOptions="Center" /&gt;</pre></p> |  
| <h3>WebView</h3>Exibe páginas da Web ou conteúdo HTML.<p align="center">![Captura de tela de um WebView](xaml-controls-images/WebView.png "WebView")</p>[API](xref:Xamarin.Forms.WebView)  /  do [Guia](~/xamarin-forms/user-interface/webview.md) | <p valign="center"><pre>&lt;WebView Source="https://docs.microsoft.com/xamarin/"<br/>         VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-initiate-commands"></a>Exibições que iniciam comandos

| Visualizar | Exemplo |
| --- | --- |
| <h3>Botão</h3>Exibe o texto em um objeto retangular.<p align="center">![Captura de tela de um botão](xaml-controls-images/Button.png "Botão")</p>[API](xref:Xamarin.Forms.Button)  /  do [Guia](~/xamarin-forms/user-interface/button.md) | <p valign="center"><pre>&lt;Button Text="Click Me!"<br />        Font="Large"<br />        BorderWidth="1"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand"<br />        Clicked="OnButtonClicked" /&gt;</pre></p> |
| <h3>ImageButton</h3>Exibe uma imagem em um objeto retangular.<p align="center">![Captura de tela de um ImageButton](xaml-controls-images/ImageButton.png "ImageButton")</p>[API](xref:Xamarin.Forms.ImageButton)  /  do [Guia](~/xamarin-forms/user-interface/imagebutton.md) | <p valign="center"><pre>&lt;ImageButton Source="XamarinLogo.png"<br />             HorizontalOptions="Center"<br />             VerticalOptions="CenterAndExpand"<br />             Clicked="OnImageButtonClicked" /&gt;</pre></p> |
| <h3>RadioButton</h3>Permite a seleção de uma opção de um conjunto.<p align="center">![Captura de tela de um RadioButton](xaml-controls-images/RadioButton.png "RadioButton")</p>[Guia](~/xamarin-forms/user-interface/radiobutton.md) | <p valign="center"><pre>&lt;RadioButton Text="Pineapple"<br/>             CheckedChanged="OnRadioButtonCheckedChanged" /&gt;</pre></p> |
| <h3>RefreshView</h3>Fornece funcionalidade de pull para atualização para conteúdo rolável.<p align="center">![Captura de tela de um RefreshView](xaml-controls-images/RefreshView.png "RefreshView")</p>[Guia](~/xamarin-forms/user-interface/refreshview.md) | <p valign="center"><pre>&lt;RefreshView IsRefreshing="{Binding IsRefreshing}"<br />             Command="{Binding RefreshCommand}" &gt;<br />    &lt;!-- Scrollable control goes here --&gt;<br />&lt;/RefreshView&gt;</pre></p> |
| <h3>SearchBar</h3> Aceita a entrada do usuário que ele usa para executar uma pesquisa.<p align="center">![Captura de tela de um SearchBar](xaml-controls-images/SearchBar.png "SearchBar")</p>[Guia](~/xamarin-forms/user-interface/searchbar.md) | <p valign="center"><pre>&lt;SearchBar Placeholder="Enter search term"<br />           SearchButtonPressed="OnSearchBarButtonPressed" /&gt;</pre></p> |
| <h3>SwipeView</h3> Fornece itens de menu de contexto que são revelados por um gesto de passar o dedo.<p align="center">![Captura de tela de um SwipeView](xaml-controls-images/SwipeView.png "SwipeView")</p>[Guia](~/xamarin-forms/user-interface/swipeview.md) | <p valign="center"><pre>&lt;SwipeView&gt;<br />    &lt;SwipeView.LeftItems&gt;<br />        &lt;SwipeItems&gt;<br />            &lt;SwipeItem Text="Delete"<br />                       IconImageSource="delete.png"<br />                       BackgroundColor="LightPink"<br />                       Invoked="OnDeleteInvoked" /&gt;<br />        &lt;/SwipeItems&gt;<br />    &lt;/SwipeView.LeftItems&gt;<br />    &lt;!-- Content --&gt;<br />&lt;/SwipeView&gt;</pre></p> |
|     |     |

## <a name="views-for-setting-values"></a>Exibições para definir valores

| Visualizar | Exemplo |
| --- | --- |
| <h3>CheckBox</h3>Permite a seleção de um `boolean` valor.<p align="center">![Captura de tela de uma caixa de seleção](xaml-controls-images/CheckBox.png "CheckBox")</p> [Guia](~/xamarin-forms/user-interface/checkbox.md) | <p valign="center"><pre>&lt;CheckBox IsChecked="true"<br />          HorizontalOptions="Center"<br />          VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Controle deslizante</h3>Permite a seleção de um `double` valor de um intervalo contínuo.<p align="center">![Captura de tela de um controle deslizante](xaml-controls-images/Slider.png "Controle deslizante")</p>[API](xref:Xamarin.Forms.Slider)  /  do [Guia](~/xamarin-forms/user-interface/slider.md) | <p valign="center"><pre>&lt;Slider Minimum="0"<br />        Maximum="100"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Passador</h3>Permite a seleção de um `double` valor de um intervalo incremental.<p align="center">![Captura de tela de um stepper](xaml-controls-images/Stepper.png "Passador")</p>[API](xref:Xamarin.Forms.Stepper)  /  do [Guia](~/xamarin-forms/user-interface/stepper.md) | <p valign="center"><pre>&lt;Stepper Minimum="0"<br />         Maximum="10"<br />         Increment="0.1"<br />         HorizontalOptions="Center"<br />         VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Alternar</h3>Permite a seleção de um `boolean` valor.<p align="center">![Captura de tela de um comutador](xaml-controls-images/Switch.png "Alternar")</p>[API](xref:Xamarin.Forms.Switch)  /  do [Guia](~/xamarin-forms/user-interface/switch.md)| <p valign="center"><pre>&lt;Switch IsToggled="false"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>DatePicker</h3>Permite a seleção de uma data.<p align="center">![Captura de tela de um DatePicker](xaml-controls-images/DatePicker.png "DatePicker")</p>[API](xref:Xamarin.Forms.DatePicker)  /  do [Guia](~/xamarin-forms/user-interface/datepicker.md) | <p valign="center"><pre>&lt;DatePicker Format="D"<br/>            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>TimePicker</h3>Permite a seleção de uma hora.<p align="center">![Captura de tela de um seletor de](xaml-controls-images/TimePicker.png "TimePicker")</p>[API](xref:Xamarin.Forms.TimePicker)  /  do [Guia](~/xamarin-forms/user-interface/timepicker.md) | <p valign="center"><pre>&lt;TimePicker Format="T"<br />            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-for-editing-text"></a>Exibições para edição de texto

| Visualizar | Exemplo |
| --- | --- |
| <h3>Entrada</h3>Permite que uma única linha de texto seja inserida e editada.<p align="center">![Captura de tela de uma entrada](xaml-controls-images/Entry.png "Entrada")</p>[API](xref:Xamarin.Forms.Entry)  /  do [Guia](~/xamarin-forms/user-interface/text/entry.md) | <p valign="center"><pre>&lt;Entry Keyboard="Email"<br />       Placeholder="Enter email address"<br />       VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Editor</h3>Permite que várias linhas de texto sejam inseridas e editadas.<p align="center">![Captura de tela de um editor](xaml-controls-images/Editor.png "Rótulo")</p>[API](xref:Xamarin.Forms.Editor)  /  do [Guia](~/xamarin-forms/user-interface/text/editor.md) | <p valign="center"><pre>&lt;Editor VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-to-indicate-activity"></a>Exibições para indicar atividade

| Visualizar | Exemplo |
| --- | --- |
| <h3>ActivityIndicator</h3>Exibe uma animação para mostrar que o aplicativo está envolvido em uma atividade demorada, sem dar nenhuma indicação de progresso.<p align="center">![Captura de tela de um ActivityIndicator](xaml-controls-images/ActivityIndicator.png "ActivityIndicator")</p>[API](xref:Xamarin.Forms.ActivityIndicator)  /  do [Guia](~/xamarin-forms/user-interface/activityindicator.md) | <p valign="center"><pre>&lt;ActivityIndicator IsRunning="True"<br />                   VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>ProgressBar</h3>Exibe uma animação para mostrar que o aplicativo está progredindo por meio de uma atividade demorada.<p align="center">![Captura de tela de um ProgressBar](xaml-controls-images/ProgressBar.png "ProgressBar")</p>[API](xref:Xamarin.Forms.ProgressBar)  /  do [Guia](~/xamarin-forms/user-interface/progressbar.md) | <p valign="center"><pre>&lt;ProgressBar Progress=".5"<br />             VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-display-collections"></a>Exibições que mostram coleções

| Visualizar | Exemplo |
| --- | --- |
| <h3>CarouselView</h3>Exibe uma lista rolável de itens de dados.<p align="center">![Captura de tela de um CarouselView](xaml-controls-images/CarouselView.png "CarouselView")</p>[Guia](~/xamarin-forms/user-interface/carouselview/index.md) | <p valign="center"><pre>&lt;CarouselView ItemsSource="{Binding Monkeys}"&gt;<br/>              ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p>|
| <h3>CollectionView</h3>Exibe uma lista rolável de itens de dados selecionáveis, usando especificações de layout diferentes.<p align="center">![Captura de tela de um CollectionView](xaml-controls-images/CollectionView.png "CollectionView")</p>[Guia](~/xamarin-forms/user-interface/collectionview/index.md) | <p valign="center"><pre>&lt;CollectionView ItemsSource="{Binding Monkeys}"&gt;<br/>                ItemTemplate="{StaticResource MonkeyTemplate}"<br />                ItemsLayout="VerticalGrid, 2" /&gt;</pre></p> |
| <h3>IndicatorView</h3>Exibe os indicadores que representam o número de itens em um `CarouselView` .<p align="center">![Captura de tela de um IndicatorView](xaml-controls-images/IndicatorView.png "IndicatorView")</p>[Guia](~/xamarin-forms/user-interface/indicatorview.md) | <p valign="center"><pre>&lt;IndicatorView x:Name="indicatorView"<br />               IndicatorColor="LightGray"<br />               SelectedIndicatorColor="DarkGray" /&gt;</pre></p> |
| <h3>ListView</h3>Exibe uma lista rolável de itens de dados selecionáveis.<p align="center">![Captura de tela de um ListView](xaml-controls-images/ListView.png "ListView")</p>[API](xref:Xamarin.Forms.ListView)  /  do [Guia](~/xamarin-forms/user-interface/listview/index.md) | <p valign="center"><pre>&lt;ListView ItemsSource="{Binding Monkeys}"&gt;<br />          ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p> |
| <h3>Seletor</h3>Exibe um item de seleção de uma lista de cadeias de caracteres de texto.<p align="center">![Captura de tela de um seletor](xaml-controls-images/Picker.png "Seletor")</p>[API](xref:Xamarin.Forms.Picker)  /  do [Guia](~/xamarin-forms/user-interface/picker/index.md) | <p valign="center"><pre>&lt;Picker Title="Select a monkey"<br />        TitleColor="Red"&gt;<br />  &lt;Picker.ItemsSource&gt;<br />    &lt;x:Array Type="{x:Type x:String}"&gt;<br />      &lt;x:String&gt;Baboon&lt;/x:String&gt;<br />      &lt;x:String&gt;Capuchin Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Blue Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Squirrel Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Golden Lion Tamarin&lt;/x:String&gt;<br />      &lt;x:String&gt;Howler Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Japanese Macaque&lt;/x:String&gt;<br />    &lt;/x:Array&gt;<br />  &lt;/Picker.ItemsSource&gt;<br />&lt;/Picker&gt;</pre></p> |
| <h3>TableView</h3>Exibe uma lista de linhas interativas.<p align="center">![Captura de tela de um TableView](xaml-controls-images/TableView.png "TableView")</p>[API](xref:Xamarin.Forms.TableView)  /  do [Guia](~/xamarin-forms/user-interface/tableview.md) | <p valign="center"><pre>&lt;TableView Intent="Settings"&gt;<br />    &lt;TableRoot&gt;<br />        &lt;TableSection Title="Ring"&gt;<br />            &lt;SwitchCell Text="New Voice Mail" /&gt;<br />            &lt;SwitchCell Text="New Mail" On="true" /&gt;<br />        &lt;/TableSection&gt;<br />    &lt;/TableRoot&gt;<br />&lt;/TableView&gt;</pre></p> |
|     |     |

## <a name="related-links"></a>Links relacionados

- [Xamarin.FormsExemplo de FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.FormsAmostras](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.FormsDocumentação da API](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
