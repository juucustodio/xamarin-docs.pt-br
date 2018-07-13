---
title: Introdução aos comportamentos
description: Comportamentos permitem que você adicionar funcionalidade a controles de interface do usuário sem ter a subclasse-los. Em vez disso, a funcionalidade é implementada em uma classe de comportamento e anexada ao controle, como se fosse parte do próprio controle. Este artigo fornece uma introdução aos comportamentos.
ms.prod: xamarin
ms.assetid: 0DF1EF8C-A212-4142-A3C6-DF760A82A757
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 176f41d4b7349af2cf7cc49de8ba0789ad2f8c11
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995808"
---
# <a name="introduction-to-behaviors"></a>Introdução aos comportamentos

_Comportamentos permitem que você adicionar funcionalidade a controles de interface do usuário sem ter a subclasse-los. Em vez disso, a funcionalidade é implementada em uma classe de comportamento e anexada ao controle, como se fosse parte do próprio controle. Este artigo fornece uma introdução aos comportamentos._

Os comportamentos permitem que você implemente código que normalmente seria necessário para gravar como code-behind, porque ele interage diretamente com a API do controle de forma que possa ser concisa anexado ao controle e empacotado para reutilização em mais de um aplicativo. Eles podem ser usados para fornecer uma ampla gama de funcionalidade para controles, como:

- Adicionando um validador de email para um [ `Entry` ](xref:Xamarin.Forms.Entry).
- Criando um controle de classificação usando um reconhecedor de gestos de toque.
- Controlando uma animação.
- Adicionar um efeito a um controle.

Os comportamentos também permitem cenários mais avançados. No contexto de *comandando*, os comportamentos são uma abordagem útil para se conectar a um controle a um comando. Além disso, eles podem ser usados para associar a controles que não foram projetados para interagir com os comandos de comandos. Por exemplo, eles podem ser usados para invocar um comando em resposta ao acionamento de um evento.

Xamarin. Forms dá suporte a dois estilos diferentes de comportamentos:

- **Comportamentos do xamarin. Forms** – as classes que derivam de [ `Behavior` ](xref:Xamarin.Forms.Behavior) ou [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) classe, onde `T` é o tipo do controle ao qual o comportamento deve ser aplicada. Para obter mais informações sobre os comportamentos do xamarin. Forms, consulte [comportamentos do xamarin. Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md) e [reutilizável comportamentos](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md).
- **Anexado comportamentos** – `static` classes com um ou mais propriedades anexadas. Para obter mais informações sobre comportamentos anexados, consulte [comportamentos anexados](~/xamarin-forms/app-fundamentals/behaviors/attached.md).

Este guia se concentra em comportamentos do xamarin. Forms porque eles são a abordagem preferida para construção de comportamento.



## <a name="related-links"></a>Links relacionados

- [Comportamento](xref:Xamarin.Forms.Behavior)
- [Comportamento&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
