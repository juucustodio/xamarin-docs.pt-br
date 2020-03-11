---
title: Recursos de plataforma do Xamarin. Android
description: Os documentos nesta seção abrangem recursos específicos do Android. Aqui, você encontrará tópicos como usar fragmentos, trabalhar com mapas e encapsular dados com provedores de conteúdo.
ms.prod: xamarin
ms.assetid: DDE54082-6E2B-9ED9-05FB-D9C1D1B1258E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2018
ms.openlocfilehash: 3ad3661ff50ad7f4be95421de57dc5017392a59f
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "78292167"
---
# <a name="platform-features"></a>Recursos da plataforma

_Os documentos nesta seção abrangem recursos específicos do Android. Aqui, você encontrará tópicos como usar fragmentos, trabalhar com mapas e encapsular dados com provedores de conteúdo._

## <a name="android-beam"></a>[Beam do Android](~/android/platform/android-beam.md)

O feixe do Android é uma nova tecnologia NFC (comunicação a curta distância) no Android 4 que permite que os aplicativos compartilhem informações sobre o NFC quando estiverem próximos.

## <a name="working-with-files"></a>[Trabalhando com arquivos](~/android/platform/files/index.md)

Esta seção discute como acessar arquivos no Xamarin. Android.

## <a name="fingerprint-authentication"></a>[Autenticação de impressão digital](~/android/platform/fingerprint-authentication/index.md)

Esta seção discute como usar a autenticação de impressão digital, introduzida pela primeira vez no Android 6,0, em um aplicativo Xamarin. Android.

## <a name="firebase-job-dispatcher"></a>[Dispatcher de trabalho do Firebase](~/android/platform/firebase-job-dispatcher.md)

Este guia discute o Dispatcher de trabalhos do firebase e como usá-lo para simplificar a execução de trabalhos em segundo plano em um aplicativo Xamarin. Android.

## <a name="fragments"></a>[Fragmentos](~/android/platform/fragments/index.md)

O Android 3,0 introduziu fragmentos, mostrando como dar suporte a designs mais flexíveis para vários tamanhos de tela diferentes encontrados em telefones e tablets. Este artigo abordará como usar fragmentos para desenvolver aplicativos Xamarin. Android e também como dar suporte a fragmentos em dispositivos anteriores ao Android 3,0 (nível de API 11).

## <a name="app-linking"></a>[Vinculação de aplicativo](~/android/platform/app-linking.md)

Este guia discutirá como o Android 6,0 dá suporte à _vinculação de aplicativos_, uma técnica que permite que os aplicativos móveis respondam a URLs em sites. Ele discutirá como implementar a vinculação de aplicativos em um aplicativo Android 6,0 e como configurar um site para conceder permissões ao aplicativo móvel para lidar com links de aplicativo para o domínio.

## <a name="android-10"></a>[Android 10](~/android/platform/android-10.md)

Este artigo fornece uma descrição dos novos recursos do Android 10, explica como preparar o Xamarin. Android para desenvolvimento do Android 10 e fornece links para aplicativos de exemplo que ilustram como usar os recursos do Oreo do Android em aplicativos Xamarin. Android.

## <a name="android-9-pie"></a>[Pizza do Android 9](~/android/platform/pie.md)

Este artigo fornece uma descrição dos novos recursos da pizza do Android, explica como preparar o Xamarin. Android para desenvolvimento de pizza do Android e fornece um aplicativo de exemplo que ilustra como usar os novos recursos de recorte e notificação de pizza do Android no Aplicativos Xamarin. Android.

## <a name="android-8-oreo"></a>[Android 8 Oreo](~/android/platform/oreo.md)

Este artigo fornece uma descrição dos novos recursos do Android Oreo, explica como preparar o Xamarin. Android para desenvolvimento Oreo do Android e fornece links para aplicativos de exemplo que ilustram como usar recursos Oreo do Android em aplicativos Xamarin. Android.

## <a name="android-7-nougat"></a>[Android 7 Nougat](~/android/platform/nougat.md)

Este artigo fornece uma visão geral de alto nível dos novos recursos introduzidos no Android 7,0 nougat.

## <a name="android-6-marshmallow"></a>[Android 6 Marshmallow](~/android/platform/marshmallow.md)

Este artigo fornece uma visão geral de alto nível dos novos recursos introduzidos no Android 6,0 marshmallow.

