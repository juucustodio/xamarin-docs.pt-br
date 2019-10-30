---
title: Partes e funcionalidade de ListView
ms.prod: xamarin
ms.assetid: ABA40FED-FF68-C0B0-BC43-C748CEE585D8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2017
ms.openlocfilehash: b8fd44a70f4c7ecdcf7919dec1c81461200b35bf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028859"
---
# <a name="xamarinandroid-listview-parts-and-functionality"></a>Xamarin.Android ListView Parts and Functionality

A `ListView` consists of the following parts:

- **Rows** &ndash; The visible representation of the data in the list.

- **Adapter** &ndash; A non-visual class that binds the data source to the list view.

- **Fast Scrolling** &ndash; A handle that lets the user scroll the length of the list.

- **Índice de seção** &ndash; um elemento de interface do usuário que flutua sobre as linhas de rolagem para indicar onde na lista as linhas atuais estão localizadas.

Essas capturas de tela usam um controle de `ListView` básico para mostrar como a rolagem rápida e o índice de seção são renderizados:

[![capturas de tela de aplicativos usando linhas antigas, rolagem rápida e índice de seção simples](parts-and-functionality-images/listviewparts.png)](parts-and-functionality-images/listviewparts.png#lightbox)

Os elementos que compõem uma `ListView` são descritos mais detalhadamente abaixo:

## <a name="rows"></a>Linhas

Cada linha tem seu próprio `View`. A exibição pode ser uma das exibições internas definidas em `Android.Resources`, ou uma exibição personalizada. Cada linha pode usar o mesmo layout de exibição ou todas elas podem ser diferentes. Há exemplos neste documento de uso de layouts internos e outros explicando como definir layouts personalizados.

## <a name="adapter"></a>Adaptador

O controle `ListView` requer uma `Adapter` para fornecer a `View` formatada para cada linha. O Android possui adaptadores e exibições internos que podem ser usados ou classes personalizadas podem ser criadas.

## <a name="fast-scrolling"></a>Rolagem rápida

Quando um `ListView` contém muitas linhas de dados, a rolagem rápida pode ser habilitada para ajudar o usuário a navegar para qualquer parte da lista. A rolagem rápida "barra de rolagem" pode ser opcionalmente habilitada (e personalizada no nível da API 11 e superior).

## <a name="section-index"></a>Índice da seção

Ao rolar por listas longas, o índice de seção opcional fornece ao usuário comentários sobre qual parte da lista está sendo exibida no momento. Ele só é apropriado em listas longas, normalmente em conjunto com a rolagem rápida.

## <a name="classes-overview"></a>Visão geral de classes

As classes primárias usadas para exibir `ListViews` são mostradas aqui:

[![diagrama UML que ilustra as relações entre ListView e classes associadas](parts-and-functionality-images/image2.png)](parts-and-functionality-images/image2.png#lightbox)

A finalidade de cada classe é descrita abaixo:

- **ListView** &ndash; elemento de interface do usuário que exibe uma coleção rolável de linhas. Em telefones, ele geralmente usa a tela inteira (nesse caso, a classe `ListActivity` pode ser usada) ou pode fazer parte de um layout maior em telefones ou dispositivos Tablet.

- **Exibir** &ndash; uma exibição no Android pode ser qualquer elemento de interface do usuário, mas no contexto de um `ListView` ele requer que um `View` seja fornecido para cada linha.

- **BaseAdapter** &ndash; classe base para implementações de adaptador para associar um `ListView` a uma fonte de dados.

- **ArrayAdapter** &ndash; classe de adaptador interna que associa uma matriz de cadeias de caracteres a uma `ListView` para exibição. O `ArrayAdapter<T>` genérico faz o mesmo para outros tipos.

- **CursorAdapter** &ndash; usar `CursorAdapter` ou `SimpleCursorAdapter` para exibir dados com base em uma consulta do SQLite.

Este documento contém exemplos simples que usam um `ArrayAdapter`, bem como exemplos mais complexos que exigem implementações personalizadas de `BaseAdapter` ou `CursorAdapter`.
