---
title: Portabilidade de Java paraC#
description: Uma terceira opção para usar Java em um aplicativo xamarin. Android é a portabilidade de código-fonte Java para C#.
ms.prod: xamarin
ms.assetid: 39E528BD-010F-47FC-BE48-8E7848E30454
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/05/2016
ms.openlocfilehash: 9beb6d59c9376a404c06af7f0cd1efd985929843
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104265"
---
# <a name="porting-java-to-c"></a>Portabilidade de Java paraC#

_Uma terceira opção para usar Java em um aplicativo xamarin. Android é a portabilidade de código-fonte Java para C#._

## <a name="overview"></a>Visão geral

Essa abordagem pode ser de interesse para organizações que:

-  **Estiver alternando pilhas de tecnologia do Java para C#.**
-  **Deve manter uma C# e uma versão de Java do mesmo produto.**
-  **Deseja ter uma versão do .NET de uma biblioteca Java popular.**


Há duas maneiras para portar código Java para o C#. A primeira maneira é portar o código manualmente. Isso envolve desenvolvedores especializados que entender o .NET e Java e estiver familiarizado com as linguagens adequadas para cada idioma. Essa abordagem faz mais sentido para pequenas quantidades de código, ou para organizações que desejam migrar completamente da Java para C#.

A segunda metodologia de portabilidade é tentar automatizar o processo usando um conversor de código, como [nitidez](https://github.com/mono/sharpen). [Aperfeiçoe](https://github.com/mono/sharpen) é um conversor de código-fonte aberto do Versant usada originalmente para o código para a porta *db4o* do Java para C#. db4o é um banco de dados orientado a objeto que Versant desenvolvido em Java e, em seguida, movido para o .NET. Usando um conversor de código talvez faça sentido para projetos que deve existir em ambas as linguagens e que exigem algum paridade entre os dois.

Um exemplo de quando uma ferramenta de conversão de código automático faz sentido que pode ser visto na [ngit](https://github.com/mono/ngit) projeto.
Ngit é uma porta do projeto Java [jgit](http://eclipse.org/).
Jgit em si é uma implementação Java do [Git](http://git-scm.com/) sistema de gerenciamento de código de origem. Para gerar C# código do Java, o uso de programadores ngit personalizado automatizada de sistema para extrair o código Java de jgit, aplicar alguns patches para acomodar o processo de conversão e, em seguida, executar nitidez, que gera o C# código. Isso permite que o projeto ngit aproveitar o trabalho em andamento e contínuo que é feito em jgit.

Geralmente há uma quantidade não trivial de trabalho envolvido com uma ferramenta de conversão de código automático de inicialização, e isso pode se revelar uma barreira para usar. Em muitos casos, pode ser mais simples e mais fácil de Java de porta para C# manualmente.



## <a name="related-links"></a>Links relacionados

- [Aperfeiçoe a ferramenta de conversão](https://github.com/mono/sharpen)
