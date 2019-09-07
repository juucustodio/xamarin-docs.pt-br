---
title: Partes e funcionalidade de ListView
ms.prod: xamarin
ms.assetid: ABA40FED-FF68-C0B0-BC43-C748CEE585D8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2017
ms.openlocfilehash: 4566ee5d203b5d098133aebe2c32dbaec712e17a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764219"
---
# <a name="xamarinandroid-listview-parts-and-functionality"></a>Funcionalidades e partes de ListView do Xamarin. Android

Um `ListView` consiste nas seguintes partes:

- **Linhas** &ndash; A representação visível dos dados na lista.

- **Adaptador** do &ndash; Uma classe não visual que associa a fonte de dados ao modo de exibição de lista.

- **Rolagem rápida** &ndash; Um identificador que permite ao usuário rolar o comprimento da lista.

- **Índice da seção** &ndash; Um elemento de interface do usuário que flutua sobre as linhas de rolagem para indicar onde na lista as linhas atuais estão localizadas.

Essas capturas de tela usam `ListView` um controle básico para mostrar como a rolagem rápida e o índice de seção são renderizados:

[![Capturas de tela de aplicativos usando linhas antigas, rolagem rápida e índice de seção simples](parts-and-functionality-images/listviewparts.png)](parts-and-functionality-images/listviewparts.png#lightbox)

Os elementos que compõem um `ListView` são descritos mais detalhadamente abaixo:

## <a name="rows"></a>Linhas

Cada linha tem sua própria `View`. A exibição pode ser uma das exibições internas definidas em `Android.Resources`ou uma exibição personalizada. Cada linha pode usar o mesmo layout de exibição ou todas elas podem ser diferentes. Há exemplos neste documento de uso de layouts internos e outros explicando como definir layouts personalizados.

## <a name="adapter"></a>Adaptador

O `ListView` controle requer um `Adapter` para fornecer o formatado `View` para cada linha. O Android possui adaptadores e exibições internos que podem ser usados ou classes personalizadas podem ser criadas.

## <a name="fast-scrolling"></a>Rolagem rápida

Quando um `ListView` contém muitas linhas de dados, a rolagem rápida pode ser habilitada para ajudar o usuário a navegar para qualquer parte da lista. A rolagem rápida "barra de rolagem" pode ser opcionalmente habilitada (e personalizada no nível da API 11 e superior).

## <a name="section-index"></a>Índice da seção

Ao rolar por listas longas, o índice de seção opcional fornece ao usuário comentários sobre qual parte da lista está sendo exibida no momento. Ele só é apropriado em listas longas, normalmente em conjunto com a rolagem rápida.

## <a name="classes-overview"></a>Visão geral de classes

As classes primárias usadas para `ListViews` exibição são mostradas aqui:

[![Diagrama UML que ilustra as relações entre ListView e classes associadas](parts-and-functionality-images/image2.png)](parts-and-functionality-images/image2.png#lightbox)

A finalidade de cada classe é descrita abaixo:

- **ListView** &ndash; elemento de interface do usuário que exibe uma coleção rolável de linhas. Em telefones, ele geralmente usa a tela inteira (nesse caso, a `ListActivity` classe pode ser usada) ou pode fazer parte de um layout maior em telefones ou dispositivos Tablet.

- **Exibir** uma exibição no Android pode ser qualquer elemento da interface do usuário, mas no contexto de `ListView` uma ti, `View` é necessário fornecer um a ser fornecido para cada linha. &ndash;

- **BaseAdapter** Classe base para implementações de adaptador para `ListView` associar um a uma fonte de dados. &ndash;

- **ArrayAdapter** Classe de adaptador interna que associa uma matriz de cadeias de caracteres a `ListView` uma para exibição. &ndash; O genérico `ArrayAdapter<T>` faz o mesmo para outros tipos.

- **CursorAdapter** &ndash; Use oupara`SimpleCursorAdapter` exibir dados com base em uma consulta do SQLite. `CursorAdapter`

Este documento contém exemplos simples que usam um `ArrayAdapter` , bem como exemplos mais complexos que exigem implementações personalizadas `BaseAdapter` do `CursorAdapter`ou do.
