---
title: Visão geral da integração Java
description: O ecossistema de Java inclui um conjunto diversificado e enorme de componentes. Muitos desses componentes podem ser usados para reduzir o tempo necessário para desenvolver um aplicativo do Android. Este documento apresentará e fornecem uma visão geral de algumas das maneiras que os desenvolvedores podem usar esses componentes existentes do Java para melhorar sua experiência de desenvolvimento de aplicativo xamarin.
ms.prod: xamarin
ms.assetid: 7B5B8695-1C49-19BF-AE99-948CDCBD2A20
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/18/2017
ms.openlocfilehash: dbaf17479ae077fced425df5ac31bdbbc4e06b64
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="java-integration-overview"></a>Visão geral da integração Java

_O ecossistema de Java inclui um conjunto diversificado e enorme de componentes. Muitos desses componentes podem ser usados para reduzir o tempo necessário para desenvolver um aplicativo do Android. Este documento apresentará e fornecem uma visão geral de algumas das maneiras que os desenvolvedores podem usar esses componentes existentes do Java para melhorar sua experiência de desenvolvimento de aplicativo xamarin._


## <a name="overview"></a>Visão geral

Devido à extensão do ecossistema do Java, é muito provável que qualquer determinada funcionalidade necessária para um aplicativo xamarin já foram codificada em Java. Por isso, é interessante para tentar reutilizar essas bibliotecas existentes ao criar um aplicativo xamarin. 

Há três maneiras possíveis de reutilizar bibliotecas Java em um aplicativo xamarin: 

-   **Criar uma biblioteca de associações de Java** &ndash; com essa técnica, um projeto de xamarin é usado para criar o c# wrappers em torno de tipos de Java. Um aplicativo xamarin pode fazer referência os wrappers c# criados por este projeto e, em seguida, usar o `.jar` arquivo. 

-   **Interface nativa Java** &ndash; o *Java nativo* *Interface* (JNI) é uma estrutura que permite que o código Java não (como C++ ou c#) para chamar ou ser chamado pelo código de Java em execução dentro de uma JVM . 

-   **O código da porta** &ndash; esse método envolve colocar o código-fonte Java e, em seguida, convertê-la em c#. Isso pode ser feito manualmente ou usando uma ferramenta automatizada como ajustar nitidez. 

O núcleo das duas primeiras técnicas é o *Interface nativa Java* (JNI). JNI é uma estrutura que permite que aplicativos não escritos em Java interagir com o código Java em execução em uma máquina Virtual Java. Xamarin usa JNI para criar *associações* de código c#. 

A primeira técnica é um método mais automatizado, declarativo à associação de bibliotecas de Java. Ele envolve o uso no Visual Studio para Mac ou um tipo de projeto do Visual Studio que é fornecido pelo xamarin &ndash; a biblioteca de associações de Java. Para criar com êxito essas associações, uma biblioteca de associações de Java ainda podem exigir que algumas modificações manuais, mas não tantos como seria uma abordagem JNI pura. Consulte [associação a uma biblioteca Java](~/android/platform/binding-java-library/index.md) para obter mais informações sobre as bibliotecas de associação de Java. 

A segunda técnica, usando JNI, funciona em um nível muito menor, mas pode fornecer para ter maior controle e acesso a métodos de Java que normalmente não é acessíveis por meio de uma biblioteca de ligação de Java. 

A terceira técnica é radicalmente diferente dos dois anteriores: portar o código do Java no c#. Portabilidade de código de um idioma para outro pode ser um processo muito trabalhoso, mas é possível reduzir que esforço com a Ajuda de uma ferramenta chamada *nitidez*. Nitidez é uma ferramenta de código-fonte aberto que é um Java-para-C# conversor. 



## <a name="summary"></a>Resumo

Este documento fornecida uma visão geral de algumas das maneiras diferentes que bibliotecas do Java podem ser reutilizadas em um aplicativo xamarin. Introduziu os conceitos de associações e gerenciados callable wrappers e discutido opções para portar o código Java para c#. 


## <a name="related-links"></a>Links relacionados

- [Arquitetura](~/android/internals/architecture.md)
- [Associação de uma biblioteca Java](~/android/platform/binding-java-library/index.md)
- [Trabalhando com JNI](~/android/platform/java-integration/working-with-jni.md)
- [Ajustar Nitidez](https://github.com/slluis/sharpen)
- [Interface nativa do Java](http://docs.oracle.com/javase/7/docs/technotes~/jni/index.html)