## <a name="android-5-lollipop"></a>[Android 5 Lollipop](~/android/platform/lollipop.md)

Este guia fornece uma visão geral dos novos recursos de pirulito do Android 5,0, como o tema material, CardView, RecyclerView e notificações de cabeça e links para artigos detalhados que ajudam a usar esses novos recursos em seu aplicativo.

## <a name="android-44-kitkat"></a>[Android 4.4 KitKat](~/android/platform/kitkat.md)

O Android 4,4 (KitKat) vem carregado com um infinidade de recursos para usuários e desenvolvedores. Este guia destaca vários desses recursos e fornece exemplos de código e detalhes de implementação para ajudá-lo a tirar o máximo proveito do KitKat.

## <a name="android-41-jelly-bean"></a>[Android 4.1 Jelly Bean](~/android/platform/jelly-bean.md)

Este documento fornecerá uma visão geral de alto nível dos novos recursos para desenvolvedores que foram introduzidos no Android 4,1. Esses recursos incluem: notificações aprimoradas, atualizações no feixe do Android para compartilhar arquivos grandes, atualizações em multimídia, descoberta de rede ponto a ponto, animações, novas permissões.

## <a name="android-40-ice-cream-sandwich"></a>[Android 4.0 Ice Cream Sandwich](~/android/platform/ice-cream-sandwich.md)

Este artigo descreve vários dos novos recursos disponíveis para desenvolvedores de aplicativos com a *API do Android 4-Ice de sorvete*.
Ele abrange várias novas tecnologias de interface do usuário e, em seguida, examina uma variedade de novos recursos que o Android 4 oferece para o compartilhamento de dados entre aplicativos e entre dispositivos.

## <a name="working-with-the-android-manifest"></a>[Trabalhando com o manifesto do Android](android-manifest.md)

Este artigo apresenta o arquivo AndroidManifest. xml e como ele pode ser usado para controlar a funcionalidade e descrever os requisitos de um aplicativo mono para Android.

## <a name="introduction-to-content-providers"></a>[Introdução aos provedores de conteúdo](~/android/platform/content-providers/index.md)

Um ContentProvider encapsula um repositório de dados e fornece uma API para acessá-lo. O provedor existe como parte de um aplicativo Android que também fornece uma interface do usuário para exibir/gerenciar os dados. O principal benefício de usar um provedor de conteúdo é permitir que outros aplicativos acessem facilmente os dados encapsulados usando um objeto de cliente de provedor (chamado de ContentResolver retornem). Juntos, um provedor de conteúdo e um resolvedor de conteúdo oferecem uma API entre aplicativos consistente para acesso a dados que é simples de criar e consumir. Este documento mostra como acessar e criar o ContentProviders com o Xamarin. Android.

## <a name="maps-and-location"></a>[Mapas e local](~/android/platform/maps-and-location/index.md)

Esta seção discute como usar mapas e localização com o Xamarin. Android. Ele aborda tudo, desde o aproveitamento do aplicativo de mapas interno até o uso direto da [API do Google Maps para Android v2](https://developers.google.com/maps/documentation/android/) . Além disso, ele explica como usar uma única API para trabalhar com serviços de localização, que usam a triangulação de celular para permitir que um aplicativo obtenha correções de local, local de Wi-Fi e GPS.

## <a name="android-speech"></a>[Fala do Android](~/android/platform/speech.md)

Esta seção discute como usar o Conversão de Texto em Fala do Android e a fala para instalações de texto. Ele também aborda a instalação de pacotes de idiomas e a interpretação do texto falado no dispositivo.

## <a name="binding-a-java-library"></a>[Associação de uma biblioteca Java](binding-java-library/index.md)

Este guia explica como incorporar bibliotecas Java em aplicativos Xamarin. Android criando uma biblioteca de associações.

## <a name="bind-a-kotlin-library"></a>[Associar uma biblioteca do Kotlin](binding-kotlin-library/index.md)

Este guia explica como criar C# associações para o código Kotlin, possibilitando o consumo de bibliotecas nativas em um aplicativo Xamarin. Android.

## <a name="java-integration"></a>[Integração com Java](java-integration/index.md)

Este artigo fornece uma visão geral das maneiras como os desenvolvedores podem reutilizar componentes Java existentes em aplicativos Xamarin. Android.

## <a name="renderscript"></a>[Renderscript](renderscript.md)

Este guia discute o renderscript.
