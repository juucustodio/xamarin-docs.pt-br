---
title: Controles
description: Xamarin expõe todos os objetos de interface de usuário nativa fornecidos pela Apple. Eles são adicionados facilmente a aplicativos xamarin usando o iOS Designer, construtor de Interface do Xcode ou programaticamente. Independentemente do método escolhido, xamarin expõe todas as propriedades do objeto de interface de usuário e métodos em c#.
ms.prod: xamarin
ms.assetid: C00EA232-ADCC-42AD-BF86-B526414A21C6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 82b2998319d4e78ee4f58a6d024032a509724537
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="controls"></a>Controles

_Xamarin expõe todos os objetos de interface de usuário nativa fornecidos pela Apple. Eles são adicionados facilmente a aplicativos xamarin usando o iOS Designer, construtor de Interface do Xcode ou programaticamente. Independentemente do método escolhido, xamarin expõe todas as propriedades do objeto de interface de usuário e métodos em c#._

Este documento apresenta alguns dos controles de interface de usuário do iOS mais comuns e como usá-los.

## <a name="alertsalertsmd"></a>[Alertas](alerts.md)

A partir do iOS 8, UIAlertController tem UIActionSheet substituído concluído e UIAlertView que agora são preteridos.

## <a name="buttonsbuttonsmd"></a>[Botões](buttons.md)

A classe UIButton é usada para representar vários estilos diferentes de botão nas telas do iOS. Esta seção apresenta as diferentes opções para trabalhar com os botões no iOS.

## <a name="collection-viewsuicollectionviewmd"></a>[Exibições de coleção](uicollectionview.md)

Exibições de coleção, disponíveis no `UICollectionView` da classe, são um novo conceito no iOS 6 que apresentam apresentar vários itens na tela usando layouts. Os padrões para fornecer dados para um `UICollectionView` para criar itens e interagir com os seguinte itens a delegação mesmo e fonte de dados padrões usados no desenvolvimento do iOS.

## <a name="imagesimagemd"></a>[Imagens](image.md)

Adicionando imagens ao seu aplicativo requer duas etapas: primeiro, adicionar imagens ao projeto. em seguida, adicione controles e código para exibi-los em uma tela. Consulte o [trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md) do artigo para obter mais cobertura da imagem tratamento em xamarin.

## <a name="manual-camera-controlsintro-to-manual-camera-controlsmd"></a>[Controles de câmera manuais](intro-to-manual-camera-controls.md)

Os controles de câmera Manual, fornecida pelo `AVFoundation Framework` no iOS 8, permitir que um aplicativo móvel assumir o controle completo sobre a câmera de um dispositivo iOS. Esse nível refinado de controle pode ser usado para criar aplicativos de câmera nível professional e fornecer composições artista ajustando os parâmetros da câmera ao tirar uma imagem ou um vídeo ainda.

## <a name="mapsios-mapsindexmd"></a>[Mapas](ios-maps/index.md)

Mapas são um recurso comum em todos os sistemas operacionais móveis modernos. iOS oferece suporte a mapeamento nativamente por meio da estrutura de Kit de mapa. Com o Kit de mapa, aplicativos podem facilmente adicionar mapas avançados e interativos. Esses mapas podem ser personalizados de várias maneiras, como adicionar anotações para marcar locais em um mapa e sobreposição de elementos gráficos de formas arbitrárias. Kit de mapa ainda tem suporte interno para mostrar o local atual de um dispositivo.

## <a name="labelslabelsmd"></a>[Rótulos](labels.md)

O `UILabel` controle é usado para exibir único e várias linhas, de leitura somente texto.

## <a name="pickers-and-date-pickerspickermd"></a>[Selecionadores e data selecionadores](picker.md)

O controle de seletor exibe o controle de 'roda como' que contém uma lista de rolagem de valores com o valor selecionado está sendo realçada. Os usuários gire a roda para selecionar a opção que desejarem.

Um usuário específico caso para selecionadores-a para definir a data e / ou hora. Para fornecer esse Apple criou uma subclasse personalizada de UIPickerView chamado UIDatePicker.

## <a name="progress-and-activity-indicatorsprogress-activity-indicatormd"></a>[Indicadores de progresso e de atividade](progress-activity-indicator.md)

iOS fornece duas maneiras principais para indicar o progresso em seu aplicativo: indicadores de atividade (incluindo um determinado _rede_ indicador de atividade) e barras de progresso.

## <a name="search-barssearchbarmd"></a>[Barras de pesquisa](searchbar.md)

O UISearchBar é usado para pesquisar por meio de uma lista de valores. 

## <a name="sliders-steppers-and-segmented-controlsslider-switch-segmented-controlsmd"></a>[Controles deslizantes, Steppers e controles segmentados](slider-switch-segmented-controls.md)

O controle deslizante permite simples seleção de um valor numérico em um intervalo. iOS usa o `UISwitch` um booliano que pode ser representado por um botão de opção em outras plataformas de entrada. Um controle segmentado é uma maneira organizada para permitir que os usuários interajam com um pequeno número de opções.

## <a name="stack-viewuistackviewmd"></a>[Exibição de pilha](uistackview.md)

O controle de exibição de pilha (`UIStackView`) aproveita o poder do Layout automático e Classes de tamanho para gerenciar uma pilha de sub-visualizações, horizontal ou verticalmente, que responde dinamicamente para o tamanho da tela e a orientação do dispositivo iOS.

## <a name="tables-and-cellstablesindexmd"></a>[Tabelas e células](tables/index.md)

sua seção apresenta as classes usadas para criar e exibir tabelas e fornece exemplos de como usá-los em xamarin. Ele aborda usando a aparência padrão para tabelas, personalizando o layout, Implementando edição e usando o Designer do iOS Xamarin para criar uma tabela visualmente. Às vezes, a exibição, obviamente, é uma lista de linhas (como o aplicativo de música) e outras vezes é difícil reconhecer o controle de tabela (como editar no aplicativo contatos ou uma conversa em aplicativos de mensagens).

## <a name="text-inputtext-inputmd"></a>[Entrada de texto](text-input.md)

Aceitar entrada de texto do usuário é realizada com o `UITextField` para entradas de linha única e UITextView para várias linhas de texto editável. Você pode arrastar um desses controles em uma tela e clique duas vezes para definir o texto inicial.

## <a name="tab-bars-and-tab-bar-controllerscreating-tabbed-applicationsmd"></a>[Barras de guia e controladores de barra de guias](creating-tabbed-applications.md)

aplicativos do iOS usando uma interface de usuário de guia de navegação são criados usando a classe UITabBarController. Este artigo examinaremos como configurar um aplicativo com guias que contém vários controladores e exibições. Em seguida, examinaremos como carregar um UITabBarController quando não for o controlador de raiz, como após uma tela de logon.

## <a name="web-viewsuiwebviewmd"></a>[Exibições da Web](uiwebview.md)

Neste artigo, iremos explorar cada uma das três exibições da Web fornecidas pela Apple: `UIWebView`, `WKWebview`, e `SFSafariViewController`, suas semelhanças e diferenças e como eles podem ser usados.

## <a name="related-links"></a>Links relacionados

- [Controles (exemplo)](https://developer.xamarin.com/samples/Controls/)
