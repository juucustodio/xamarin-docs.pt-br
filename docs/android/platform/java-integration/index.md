---
title: Visão geral da integração do Java
description: O ecossistema do Java inclui uma coleção grande e diversificada de componentes. Muitos desses componentes podem ser usados para reduzir o tempo necessário para desenvolver um aplicativo do Android. Este documento apresentará e fornecem uma visão geral de algumas das maneiras que os desenvolvedores podem usar esses componentes Java existentes para melhorar sua experiência de desenvolvimento de aplicativo do xamarin. Android.
ms.prod: xamarin
ms.assetid: 7B5B8695-1C49-19BF-AE99-948CDCBD2A20
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/18/2017
ms.openlocfilehash: 3ab31fb7cac97fbae3315f51daf3dd4b1edbcc1d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112373"
---
# <a name="java-integration-overview"></a>Visão geral da integração do Java

_O ecossistema do Java inclui uma coleção grande e diversificada de componentes. Muitos desses componentes podem ser usados para reduzir o tempo necessário para desenvolver um aplicativo do Android. Este documento apresentará e fornecem uma visão geral de algumas das maneiras que os desenvolvedores podem usar esses componentes Java existentes para melhorar sua experiência de desenvolvimento de aplicativo do xamarin. Android._


## <a name="overview"></a>Visão geral

Dada a extensão do ecossistema do Java, é muito provável que qualquer determinada funcionalidade necessária para um aplicativo xamarin. Android já tiver sido codificada em Java. Por isso, é atraente para tentar reutilizar essas bibliotecas existentes ao criar um aplicativo xamarin. Android. 

Há três maneiras possíveis de reutilizar bibliotecas Java em um aplicativo xamarin. Android: 

-   **Criar uma biblioteca de associações de Java** &ndash; com essa técnica, um projeto xamarin. Android é usado para criar C# wrappers em torno de tipos Java. Um aplicativo xamarin. Android pode fazer referência a C# wrappers criado por este projeto e, em seguida, use o `.jar` arquivo. 

-   **Interface nativa do Java** &ndash; as *Java nativo* *Interface* (JNI) é uma estrutura que permite que o código não-Java (como C++ ou C#) para chamar ou ser chamado pelo código Java em execução dentro de uma JVM. 

-   **Portar o código** &ndash; esse método envolve pegar o código-fonte Java e, em seguida, convertê-la em C#. Isso pode ser feito manualmente ou usando uma ferramenta automatizada como ajustar nitidez. 

No núcleo das duas primeiras técnicas é o *Interface nativa Java* (JNI). JNI é uma estrutura que permite que os aplicativos que não são escritos em Java interagir com o código Java em execução em uma máquina Virtual Java. Xamarin. Android usa JNI criem *ligações* para C# código. 

A primeira técnica é uma abordagem mais automatizada e declarativa para bibliotecas Java de associação. Ela envolve usando o Visual Studio para Mac ou um tipo de projeto do Visual Studio que é fornecido pelo xamarin. Android &ndash; a biblioteca de associações de Java. Para criar com êxito essas associações, uma biblioteca de associações de Java ainda podem exigir que algumas modificações manuais, mas não tantos como seria uma abordagem essencialmente de JNI. Ver [associação de uma biblioteca Java](~/android/platform/binding-java-library/index.md) para obter mais informações sobre as bibliotecas Java de associação. 

A segunda técnica, usando JNI, funciona em um nível muito menor, mas pode fornecer para ter maior controle e acesso a métodos de Java que normalmente não é acessíveis por meio de uma biblioteca de vinculação de Java. 

A terceira técnica é radicalmente diferente das duas anteriores: portar o código do Java para C#. Portabilidade de código de um idioma para outro pode ser um processo muito trabalhoso, mas é possível reduzir a que o esforço com a Ajuda de uma ferramenta chamada *nitidez*. Aperfeiçoe é uma ferramenta de software livre que é um Java-para-C# conversor. 



## <a name="summary"></a>Resumo

Este documento fornecido uma visão geral de algumas das diferentes maneiras de que as bibliotecas do Java podem ser reutilizadas em um aplicativo xamarin. Android. Apresentou os conceitos de associações e callable wrappers gerenciados e discutido opções de portabilidade de código Java para C#. 


## <a name="related-links"></a>Links relacionados

- [Arquitetura](~/android/internals/architecture.md)
- [Associação de uma biblioteca Java](~/android/platform/binding-java-library/index.md)
- [Trabalhar com JNI](~/android/platform/java-integration/working-with-jni.md)
- [Ajustar Nitidez](https://github.com/slluis/sharpen)
- [Interface nativa do Java](http://docs.oracle.com/javase/7/docs/technotes~/jni/index.html)
