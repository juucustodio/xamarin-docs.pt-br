---
title: Associar bibliotecas Kotlin do Android
description: Este documento descreve como criar C# associações para o código Kotlin, possibilitando o consumo de bibliotecas nativas em um aplicativo Xamarin. Android.
ms.prod: xamarin
ms.assetid: AB03A6C4-5A5A-4EAD-AD51-D887B20A3551
ms.technology: xamarin-android
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: ec7d154b0d7fcb055bd398089e142fe8b1d9f60e
ms.sourcegitcommit: b751605179bef8eee2df92cb484011a7dceb6fda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2020
ms.locfileid: "78292471"
---
# <a name="bind-android-kotlin-libraries"></a>Associar bibliotecas Kotlin do Android

A plataforma Android, juntamente com suas linguagens e ferramentas nativas, está constantemente evoluindo e há muitas bibliotecas de terceiros que foram desenvolvidas usando as ofertas mais recentes. Maximizar o código e a reutilização de componentes é um dos principais objetivos do desenvolvimento de várias plataformas. A capacidade de reutilizar os componentes criados com Kotlin tornou-se cada vez mais importante para os desenvolvedores do Xamarin, pois sua popularidade entre os desenvolvedores continua crescendo. Talvez você já esteja familiarizado com o processo de vinculação de bibliotecas [Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/) regulares. A documentação adicional agora está disponível descrevendo o processo de [vinculação de uma biblioteca Kotlin](walkthrough.md), para que elas sejam consumíveis por um aplicativo Xamarin da mesma maneira. A finalidade deste documento é descrever uma abordagem de alto nível para criar uma associação Kotlin para o Xamarin.

## <a name="high-level-approach"></a>Abordagem de alto nível

Com o Xamarin, você pode associar qualquer biblioteca nativa de terceiros a ser consumível por um aplicativo Xamarin. Kotlin é a nova linguagem e a criação de associação para bibliotecas criadas com esse idioma requer algumas etapas e ferramentas adicionais. Essa abordagem envolve as quatro etapas a seguir:

1. Criar a biblioteca nativa
1. Preparar os metadados do Xamarin, que permitem que as ferramentas do C# xamarin gerem classes
1. Criar uma biblioteca de associação do Xamarin usando a biblioteca nativa e os metadados
1. Consumir a biblioteca de associação do Xamarin em um aplicativo Xamarin

As seções a seguir descrevem essas etapas com detalhes adicionais.

### <a name="build-the-native-library"></a>Criar a biblioteca nativa

A primeira etapa é obter uma biblioteca Kotlin nativa (pacote AAR, que é um arquivo Android). Você pode solicitá-lo diretamente de um fornecedor ou compilá-lo por conta própria.

### <a name="prepare-the-xamarin-metadata"></a>Preparar os metadados do Xamarin

A segunda etapa é preparar o arquivo de transformação de metadados, que será usado pelas ferramentas do Xamarin para gerar as respectivas C# classes. No cenário de melhor caso, esse arquivo pode estar vazio onde todas as classes são descobertas e geradas pelas ferramentas do Xamarin. Em alguns casos, a transformação de metadados deve ser aplicada para gerar o código correto C# e/ou desejado. Em muitos casos, um desmontador AAR, como o [Java decompilador (JD)](http://java-decompiler.github.io/), deve ser usado para identificar dependências ocultas e classes indesejadas que você deseja excluir da lista C# final de classes a serem geradas. Os metadados finais devem representar a interface pública na qual o aplicativo Xamarin. Android de referência irá interagir com o.

### <a name="build-a-xamarinandroid-binding-library"></a>Criar uma biblioteca de associação do Xamarin. Android

A terceira etapa é criar um projeto especial – uma biblioteca de associação do Xamarin. Android. Ele inclui as bibliotecas Kotlin como referências nativas e a transformação de metadados definida na etapa anterior. No momento da gravação, um projeto separado de biblioteca de associação do Android é necessário para cada pacote AAR que está sendo referenciado. A biblioteca de associação deve adicionar o pacote [Xamarin. Kotlin. StdLib](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/) para dar suporte à biblioteca padrão do Kotlin.

### <a name="consume-the-xamarin-binding-library"></a>Consumir a biblioteca de associação do Xamarin

A quarta e a etapa final é fazer referência à biblioteca de associação em um aplicativo Xamarin. Android. Adicionar uma referência à biblioteca de associação do Xamarin. Android permite que seu aplicativo Xamarin use as classes Kotlin expostas de dentro desse pacote.

## <a name="walkthrough"></a>Passo a passo

A abordagem acima descreve as etapas de alto nível necessárias para criar uma associação Kotlin para o Xamarin. Há muitas etapas de nível inferior envolvidas e mais detalhes a serem considerados ao preparar essas associações em prática, incluindo a adaptação a alterações nas ferramentas e linguagens nativas. A intenção é ajudá-lo a obter uma compreensão mais profunda desse conceito e das etapas de alto nível envolvidas nesse processo. Para obter um guia passo a passo detalhado, consulte a documentação passo a passos de [ligação do Xamarin Kotlin](walkthrough.md) .

## <a name="related-links"></a>Links relacionados

- [Android Studio](https://developer.android.com/studio)
- [Instalação do gradle](https://gradle.org/install/)
- [Visual Studio para Mac](https://visualstudio.microsoft.com/downloads)
- [Descompilador de Java](http://java-decompiler.github.io/)
- [Biblioteca BubblePicker Kotlin](https://github.com/igalata/Bubble-Picker)
- [Biblioteca Java de associação](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)
- [XPath](https://www.w3.org/TR/xpath/)
- [Metadados de associação Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata)
- [Xamarin. Kotlin. StdLib NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)
- [Repositório de projetos de exemplo](https://github.com/xamcat/xamarin-binding-kotlin-framework)
