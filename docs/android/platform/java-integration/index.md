---
title: Integração java com Xamarin.Android
description: O ecossistema Java inclui uma coleção diversificada e imensa de componentes. Muitos desses componentes podem ser usados para reduzir o tempo necessário para desenvolver um aplicativo Android. Este documento introduzirá e fornecerá uma visão geral de alto nível de algumas das maneiras pelas quais os desenvolvedores podem usar esses componentes Java existentes para melhorar sua experiência de desenvolvimento de aplicativos Xamarin.Android.
ms.prod: xamarin
ms.assetid: 7B5B8695-1C49-19BF-AE99-948CDCBD2A20
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/18/2017
ms.openlocfilehash: ecaa02e036c74074b4fa922ea079355b72ff02e2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020093"
---
# <a name="java-integration-with-xamarinandroid"></a>Integração java com Xamarin.Android

_O ecossistema Java inclui uma coleção diversificada e imensa de componentes. Muitos desses componentes podem ser usados para reduzir o tempo necessário para desenvolver um aplicativo Android. Este documento introduzirá e fornecerá uma visão geral de alto nível de algumas das maneiras pelas quais os desenvolvedores podem usar esses componentes Java existentes para melhorar sua experiência de desenvolvimento de aplicativos Xamarin.Android._

## <a name="overview"></a>Visão geral

Dada a extensão do ecossistema Java, é muito provável que qualquer funcionalidade necessária para um aplicativo Xamarin.Android já tenha sido codificada em Java. Por causa disso, é atraente tentar reutilizar essas bibliotecas existentes ao criar um aplicativo Xamarin.Android.

Existem três maneiras possíveis de reutilizar bibliotecas Java em um aplicativo Xamarin.Android: 

- **Crie uma Biblioteca** &ndash; de Vinculações Java Com essa técnica, um projeto Xamarin.Android é usado para criar invólucros C# em torno dos tipos Java. Um aplicativo Xamarin.Android pode então referenciar os invólucros C# criados por este projeto e, em seguida, usar o `.jar` arquivo. 

- **Interface** &ndash; nativa de Java A *Interface* *Nativa java* (JNI) é uma estrutura que permite que códigos não Java (como C++ ou C#) chamem ou sejam chamados por código Java rodando dentro de um JVM. 

- **Portar o código** &ndash; Este método envolve pegar o código-fonte Java e, em seguida, convertê-lo para C#. Isso pode ser feito manualmente, ou usando uma ferramenta automatizada como a Sharpen. 

No centro das duas primeiras técnicas está a *Interface Nativa Java* (JNI). JNI é uma estrutura que permite que aplicativos não gravados em Java interajam com código Java em execução em uma Máquina Virtual Java. Xamarin.Android usa JNI para criar *vinculações* para código C#. 

A primeira técnica é uma abordagem mais automatizada e declarativa para vincular bibliotecas Java. Envolve o uso do Visual Studio para Mac ou um tipo de &ndash; projeto do Visual Studio que é fornecido pelo Xamarin.Android the Java Bindings Library. Para criar com sucesso essas vinculações, uma Biblioteca de Vinculações Java ainda pode exigir algumas modificações manuais, mas não tantas quanto uma abordagem JNI pura. Consulte [Vincular uma Biblioteca Java](~/android/platform/binding-java-library/index.md) para obter mais informações sobre bibliotecas Java Binding. 

A segunda técnica, usando JNI, funciona em um nível muito mais baixo, mas pode fornecer um controle mais fino e acesso a métodos Java que normalmente não seriam acessíveis através de uma Biblioteca de Ligação Java. 

A terceira técnica é radicalmente diferente das duas anteriores: portando o código de Java para C#. A portação de código de uma língua para outra pode ser um processo muito trabalhoso, mas é possível reduzir esse esforço com a ajuda de uma ferramenta chamada *Sharpen*. Sharpen é uma ferramenta de código aberto que é um conversor Java-to-C#. 

## <a name="summary"></a>Resumo

Este documento forneceu uma visão geral de alto nível de algumas das diferentes maneiras pelas quais as bibliotecas de Java podem ser reutilizadas em um aplicativo Xamarin.Android. Ele introduziu os conceitos de vinculações e invólucros caláveis gerenciados, e discutiu opções para portar código Java para C#. 

## <a name="related-links"></a>Links relacionados

- [Arquitetura](~/android/internals/architecture.md)
- [Associação de uma biblioteca Java](~/android/platform/binding-java-library/index.md)
- [Trabalhando com jni](~/android/platform/java-integration/working-with-jni.md)
- [Ajustar Nitidez](https://github.com/slluis/sharpen)
- [Interface nativa de Java](https://docs.oracle.com/javase/7/docs/technotes~/jni/index.html)
