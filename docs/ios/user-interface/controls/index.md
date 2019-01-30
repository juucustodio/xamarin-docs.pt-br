---
title: Controles de Interface do usuário no xamarin. IOS
description: Este documento leva a guias que descrevem diversos iOS interface controles de usuário disponíveis para desenvolvedores do xamarin. IOS. Conteúdo vinculado discute alertas, botões, exibições de coleção, imagens, controles de câmera manuais, mapas, rótulos, selecionadores, selecionadores de data e muito mais.
ms.prod: xamarin
ms.assetid: C00EA232-ADCC-42AD-BF86-B526414A21C6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: d5b7d4a372704079343a357c1d341a5260fcf583
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233270"
---
# <a name="user-interface-controls-in-xamarinios"></a>Controles de Interface do usuário no xamarin. IOS

Este documento apresenta alguns dos controles de interface de usuário do iOS mais comuns e como usá-los.

## <a name="alertsalertsmd"></a>[Alertas](alerts.md)

Começando com o iOS 8, UIAlertController tem UIActionSheet substituído concluído e UIAlertView que agora são preteridos.

## <a name="buttonsbuttonsmd"></a>[Botões](buttons.md)

A classe UIButton é usada para representar vários estilos diferentes de um botão em telas de iOS. Esta seção apresenta as diferentes opções para trabalhar com os botões no iOS.

## <a name="collection-viewsuicollectionviewmd"></a>[Exibições de coleção](uicollectionview.md)

Exibições de coleção disponíveis no `UICollectionView` de classe, são um novo conceito no iOS 6 que apresentam apresentar vários itens na tela usando layouts. Os padrões para fornecer dados a um `UICollectionView` para criar itens e interagir com esses acompanhamento de itens a delegação mesma e fonte de dados padrões comumente usados no desenvolvimento do iOS.

## <a name="imagesimagemd"></a>[Imagens](image.md)

Adicionando imagens ao seu aplicativo requer duas etapas: primeiro, adicionar imagens ao projeto. em seguida, adicione controles e código para exibi-los em uma tela. Consulte a [trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md) do artigo para obter mais cobertura da imagem tratamento no xamarin. IOS.

## <a name="manual-camera-controlsintro-to-manual-camera-controlsmd"></a>[Controles de câmera manuais](intro-to-manual-camera-controls.md)

Os controles de câmera manuais, fornecida pelo `AVFoundation Framework` no iOS 8, que um aplicativo móvel para assumir controle total sobre a câmera de um dispositivo iOS. Esse nível refinado de controle pode ser usado para criar aplicativos de câmera de nível profissional e forneça composições artista ao ajustar os parâmetros da câmera ao mesmo tempo, uma imagem ou um vídeo ainda.

## <a name="mapsios-mapsindexmd"></a>[Mapas](ios-maps/index.md)

Mapas são um recurso comum em todos os sistemas operacionais móveis modernos. iOS oferece suporte a mapeamento nativamente por meio da estrutura de Kit de mapa. Com o Kit de mapa de aplicativos podem adicionar facilmente mapas sofisticados e interativos. Esses mapas podem ser personalizados em uma variedade de formas, como adicionar anotações para marcar locais em um mapa e sobreposição de elementos gráficos de formas arbitrárias. O Map Kit ainda tem suporte interno para mostrando o local atual de um dispositivo.

## <a name="labelslabelsmd"></a>[Rótulos](labels.md)

O `UILabel` controle é usado para a exibição única e multilinha, ler somente texto.

## <a name="pickers-and-date-pickerspickermd"></a>[Seletores e selecionadores de data](picker.md)

O controle de seletor exibe o controle de 'tipo de roda' que contém uma lista rolável de valores com o valor selecionado que está sendo realçada. Os usuários gire a roda para selecionar a opção que desejarem.

Um usuário específico maiusculas e minúsculas para seletores para definir a data e / ou hora. Para fornecer essa Apple criou uma subclasse personalizada da classe UIPickerView chamada UIDatePicker.

## <a name="progress-and-activity-indicatorsprogress-activity-indicatormd"></a>[Indicadores de progresso e de atividade](progress-activity-indicator.md)

iOS fornece duas maneiras principais para indicar o progresso em seu aplicativo: Indicadores de atividade (incluindo um determinado _rede_ indicador de atividade) e barras de progresso.

## <a name="search-barssearchbarmd"></a>[Barras de pesquisa](searchbar.md)

O UISearchBar é usado para pesquisar por meio de uma lista de valores. 

## <a name="sliders-switches-and-segmented-controlsslider-switch-segmented-controlsmd"></a>[Controles deslizantes, opções e controles segmentados](slider-switch-segmented-controls.md)

Permite o controle deslizante simples seleção de um valor numérico em um intervalo. o iOS usa o `UISwitch` como um valor booliano que pode ser representado por um botão de opção em outras plataformas de entrada. Um controle segmentado é uma maneira organizada para permitir que os usuários interajam com um pequeno número de opções.

## <a name="stack-viewuistackviewmd"></a>[Exibição de pilha](uistackview.md)

O controle de exibição de pilha (`UIStackView`) aproveita o poder do Layout automático e Classes de tamanho para gerenciar uma pilha de subexibições, horizontal ou verticalmente, que responde dinamicamente para o tamanho de tela e a orientação do dispositivo iOS.

## <a name="tables-and-cellstablesindexmd"></a>[Tabelas e células](tables/index.md)

sua seção apresenta as classes usadas para criar e exibir tabelas e fornece exemplos de como usá-los no xamarin. IOS. Ele abordará usando a aparência padrão para tabelas, personalizando o layout, a implementação de edição e usando o Designer do iOS Xamarin para criar uma tabela visualmente. Às vezes, a exibição é, obviamente, uma lista de linhas (por exemplo, o aplicativo de música) e outras vezes é difícil reconhecer o controle de tabela (como editar no aplicativo contatos ou uma conversa em um aplicativo de mensagens).

## <a name="text-inputtext-inputmd"></a>[Entrada de texto](text-input.md)

Aceitar entrada de texto do usuário é realizada com o `UITextField` para entradas de linha única e UITextView para texto editável de várias linhas. Você pode arrastar qualquer um desses controles para uma tela e clique duas vezes para definir o texto inicial.

## <a name="tab-bars-and-tab-bar-controllerscreating-tabbed-applicationsmd"></a>[Barras de guia e controladores de barra de guias](creating-tabbed-applications.md)

aplicativos iOS usando uma interface do usuário de navegação da guia são criados usando a classe UITabBarController. Neste artigo examinaremos como configurar um aplicativo com guias que contém vários controladores e exibições. Em seguida, examinaremos como carregar um UITabBarController quando não for o controlador de raiz, como após uma tela de logon.

## <a name="web-viewsuiwebviewmd"></a>[Exibições da Web](uiwebview.md)

Neste artigo, vamos explorar cada uma das três exibições da Web fornecido pela Apple: `UIWebView`, `WKWebview`, e `SFSafariViewController`, suas semelhanças e diferenças e como eles podem ser usados.

## <a name="related-links"></a>Links relacionados

- [Controles (amostra)](https://developer.xamarin.com/samples/Controls/)
