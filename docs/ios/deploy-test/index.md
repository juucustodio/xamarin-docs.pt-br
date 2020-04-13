---
title: Implantando e testando aplicativos Xamarin.iOS
description: Este documento tem links para vários guias que descrevem tópicos relacionados à implantação e teste de um aplicativo Xamarin.iOS. Por exemplo, distribuição de aplicativos, arquivos .ipa, provisionamento, implantação sem fio, TestFlight e depuração.
ms.prod: xamarin
ms.assetid: 2DBF3BF9-79E7-4E24-AF26-E34C972B0169
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: f3f5e27e97b7b62ade66ea2dc50a79ac03d51f90
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73026463"
---
# <a name="deploying-and-testing-xamarinios-apps"></a>Implantando e testando aplicativos Xamarin.iOS

Esta seção aborda tópicos usados para testar um aplicativo, bem como para distribuí-lo. Os tópicos aqui incluem coisas como ferramentas usadas para depuração, implantação em testadores e como publicar um aplicativo na App Store.

## <a name="app-distribution"></a>[Distribuição de aplicativos](~/ios/deploy-test/app-distribution/index.md)

Este artigo mostra como configurar, compilar e publicar um aplicativo Xamarin.iOS para distribuição através de várias maneiras diferentes, incluindo:

- [Distribuição da App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [Distribuição interna (Empresa)](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Distribuição Ad Hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)

## <a name="ipa-deployment"></a>[Implantação de IPA](~/ios/deploy-test/app-distribution/ipa-support.md)

As Implantações da Empresa e Ad Hoc permitem aos desenvolvedores criar pacotes que podem ser distribuídos para teste ou para usuários internos da empresa. Este documento explica como criar um IPA que pode ser sincronizado com um dispositivo iOS usando o iTunes.

## <a name="provisioning"></a>[Provisionamento](provisioning/index.md)

Este conjunto de guias abrange a assinatura de código e princípios básicos de provisionamento, por exemplo, como trabalhar com listas de propriedades e como configurar um aplicativo para serviços de aplicativo. 

## <a name="wireless-deployment"></a>[Implantação sem fio](wireless-deployment.md)

 O Xcode 9 introduziu a opção de implantação em um dispositivo iOS ou Apple TV por meio de uma rede, em vez de exigir instalações físicas sempre que você quiser implantar e depurar o aplicativo. Esse recurso está atualmente na visualização.

## <a name="testflight"></a>[TestFlight](~/ios/deploy-test/testflight.md)

O TestFlight agora é de propriedade da Apple e é a principal maneira de fazer o teste beta de seus aplicativos Xamarin.iOS. Este artigo o orientará por todas as etapas do Processo de TestFlight – do upload do aplicativo até o trabalho com o iTunes Connect.

## <a name="debugging-in-xamarinios"></a>[Depuração no Xamarin.iOS](~/ios/deploy-test/debugging-in-xamarin-ios.md)

Os IDEs (ambientes de desenvolvimento integrado) do Visual Studio e do Visual Studio para Mac incluem suporte para a depuração de aplicativos Xamarin.iOS no simulador de iOS e em dispositivos iOS. Este artigo mostra como usar o depurador e também como configurar várias opções às quais ele oferece suporte.

## <a name="touchunit"></a>[Touch.Unit](~/ios/deploy-test/touch.unit.md)

Este documento descreve como criar testes de unidade para seus projetos do Xamarin.iOS.
Os testes de unidade com o Xamarin.iOS são feitos usando a estrutura Touch.Unit que inclui um executor de teste iOS, bem como uma versão modificada da estrutura [NUnitLite](http://www.nunitlite.com/) que fornece um conjunto familiar de APIs para gravar testes de unidade.

## <a name="using-instruments-to-detect-native-leaks-using-markheap"></a>[Uso do Instrumentos para detectar perdas nativas usando o MarkHeap](~/ios/deploy-test/using-instruments-to-detect-native-leaks-using-markheap.md)

Este artigo descreve como usar o Instrumentos em qualquer dispositivo iOS e qualquer aplicativo Xamarin.iOS. Ele também mostra como analisar aplicativos no simulador.

## <a name="walkthrough---using-apples-instrument-tool"></a>[Passo a passo – usando a ferramenta Instruments da Apple](~/ios/deploy-test/walkthrough-apples-instrument.md)

Este artigo explica como usar a ferramenta Instrumentos da Apple para diagnosticar problemas de memória em um aplicativo iOS compilado com o Xamarin. Demonstra como inicializar o Instrumentos, tirar instantâneos do heap e analisar o aumento da memória. Ele também mostra como usar o Instrumentos para exibir e identificar as linhas exatas do código que causam o problema de memória.

## <a name="linking-on-ios"></a>[Vinculação no iOS](linker.md)

Explica como funciona o vinculador para garantir o menor pacote de aplicativos possível e como modificar as configurações e o uso.

## <a name="xamarinios-performance"></a>[Desempenho xamarin.iOS](performance.md)

Há várias técnicas para aumentar o desempenho dos aplicativos criados com o Xamarin.iOS. Coletivamente, essas técnicas podem reduzir de forma considerável a quantidade de trabalho que está sendo executado por uma CPU e a quantidade de memória consumida por um aplicativo.

## <a name="mtouch"></a>[mtouch](mtouch.md)

Notas e informações sobre o mtouch.exe, a ferramenta de linha de comando que compila seu projeto em um aplicativo que pode ser usado em iOS.

## <a name="ios-build-mechanics"></a>[Mecânica de compilação do iOS](ios-build-mechanics.md)

Este guia explora como determinar o ritmo dos seus aplicativos e como usar métodos que podem ser empregados para compilações mais rápidas para todas as configurações de compilação.
