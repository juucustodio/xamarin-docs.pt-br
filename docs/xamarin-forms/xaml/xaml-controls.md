---
title: Controles XAML
description: Todas as exibições definidas no Xamarin. Forms podem ser referenciadas de arquivos XAML.
ms.topic: article
ms.prod: xamarin
ms.assetid: 639BD392-1496-41BB-BB09-7652273AC9D8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/11/2019
ms.openlocfilehash: f73de44cbae0281d56cc6fd7ab21f8aa60264ed4
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696116"
---
# <a name="xaml-controls"></a>Controles XAML

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

As exibições são objetos de interface do usuário, como rótulos, botões e controles deslizantes que normalmente são conhecidos como *controles* ou *widgets* em outros ambientes de programação gráfica. As exibições suportadas pelo Xamarin. Forms derivam da classe [`View`](xref:Xamarin.Forms.View) .

Todas as exibições definidas no Xamarin. Forms podem ser referenciadas de arquivos XAML.

## <a name="views-for-presentation"></a>Exibições para apresentação

|     |     |
| --- | --- |
| <h3>BoxView</h3>Exibe um retângulo de uma cor específica.<p align="center">![Captura de tela de um BoxView](xaml-controls-images/BoxView.png "BoxView")</p>[Guia](~/xamarin-forms/user-interface/boxview.md) de  /  de [API](xref:Xamarin.Forms.BoxView) | <pre valign="center">&lt;BoxView Color="Accent"<br />         WidthRequest="150"<br />         HeightRequest="150"<br />         HorizontalOptions="Center"&gt;</pre></p> |
| <h3>Image</h3>Exibe um bitmap.<p align="center">![Captura de tela de uma imagem](xaml-controls-images/Image.png "Image")</p>[Guia](~/xamarin-forms/user-interface/images.md) de  /  de [API](xref:Xamarin.Forms.Image) | <pre>&lt;Image Source="https://aka.ms/campus.jpg"<br />       Aspect="AspectFit"<br />       HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>Rotular</h3>Exibe uma ou mais linhas de texto.<p align="center">![Captura de tela de um rótulo](xaml-controls-images/Label.png "Rotular")</p>[Guia](~/xamarin-forms/user-interface/text/label.md) de  /  de [API](xref:Xamarin.Forms.Label) | <p valign="center"><pre>&lt;Label Text="Hello, Xamarin.Forms!"<br />       FontSize="Large"<br />       FontAttributes="Italic"<br />       HorizontalTextAlignment="Center" /&gt;</pre></p> |
| <h3>Mapa</h3>Exibe um mapa.<p align="center">![Captura de tela de um mapa](xaml-controls-images/Map.png "Mapa")</p>[Guia](~/xamarin-forms/user-interface/map/index.md) de  /  de [API](xref:Xamarin.Forms.Maps.Map) | <p valign="center"><pre>&lt;maps:Map ItemsSource="{Binding Locations}" /&gt;</pre></p> |
| <h3>WebView</h3>Exibe páginas da Web ou conteúdo HTML.<p align="center">![Captura de tela de um WebView](xaml-controls-images/WebView.png "WebView")</p>[Guia](~/xamarin-forms/user-interface/webview.md) de  /  de [API](xref:Xamarin.Forms.WebView) | <p valign="center"><pre>&lt;WebView Source="https://docs.microsoft.com/xamarin/"<br/>         VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-initiate-commands"></a>Exibições que iniciam comandos

|     |     |
| --- | --- |
| <h3>Botão</h3>Exibe o texto em um objeto retangular.<p align="center">![Captura de tela de um botão](xaml-controls-images/Button.png "Botão")</p>[Guia](~/xamarin-forms/user-interface/button.md) de  /  de [API](xref:Xamarin.Forms.Button) | <p valign="center"><pre>&lt;Button Text="Click Me!"<br />        Font="Large"<br />        BorderWidth="1"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand"<br />        Clicked="OnButtonClicked" /&gt;</pre></p> |
| <h3>ImageButton</h3>Exibe uma imagem em um objeto retangular.<p align="center">![Captura de tela de um ImageButton](xaml-controls-images/ImageButton.png "ImageButton")</p>[Guia](~/xamarin-forms/user-interface/imagebutton.md) de  /  de [API](xref:Xamarin.Forms.ImageButton) | <p valign="center"><pre>&lt;ImageButton Source="XamarinLogo.png"<br />             HorizontalOptions="Center"<br />             VerticalOptions="CenterAndExpand"<br />             Clicked="OnImageButtonClicked" /&gt;</pre></p> |
| <h3>RefreshView</h3>Fornece funcionalidade de pull para atualização para conteúdo rolável.<p align="center">![Captura de tela de um RefreshView](xaml-controls-images/RefreshView.png "RefreshView")</p>[Orienta](~/xamarin-forms/user-interface/refreshview.md) | <p valign="center"><pre>&lt;RefreshView IsRefreshing="{Binding IsRefreshing}"<br />             Command="{Binding RefreshCommand}" &gt;<br />    &lt;!-- Scrollable control goes here --&gt;<br />&lt;/RefreshView&gt;</pre></p> |

