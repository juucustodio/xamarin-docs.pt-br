---
title: Controles do Android (widgets)
description: Blocos de construção para criar interfaces de usuário do Xamarin. Android
ms.prod: xamarin
ms.assetid: B7A82166-B920-4672-B7A2-20DD5E0B5AEF
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: 31f6c0dd0d4f5452ebc2cbde0cc44cd9c47eeb9a
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510318"
---
# <a name="xamarinandroid-controls-widgets"></a>Controles Xamarin. Android (widgets)

O Xamarin. Android expõe todos os controles de interface do usuário nativos (widgets) fornecidos pelo Android. Esses controles podem ser facilmente adicionados aos aplicativos Xamarin. Android usando o Designer Android ou de forma programática por meio de arquivos de layout XML. Independentemente do método escolhido, o Xamarin. Android expõe todas as propriedades e métodos do objeto da interface do usuário C#no. As seções a seguir apresentam os controles de interface do usuário do Android mais comuns e explicam como incorporá-los aos aplicativos Xamarin. Android.

## <a name="action-barandroiduser-interfacecontrolsaction-barmd"></a>[Barra de ação](~/android/user-interface/controls/action-bar.md) 

`ActionBar`é uma barra de ferramentas que exibe o título da atividade, as interfaces de navegação e outros itens interativos. Normalmente, a barra de ação aparece na parte superior da janela de uma atividade.

![Exemplo de ActionBar](images/action-bar.png)


## <a name="auto-completeandroiduser-interfacecontrolsauto-completemd"></a>[Preenchimento Automático](~/android/user-interface/controls/auto-complete.md)

`AutoCompleteTextView`é um elemento de exibição de texto editável que mostra sugestões de conclusão automaticamente enquanto o usuário está digitando. A lista de sugestões é exibida em um menu suspenso do qual o usuário pode escolher um item para substituir o conteúdo da caixa de edição.

![Exemplo de preenchimento automático](images/auto-complete.png)


## <a name="buttonsandroiduser-interfacecontrolsbuttonsindexmd"></a>[Botões](~/android/user-interface/controls/buttons/index.md)

Os botões são elementos de interface de usuário que o usuário toca para executar uma ação.

![Botões de exemplo](images/buttons.png)


## <a name="calendarandroiduser-interfacecontrolscalendarmd"></a>[Calendar](~/android/user-interface/controls/calendar.md)

A `Calendar` classe é usada para converter uma instância específica no tempo (um valor de milissegundo que é deslocado da época) para valores como ano, mês, hora, dia do mês e a data da próxima semana.
`Calendar`o oferece suporte a uma infinidade de opções de interação com dados de calendário, incluindo a capacidade de ler e gravar eventos, participantes e lembretes. Usando o provedor de calendário em seu aplicativo, os dados que você adicionar por meio da API aparecerão no aplicativo de calendário interno que vem com o Android.

![Calendário de exemplo](images/calendar.png)


## <a name="cardviewandroiduser-interfacecontrolscard-viewmd"></a>[CardView](~/android/user-interface/controls/card-view.md)

`CardView`é um componente de interface do usuário que apresenta conteúdo de texto e imagem em exibições semelhantes a cartões. `CardView`é implementado como um `FrameLayout` Widget com cantos arredondados e uma sombra. Normalmente, um `CardView` é usado para apresentar um único item de linha em `ListView` um `GridView` grupo de ou de exibição.

![Exemplo de exibição de cartão](images/cardview.png)


## <a name="edit-textandroiduser-interfacecontrolsedit-textmd"></a>[Editar texto](~/android/user-interface/controls/edit-text.md)

`EditText`é um elemento de interface do usuário que é usado para inserir e modificar texto.

![Exemplo de texto de edição](images/edit-text.png)


## <a name="galleryandroiduser-interfacecontrolsgallerymd"></a>[Gallery](~/android/user-interface/controls/gallery.md)

