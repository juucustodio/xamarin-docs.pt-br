---
ms.assetid: 4D47185C-8998-4903-AE64-7E2A67F9DF7A
title: Comparação de controles de interface do usuário
description: Este documento fornece uma comparação dos controles de interface do usuário entre xamarin. Forms, formulários do Windows e WPF. Também contém links para documentação adicional que compara WPF para xamarin. Forms.
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: d97756952d7de724e1ef573092456e24294a74db
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782340"
---
# <a name="ui-controls-comparison"></a>Comparação de controles de interface do usuário

Abaixo está uma comparação de controles de formulários do Windows e WPF, com base em xamarin. Forms [essa tabela](/dotnet/framework/wpf/advanced/windows-forms-controls-and-equivalent-wpf-controls).

Leia mais sobre o [as semelhanças e diferenças entre WPF e xamarin. Forms](wpf.md) para ajudar a atualizar o seu conhecimento de área de trabalho para desenvolvimento de aplicativos móveis.

|Windows Forms|WPF|Xamarin.Forms|
|--- |--- |--- |
|[BindingNavigator](https://msdn.microsoft.com/library/system.windows.forms.bindingnavigator(v=vs.110).aspx)|-|-|
|[BindingSource](https://msdn.microsoft.com/library/system.windows.forms.bindingsource(v=vs.110).aspx)|[CollectionViewSource](https://msdn.microsoft.com/library/system.windows.data.collectionviewsource(v=vs.110).aspx)|Associação de propriedade, por exemplo. BindingContext|
|[Button](https://msdn.microsoft.com/library/system.windows.forms.button(v=vs.110).aspx)|[Button](https://msdn.microsoft.com/library/system.windows.controls.button(v=vs.110).aspx)|Botão|
|[CheckBox](https://msdn.microsoft.com/library/system.windows.forms.checkbox(v=vs.110).aspx)|[CheckBox](https://msdn.microsoft.com/library/system.windows.controls.checkbox(v=vs.110).aspx)|Alternar|
|[CheckedListBox](https://msdn.microsoft.com/library/system.windows.forms.checkedlistbox(v=vs.110).aspx)|[Caixa de listagem](https://msdn.microsoft.com/library/system.windows.controls.listbox(v=vs.110).aspx) com composição.|ListView com composição.|
|[ColorDialog](https://msdn.microsoft.com/library/system.windows.forms.colordialog(v=vs.110).aspx)|-|-|
|[ComboBox](https://msdn.microsoft.com/library/system.windows.forms.combobox(v=vs.110).aspx)|[Caixa de combinação](https://msdn.microsoft.com/library/system.windows.controls.combobox(v=vs.110).aspx) (não tem suporte de preenchimento automático)|Seletor|
|[ContextMenuStrip](https://msdn.microsoft.com/library/system.windows.forms.contextmenustrip(v=vs.110).aspx)|[ContextMenu](https://msdn.microsoft.com/library/system.windows.controls.contextmenu(v=vs.110).aspx)|-|
|[DataGridView](https://msdn.microsoft.com/library/system.windows.forms.datagridview(v=vs.110).aspx)|[DataGrid](https://msdn.microsoft.com/library/system.windows.controls.datagrid(v=vs.110).aspx)|-|
|[DateTimePicker](https://msdn.microsoft.com/library/system.windows.forms.datetimepicker(v=vs.110).aspx)|[DatePicker](https://msdn.microsoft.com/library/system.windows.controls.datepicker(v=vs.110).aspx)|DatePicker & TimePicker|
|[DomainUpDown](https://msdn.microsoft.com/library/system.windows.forms.domainupdown(v=vs.110).aspx)|[Caixa de texto](https://msdn.microsoft.com/library/system.windows.controls.textbox(v=vs.110).aspx) e dois [RepeatButton](https://msdn.microsoft.com/library/system.windows.controls.primitives.repeatbutton(v=vs.110).aspx) controles.|Seletor|
|[ErrorProvider](https://msdn.microsoft.com/library/system.windows.forms.errorprovider(v=vs.110).aspx)|-|-|
|[FlowLayoutPanel](https://msdn.microsoft.com/library/system.windows.forms.flowlayoutpanel(v=vs.110).aspx)|[WrapPanel](https://msdn.microsoft.com/library/system.windows.controls.wrappanel(v=vs.110).aspx) ou [StackPanel](https://msdn.microsoft.com/library/system.windows.controls.stackpanel(v=vs.110).aspx)|Controle personalizado StackLayout ou WrapLayout|
|[FolderBrowserDialog](https://msdn.microsoft.com/library/system.windows.forms.folderbrowserdialog(v=vs.110).aspx)|-|-|
|[FolderBrowserDialog](https://msdn.microsoft.com/library/system.windows.forms.fontdialog(v=vs.110).aspx)|-|-|
|[formulário](https://msdn.microsoft.com/library/system.windows.forms.form(v=vs.110).aspx)|[Janela](https://msdn.microsoft.com/library/system.windows.window(v=vs.110).aspx)|Página|
|[GroupBox](https://msdn.microsoft.com/library/system.windows.forms.groupbox(v=vs.110).aspx)|[GroupBox](https://msdn.microsoft.com/library/system.windows.controls.groupbox(v=vs.110).aspx)|-|
|[HelpProvider](https://msdn.microsoft.com/library/system.windows.forms.helpprovider(v=vs.110).aspx)|Nenhum controle equivalente (use dicas de ferramenta).|-|
|[HScrollBar](https://msdn.microsoft.com/library/system.windows.forms.hscrollbar(v=vs.110).aspx)|[Barra de rolagem](https://msdn.microsoft.com/library/system.windows.controls.primitives.scrollbar(v=vs.110).aspx) (rolagem é embutida em controles de contêiner)|Use ScrollView|
|[ImageList](https://msdn.microsoft.com/library/system.windows.forms.imagelist(v=vs.110).aspx)|-|-|
|[Rótulo](https://msdn.microsoft.com/library/system.windows.forms.label(v=vs.110).aspx)|[Rótulo](https://msdn.microsoft.com/library/system.windows.controls.label(v=vs.110).aspx)|Rotular|
|[LinkLabel](https://msdn.microsoft.com/library/system.windows.forms.linklabel(v=vs.110).aspx)|Nenhum controle equivalente (você pode usar o [hiperlink](https://msdn.microsoft.com/library/system.windows.documents.hyperlink(v=vs.110).aspx) classe para hospedar hiperlinks no conteúdo de fluxo).|-|
|[ListBox](https://msdn.microsoft.com/library/system.windows.forms.listbox(v=vs.110).aspx)|[ListBox](https://msdn.microsoft.com/library/system.windows.controls.listbox(v=vs.110).aspx)|Use ListView|
|[ListView](https://msdn.microsoft.com/library/system.windows.forms.listview(v=vs.110).aspx)|[ListView](https://msdn.microsoft.com/library/system.windows.controls.listview(v=vs.110).aspx)|ListView|
|[MaskedTextBox](https://msdn.microsoft.com/library/system.windows.forms.maskedtextbox(v=vs.110).aspx)|-|-|
|[MenuStrip](https://msdn.microsoft.com/library/system.windows.forms.menustrip(v=vs.110).aspx)|[Menu](https://msdn.microsoft.com/library/system.windows.controls.menu(v=vs.110).aspx)|Considere MasterDetailPage ou TabbedPage|
|[MonthCalendar](https://msdn.microsoft.com/library/system.windows.forms.monthcalendar(v=vs.110).aspx)|[Calendar](https://msdn.microsoft.com/library/system.windows.controls.calendar(v=vs.110).aspx)|-|
|[NotifyIcon](https://msdn.microsoft.com/library/system.windows.forms.notifyicon(v=vs.110).aspx)|-|-|
|[NumericUpDown](https://msdn.microsoft.com/library/system.windows.forms.numericupdown(v=vs.110).aspx)|[Caixa de texto](https://msdn.microsoft.com/library/system.windows.controls.textbox(v=vs.110).aspx) e dois [RepeatButton](https://msdn.microsoft.com/library/system.windows.controls.primitives.repeatbutton(v=vs.110).aspx) controles.|Seletor|
|[OpenFileDialog](https://msdn.microsoft.com/library/system.windows.forms.openfiledialog(v=vs.110).aspx)|[OpenFileDialog](https://msdn.microsoft.com/library/microsoft.win32.openfiledialog(v=vs.110).aspx)|-|
|[PageSetupDialog](https://msdn.microsoft.com/library/system.windows.forms.pagesetupdialog(v=vs.110).aspx)|-|-|
|[Painel](https://msdn.microsoft.com/library/system.windows.forms.panel(v=vs.110).aspx)|[Tela](https://msdn.microsoft.com/library/system.windows.controls.canvas(v=vs.110).aspx)|Modo de exibição ou AbsoluteLayout|
|[PictureBox](https://msdn.microsoft.com/library/system.windows.forms.picturebox(v=vs.110).aspx)|[Image](https://msdn.microsoft.com/library/system.windows.controls.image(v=vs.110).aspx)|Image|
|[PrintDialog](https://msdn.microsoft.com/library/system.windows.forms.printdialog(v=vs.110).aspx)|[PrintDialog](https://msdn.microsoft.com/library/system.windows.controls.printdialog(v=vs.110).aspx)|-|
|[PrintDocument](https://msdn.microsoft.com/library/system.drawing.printing.printdocument(v=vs.110).aspx)|-|-|
|[PrintPreviewControl](https://msdn.microsoft.com/library/system.windows.forms.printpreviewcontrol(v=vs.110).aspx)|[DocumentViewer](https://msdn.microsoft.com/library/system.windows.controls.documentviewer(v=vs.110).aspx)|-|
|[PrintPreviewDialog](https://msdn.microsoft.com/library/system.windows.forms.printpreviewdialog(v=vs.110).aspx)|-|-|
|[ProgressBar](https://msdn.microsoft.com/library/system.windows.forms.progressbar(v=vs.110).aspx)|[ProgressBar](https://msdn.microsoft.com/library/system.windows.controls.progressbar(v=vs.110).aspx)|ProgressBar|
|[PropertyGrid](https://msdn.microsoft.com/library/system.windows.forms.propertygrid(v=vs.110).aspx)|-|-|
|[RadioButton](https://msdn.microsoft.com/library/system.windows.forms.radiobutton(v=vs.110).aspx)|[RadioButton](https://msdn.microsoft.com/library/system.windows.controls.radiobutton(v=vs.110).aspx)|-|
|[RichTextBox](https://msdn.microsoft.com/library/system.windows.forms.richtextbox(v=vs.110).aspx)|[RichTextBox](https://msdn.microsoft.com/library/system.windows.controls.richtextbox(v=vs.110).aspx)|Editor não oferece suporte a texto (formatado) avançado, entrada de texto de única linha|
|[SaveFileDialog](https://msdn.microsoft.com/library/system.windows.forms.savefiledialog(v=vs.110).aspx)|[SaveFileDialog](https://msdn.microsoft.com/library/microsoft.win32.savefiledialog(v=vs.110).aspx)|-|
|[ScrollableControl](https://msdn.microsoft.com/library/system.windows.forms.scrollablecontrol(v=vs.110).aspx)|[ScrollViewer](https://msdn.microsoft.com/library/system.windows.controls.scrollviewer(v=vs.110).aspx)|ScrollView|
|[SoundPlayer](https://msdn.microsoft.com/library/system.media.soundplayer(v=vs.110).aspx)|[Media Player](https://msdn.microsoft.com/library/system.windows.media.mediaplayer(v=vs.110).aspx)|-|
|[SplitContainer](https://msdn.microsoft.com/library/system.windows.forms.splitcontainer(v=vs.110).aspx)|[GridSplitter](https://msdn.microsoft.com/library/system.windows.controls.gridsplitter(v=vs.110).aspx)|Considere MasterDetailPage|
|[StatusStrip](https://msdn.microsoft.com/library/system.windows.forms.statusstrip(v=vs.110).aspx)|[StatusBar](https://msdn.microsoft.com/library/system.windows.controls.primitives.statusbar(v=vs.110).aspx)|-|
|[TabControl](https://msdn.microsoft.com/library/system.windows.forms.tabcontrol(v=vs.110).aspx)|[TabControl](https://msdn.microsoft.com/library/system.windows.controls.tabcontrol(v=vs.110).aspx)|TabbedPage|
|[TableLayoutPanel](https://msdn.microsoft.com/library/system.windows.forms.tablelayoutpanel(v=vs.110).aspx)|[Grade](https://msdn.microsoft.com/library/system.windows.controls.grid(v=vs.110).aspx)|Grade|
|[TextBox](https://msdn.microsoft.com/library/system.windows.forms.textbox(v=vs.110).aspx)|[TextBox](https://msdn.microsoft.com/library/system.windows.controls.textbox(v=vs.110).aspx)|Editor não oferece suporte a RTF (formatado)|
|[Timer](https://msdn.microsoft.com/library/system.windows.forms.timer(v=vs.110).aspx)|[DispatcherTimer](https://msdn.microsoft.com/library/system.windows.threading.dispatchertimer(v=vs.110).aspx)|Device.StartTime()|
|[ToolStrip](https://msdn.microsoft.com/library/system.windows.forms.toolstrip(v=vs.110).aspx)|[ToolBar](https://msdn.microsoft.com/library/system.windows.controls.toolbar(v=vs.110).aspx)|Page.ToolbarItems e ToolbarItem|
|[ToolStripContainer](https://msdn.microsoft.com/library/system.windows.forms.toolstripcontainer(v=vs.110).aspx), [ToolStripDropDown](https://msdn.microsoft.com/library/system.windows.forms.toolstripdropdown(v=vs.110).aspx), [ToolStripDropDownMenu](https://msdn.microsoft.com/library/system.windows.forms.toolstripdropdownmenu(v=vs.110).aspx), [ToolStripPanel](https://msdn.microsoft.com/library/system.windows.forms.toolstrippanel(v=vs.110).aspx)|[Barra de ferramentas](https://msdn.microsoft.com/library/system.windows.controls.toolbar(v=vs.110).aspx) com composição.|Page.ToolbarItems e ToolbarItem com composição|
|[ToolTip](https://msdn.microsoft.com/library/system.windows.forms.tooltip(v=vs.110).aspx)|[ToolTip](https://msdn.microsoft.com/library/system.windows.controls.tooltip(v=vs.110).aspx)|Use os recursos de acessibilidade|
|[Barra de controle](https://msdn.microsoft.com/library/system.windows.forms.trackbar(v=vs.110).aspx)|[Slider](https://msdn.microsoft.com/library/system.windows.controls.slider(v=vs.110).aspx)|Controle deslizante|
|[TreeView](https://msdn.microsoft.com/library/system.windows.forms.treeview(v=vs.110).aspx)|[TreeView](https://msdn.microsoft.com/library/system.windows.controls.treeview(v=vs.110).aspx)|Considere a possibilidade de ListView hierárquica em um NavigationPage|
|[UserControl](https://msdn.microsoft.com/library/system.windows.forms.usercontrol(v=vs.110).aspx)|[UserControl](https://msdn.microsoft.com/library/system.windows.controls.usercontrol(v=vs.110).aspx)|Modo de exibição e também renderizadores personalizados|
|[VScrollBar](https://msdn.microsoft.com/library/system.windows.forms.vscrollbar(v=vs.110).aspx)|[ScrollBar](https://msdn.microsoft.com/library/system.windows.controls.primitives.scrollbar(v=vs.110).aspx)|Use ScrollView|
|[WebBrowser](https://msdn.microsoft.com/library/system.windows.forms.webbrowser(v=vs.110).aspx)|WebBrowser|WebView|
