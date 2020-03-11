---
title: Integração do Java com o Xamarin. Android
description: O ecossistema Java inclui uma coleção diversificada e imensa de componentes. Muitos desses componentes podem ser usados para reduzir o tempo necessário para desenvolver um aplicativo Android. Este documento apresentará e fornecerá uma visão geral de alto nível de algumas das maneiras como os desenvolvedores podem usar esses componentes Java existentes para aprimorar sua experiência de desenvolvimento de aplicativos Xamarin. Android.
ms.prod: xamarin
ms.assetid: 7B5B8695-1C49-19BF-AE99-948CDCBD2A20
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/18/2017
ms.openlocfilehash: ecaa02e036c74074b4fa922ea079355b72ff02e2
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73020093"
---
# <a name="java-integration-with-xamarinandroid"></a>Integração do Java com o Xamarin. Android

_O ecossistema Java inclui uma coleção diversificada e imensa de componentes. Muitos desses componentes podem ser usados para reduzir o tempo necessário para desenvolver um aplicativo Android. Este documento apresentará e fornecerá uma visão geral de alto nível de algumas das maneiras como os desenvolvedores podem usar esses componentes Java existentes para aprimorar sua experiência de desenvolvimento de aplicativos Xamarin. Android._

## <a name="overview"></a>Visão geral

Dada a extensão do ecossistema do Java, é muito provável que qualquer funcionalidade específica necessária para um aplicativo Xamarin. Android já tenha sido codificada em Java. Por isso, é atraente tentar reutilizar essas bibliotecas existentes ao criar um aplicativo Xamarin. Android.

Há três maneiras possíveis de reutilizar bibliotecas Java em um aplicativo Xamarin. Android: 

- **Criar uma biblioteca de associações Java** &ndash; com essa técnica, um projeto Xamarin. Android é usado para criar C# wrappers em todos os tipos de Java. Um aplicativo Xamarin. Android pode referenciar os C# wrappers criados por esse projeto e, em seguida, usar o arquivo `.jar`. 

- A **interface nativa do java** &ndash; a *interface* *nativa do Java* (JNI) é uma estrutura que permite que código não Java ( C#como C++ ou) chame ou seja chamado pelo código Java em execução dentro de uma JVM. 

- **Porta o código** &ndash; esse método envolve pegar o código-fonte Java e, em seguida, C#convertê-lo em. Isso pode ser feito manualmente ou usando uma ferramenta automatizada, como nitidez. 

No núcleo das duas primeiras técnicas está a *interface nativa do Java* (JNI). JNI é uma estrutura que permite que aplicativos não escritos em Java interajam com código Java em execução em um Máquina Virtual Java. O Xamarin. Android usa JNI para criar *associações* para C# o código. 

A primeira técnica é uma abordagem mais automatizada e declarativa para a associação de bibliotecas Java. Ele envolve o uso de Visual Studio para Mac ou de um tipo de projeto do Visual Studio que é fornecido pelo Xamarin. Android &ndash; a biblioteca de associações Java. Para criar essas associações com êxito, uma biblioteca de associações Java pode ainda exigir algumas modificações manuais, mas não tanto quanto uma abordagem JNI pura. Consulte [associando uma biblioteca Java](~/android/platform/binding-java-library/index.md) para obter mais informações sobre bibliotecas de associação Java. 

A segunda técnica, usando JNI, funciona em um nível muito mais baixo, mas pode fornecer controle e acesso mais precisos a métodos Java que normalmente não seriam acessíveis por meio de uma biblioteca de associação Java. 

A terceira técnica é radicalmente diferente das duas anteriores: portando o código de Java para C#. A portabilidade do código de um idioma para outro pode ser um processo muito trabalhoso, mas é possível reduzir esse esforço com a ajuda de uma ferramenta chamada *nitidez*. A nitidez é uma ferramenta de código aberto que é Java-toC# -converter. 

## <a name="summary"></a>Resumo

Este documento forneceu uma visão geral de alto nível de algumas das diferentes maneiras pelas quais as bibliotecas do Java podem ser reutilizadas em um aplicativo Xamarin. Android. Ele introduziu os conceitos de associações e wrappers chamáveis gerenciados e abordou opções para portar código C#Java para. 

## <a name="related-links"></a>Links relacionados

- [Arquitetura](~/android/internals/architecture.md)
- [Associação de uma biblioteca Java](~/android/platform/binding-java-library/index.md)
- [Trabalhando com JNI](~/android/platform/java-integration/working-with-jni.md)
- [Ajustar Nitidez](https://github.com/slluis/sharpen)
- [Interface nativa do Java](https://docs.oracle.com/javase/7/docs/technotes~/jni/index.html)