`Gallery`é um widget de layout usado para exibir itens em uma lista de rolagem horizontal; Ele posiciona a seleção atual no centro da exibição.

![Galeria de exemplo](images/gallery.png)


## <a name="navigation-barandroiduser-interfacecontrolsnavigation-barmd"></a>[Barra de navegação](~/android/user-interface/controls/navigation-bar.md)

A *barra de navegação* fornece controles de navegação em dispositivos que não incluem botões de hardware para **página inicial**, **voltar**e **menu**.

![Barra de navegação de exemplo](images/navigation-bar.png)


## <a name="pickersandroiduser-interfacecontrolspickersindexmd"></a>[Seletores](~/android/user-interface/controls/pickers/index.md)

Os seletores são elementos da interface do usuário que permitem que ele escolha uma data ou uma hora usando caixas de diálogo que são fornecidas pelo Android.

![Seletor de exemplo](images/picker.png)


## <a name="popup-menuandroiduser-interfacecontrolspopup-menumd"></a>[Menu pop-up](~/android/user-interface/controls/popup-menu.md)

`PopupMenu`é usado para exibir os menus pop-up que são anexados a uma determinada exibição.

![Menu pop-up de exemplo](images/popup-menu.png)


## <a name="ratingbarandroiduser-interfacecontrolsratingbarmd"></a>[RatingBar](~/android/user-interface/controls/ratingbar.md)

Um `RatingBar` é um elemento de interface do usuário que exibe uma classificação em estrelas.

![Exemplo de um RatingBar](ratingbar-images/01-ratingbar.png)


## <a name="spinnerandroiduser-interfacecontrolsspinnermd"></a>[Controle giratório](~/android/user-interface/controls/spinner.md)

`Spinner`é um elemento de interface do usuário que fornece uma maneira rápida de selecionar um valor de um conjunto. É simmilar para uma lista suspensa. 

![Exemplo de controle giratório](images/spinner.png)


## <a name="switchandroiduser-interfacecontrolsswitchmd"></a>[Switch](~/android/user-interface/controls/switch.md)

`Switch`é um elemento de interface de usuário que permite que um usuário alterne entre dois Estados, como ativado ou desativado. O `Switch` valor padrão é off.

![Opção de exemplo](images/switch.png)


## <a name="textureviewandroiduser-interfacecontrolstexture-viewmd"></a>[TextureView](~/android/user-interface/controls/texture-view.md)

`TextureView`é uma exibição que usa renderização 2D acelerada por hardware para permitir que um fluxo de conteúdo de vídeo ou OpenGL seja exibido.

![Exemplo de exibição de textura](images/texture-view.png)


## <a name="toolbarandroiduser-interfacecontrolstool-barindexmd"></a>[ToolBar](~/android/user-interface/controls/tool-bar/index.md)

O `Toolbar` widget (introduzido no Android 5,0 pirulito) pode ser considerado uma generalização da interface &ndash; da barra de ações para substituir a barra de ação. O `Toolbar` pode ser usado em qualquer lugar em um layout de aplicativo e é muito mais personalizável do que uma barra de ação.

![Barra de ferramentas de exemplo](images/toolbar.png)


## <a name="viewpagerandroiduser-interfacecontrolsview-pagerindexmd"></a>[ViewPager](~/android/user-interface/controls/view-pager/index.md) 

O `ViewPager` é um Gerenciador de layout que permite que o usuário vire à esquerda e à direita por meio de páginas de dados.

![Exemplo de ViewPager](images/viewpager.png)


## <a name="webviewandroiduser-interfacecontrolsweb-viewmd"></a>[WebView](~/android/user-interface/controls/web-view.md)

`WebView`é um elemento de interface do usuário que permite que você crie sua própria janela para exibir páginas da Web (ou até mesmo desenvolver um navegador completo).

![Exibição da Web de exemplo](images/web-view.png)

