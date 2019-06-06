---
title: Controles XAML
description: Todas as exibições que são definidas no xamarin. Forms podem ser referenciadas em arquivos XAML.
ms.topic: article
ms.prod: xamarin
ms.assetid: 639BD392-1496-41BB-BB09-7652273AC9D8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/03/2019
ms.openlocfilehash: 8217c6436cc8cfe8f4b9c6cc3445157319e8c0bf
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66741110"
---
# <a name="xaml-controls"></a>Controles XAML

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/)

Modos de exibição são objetos de interface do usuário, como rótulos, botões e controles deslizantes que são normalmente conhecidos como *controles* ou *widgets* em outros ambientes de programação gráficas. As exibições compatíveis com o xamarin. Forms todos derivam de [ `View` ](xref:Xamarin.Forms.View) classe.

Todas as exibições que são definidas no xamarin. Forms podem ser referenciadas em arquivos XAML.

## <a name="views-for-presentation"></a>Modos de exibição para apresentação

|     |     |
| --- | --- |
| <h3>BoxView</h3>Exibe um retângulo de uma cor específica.<p align="center">![Captura de tela de um BoxView](xaml-controls-images/BoxView.png "BoxView")</p>[API](xref:Xamarin.Forms.BoxView) / [guia](https://developer.xamarin.com/guides/xamarin-forms/user-interface/boxview/) | <pre valign="center">&lt;BoxView Color="Accent"<br />         WidthRequest="150"<br />         HeightRequest="150"<br />         HorizontalOptions="Center"&gt;</pre></p> |
| <h3>Image</h3>Exibe um bitmap.<p align="center">![Captura de tela de uma imagem](xaml-controls-images/Image.png "imagem")</p>[API](xref:Xamarin.Forms.Image) / [guia](~/xamarin-forms/user-interface/images.md) | <pre>&lt;Image Source="https://aka.ms/campus.jpg"<br />       Aspect="AspectFit"<br />       HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>Rotular</h3>Exibe um ou mais linhas de texto.<p align="center">![Captura de tela de um rótulo](xaml-controls-images/Label.png "rótulo")</p>[API](xref:Xamarin.Forms.Label) / [guia](~/xamarin-forms/user-interface/text/label.md) | <p valign="center"><pre>&lt;Label Text="Hello, Xamarin.Forms!"<br />       FontSize="Large"<br />       FontAttributes="Italic"<br />       HorizontalTextAlignment="Center" /&gt;</pre></p> |
| <h3>Mapa</h3>Exibe um mapa.<p align="center">![Captura de tela de um mapa](xaml-controls-images/Map.png "mapa")</p>[API](xref:Xamarin.Forms.Maps.Map) / [guia](~/xamarin-forms/user-interface/map.md) | <p valign="center"><pre>&lt;maps:Map ItemsSource="{Binding Locations}" /&gt;</pre></p> |
| <h3>WebView</h3>Exibe as páginas da Web ou conteúdo HTML.<p align="center">![Captura de tela do WebView](xaml-controls-images/WebView.png "WebView")</p>[API](xref:Xamarin.Forms.WebView) / [guia](~/xamarin-forms/user-interface/webview.md) | <p valign="center"><pre>&lt;WebView Source="https://docs.microsoft.com/xamarin/"<br/>         VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-initiate-commands"></a>Modos de exibição que iniciam comandos

|     |     |
| --- | --- |
| <h3>Botão</h3>Exibe o texto em um objeto retangular.<p align="center">![Captura de tela de um botão](xaml-controls-images/Button.png "botão")</p>[API](xref:Xamarin.Forms.Button) / [guia](~/xamarin-forms/user-interface/button.md) | <p valign="center"><pre>&lt;Button Text="Click Me!"<br />        Font="Large"<br />        BorderWidth="1"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand"<br />        Clicked="OnButtonClicked" /&gt;</pre></p> |
| <h3>ImageButton</h3>Exibe uma imagem em um objeto retangular.<p align="center">![Captura de tela de um ImageButton](xaml-controls-images/ImageButton.png "ImageButton")</p>[API](xref:Xamarin.Forms.ImageButton) / [guia](~/xamarin-forms/user-interface/imagebutton.md) | <p valign="center"><pre>&lt;ImageButton Source="XamarinLogo.png"<br />             HorizontalOptions="Center"<br />             VerticalOptions="CenterAndExpand"<br />             Clicked="OnImageButtonClicked" /&gt;</pre></p> |
| <h3>SearchBar</h3>Exibe uma barra de pesquisa, para executar uma pesquisa.<p align="center">![Captura de tela de um SearchBar](xaml-controls-images/SearchBar.png "SearchBar")</p>[API](xref:Xamarin.Forms.SearchBar) | <p valign="center"><pre>&lt;SearchBar Placeholder="Xamarin.Forms Property"<br />           SearchButtonPressed="OnSearchBarButtonPressed" /&gt;</pre></p> |
|     |     |

## <a name="views-for-setting-values"></a>Modos de exibição para definir valores

|     |     |
| --- | --- |
| <h3>Controle deslizante</h3>Permite a seleção de um `double` valor de um intervalo contínuo.<p align="center">![Captura de tela de um controle deslizante](xaml-controls-images/Slider.png "controle deslizante")</p>[API](xref:Xamarin.Forms.Slider) / [guia](~/xamarin-forms/user-interface/slider.md) | <p valign="center"><pre>&lt;Slider Minimum="0"<br />        Maximum="100"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Escalonador</h3>Permite a seleção de um `double` valor de um intervalo de incremental.<p align="center">![Captura de tela de um seletor](xaml-controls-images/Stepper.png "escalonador")</p>[API](xref:Xamarin.Forms.Stepper) / [guia](~/xamarin-forms/user-interface/stepper.md) | <p valign="center"><pre>&lt;Stepper Minimum="0"<br />         Maximum="10"<br />         Increment="0.1"<br />         HorizontalOptions="Center"<br />         VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Alternar</h3>Permite a seleção de um `boolean` valor.<p align="center">![Captura de tela de um comutador](xaml-controls-images/Switch.png "Switch")</p>[API](xref:Xamarin.Forms.Switch) | <p valign="center"><pre>&lt;Switch IsToggled="false"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>DatePicker</h3>Permite a seleção de uma data.<p align="center">![Captura de tela de um DatePicker](xaml-controls-images/DatePicker.png "DatePicker")</p>[API](xref:Xamarin.Forms.DatePicker) / [guia](~/xamarin-forms/user-interface/datepicker.md) | <p valign="center"><pre>&lt;DatePicker Format="D"<br/>            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>TimePicker</h3>Permite a seleção de uma hora.<p align="center">![Captura de tela de um TimePicker](xaml-controls-images/TimePicker.png "TimePicker")</p>[API](xref:Xamarin.Forms.TimePicker) / [guia](~/xamarin-forms/user-interface/timepicker.md) | <p valign="center"><pre>&lt;TimePicker Format="T"<br />            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-for-editing-text"></a>Modos de exibição de edição de texto

|     |     |
| --- | --- |
| <h3>Entrada</h3>Permite que uma única linha de texto a ser inserido e editadas.<p align="center">![Captura de tela de uma entrada](xaml-controls-images/Entry.png "entrada")</p>[API](xref:Xamarin.Forms.Entry) / [guia](~/xamarin-forms/user-interface/text/entry.md) | <p valign="center"><pre>&lt;Entry Keyboard="Email"<br />       Placeholder="Enter email address"<br />       VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Editor</h3>Permite que várias linhas de texto a ser inserido e editado.<p align="center">![Captura de tela de um Editor](xaml-controls-images/Editor.png "rótulo")</p>[API](xref:Xamarin.Forms.Editor) / [guia](~/xamarin-forms/user-interface/text/editor.md) | <p valign="center"><pre>&lt;Editor VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-to-indicate-activity"></a>Modos de exibição para indicar a atividade

|     |     |
| --- | --- |
| <h3>ActivityIndicator</h3>Exibe uma animação para mostrar que o aplicativo está envolvido em uma atividade demorada, sem fornecer nenhuma indicação de progresso.<p align="center">![Captura de tela de um ActivityIndicator](xaml-controls-images/ActivityIndicator.png "ActivityIndicator")</p>[API](xref:Xamarin.Forms.ActivityIndicator) | <p valign="center"><pre>&lt;ActivityIndicator IsRunning="True"<br />                   VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>ProgressBar</h3>Exibe uma animação para mostrar que o aplicativo está em andamento através de uma atividade demorada.<p align="center">![Captura de tela de um ProgressBar](xaml-controls-images/ProgressBar.png "ProgressBar")</p>[API](xref:Xamarin.Forms.ProgressBar) | <p valign="center"><pre>&lt;ProgressBar Progress=".5"<br />             VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-display-collections"></a>Exibições que mostram coleções

|     |     |
| --- | --- |
| <h3>CollectionView</h3>Exibe uma lista rolável de itens selecionáveis de dados, usando as especificações de layout diferente.<p align="center">![Captura de tela de uma CollectionView](xaml-controls-images/CollectionView.png "CollectionView")</p>[Guia](~/xamarin-forms/user-interface/collectionview/index.md) | <p valign="center"><pre>&lt;CollectionView ItemsSource="{Binding Monkeys}"&gt;<br/>                ItemTemplate="{StaticResource MonkeyTemplate}"<br />    &lt;CollectionView.ItemsLayout&gt;<br />       &lt;GridItemsLayout Orientation="Vertical"<br />                        Span="2" /&gt;<br />    &lt;/CollectionView.ItemsLayout&gt;<br />&lt;/CollectionView/&gt;</pre></p> |
| <h3>ListView</h3>Exibe uma lista rolável de itens de dados podem ser selecionados.<p align="center">![Captura de tela de um ListView](xaml-controls-images/ListView.png "ListView")</p>[API](xref:Xamarin.Forms.ListView) / [guia](~/xamarin-forms/user-interface/listview/index.md) | <p valign="center"><pre>&lt;ListView ItemsSource="{Binding Monkeys}"&gt;<br />          ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p> |
| <h3>Seletor</h3>Exibe o item de seleção de uma lista de cadeias de caracteres de texto.<p align="center">![Captura de tela de um seletor](xaml-controls-images/Picker.png "seletor")</p>[API](xref:Xamarin.Forms.Picker) / [guia](~/xamarin-forms/user-interface/picker/index.md) | <p valign="center"><pre>&lt;Picker Title="Select a monkey"<br />        TitleColor="Red"&gt;<br />  &lt;Picker.ItemsSource&lt;<br />    &lt;x:Array Type="{x:Type x:String}"&gt;<br />      &lt;x:String&gt;Baboon&lt;/x:String&gt;<br />      &lt;x:String&gt;Capuchin Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Blue Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Squirrel Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Golden Lion Tamarin&lt;/x:String&gt;<br />      &lt;x:String&gt;Howler Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Japanese Macaque&lt;/x:String&gt;<br />    &lt;/x:Array&gt;<br />  &lt;/Picker.ItemsSource&gt;<br />&lt;/Picker&gt;</pre></p> |
| <h3>Modo de tabela</h3>Exibe uma lista de linhas interativas.<p align="center">![Captura de tela de um modo de tabela](xaml-controls-images/TableView.png "modo de tabela")</p>[API](xref:Xamarin.Forms.TableView) / [guia](~/xamarin-forms/user-interface/tableview.md) | <p valign="center"><pre>&lt;TableView Intent="Settings"&gt;<br />    &lt;TableRoot&gt;<br />        &lt;TableSection Title="Ring"&gt;<br />            &lt;SwitchCell Text="New Voice Mail" /&gt;<br />            &lt;SwitchCell Text="New Mail" On="true" /&gt;<br />        &lt;/TableSection&gt;<br />    &lt;/TableRoot&gt;<br />&lt;/TableView&gt;</pre></p> |
|     |     |

## <a name="related-links"></a>Links relacionados

- [Exemplo de xamarin. Forms FormsGallery](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/)
- [Amostras do Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Documentação da API do Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
