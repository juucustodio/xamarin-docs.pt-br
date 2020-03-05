---
title: Associar bibliotecas Swift do iOS
description: Este documento descreve como criar C# associações para código Swift, possibilitando o consumo de bibliotecas nativas e CocoaPods em um aplicativo Xamarin. Ios.
ms.prod: xamarin
ms.assetid: 890EFCCA-A2A2-4561-88EA-30DE3041F61D
ms.technology: xamarin-ios
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: 9a683f31016a9db4271e3909e421f27ef83c2080
ms.sourcegitcommit: b751605179bef8eee2df92cb484011a7dceb6fda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2020
ms.locfileid: "78292258"
---
# <a name="bind-ios-swift-libraries"></a>Associar bibliotecas Swift do iOS

A plataforma iOS, juntamente com suas ferramentas e linguagens nativas, está constantemente evoluindo e há muitas bibliotecas de terceiros que foram desenvolvidas usando as ofertas mais recentes. Maximizar o código e a reutilização de componentes é um dos principais objetivos do desenvolvimento de várias plataformas. A capacidade de reutilizar os componentes criados com o Swift tornou-se cada vez mais importante para os desenvolvedores do Xamarin, pois sua popularidade entre os desenvolvedores continua crescendo. Talvez você já esteja familiarizado com o processo de vinculação de bibliotecas [Objective-C](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough) regulares. A documentação adicional agora está disponível, descrevendo o processo de [vinculação de uma estrutura Swift](walkthrough.md), para que eles sejam consumíveis por um aplicativo Xamarin da mesma maneira. A finalidade deste documento é descrever uma abordagem de alto nível para criar uma associação Swift para o Xamarin.

## <a name="high-level-approach"></a>Abordagem de alto nível

Com o Xamarin, você pode associar qualquer biblioteca nativa de terceiros a ser consumível por um aplicativo Xamarin. O Swift é a nova linguagem e a criação de associação para bibliotecas criadas com esse idioma requer algumas etapas e ferramentas adicionais. Essa abordagem envolve as quatro etapas a seguir:

1. Criando a biblioteca nativa
1. Preparando os metadados do Xamarin, que permitem que as ferramentas do C# xamarin gerem classes
1. Criando uma biblioteca de associação do Xamarin usando a biblioteca nativa e os metadados
1. Consumindo a biblioteca de associação do Xamarin em um aplicativo Xamarin

As seções a seguir descrevem essas etapas com detalhes adicionais.

### <a name="build-the-native-library"></a>Criar a biblioteca nativa

A primeira etapa é ter uma estrutura Swift nativa pronta com o cabeçalho Objective-C criado. Esse arquivo é um cabeçalho gerado automaticamente que expõe as classes, métodos e campos Swift desejados, tornando-os acessíveis para o Objective-C e, por fim C# , por meio de uma biblioteca de associação do Xamarin. Esse arquivo está localizado na estrutura sob o seguinte caminho: **\<FrameworkName >. Framework/Headers/\<FrameworkName >-Swift. h**. Se a interface exposta tiver todos os membros necessários, você poderá pular para a próxima etapa. Caso contrário, serão necessárias mais etapas para expor esses membros. A abordagem dependerá se você tem acesso ao código-fonte do Swift Framework:

- Se você tiver acesso ao código, poderá decorar os membros Swift necessários com o atributo `@objc` e aplicar algumas regras adicionais para permitir que as ferramentas de Build do Xcode saibam que esses membros devem ser expostos ao mundo Objective-C e ao cabeçalho.
- Se você não tiver o acesso ao código-fonte, será necessário criar um proxy Swift Framework, que encapsula a estrutura Swift original e define a interface pública exigida pelo seu aplicativo usando o atributo `@objc`.

### <a name="prepare-the-xamarin-metadata"></a>Preparar os metadados do Xamarin

A segunda etapa é preparar as interfaces de definição de API, que são usadas por um projeto de C# Associação para gerar classes. Essas definições podem ser criadas manualmente ou automaticamente pela ferramenta de [nitidez do objetivo](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/) e pelo arquivo de cabeçalho gerado automaticamente **\<FrameworkName >-Swift. h** . Depois que os metadados são gerados, eles devem ser verificados e validados manualmente.

### <a name="build-the-xamarinios-binding-library"></a>Compilar a biblioteca de associação do Xamarin. iOS

A terceira etapa é criar uma biblioteca de associação Project-Xamarin. iOS especial. Ele faz referência às estruturas e aos metadados preparados na etapa anterior junto com quaisquer dependências adicionais das quais a respectiva estrutura depende. Ele também manipula a vinculação das estruturas nativas referenciadas com o aplicativo Xamarin. iOS de consumo.

### <a name="consume-the-xamarin-binding-library"></a>Consumir a biblioteca de associação do Xamarin

A quarta e última etapa é fazer referência à biblioteca de associação em um aplicativo Xamarin. iOS. É suficiente habilitar o uso da biblioteca nativa em aplicativos Xamarin. iOS direcionados para iOS 12,2 e superior. Para os aplicativos direcionados a uma versão inferior, algumas etapas adicionais são necessárias:

- Adicione dependências de dylib Swift para suporte a tempo de execução. A partir do iOS 12,2 e do Swift 5,1, a linguagem tornou-se a ABI (interface binária de aplicativo) estável e compatível. É por isso que qualquer aplicativo destinado a uma versão inferior do iOS precisa incluir dependências de dylibs Swift usadas pela estrutura. Use o [pacote NuGet SwiftRuntimeSupport](https://www.nuget.org/packages/Xamarin.iOS.SwiftRuntimeSupport/) para incluir automaticamente as dependências de dylib necessárias no pacote de aplicativos resultante.
- Adicione a pasta **SwiftSupport** com dylibs assinado, que é validado pelo AppStore durante o carregamento do processo. O pacote deve ser assinado e distribuído para o AppStore Connect usando as ferramentas do Xcode; caso contrário, ele será rejeitado automaticamente.

## <a name="walkthrough"></a>Passo a passo

A abordagem acima descreve as etapas de alto nível necessárias para criar uma associação Swift para o Xamarin. Há muitas etapas de nível inferior envolvidas e mais detalhes a serem considerados ao preparar essas associações em prática, incluindo a adaptação a alterações nas ferramentas e linguagens nativas. A intenção é ajudá-lo a obter uma compreensão mais profunda desse conceito e das etapas de alto nível envolvidas nesse processo. Para obter um guia passo a passo detalhado, consulte a documentação de referência [rápida do Xamarin Swift](walkthrough.md) .

## <a name="related-links"></a>Links relacionados

- [Xcode](https://apps.apple.com/us/app/xcode/id497799835)
- [Visual Studio para Mac](https://visualstudio.microsoft.com/downloads)
- [Objective Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/)
- [Verificação de metadados de nitidez](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/platform/verify)
- [Estrutura de objetivo de associação-C](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough)
- [SDK do iOS Gigya (estrutura Swift)](https://developers.gigya.com/display/GD/Swift+SDK)
- [Swift 5,1 de estabilidade da ABI](https://swift.org/blog/swift-5-1-released/)
- [NuGet SwiftRuntimeSupport](https://www.nuget.org/packages/Xamarin.iOS.SwiftRuntimeSupport/)
- [Automação do Xamarin UITest](https://docs.microsoft.com/appcenter/test-cloud/uitest/)
- [Configuração do Xamarin. iOS UITest](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)
- [Test Cloud AppCenter](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)
- [Repositório de projetos de exemplo](https://github.com/xamcat/xamarin-binding-swift-framework)
