---
title: Portabilidade Java para c#
description: Uma terceira opção para usar o Java em um aplicativo xamarin é portar o código-fonte Java para c#.
ms.prod: xamarin
ms.assetid: 39E528BD-010F-47FC-BE48-8E7848E30454
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/05/2016
ms.openlocfilehash: c2d05b101c627dab42dc1343eab2a408d1bd010f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30762591"
---
# <a name="porting-java-to-c"></a>Portabilidade Java para c#

_Uma terceira opção para usar o Java em um aplicativo xamarin é portar o código-fonte Java para c#._

## <a name="overview"></a>Visão geral

Essa abordagem pode ser de interesse para organizações que:

-  **Está alternando pilhas de tecnologia do Java para c#.**
-  **Deve manter um c# e uma versão de Java do mesmo produto.**
-  **Deseja ter uma versão do .NET de uma biblioteca Java popular.**


Há duas maneiras de portar código de Java para c#. O primeiro modo é portar o código manualmente. Isso envolve desenvolvedores experientes que entender .NET e Java e estejam familiarizado com as linguagens adequadas para cada idioma. Essa abordagem faz mais sentido para pequenas quantidades de código, ou para organizações que desejam migrar completamente Java para c#.

A metodologia de portabilidade de segundo é tentar automatizar o processo usando um conversor de código, como [nitidez](https://github.com/mono/sharpen). [Nitidez](https://github.com/mono/sharpen) é um conversor de código aberto de Versant usada originalmente para o código para a porta *db4o* do Java para c#. db4o é um banco de dados orientado a objeto que Versant desenvolvido em Java e, em seguida, compilado para .NET. Usar um conversor de código pode fazer sentido para projetos que deve existir em ambos os idiomas e que exigem algumas paridade entre os dois.

Um exemplo de quando uma ferramenta de conversão de código automática faz sentido pode ser visto no [ngit](https://github.com/mono/ngit) projeto.
Ngit é uma porta do projeto Java [jgit](http://eclipse.org/).
Jgit em si é uma implementação Java do [Git](http://git-scm.com/) sistema de gerenciamento de código fonte. Para gerar o código c# do Java, os programadores ngit usam um sistema automatizado personalizado para extrair o código Java jgit, aplicar alguns patches para acomodar o processo de conversão e execute a nitidez, que gera o código c#. Isso permite que o projeto ngit para se beneficiar com o trabalho contínuo, em andamento que é feito em jgit.

Geralmente há uma quantidade considerável de trabalho envolvido com uma ferramenta de conversão automática de código de inicialização, e isso pode revelar uma barreira para usar. Em muitos casos, pode ser mais simples e mais fácil de Java de porta para c# manualmente.



## <a name="related-links"></a>Links relacionados

- [Ferramenta de conversão nitidez](https://github.com/mono/sharpen)
