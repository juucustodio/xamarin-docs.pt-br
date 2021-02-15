---
title: Controles de interface do usuário no Xamarin. iOS
description: Este documento contém links para guias que descrevem os vários controles de interface do usuário do iOS disponíveis para os desenvolvedores do Xamarin. iOS. Conteúdo vinculado discute alertas, botões, exibições de coleção, imagens, controles de câmera manual, mapas, rótulos, seletores, selecionadores de datas e muito mais.
ms.prod: xamarin
ms.assetid: C00EA232-ADCC-42AD-BF86-B526414A21C6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 336e2468a3532b300697ed7fe596864e1bcf9622
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433966"
---
# <a name="user-interface-controls-in-xamarinios"></a>Controles de interface do usuário no Xamarin. iOS

Este documento apresenta alguns dos controles de interface do usuário do iOS mais comuns e como usá-los.

## <a name="alerts"></a>[Alertas](alerts.md)

A partir do iOS 8, o UIAlertController foi concluído, o UIActionSheet e o UIAlertView já foram substituídos.

## <a name="buttons"></a>[Botões](buttons.md)

A classe UIButton é usada para representar vários estilos diferentes de botão em telas do iOS. Esta seção apresenta as diferentes opções para trabalhar com botões no iOS.

## <a name="collection-views"></a>[Exibições de coleção](uicollectionview.md)

Exibições de coleção, disponíveis na `UICollectionView` classe, são um novo conceito no Ios 6 que introduz a apresentação de vários itens na tela usando layouts. Os padrões para fornecer dados para um `UICollectionView` para criar itens e interagir com esses itens seguem os mesmos padrões de delegação e fonte de dados comumente usados no desenvolvimento do Ios.

## <a name="images"></a>[Imagens](image.md)

A adição de imagens ao seu aplicativo requer duas etapas: primeiro, adicione as imagens ao seu projeto; em seguida, adicione controles e código para exibi-los em uma tela. Consulte o artigo [trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md) para obter uma cobertura mais detalhada da manipulação de imagens no Xamarin. Ios.

## <a name="manual-camera-controls"></a>[Controles de câmera manuais](intro-to-manual-camera-controls.md)

Os controles de câmera manual, fornecidos pelo `AVFoundation Framework` no Ios 8, permitem que um aplicativo móvel assuma o controle total sobre a câmera de um dispositivo IOS. Esse nível refinado de controle pode ser usado para criar aplicativos de câmera de nível profissional e fornecer composições de artista ajustando os parâmetros da câmera ao mesmo tempo em que faz uma imagem ou um vídeo ainda.

## <a name="maps"></a>[Mapas](ios-maps/index.md)

Os mapas são um recurso comum em todos os sistemas operacionais móveis modernos. o iOS oferece suporte de mapeamento nativamente por meio da estrutura do MAP Kit. Com o MAP Kit, os aplicativos podem adicionar facilmente mapas sofisticados e interativos. Esses mapas podem ser personalizados de várias maneiras, como adicionar anotações para marcar locais em um mapa e sobrepor elementos gráficos de formas arbitrárias. O MAP kit até mesmo tem suporte interno para mostrar o local atual de um dispositivo.

## <a name="labels"></a>[Rótulos](labels.md)

O `UILabel` controle é usado para exibir texto único e de várias linhas, somente leitura.

## <a name="pickers-and-date-pickers"></a>[Seletores e seletores de data](picker.md)

O controle seletor exibe o controle ' da roda ' que contém uma lista rolável de valores com o valor selecionado sendo realçado. Os usuários giram a roda para selecionar a opção que desejam.

Um caso de usuário específico para os seletores definirem a data e/ou a hora. Para fornecer essa Apple, foi criada uma subclasse personalizada da classe UIPickerView chamada UIDatePicker.

## <a name="progress-and-activity-indicators"></a>[Indicadores de progresso e de atividade](progress-activity-indicator.md)

o iOS fornece duas maneiras principais de indicar o progresso em seu aplicativo: indicadores de atividade (incluindo um indicador de atividade de _rede_ específico) e barras de progresso.

## <a name="search-bars"></a>[Barras de pesquisa](searchbar.md)

O UISearchBar é usado para pesquisar uma lista de valores. 

## <a name="sliders-switches-and-segmented-controls"></a>[Controles deslizantes, opções e controles segmentados](slider-switch-segmented-controls.md)

O controle deslizante permite a seleção simples de um valor numérico dentro de um intervalo. o iOS usa o `UISwitch` como uma entrada booliana que pode ser representada por um botão de opção em outras plataformas. Um controle segmentado é uma maneira organizada de permitir que os usuários interajam com um pequeno número de opções.

## <a name="stack-view"></a>[Exibição de pilha](uistackview.md)

O controle de exibição de pilha ( `UIStackView` ) aproveita o poder do layout automático e de classes de tamanho para gerenciar uma pilha de subexibições, tanto horizontal quanto verticalmente, que responde dinamicamente à orientação e ao tamanho da tela do dispositivo IOS.

## <a name="tables-and-cells"></a>[Tabelas e células](tables/index.md)

sua seção apresenta as classes usadas para criar e exibir tabelas e, em seguida, fornece exemplos de como usá-las no Xamarin. iOS. Ele abordará o uso da aparência padrão para tabelas, personalizando o layout, implementando a edição e usando o Xamarin iOS designer para criar uma tabela visualmente. Às vezes, a exibição é obviamente uma lista de linhas (como o aplicativo de música) e outras vezes é difícil reconhecer o controle de tabela (como edição no aplicativo de contatos ou uma conversa no aplicativo de mensagens).

## <a name="text-input"></a>[Entrada de Texto](text-input.md)

A aceitação da entrada de texto de usuário é realizada com o `UITextField` para entradas de linha única e UITextView para texto editável de várias linhas. Você pode arrastar um desses controles para uma tela e clicar duas vezes para definir o texto inicial.

## <a name="tab-bars-and-tab-bar-controllers"></a>[Barras de guia e controladores de barra de guias](creating-tabbed-applications.md)

os aplicativos iOS que usam uma interface do usuário de navegação por guias são criados usando a classe UITabBarController. Neste artigo, vamos examinar como configurar um aplicativo com guias que contenha vários controladores e exibições. Em seguida, examinaremos como carregar um UITabBarController quando ele não for o controlador raiz, como após uma tela de logon.

## <a name="web-views"></a>[Exibições da Web](webview.md)

Neste artigo, exploraremos as exibições da Web fornecidas pela Apple – `WKWebview` e `SFSafariViewController` suas semelhanças e diferenças e como elas podem ser usadas.

## <a name="related-links"></a>Links Relacionados

- [Controles (exemplo)](/samples/xamarin/ios-samples/controls)