## <a name="views-for-setting-values"></a>Exibições para definir valores

|     |     |
| --- | --- |
| <h3>CheckBox</h3>Permite a seleção de um valor de `boolean`.<p align="center">![Captura de tela de uma caixa de seleção](xaml-controls-images/CheckBox.png "CheckBox")</p> [Orienta](~/xamarin-forms/user-interface/checkbox.md) | <p valign="center"><pre>&lt;CheckBox IsChecked="true"<br />          HorizontalOptions="Center"<br />          VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Controle deslizante</h3>Permite a seleção de um valor de `double` de um intervalo contínuo.<p align="center">![Captura de tela de um controle deslizante](xaml-controls-images/Slider.png "Controle deslizante")</p>[Guia](~/xamarin-forms/user-interface/slider.md) de  /  de [API](xref:Xamarin.Forms.Slider) | <p valign="center"><pre>&lt;Slider Minimum="0"<br />        Maximum="100"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Passador</h3>Permite a seleção de um valor de `double` de um intervalo incremental.<p align="center">![Captura de tela de um stepper](xaml-controls-images/Stepper.png "Passador")</p>[Guia](~/xamarin-forms/user-interface/stepper.md) de  /  de [API](xref:Xamarin.Forms.Stepper) | <p valign="center"><pre>&lt;Stepper Minimum="0"<br />         Maximum="10"<br />         Increment="0.1"<br />         HorizontalOptions="Center"<br />         VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Alternar</h3>Permite a seleção de um valor de `boolean`.<p align="center">![Captura de tela de um comutador](xaml-controls-images/Switch.png "Alternar")</p>[Guia](~/xamarin-forms/user-interface/switch.md) de  /  de [API](xref:Xamarin.Forms.Switch)| <p valign="center"><pre>&lt;Switch IsToggled="false"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>DatePicker</h3>Permite a seleção de uma data.<p align="center">![Captura de tela de um DatePicker](xaml-controls-images/DatePicker.png "DatePicker")</p>[Guia](~/xamarin-forms/user-interface/datepicker.md) de  /  de [API](xref:Xamarin.Forms.DatePicker) | <p valign="center"><pre>&lt;DatePicker Format="D"<br/>            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>TimePicker</h3>Permite a seleção de uma hora.<p align="center">![Captura de tela de um seletor de](xaml-controls-images/TimePicker.png "TimePicker")</p>[Guia](~/xamarin-forms/user-interface/timepicker.md) de  /  de [API](xref:Xamarin.Forms.TimePicker) | <p valign="center"><pre>&lt;TimePicker Format="T"<br />            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-for-editing-text"></a>Exibições para edição de texto

|     |     |
| --- | --- |
| <h3>Entrada</h3>Permite que uma única linha de texto seja inserida e editada.<p align="center">![Captura de tela de uma entrada](xaml-controls-images/Entry.png "Entrada")</p>[Guia](~/xamarin-forms/user-interface/text/entry.md) de  /  de [API](xref:Xamarin.Forms.Entry) | <p valign="center"><pre>&lt;Entry Keyboard="Email"<br />       Placeholder="Enter email address"<br />       VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Editor</h3>Permite que várias linhas de texto sejam inseridas e editadas.<p align="center">![Captura de tela de um editor](xaml-controls-images/Editor.png "Rotular")</p>[Guia](~/xamarin-forms/user-interface/text/editor.md) de  /  de [API](xref:Xamarin.Forms.Editor) | <p valign="center"><pre>&lt;Editor VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-to-indicate-activity"></a>Exibições para indicar atividade

