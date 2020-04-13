---
title: Portando Java para C# para Xamarin.Android
description: Uma terceira opção para usar Java em um aplicativo Xamarin.Android é portar o código fonte Java para C#.
ms.prod: xamarin
ms.assetid: 39E528BD-010F-47FC-BE48-8E7848E30454
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/05/2016
ms.openlocfilehash: 8f96fcc4aadcd8f082d55dc568b2517f048edaf2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027187"
---
# <a name="porting-java-to-c-for-xamarinandroid"></a>Portando Java para C# para Xamarin.Android

Essa abordagem pode ser de interesse das organizações que:

- **Estão mudando pilhas de tecnologia de Java para C#.**
- **Deve manter uma versão C# e java do mesmo produto.**
- **Deseja ter uma versão .NET de uma biblioteca Java popular.**

Existem duas maneiras de portar código Java para C#. A primeira maneira é portar o código manualmente. Isso envolve desenvolvedores qualificados que entendem tanto .NET quanto Java e estão familiarizados com as expressões adequadas para cada idioma. Essa abordagem faz mais sentido para pequenas quantidades de código, ou para organizações que desejam se afastar completamente de Java para C#.

A segunda metodologia de portação é tentar automatizar o processo usando um conversor de código, como [o Sharpen](https://github.com/mono/sharpen). [Sharpen](https://github.com/mono/sharpen) é um conversor de código aberto da Versant que foi originalmente usado para portar o código para *db4o* de Java para C#. db4o é um banco de dados orientado a objetos que o Versant desenvolveu em Java e, em seguida, foi portado para .NET. Usar um conversor de código pode fazer sentido para projetos que devem existir em ambos os idiomas e que requerem alguma paridade entre os dois.

Um exemplo de quando uma ferramenta automatizada de conversão de código faz sentido pode ser visto no projeto [ngit.](https://github.com/mono/ngit)
Ngit é uma porta do projeto Java [jgit](https://eclipse.org/).
Jgit em si é uma implementação Java do sistema de gerenciamento de código fonte [Git.](https://git-scm.com/) Para gerar o código C# de Java, os programadores ngit usam um sistema automatizado personalizado para extrair o código Java do jgit, aplicar alguns patches para acomodar o processo de conversão e, em seguida, executar o Sharpen, que gera o código C#. Isso permite que o projeto ngit se beneficie do trabalho contínuo e contínuo que é feito no jgit.

Muitas vezes há uma quantidade não trivial de trabalho envolvida com bootstrapping uma ferramenta de conversão de código automatizada, e isso pode ser uma barreira para usar. Em muitos casos, pode ser mais simples e fácil portar Java para C# à mão.

## <a name="related-links"></a>Links relacionados

- [Ferramenta de conversão de afiação](https://github.com/mono/sharpen)
