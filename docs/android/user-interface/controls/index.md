---
title: Controles Android (Widgets)
description: Blocos de construção para a criação de Interfaces de usuário do xamarin
ms.prod: xamarin
ms.assetid: B7A82166-B920-4672-B7A2-20DD5E0B5AEF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 28418c3b3fedcd24963008eb3b59ffa782d791f1
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2018
---
# <a name="android-controls-widgets"></a>Controles Android (Widgets)

Xamarin expõe todos os controles de interface de usuário nativa (widgets) fornecidos pelo Android. Esses controles podem ser adicionados facilmente a aplicativos xamarin usando o Designer Android ou programaticamente por meio de arquivos de layout XML. Independentemente do método escolhido, xamarin expõe todas as propriedades do objeto de interface de usuário e os métodos em c#. As seções a seguir apresentam os controles de interface de usuário do Android mais comuns e explicam como incorporá-los a aplicativos xamarin.

## <a name="action-barandroiduser-interfacecontrolsaction-barmd"></a>[Barra de ação](~/android/user-interface/controls/action-bar.md) 

`ActionBar` é uma barra de ferramentas que exibe o título da atividade, interfaces de navegação e outros itens interativos. Normalmente, a barra de ação aparece na parte superior da janela de uma atividade.

![Barra de ação de exemplo](images/action-bar.png)


## <a name="auto-completeandroiduser-interfacecontrolsauto-completemd"></a>[Preenchimento Automático](~/android/user-interface/controls/auto-complete.md)

`AutoCompleteTextView` é um elemento de exibição de texto editável que mostre sugestões de preenchimento automaticamente enquanto o usuário está digitando. A lista de sugestões é exibida em uma lista suspensa menu no qual o usuário pode escolher um item para substituir o conteúdo da caixa de edição com.

![Exemplo de preenchimento automático](images/auto-complete.png)


## <a name="buttonsandroiduser-interfacecontrolsbuttonsindexmd"></a>[Botões](~/android/user-interface/controls/buttons/index.md)

Botões são elementos de interface do usuário que o usuário toca para executar uma ação.

![Botões de exemplo](images/buttons.png)


## <a name="calendarandroiduser-interfacecontrolscalendarmd"></a>[Calendar](~/android/user-interface/controls/calendar.md)

O `Calendar` classe é usada para converter uma instância específica no tempo (um valor de milissegundos é deslocado em relação a época) para valores, como ano, mês, hora, dia do mês e a data da próxima semana.
`Calendar` oferece suporte a uma ampla gama de opções de interação com os dados de calendário, incluindo a capacidade de ler e gravar eventos, participantes e lembretes. Usando o provedor de calendário em seu aplicativo, você adicionar por meio da API de dados serão exibida no aplicativo do calendário interno que acompanha o Android.

![Exemplo de calendário](images/calendar.png)


## <a name="cardviewandroiduser-interfacecontrolscard-viewmd"></a>[CardView](~/android/user-interface/controls/card-view.md)

`CardView` é um componente de interface do usuário que apresenta o conteúdo de texto e imagem em exibições que lembram cartões. `CardView` é implementado como um `FrameLayout` widget com cantos arredondados e uma sombra. Normalmente, um `CardView` é usado para apresentar um item de linha em uma `ListView` ou `GridView` grupo do modo de exibição.

![Modo de exibição de cartão de exemplo](images/cardview.png)


## <a name="edit-textandroiduser-interfacecontrolsedit-textmd"></a>[Editar texto](~/android/user-interface/controls/edit-text.md)

`EditText` é um elemento de interface do usuário que é usado para inserir e modificar o texto.

![Edição de texto de exemplo](images/edit-text.png)


## <a name="galleryandroiduser-interfacecontrolsgallerymd"></a>[Gallery](~/android/user-interface/controls/gallery.md)

`Gallery` é um widget de layout que é usado para exibir itens em uma lista de rolagem horizontal; Ele posiciona a seleção atual no centro da exibição.

![Galeria de exemplo](images/gallery.png)


## <a name="navigation-barandroiduser-interfacecontrolsnavigation-barmd"></a>[Barra de navegação](~/android/user-interface/controls/navigation-bar.md)

O *barra de navegação* fornece controles de navegação em dispositivos que não têm os botões de hardware para **início**, **novamente**, e **Menu**.

![Barra de navegação de exemplo](images/navigation-bar.png)


## <a name="pickersandroiduser-interfacecontrolspickersindexmd"></a>[Seletores](~/android/user-interface/controls/pickers/index.md)

*Seletores de* são elementos de interface do usuário que permitem ao usuário selecionar uma data ou hora usando caixas de diálogo que são fornecidas pelo Android.

![Seletor de exemplo](images/picker.png)


## <a name="popup-menuandroiduser-interfacecontrolspopup-menumd"></a>[Menu pop-up](~/android/user-interface/controls/popup-menu.md)

`PopupMenu` é usado para exibir menus pop-up que estão anexados a uma exibição específica.

![Menu pop-up de exemplo](images/popup-menu.png)


## <a name="spinnerandroiduser-interfacecontrolsspinnermd"></a>[Controle giratório](~/android/user-interface/controls/spinner.md)

`Spinner` é um elemento de interface do usuário que fornece uma maneira rápida de selecionar um valor de um conjunto. É simmilar para uma lista suspensa. 

![Controle giratório de exemplo](images/spinner.png)


## <a name="switchandroiduser-interfacecontrolsswitchmd"></a>[Switch](~/android/user-interface/controls/switch.md)

`Switch` é um elemento de interface do usuário que permite que um usuário alternar entre os dois estados, tais como ativo ou desativado. O `Switch` valor padrão é OFF.

![Opção de exemplo](images/switch.png)


## <a name="textureviewandroiduser-interfacecontrolstexture-viewmd"></a>[TextureView](~/android/user-interface/controls/texture-view.md)

`TextureView` é uma exibição que usa a renderização 2D acelerada por hardware para habilitar um vídeo ou OpenGL fluxo de conteúdo a ser exibido.

![Exibição de textura de exemplo](images/texture-view.png)


## <a name="toolbarandroiduser-interfacecontrolstool-barindexmd"></a>[ToolBar](~/android/user-interface/controls/tool-bar/index.md)

O `Toolbar` widget (introduzido no Android 5.0 de pirulito) pode ser pensada como uma generalização da interface da barra de ação &ndash; destina-se para substituir a barra de ação. O `Toolbar` pode ser usado em qualquer lugar no layout de um aplicativo, e é muito mais personalizável do que uma barra de ação.

![Barra de ferramentas de exemplo](images/toolbar.png)


## <a name="viewpagerandroiduser-interfacecontrolsview-pagerindexmd"></a>[ViewPager](~/android/user-interface/controls/view-pager/index.md) 

O `ViewPager` é um Gerenciador de layout que permite que o usuário virar à esquerda e direita nas páginas de dados.

![Exemplo ViewPager](images/viewpager.png)


## <a name="webviewandroiduser-interfacecontrolsweb-viewmd"></a>[WebView](~/android/user-interface/controls/web-view.md)

`WebView` é um elemento de interface do usuário que permite que você criar sua própria janela para exibir páginas da web (ou até mesmo desenvolver um navegador concluído).

![Exibição da Web de exemplo](images/web-view.png)

