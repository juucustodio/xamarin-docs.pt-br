---
title: Introdução aos comportamentos
description: Comportamentos permitem adicionar funcionalidade a controles de interface do usuário sem a necessidade de subclasse-los. Em vez disso, a funcionalidade é implementada em uma classe de comportamento e anexada ao controle, como se fosse parte do próprio controle. Este artigo fornece uma introdução aos comportamentos.
ms.prod: xamarin
ms.assetid: 0DF1EF8C-A212-4142-A3C6-DF760A82A757
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: dc6d8396c2908d251290e4540dbb3cec3344542f
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732782"
---
# <a name="introduction-to-behaviors"></a>Introdução aos comportamentos

_Comportamentos permitem adicionar funcionalidade a controles de interface do usuário sem a necessidade de subclasse-los. Em vez disso, a funcionalidade é implementada em uma classe de comportamento e anexada ao controle, como se fosse parte do próprio controle. Este artigo fornece uma introdução aos comportamentos._

Comportamentos permitem implementar o código que normalmente seria necessário escrever lógica, porque ele interage diretamente com a API de controle de forma que possa ser maneira concisa anexado ao controle e empacotado para reutilização em mais de um aplicativo. Eles podem ser usados para fornecer uma gama completa de funcionalidade para controles, como:

- Adicionando um validador de email para um [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/).
- Criando um controle de classificação usando um reconhecedor de gestos de toque.
- Controlando uma animação.
- Adicionando um efeito a um controle.

Comportamentos também habilitam cenários mais avançados. No contexto de *ordenar*, comportamentos são uma abordagem útil para se conectar a um controle a um comando. Além disso, eles podem ser usados para associar a controles que não foram projetados para interagir com os comandos de comandos. Por exemplo, eles podem ser usados para invocar um comando em resposta a um evento de acionamento.

Xamarin. Forms oferece suporte a dois estilos diferentes de comportamentos:

- **Xamarin. Forms comportamentos** – classes que derivam de [ `Behavior` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/) ou [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) classe, onde `T` é o tipo de controle para o qual o comportamento deve ser aplicada. Para obter mais informações sobre comportamentos xamarin. Forms, consulte [xamarin. Forms comportamentos](~/xamarin-forms/app-fundamentals/behaviors/creating.md) e [reutilizável comportamentos](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md).
- **Anexado comportamentos** – `static` classes com uma ou mais propriedades anexadas. Para obter mais informações sobre comportamentos anexados, consulte [anexado comportamentos](~/xamarin-forms/app-fundamentals/behaviors/attached.md).

Este guia se concentra em comportamentos xamarin. Forms porque eles são a melhor abordagem para construção de comportamento.



## <a name="related-links"></a>Links relacionados

- [Comportamento](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)
- [Comportamento&lt;T&gt;](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)
