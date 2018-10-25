---
title: Controles Android (Widgets)
description: Blocos de construção para a criação de Interfaces de usuário do xamarin. Android
ms.prod: xamarin
ms.assetid: B7A82166-B920-4672-B7A2-20DD5E0B5AEF
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: 842fb1df2c9cc1aaf1a106687179a3730c2503bd
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "32436707"
---
# <a name="android-controls-widgets"></a>Controles Android (Widgets)

Xamarin. Android expõe todos os controles da interface do usuário nativas (widgets) fornecidos pelo Android. Esses controles podem ser adicionados facilmente para aplicativos xamarin. Android usando o Designer do Android ou programaticamente por meio de arquivos de layout XML. Independentemente do método escolhido, o xamarin. Android expõe todas as propriedades do objeto de interface de usuário e métodos em c#. As seções a seguir apresentam os controles de interface do usuário do Android mais comuns e explicam como incorporá-las em aplicativos xamarin. Android.

## <a name="action-barandroiduser-interfacecontrolsaction-barmd"></a>[Barra de ação](~/android/user-interface/controls/action-bar.md) 

`ActionBar` é uma barra de ferramentas que exibe o título da atividade, interfaces de navegação e outros itens interativos. Normalmente, a barra de ação aparece na parte superior da janela de uma atividade.

![Barra de ação de exemplo](images/action-bar.png)


## <a name="auto-completeandroiduser-interfacecontrolsauto-completemd"></a>[Preenchimento Automático](~/android/user-interface/controls/auto-complete.md)

`AutoCompleteTextView` é um elemento de exibição de texto editável que mostra sugestões de preenchimento automaticamente enquanto o usuário está digitando. A lista de sugestões é exibida em um menu drop-down da qual o usuário pode escolher um item para substituir o conteúdo da caixa de edição com.

![Exemplo de preenchimento automático](images/auto-complete.png)


## <a name="buttonsandroiduser-interfacecontrolsbuttonsindexmd"></a>[Botões](~/android/user-interface/controls/buttons/index.md)

Botões são elementos de interface do usuário que o usuário toca para executar uma ação.

![Botões de exemplo](images/buttons.png)


## <a name="calendarandroiduser-interfacecontrolscalendarmd"></a>[Calendar](~/android/user-interface/controls/calendar.md)

O `Calendar` classe é usada para converter uma instância específica no tempo (um valor de milissegundos que é deslocado de época) para valores, como ano, mês, hora, dia do mês e a data da próxima semana.
`Calendar` dá suporte a uma ampla gama de opções de interação com os dados de calendário, incluindo a capacidade de ler e gravar eventos, os participantes e lembretes. Usando o provedor de calendário em seu aplicativo, adicionar por meio da API de dados serão exibida no aplicativo embutido de calendário que vem com o Android.

![Calendário de exemplo](images/calendar.png)


## <a name="cardviewandroiduser-interfacecontrolscard-viewmd"></a>[CardView](~/android/user-interface/controls/card-view.md)

`CardView` é um componente de interface do usuário que apresenta o conteúdo de texto e imagem em modos de exibição que se assemelhem cartões. `CardView` é implementado como um `FrameLayout` widget com cantos arredondados e uma sombra. Normalmente, uma `CardView` é usado para apresentar um item de linha única em um `ListView` ou `GridView` grupo do modo de exibição.

![Modo de exibição de cartão de exemplo](images/cardview.png)


## <a name="edit-textandroiduser-interfacecontrolsedit-textmd"></a>[Editar texto](~/android/user-interface/controls/edit-text.md)

`EditText` é um elemento de interface do usuário que é usado para inserir e modificar o texto.

![Edição de texto de exemplo](images/edit-text.png)


## <a name="galleryandroiduser-interfacecontrolsgallerymd"></a>[Gallery](~/android/user-interface/controls/gallery.md)

