---
title: Comandos e ferramentas de Sharpie objetivo
description: Este documento fornece uma visão geral das ferramentas incluídas com o objetivo Sharpie e os argumentos de linha de comando para usar com eles.
ms.prod: xamarin
ms.assetid: A84E209B-8932-4CC1-BAD1-7FD51F798A97
author: asb3993
ms.author: amburns
ms.date: 10/05/2015
ms.openlocfilehash: 718b5104ddc4593d080b88b062c42d371d9e8e2e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61261162"
---
# <a name="objective-sharpie-tools--commands"></a>Comandos e ferramentas de Sharpie objetivo

_Visão geral das ferramentas incluídas com o objetivo Sharpie e os argumentos de linha de comando para usá-los._

<style type="text/css"> .terminal-blue { color: rgb(10,96,254); } .terminal-green { color: rgb(12,156,26); } .terminal-magenta { color: rgb(152,12,103); } </style>


Depois que o objetivo Sharpie é com êxito [instalados](~/cross-platform/macios/binding/objective-sharpie/get-started.md), abra um terminal e se familiarizar com as <em>comandos</em> objetivo Sharpie tem a oferecer:

<pre>$ <b>sharpie -help</b>
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
  docs               Open the Objective Sharpie online documentation</pre>

Objetivo Sharpie fornece as seguintes ferramentas:

|Ferramenta|Descrição|
|--- |--- |
|**xcode**|Fornece informações sobre a instalação atual do Xcode e as versões do iOS e Mac SDKs que estão disponíveis. Vamos usar essas informações posteriormente ao gerarmos nosso associações.|
|**pod**|Procura, configura, instala (em um diretório local) e associa o Objective-C [CocoaPod](https://cocoapods.org/) bibliotecas disponíveis do repositório principal Spec. Essa ferramenta avalia o CocoaPod instalado para deduzir automaticamente a entrada correta para passar para o `bind` abaixo da ferramenta. No 3.0!|
|**bind**|Analisa os arquivos de cabeçalho (`*.h`) na biblioteca de Objective-C em inicial [ApiDefinition.cs e StructsAndEnums.cs](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) arquivos.|
|**update**|Verifica se há versões mais recentes do objetivo Sharpie e baixa e inicia o instalador se houver uma disponível.|
|**verify-docs**|Mostra informações detalhadas sobre `[Verify]` atributos.|
|**docs**|Navega para este documento no seu navegador da web padrão.|

Para obter ajuda sobre uma ferramenta Sharpie objetivo específica, insira o nome da ferramenta e o `-help` opção. Por exemplo, `sharpie xcode -help` retorna a seguinte saída:

<pre>$ <b>sharpie xcode -help</b>
usage: sharpie xcode [OPTIONS]

Options:
  -h, -help        Show detailed help
  -v, -verbose     Be verbose with output

Xcode Options:
  -sdks            List all available Xcode SDKs. Pass -verbose for more details.</pre>

Para que possamos começar o processo de associação, precisamos obter informações sobre nossos SDKs instalados atuais digitando o seguinte comando no Terminal `sharpie xcode -sdks`. A saída pode ser diferente dependendo de quais versões do Xcode instalada. Objetivo Sharpie procura SDKs instalados em qualquer `Xcode*.app` sob o `/Applications` diretório:

<pre>$ <b>sharpie xcode -sdks</b>
<span class="terminal-blue">sdk:</span> appletvos9.0    <span class="terminal-green">arch:</span> arm64
<span class="terminal-blue">sdk:</span> iphoneos9.1     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> iphoneos9.0     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> iphoneos8.4     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> macosx10.11     <span class="terminal-green">arch:</span> x86_64  i386
<span class="terminal-blue">sdk:</span> macosx10.10     <span class="terminal-green">arch:</span> x86_64  i386
<span class="terminal-blue">sdk:</span> watchos2.0      <span class="terminal-green">arch:</span> armv7</pre>

Acima, podemos ver que temos a `iphoneos9.1` SDK instalado na máquina e tem `arm64` suporte de arquitetura. Vamos usar esse valor para todos os exemplos nesta seção. Com essas informações em vigor, estamos prontos para analisar um arquivos de cabeçalho de biblioteca do Objective-C em inicial `ApiDefinition.cs` e `StructsAndEnums.cs` para o projeto de associação.

## <a name="related-links"></a>Links relacionados

- [Xamarin University curso: Compilando uma biblioteca de associações do Objective-C](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin University curso: Criar uma biblioteca de associações do Objective-C com objetivo Sharpie](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)