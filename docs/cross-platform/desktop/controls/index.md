---
ms.assetid: 4D47185C-8998-4903-AE64-7E2A67F9DF7A
title: Comparação de controles da interface do usuário
description: Este documento fornece uma comparação dos controles da interface do usuário entre o Xamarin. Forms, o Windows Forms e o WPF. Ele também se vincula a outra documentação que compara o WPF ao Xamarin. Forms.
author: davidortinau
ms.author: daortin
ms.date: 04/26/2017
ms.openlocfilehash: b4cffd9e95f24dea9fc5fed5a6badeec624a4e25
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453084"
---
# <a name="ui-controls-comparison"></a>Comparação de controles da interface do usuário

Veja abaixo uma comparação dos controles Xamarin. Forms com o Windows Forms e o WPF, com base [nesta tabela](/dotnet/framework/wpf/advanced/windows-forms-controls-and-equivalent-wpf-controls).

Leia mais sobre as [semelhanças e diferenças entre o WPF e o Xamarin. Forms](wpf.md) para ajudar a atualizar seu conhecimento de área de trabalho para o desenvolvimento de aplicativos móveis.

|Windows Forms|WPF|Xamarin.Forms|
|--- |--- |--- |
|[BindingNavigator](/dotnet/api/system.windows.forms.bindingnavigator)|-|-|
|[BindingSource](/dotnet/api/system.windows.forms.bindingsource)|[CollectionViewSource](/dotnet/api/system.windows.data.collectionviewsource)|Propriedade Binding, por exemplo, BindingContext|
|[Botão](/dotnet/api/system.windows.forms.button)|[Botão](/dotnet/api/system.windows.controls.button)|Botão|
|[CheckBox](/dotnet/api/system.windows.forms.checkbox)|[CheckBox](/dotnet/api/system.windows.controls.checkbox)|Comutador|
|[CheckedListBox](/dotnet/api/system.windows.forms.checkedlistbox)|[ListBox](/dotnet/api/system.windows.controls.listbox) com composição.|ListView com composição.|
|[ColorDialog](/dotnet/api/system.windows.forms.colordialog)|-|-|
|[ComboBox](/dotnet/api/system.windows.forms.combobox)|[ComboBox](/dotnet/api/system.windows.controls.combobox) (não dá suporte para preenchimento automático)|Seletor|
|[ContextMenuStrip](/dotnet/api/system.windows.forms.contextmenustrip)|[ContextMenu](/dotnet/api/system.windows.controls.contextmenu)|-|
|[DataGridView](/dotnet/api/system.windows.forms.datagridview)|[DataGrid](/dotnet/api/system.windows.controls.datagrid)|-|
|[DateTimePicker](/dotnet/api/system.windows.forms.datetimepicker)|[DatePicker](/dotnet/api/system.windows.controls.datepicker)|DatePicker de & de seleção|
|[DomainUpDown](/dotnet/api/system.windows.forms.domainupdown)|[Caixa de texto](/dotnet/api/system.windows.controls.textbox) e dois controles [RepeatButton](/dotnet/api/system.windows.controls.primitives.repeatbutton) .|Passador|
|[ErrorProvider](/dotnet/api/system.windows.forms.errorprovider)|-|-|
|[FlowLayoutPanel](/dotnet/api/system.windows.forms.flowlayoutpanel)|[WrapPanel](/dotnet/api/system.windows.controls.wrappanel) ou [StackPanel](/dotnet/api/system.windows.controls.stackpanel)|StackLayout ou FlexLayout|
|[FolderBrowserDialog](/dotnet/api/system.windows.forms.folderbrowserdialog)|-|-|
|[FontDialog](/dotnet/api/system.windows.forms.fontdialog)|-|-|
|[Formulário](/dotnet/api/system.windows.forms.form)|[Window](/dotnet/api/system.windows.window)|Página|
|[GroupBox](/dotnet/api/system.windows.forms.groupbox)|[GroupBox](/dotnet/api/system.windows.controls.groupbox)|-|
|[HelpProvider](/dotnet/api/system.windows.forms.helpprovider)|Nenhum controle equivalente (use ToolTips).|-|
|[HScrollBar](/dotnet/api/system.windows.forms.hscrollbar)|[ScrollBar](/dotnet/api/system.windows.controls.primitives.scrollbar) (a rolagem é incorporada aos controles de contêiner)|usar ScrollView|
|[ImageList](/dotnet/api/system.windows.forms.imagelist)|-|-|
|[Chamada](/dotnet/api/system.windows.forms.label)|[Chamada](/dotnet/api/system.windows.controls.label)|Rótulo|
|[LinkLabel](/dotnet/api/system.windows.forms.linklabel)|Nenhum controle equivalente (você pode usar a classe [Hyperlink](/dotnet/api/system.windows.documents.hyperlink) para hospedar hiperlinks dentro do conteúdo do fluxo).|-|
|[ListBox](/dotnet/api/system.windows.forms.listbox)|[ListBox](/dotnet/api/system.windows.controls.listbox)|Usar ListView|
|[ListView](/dotnet/api/system.windows.forms.listview)|[ListView](/dotnet/api/system.windows.controls.listview)|ListView|
|[MaskedTextBox](/dotnet/api/system.windows.forms.maskedtextbox)|-|-|
|[MenuStrip](/dotnet/api/system.windows.forms.menustrip)|[Menu](/dotnet/api/system.windows.controls.menu)|Considere MasterDetailPage ou TabbedPage|
|[MonthCalendar](/dotnet/api/system.windows.forms.monthcalendar)|[Calendário](/dotnet/api/system.windows.controls.calendar)|-|
|[NotifyIcon](/dotnet/api/system.windows.forms.notifyicon)|-|-|
|[NumericUpDown](/dotnet/api/system.windows.forms.numericupdown)|[Caixa de texto](/dotnet/api/system.windows.controls.textbox) e dois controles [RepeatButton](/dotnet/api/system.windows.controls.primitives.repeatbutton) .|Passador|
|[OpenFileDialog](/dotnet/api/system.windows.forms.openfiledialog)|[OpenFileDialog](/dotnet/api/microsoft.win32.openfiledialog)|-|
|[PageSetupDialog](/dotnet/api/system.windows.forms.pagesetupdialog)|-|-|
|[Painel](/dotnet/api/system.windows.forms.panel)|[Tela](/dotnet/api/system.windows.controls.canvas)|Exibir ou AbsoluteLayout|
|[PictureBox](/dotnet/api/system.windows.forms.picturebox)|[Imagem](/dotnet/api/system.windows.controls.image)|Imagem|
|[PrintDialog](/dotnet/api/system.windows.forms.printdialog)|[PrintDialog](/dotnet/api/system.windows.controls.printdialog)|-|
|[PrintDocument](/dotnet/api/system.drawing.printing.printdocument)|-|-|
|[PrintPreviewControl](/dotnet/api/system.windows.forms.printpreviewcontrol)|[DocumentViewer](/dotnet/api/system.windows.controls.documentviewer)|-|
|[PrintPreviewDialog](/dotnet/api/system.windows.forms.printpreviewdialog)|-|-|
|[ProgressBar](/dotnet/api/system.windows.forms.progressbar)|[ProgressBar](/dotnet/api/system.windows.controls.progressbar)|ProgressBar|
|[PropertyGrid](/dotnet/api/system.windows.forms.propertygrid)|-|-|
|[RadioButton](/dotnet/api/system.windows.forms.radiobutton)|[RadioButton](/dotnet/api/system.windows.controls.radiobutton)|-|
|[RichTextBox](/dotnet/api/system.windows.forms.richtextbox)|[RichTextBox](/dotnet/api/system.windows.controls.richtextbox)|O editor não dá suporte a texto rico (formatado), entrada para texto de linha única|
|[SaveFileDialog](/dotnet/api/system.windows.forms.savefiledialog)|[SaveFileDialog](/dotnet/api/microsoft.win32.savefiledialog)|-|
|[ScrollableControl](/dotnet/api/system.windows.forms.scrollablecontrol)|[ScrollViewer](/dotnet/api/system.windows.controls.scrollviewer)|ScrollView|
|[SoundPlayer](/dotnet/api/system.media.soundplayer)|[MediaPlayer](/dotnet/api/system.windows.media.mediaplayer)|-|
|[SplitContainer](/dotnet/api/system.windows.forms.splitcontainer)|[GridSplitter](/dotnet/api/system.windows.controls.gridsplitter)|Considerar MasterDetailPage|
|[StatusStrip](/dotnet/api/system.windows.forms.statusstrip)|[StatusBar](/dotnet/api/system.windows.controls.primitives.statusbar)|-|
|[TabControl](/dotnet/api/system.windows.forms.tabcontrol)|[TabControl](/dotnet/api/system.windows.controls.tabcontrol)|TabbedPage|
|[TableLayoutPanel](/dotnet/api/system.windows.forms.tablelayoutpanel)|[Grid](/dotnet/api/system.windows.controls.grid)|Grid|
|[TextBox](/dotnet/api/system.windows.forms.textbox)|[TextBox](/dotnet/api/system.windows.controls.textbox)|O editor não dá suporte a texto rico (formatado)|
|[Timer](/dotnet/api/system.windows.forms.timer)|[DispatcherTimer](/dotnet/api/system.windows.threading.dispatchertimer)|Dispositivo. StartTime ()|
|[ToolStrip](/dotnet/api/system.windows.forms.toolstrip)|[Barra](/dotnet/api/system.windows.controls.toolbar)|Page. ToolbarItems e ToolbarItem|
|[ToolStripContainer](/dotnet/api/system.windows.forms.toolstripcontainer), [ToolStripDropDown](/dotnet/api/system.windows.forms.toolstripdropdown), [ToolStripDropDownMenu](/dotnet/api/system.windows.forms.toolstripdropdownmenu), [ToolStripPanel](/dotnet/api/system.windows.forms.toolstrippanel)|[Barra de ferramentas](/dotnet/api/system.windows.controls.toolbar) com composição.|Page. ToolbarItems e ToolbarItem com composição|
|[ToolTip](/dotnet/api/system.windows.forms.tooltip)|[ToolTip](/dotnet/api/system.windows.controls.tooltip)|Usar recursos de acessibilidade|
|[TrackBar](/dotnet/api/system.windows.forms.trackbar)|[Controle deslizante](/dotnet/api/system.windows.controls.slider)|Controle deslizante|
|[TreeView](/dotnet/api/system.windows.forms.treeview)|[TreeView](/dotnet/api/system.windows.controls.treeview)|Considerar ListView hierárquico em um NavigationPage|
|[UserControl](/dotnet/api/system.windows.forms.usercontrol)|[UserControl](/dotnet/api/system.windows.controls.usercontrol)|Exibir e também renderizadores personalizados|
|[VScrollBar](/dotnet/api/system.windows.forms.vscrollbar)|[ScrollBar](/dotnet/api/system.windows.controls.primitives.scrollbar)|usar ScrollView|
|[WebBrowser](/dotnet/api/system.windows.forms.webbrowser)|[WebBrowser](/dotnet/api/system.windows.controls.webbrowser)|WebView|