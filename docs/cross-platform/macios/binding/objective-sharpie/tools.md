---
title: Ferramentas de nitidez do objetivo & comandos
description: Este documento fornece uma visão geral das ferramentas incluídas com a nitidez objetiva e os argumentos de linha de comando a serem usados com eles.
ms.prod: xamarin
ms.assetid: A84E209B-8932-4CC1-BAD1-7FD51F798A97
author: asb3993
ms.author: amburns
ms.date: 10/05/2015
ms.openlocfilehash: ddfe0f99991808214a6006c9504d267179adf2ab
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521862"
---
# <a name="objective-sharpie-tools--commands"></a>Ferramentas de nitidez do objetivo & comandos

_Visão geral das ferramentas incluídas com a nitidez do objetivo e os argumentos de linha de comando para usá-las._

Depois que a nitidez do objetivo for [instalada](~/cross-platform/macios/binding/objective-sharpie/get-started.md)com êxito, abra um terminal e familiarize-se com os *comandos* que a nitidez do objetivo tem a oferecer:

```
$ sharpie -help
usage: sharpie [OPTIONS] TOOL [TOOL_OPTIONS]

Options:
  -h, -help                Show detailed help
  -v, -version             Show version information

Telemetry Options:
  -tlm-about               Show a detailed overview of what usage and binding
                             information will be submitted to Xamarin by
                             default when using Objective Sharpie.
  -tlm-do-not-submit       Do not submit any usage or binding information to
                             Xamarin. Run 'sharpie -tml-about' for more
                             information.
  -tlm-do-not-identify     Do not submit Xamarin account information when
                             submitting usage or binding information to Xamarin
                             for analysis. Binding attempts and usage data will
                             be submitted anonymously if this option is
                             specified.

Available Tools:
  xcode              Get information about Xcode installations and available SDKs.
  pod                Create a Xamarin C# binding to Objective-C CocoaPods
  bind               Create a Xamarin C# binding to Objective-C APIs
  update             Update to the latest release of Objective Sharpie
  verify-docs        Show cross reference documentation for [Verify] attributes
  docs               Open the Objective Sharpie online documentation
```

A nitidez do objetivo fornece as seguintes ferramentas:

|Ferramenta|Descrição|
|--- |--- |
|**xcode**|Fornece informações sobre a instalação atual do Xcode e as versões dos SDKs do iOS e Mac que estão disponíveis. Usaremos essas informações posteriormente quando gerarmos nossas associações.|
|**pod**|Procura, configura, instala (em um diretório local) e associa bibliotecas de [CocoaPod](https://cocoapods.org/) Objective-C disponíveis no repositório de especificações mestras. Essa ferramenta avalia o CocoaPod instalado para deduzir automaticamente a entrada correta para passar para a `bind` ferramenta abaixo. Novidade no 3,0!|
|**bind**|Analisa os arquivos de cabeçalho (`*.h`) na biblioteca Objective-C nos arquivos [ApiDefinition.cs e StructsAndEnums.cs](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) iniciais.|
|**update**|Verifica se há versões mais recentes de preversão e downloads de objetivo e inicia o instalador se houver um disponível.|
|**verify-docs**|Mostra informações detalhadas sobre `[Verify]` atributos.|
|**docs**|Navega para este documento no navegador da Web padrão.|

Para obter ajuda sobre uma ferramenta de nitidez de objetivo específica, insira o nome da ferramenta e a `-help` opção. Por exemplo, `sharpie xcode -help` retorna a seguinte saída:

```
$ sharpie xcode -help
usage: sharpie xcode [OPTIONS]

Options:
  -h, -help        Show detailed help
  -v, -verbose     Be verbose with output

Xcode Options:
  -sdks            List all available Xcode SDKs. Pass -verbose for more details.
```

Antes que possamos iniciar o processo de associação, precisamos obter informações sobre nossos SDKs instalados atuais digitando o seguinte comando no terminal `sharpie xcode -sdks`. A saída pode ser diferente dependendo de quais versões do Xcode você instalou. `Xcode*.app` A`/Applications` nitidez do objetivo procura SDKs instalados em qualquer um no diretório:

```
$ sharpie xcode -sdks
sdk: appletvos9.0    arch: arm64
sdk: iphoneos9.1     arch: arm64   armv7
sdk: iphoneos9.0     arch: arm64   armv7
sdk: iphoneos8.4     arch: arm64   armv7
sdk: macosx10.11     arch: x86_64  i386
sdk: macosx10.10     arch: x86_64  i386
sdk: watchos2.0      arch: armv7
```

A partir do acima, podemos ver que o `iphoneos9.1` SDK está instalado em nosso computador e tem `arm64` suporte à arquitetura. Usaremos esse valor para todos os exemplos nesta seção. Com essas informações em vigor, estamos prontos para analisar os arquivos de cabeçalho da biblioteca Objective-C na `ApiDefinition.cs` inicial `StructsAndEnums.cs` e no projeto de associação.