`Gallery` é um widget de layout que é usado para exibir itens em uma lista de rolagem horizontal; Ele posiciona a seleção atual no centro da exibição.

![Galeria de exemplos](images/gallery.png)


## <a name="navigation-barandroiduser-interfacecontrolsnavigation-barmd"></a>[Barra de navegação](~/android/user-interface/controls/navigation-bar.md)

O *barra de navegação* fornece controles de navegação em dispositivos que não têm os botões de hardware para **Home**, **volta**, e **Menu**.

![Barra de navegação de exemplo](images/navigation-bar.png)


## <a name="pickersandroiduser-interfacecontrolspickersindexmd"></a>[Seletores](~/android/user-interface/controls/pickers/index.md)

*Selecionadores de* são elementos de interface do usuário que permitem que o usuário escolha uma data ou hora, usando as caixas de diálogo que são fornecidas pelo Android.

![Seletor de exemplo](images/picker.png)


## <a name="popup-menuandroiduser-interfacecontrolspopup-menumd"></a>[Menu pop-up](~/android/user-interface/controls/popup-menu.md)

`PopupMenu` é usado para exibir menus pop-up que estão anexados a uma exibição específica.

![Menu pop-up de exemplo](images/popup-menu.png)


## <a name="ratingbarandroiduser-interfacecontrolsratingbarmd"></a>[RatingBar](~/android/user-interface/controls/ratingbar.md)

Um `RatingBar` é um elemento de interface do usuário que exibe uma classificação de estrelas.

![Exemplo de um RatingBar](ratingbar-images/01-ratingbar.png)


## <a name="spinnerandroiduser-interfacecontrolsspinnermd"></a>[Controle giratório](~/android/user-interface/controls/spinner.md)

`Spinner` é um elemento de interface do usuário que fornece uma maneira rápida de selecionar um valor de um conjunto. Ele é simmilar a uma lista suspensa. 

![Controle giratório de exemplo](images/spinner.png)


## <a name="switchandroiduser-interfacecontrolsswitchmd"></a>[Switch](~/android/user-interface/controls/switch.md)

`Switch` é um elemento de interface do usuário que permite que o usuário alterne entre dois estados, como em ou desativado. O `Switch` valor padrão é OFF.

![Comutador de exemplo](images/switch.png)


## <a name="textureviewandroiduser-interfacecontrolstexture-viewmd"></a>[TextureView](~/android/user-interface/controls/texture-view.md)

`TextureView` é uma exibição que usa renderização 2D acelerada por hardware para habilitar um vídeo ou um fluxo de conteúdo do OpenGL a ser exibido.

![Modo de exibição de textura de exemplo](images/texture-view.png)


## <a name="toolbarandroiduser-interfacecontrolstool-barindexmd"></a>[ToolBar](~/android/user-interface/controls/tool-bar/index.md)

O `Toolbar` widget (introduzida no Android 5.0 Lollipop) pode ser pensada como uma generalização do que a interface da barra de ação &ndash; destina-se para substituir a barra de ação. O `Toolbar` pode ser usado em qualquer lugar em um layout do aplicativo e é muito mais personalizável do que uma barra de ação.

![Barra de ferramentas de exemplo](images/toolbar.png)


## <a name="viewpagerandroiduser-interfacecontrolsview-pagerindexmd"></a>[ViewPager](~/android/user-interface/controls/view-pager/index.md) 

O `ViewPager` é um Gerenciador de layout que permite que o usuário virar à esquerda e direita nas páginas de dados.

![ViewPager de exemplo](images/viewpager.png)


## <a name="webviewandroiduser-interfacecontrolsweb-viewmd"></a>[WebView](~/android/user-interface/controls/web-view.md)

`WebView` é um elemento de interface do usuário que permite que você crie sua própria janela para exibir páginas da web (ou até mesmo desenvolver um navegador concluído).

![Exibição da Web de exemplo](images/web-view.png)

