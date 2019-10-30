---
title: Portando Java para C# para Xamarin. Android
description: Uma terceira opção para usar o Java em um aplicativo Xamarin. Android é portar o código-fonte C#Java para.
ms.prod: xamarin
ms.assetid: 39E528BD-010F-47FC-BE48-8E7848E30454
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/05/2016
ms.openlocfilehash: 8f96fcc4aadcd8f082d55dc568b2517f048edaf2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027187"
---
# <a name="porting-java-to-c-for-xamarinandroid"></a>Portando Java para C# para Xamarin. Android

Essa abordagem pode ser de interesse para as organizações que:

- **Estão alternando pilhas de tecnologia de Java C#para.**
- **Deve manter um C# e uma versão Java do mesmo produto.**
- **Deseja ter uma versão .NET de uma biblioteca Java popular.**

Há duas maneiras de portar código Java para C#. A primeira maneira é portar o código manualmente. Isso envolve os desenvolvedores experientes que entendem o .NET e o Java e estão familiarizados com os idiomas apropriados para cada idioma. Essa abordagem faz mais sentido para pequenas quantidades de código ou para organizações que desejam sair completamente do Java para C#o.

A segunda metodologia de portabilidade é tentar automatizar o processo usando um conversor de código, como [nitidez](https://github.com/mono/sharpen). A [nitidez](https://github.com/mono/sharpen) é um conversor de código-fonte aberto do que foi originalmente usado para portar o código para *Db4o* de Java para C#. Db4o é um banco de dados orientado a objeto que era desenvolvido em Java e, em seguida, portado para .NET. Usar um conversor de código pode fazer sentido para projetos que devem existir em ambos os idiomas e que exigem alguma paridade entre os dois.

Um exemplo de quando uma ferramenta de conversão de código automatizada faz sentido pode ser vista no projeto [ngit](https://github.com/mono/ngit) .
Ngit é uma porta do [as jgit](https://eclipse.org/)do projeto Java.
O as jgit em si é uma implementação de Java do sistema de gerenciamento de código-fonte [git](https://git-scm.com/) . Para gerar C# código do Java, os programadores ngit usam um sistema automatizado personalizado para extrair o código Java do as jgit, aplicar alguns patches para acomodar o processo de conversão e, em seguida, executar C# a nitidez, o que gera o código. Isso permite que o projeto ngit se beneficie do trabalho contínuo e contínuo feito no as jgit.

Geralmente, há uma quantidade de trabalho não trivial envolvida na inicialização de uma ferramenta de conversão de código automatizada, e isso pode provar ser uma barreira a ser usada. Em muitos casos, pode ser mais simples e mais fácil de portar Java C# para a mão.

## <a name="related-links"></a>Links relacionados

- [Ferramenta de conversão de nitidez](https://github.com/mono/sharpen)
