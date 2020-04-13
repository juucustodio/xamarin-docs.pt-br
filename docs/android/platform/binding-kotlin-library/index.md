---
title: Vincular bibliotecas Android Kotlin
description: Este documento descreve como criar vinculações C# ao código Kotlin, tornando possível consumir bibliotecas nativas em um aplicativo Xamarin.Android.
ms.prod: xamarin
ms.assetid: AB03A6C4-5A5A-4EAD-AD51-D887B20A3551
ms.technology: xamarin-android
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: ec7d154b0d7fcb055bd398089e142fe8b1d9f60e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292471"
---
# <a name="bind-android-kotlin-libraries"></a>Vincular bibliotecas Android Kotlin

A plataforma Android, juntamente com suas línguas nativas e ferramentas, está em constante evolução e há muitas bibliotecas de terceiros que foram desenvolvidas usando as ofertas mais recentes. Maximizar o código e a reutilização de componentes é um dos principais objetivos do desenvolvimento entre plataformas. A capacidade de reutilizar componentes construídos com o Kotlin tornou-se cada vez mais importante para os desenvolvedores da Xamarin à medida que sua popularidade entre os desenvolvedores continua a crescer. Você já pode estar familiarizado com o processo de vinculação de bibliotecas [Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/) regulares. Agora está disponível documentação adicional descrevendo o processo de Vinculação de [uma Biblioteca Kotlin,](walkthrough.md)de modo que eles são consumíveis por um aplicativo Xamarin da mesma maneira. O objetivo deste documento é descrever uma abordagem de alto nível para criar uma Ligação Kotlin para Xamarin.

## <a name="high-level-approach"></a>Abordagem de alto nível

Com o Xamarin, você pode vincular qualquer biblioteca nativa de terceiros para ser consumível por um aplicativo Xamarin. Kotlin é a nova língua e criar vinculações para bibliotecas construídas com essa linguagem requer alguns passos adicionais e ferramentas. Esta abordagem envolve os quatro passos a seguir:

1. Construa a biblioteca nativa
1. Prepare os metadados xamarin, que permite que as ferramentas Xamarin gerem classes C#
1. Construa uma Biblioteca de Vinculação Xamarin usando a biblioteca nativa e os metadados
1. Consumir a Biblioteca De Ligação Xamarin em um aplicativo Xamarin

As seções a seguir descrevem essas etapas com detalhes adicionais.

### <a name="build-the-native-library"></a>Construa a biblioteca nativa

O primeiro passo é obter uma biblioteca Kotlin nativa (pacote AAR, que é um arquivo Android). Você pode solicitá-lo diretamente de um fornecedor ou construí-lo você mesmo.

### <a name="prepare-the-xamarin-metadata"></a>Prepare os metadados de Xamarin

O segundo passo é preparar o arquivo de transformação de metadados, que será usado pelas ferramentas Xamarin para gerar as respectivas classes C#. Na melhor das hipóteses, este arquivo pode estar vazio onde todas as classes são descobertas e geradas pelas ferramentas Xamarin. Em alguns casos, a transformação de metadados deve ser aplicada para gerar código C# correto e/ou desejado. Em muitos casos, um desmontador AAR, como [o Java Decompiler (JD),](http://java-decompiler.github.io/)deve ser usado para identificar dependências ocultas e classes indesejadas que você deseja excluir da lista final de classes C# a serem geradas. Os metadados finais devem representar a interface pública na qual o aplicativo Xamarin.Android de referência interagirá.

### <a name="build-a-xamarinandroid-binding-library"></a>Construa uma biblioteca de vinculação Xamarin.Android

O terceiro passo é criar um projeto especial - uma Biblioteca De Vinculação Xamarin.Android. Ele inclui as bibliotecas Kotlin como referências nativas e a transformação de metadados definida na etapa anterior. No momento da escrita, um projeto separado da Biblioteca de Vinculação do Android é necessário para cada pacote AAR que está sendo referenciado. A Biblioteca de Vinculação deve adicionar o pacote [Xamarin.Kotlin.StdLib](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/) para suportar a Biblioteca Padrão Kotlin.

### <a name="consume-the-xamarin-binding-library"></a>Consumir a biblioteca de ligação Xamarin

O quarto e o passo final é referenciar a biblioteca de vinculação em um aplicativo Xamarin.Android. Adicionar uma referência à Biblioteca de Vinculação Xamarin.Android permite que seu aplicativo Xamarin use as classes Kotlin expostas dentro desse pacote.

## <a name="walkthrough"></a>Passo a passo

A abordagem acima descreve as etapas de alto nível necessárias para criar uma Ligação Kotlin para Xamarin. Existem muitas etapas de nível inferior envolvidas e mais detalhes a serem considerados ao preparar essas vinculações na prática, incluindo a adaptação às mudanças nas ferramentas e idiomas nativos. A intenção é ajudá-lo a obter uma compreensão mais profunda desse conceito e das etapas de alto nível envolvidas neste processo. Para obter um guia detalhado passo a passo, consulte a documentação [do Passo a Passo de Ligação Xamarin Kotlin.](walkthrough.md)

## <a name="related-links"></a>Links relacionados

- [Estúdio Android](https://developer.android.com/studio)
- [Instalação de Gradle](https://gradle.org/install/)
- [Visual Studio para Mac](https://visualstudio.microsoft.com/downloads)
- [Descompilador Java](http://java-decompiler.github.io/)
- [Biblioteca BubblePicker Kotlin](https://github.com/igalata/Bubble-Picker)
- [Biblioteca Java vinculante](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)
- [Xpath](https://www.w3.org/TR/xpath/)
- [Metadados de vinculação java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata)
- [Xamarin.Kotlin.StdLib NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)
- [Repositório de projetos de amostra](https://github.com/xamcat/xamarin-binding-kotlin-framework)
