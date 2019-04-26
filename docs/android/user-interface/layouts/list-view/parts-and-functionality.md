---
title: Partes e funcionalidade de ListView
ms.prod: xamarin
ms.assetid: ABA40FED-FF68-C0B0-BC43-C748CEE585D8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2017
ms.openlocfilehash: 248baa5daceff6db01098a155600ea204547e845
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61319702"
---
# <a name="listview-parts-and-functionality"></a>Partes e funcionalidade de ListView


## <a name="overview"></a>Visão geral

Um `ListView` consiste nas seguintes partes:

- **Linhas** &ndash; representações visíveis dos dados na lista.

- **Adaptador** &ndash; uma classe não-visuais que associa a fonte de dados para o modo de exibição de lista.

- **Rolagem rápida** &ndash; um identificador que permite que o usuário role o comprimento da lista.

- **Seção de índice** &ndash; linhas de um elemento de interface do usuário que flutua sobre a rolagem para indicar onde as linhas atuais estão localizadas na lista.

Essas capturas de tela usam um basic `ListView` controle para mostrar como rolagem rápida e o índice de seção são renderizados:

[![Capturas de tela de aplicativos usando linhas antigas simples, rápida de rolagem e o índice de seção](parts-and-functionality-images/listviewparts.png)](parts-and-functionality-images/listviewparts.png#lightbox)

Os elementos que compõem um `ListView` são descritas em mais detalhes abaixo:


## <a name="rows"></a>Linhas

Cada linha tem seu próprio `View`. O modo de exibição pode ser uma das exibições internas definidas no `Android.Resources`, ou uma exibição personalizada. Cada linha podem usar o mesmo layout de exibição ou todas elas podem ser diferentes. Há exemplos neste documento do uso de layouts internos e outras pessoas, explicando como definir layouts personalizados.


## <a name="adapter"></a>Adaptador

O `ListView` controle requer uma `Adapter` fornecer formatada `View` para cada linha. Android tem internos adaptadores e exibições que podem ser usadas ou classes personalizadas podem ser criadas.


## <a name="fast-scrolling"></a>Rolagem rápida

Quando um `ListView` contém muitas linhas de dados o fast de rolagem pode ser habilitada para ajudar o usuário navegar para qualquer parte da lista. O fast rolagem 'barra de rolagem' pode ser opcionalmente habilitado (e personalizada em nível de API 11 e superior).


## <a name="section-index"></a>Índice de seção

Durante a rolagem por listas longas, o índice de seção opcional fornece ao usuário comentários sobre qual parte da lista estão exibindo no momento. Só é apropriado em listas longas, normalmente em conjunto com a rolagem rápida.


## <a name="classes-overview"></a>Visão geral de classes

Classes principais usadas para exibir `ListViews` são mostrados aqui:

[![Diagrama UML mostrando as relações entre o ListView e classes associadas](parts-and-functionality-images/image2.png)](parts-and-functionality-images/image2.png#lightbox)

A finalidade de cada classe é descrita abaixo:

- **ListView** &ndash; elemento de interface do usuário que exibe uma coleção rolável de linhas. Por telefones ele geralmente usa a tela inteira (nesse caso, o `ListActivity` classe pode ser usada) ou pode ser parte de um layout maior em telefones ou tablets.

- **Modo de exibição** &ndash; uma exibição no Android pode ser qualquer elemento de interface do usuário, mas no contexto de uma `ListView` requer um `View` deve ser fornecido para cada linha.

- **BaseAdapter** &ndash; a classe Base para implementações de adaptador associar um `ListView` para uma fonte de dados.

- **ArrayAdapter** &ndash; classe de adaptador interno que é associado a uma matriz de cadeias de caracteres para um `ListView` para exibição. Genérica `ArrayAdapter<T>` faz o mesmo para outros tipos.

- **CursorAdapter** &ndash; uso `CursorAdapter` ou `SimpleCursorAdapter` para exibir dados com base em uma consulta do SQLite.

Este documento contém exemplos simples de usarem um `ArrayAdapter` , bem como exemplos mais complexos que exigem implementações personalizadas dos `BaseAdapter` ou `CursorAdapter`.