|     |     |
| --- | --- |
| <h3>ActivityIndicator</h3>Exibe uma animação para mostrar que o aplicativo está envolvido em uma atividade demorada, sem dar nenhuma indicação de progresso.<p align="center">![Captura de tela de um ActivityIndicator](xaml-controls-images/ActivityIndicator.png "ActivityIndicator")</p>[Guia](~/xamarin-forms/user-interface/activityindicator.md) de  /  de [API](xref:Xamarin.Forms.ActivityIndicator) | <p valign="center"><pre>&lt;ActivityIndicator IsRunning="True"<br />                   VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>ProgressBar</h3>Exibe uma animação para mostrar que o aplicativo está progredindo por meio de uma atividade demorada.<p align="center">![Captura de tela de um ProgressBar](xaml-controls-images/ProgressBar.png "ProgressBar")</p>[Guia](~/xamarin-forms/user-interface/progressbar.md) de  /  de [API](xref:Xamarin.Forms.ProgressBar) | <p valign="center"><pre>&lt;ProgressBar Progress=".5"<br />             VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-display-collections"></a>Exibições que exibem coleções

|     |     |
| --- | --- |
| <h3>CarouselView</h3>Exibe uma lista rolável de itens de dados.<p align="center">![Captura de tela de um CarouselView](xaml-controls-images/CarouselView.png "CarouselView")</p>[Orienta](~/xamarin-forms/user-interface/carouselview/index.md) | <p valign="center"><pre>&lt;CarouselView ItemsSource="{Binding Monkeys}"&gt;<br/>              ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p>|
| <h3>CollectionView</h3>Exibe uma lista rolável de itens de dados selecionáveis, usando especificações de layout diferentes.<p align="center">![Captura de tela de um CollectionView](xaml-controls-images/CollectionView.png "CollectionView")</p>[Orienta](~/xamarin-forms/user-interface/collectionview/index.md) | <p valign="center"><pre>&lt;CollectionView ItemsSource="{Binding Monkeys}"&gt;<br/>                ItemTemplate="{StaticResource MonkeyTemplate}"<br />    &lt;CollectionView.ItemsLayout&gt;<br />       &lt;GridItemsLayout Orientation="Vertical"<br />                        Span="2" /&gt;<br />    &lt;/CollectionView.ItemsLayout&gt;<br />&lt;/CollectionView/&gt;</pre></p> |
| <h3>ListView</h3>Exibe uma lista rolável de itens de dados selecionáveis.<p align="center">![Captura de tela de um ListView](xaml-controls-images/ListView.png "ListView")</p>[Guia](~/xamarin-forms/user-interface/listview/index.md) de  /  de [API](xref:Xamarin.Forms.ListView) | <p valign="center"><pre>&lt;ListView ItemsSource="{Binding Monkeys}"&gt;<br />          ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p> |
| <h3>Seletor</h3>Exibe um item de seleção de uma lista de cadeias de caracteres de texto.<p align="center">![Captura de tela de um seletor](xaml-controls-images/Picker.png "Seletor")</p>[Guia](~/xamarin-forms/user-interface/picker/index.md) de  /  de [API](xref:Xamarin.Forms.Picker) | <p valign="center"><pre>&lt;Picker Title="Select a monkey"<br />        TitleColor="Red"&gt;<br />  &lt;Picker.ItemsSource&lt;<br />    &lt;x:Array Type="{x:Type x:String}"&gt;<br />      &lt;x:String&gt;Baboon&lt;/x:String&gt;<br />      &lt;x:String&gt;Capuchin Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Blue Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Squirrel Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Golden Lion Tamarin&lt;/x:String&gt;<br />      &lt;x:String&gt;Howler Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Japanese Macaque&lt;/x:String&gt;<br />    &lt;/x:Array&gt;<br />  &lt;/Picker.ItemsSource&gt;<br />&lt;/Picker&gt;</pre></p> |
| <h3>TableView</h3>Exibe uma lista de linhas interativas.<p align="center">![Captura de tela de um TableView](xaml-controls-images/TableView.png "TableView")</p>[Guia](~/xamarin-forms/user-interface/tableview.md) de  /  de [API](xref:Xamarin.Forms.TableView) | <p valign="center"><pre>&lt;TableView Intent="Settings"&gt;<br />    &lt;TableRoot&gt;<br />        &lt;TableSection Title="Ring"&gt;<br />            &lt;SwitchCell Text="New Voice Mail" /&gt;<br />            &lt;SwitchCell Text="New Mail" On="true" /&gt;<br />        &lt;/TableSection&gt;<br />    &lt;/TableRoot&gt;<br />&lt;/TableView&gt;</pre></p> |
|     |     |

## <a name="related-links"></a>Links relacionados

- [Exemplo de FormsGallery do Xamarin. Forms](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Amostras do Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Documentação da API do Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
