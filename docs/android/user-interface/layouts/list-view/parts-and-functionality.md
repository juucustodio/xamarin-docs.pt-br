---
title: Funcionalidade e partes de ListView
ms.topic: article
ms.prod: xamarin
ms.assetid: ABA40FED-FF68-C0B0-BC43-C748CEE585D8
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/21/2017
ms.openlocfilehash: a963922dfdc07164b7c20f84be4f0b87440bff13
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="listview-parts-and-functionality"></a>Funcionalidade e partes de ListView

<a name="overview" />

## <a name="overview"></a>Visão geral

Um `ListView` consiste nas seguintes partes:

- **Linhas** &ndash; a representação visível dos dados na lista.

- **Adaptador** &ndash; uma classe não-visual que vincula a fonte de dados para o modo de exibição de lista.

- **Rolagem rápida** &ndash; um identificador que permite que o usuário rolar o comprimento da lista.

- **Índice de seção** &ndash; linhas de um elemento de interface do usuário que flutua sobre a rolagem para indicar onde as linhas atuais estão localizadas na lista.

Essas capturas de tela usam um basic `ListView` controle para mostrar como rolagem rápida e o índice de seção são renderizados:

[![Capturas de tela de aplicativos usando linhas antigas simples, rápida de rolagem e o índice de seção](parts-and-functionality-images/listviewparts.png)](parts-and-functionality-images/listviewparts.png)

Os elementos que compõem um `ListView` são descritas em mais detalhes abaixo:

<a name="Rows" />

## <a name="rows"></a>Linhas

Cada linha tem seu próprio `View`. O modo de exibição pode ser qualquer uma das exibições internas definidas em `Android.Resources`, ou uma exibição personalizada. Cada linha podem usar o mesmo layout do modo de exibição ou todas elas podem ser diferentes. Há exemplos neste documento do uso de layouts internos e outros explicando como definir layouts personalizados.

<a name="Adapter" />

## <a name="adapter"></a>Adaptador

O `ListView` controle requer uma `Adapter` para fornecer o formatado `View` para cada linha. Android tem internos adaptadores e exibições que podem ser usadas ou classes personalizadas podem ser criadas.

<a name="Fast_Scrolling" />

## <a name="fast-scrolling"></a>Rolagem rápida

Quando um `ListView` contém muitas linhas de dados o fast de rolagem pode ser habilitada para ajudar o usuário navegar para qualquer parte da lista. O fast rolagem 'barra de rolagem' pode ser opcionalmente habilitado (e personalizada em nível de API 11 e superior).

<a name="Section_Index" />

## <a name="section-index"></a>Índice de seção

Ao rolar por listas longas, o índice de seção opcional fornece ao usuário comentários em qual parte da lista estão exibindo no momento. Só é apropriado em listas longas, geralmente em conjunto com rolagem rápida.

<a name="Classes_Overview" />

## <a name="classes-overview"></a>Visão geral de classes

As classes principais usadas para exibir `ListViews` são mostradas aqui:

[![Diagrama UML ilustrar relações entre ListView e classes associadas](parts-and-functionality-images/image2.png)](parts-and-functionality-images/image2.png)

A finalidade de cada classe é descrita abaixo:

- **ListView** &ndash; elemento de interface do usuário que exibe uma coleção rolável de linhas. Em telefones ele geralmente usa a tela inteira (nesse caso, a `ListActivity` classe pode ser usada) ou pode ser parte de um layout maior em telefones ou tablets.

- **Exibição** &ndash; uma exibição no Android pode ser qualquer elemento de interface do usuário, mas no contexto de um `ListView` requer um `View` deve ser fornecido para cada linha.

- **BaseAdapter** &ndash; a classe Base para implementações de adaptador associar um `ListView` para uma fonte de dados.

- **ArrayAdapter** &ndash; classe do adaptador interno que associa uma matriz de cadeias de caracteres para um `ListView` para exibição. Genérica `ArrayAdapter<T>` faz o mesmo para outros tipos.

- **CursorAdapter** &ndash; Use `CursorAdapter` ou `SimpleCursorAdapter` para exibir dados com base em uma consulta do SQLite.

Este documento contém exemplos simples de usarem um `ArrayAdapter` , bem como exemplos mais complexos que exigem implementações personalizadas de `BaseAdapter` ou `CursorAdapter`.

