---
title: Ferramentas e comandos
description: Visão geral das ferramentas incluídas com o objetivo Sharpie e os argumentos de linha de comando para usá-los.
ms.prod: xamarin
ms.assetid: A84E209B-8932-4CC1-BAD1-7FD51F798A97
author: asb3993
ms.author: amburns
ms.date: 10/05/2015
ms.openlocfilehash: 0e333ce7c336d13c8b55326a5d51a64092885dfd
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
---
# <a name="tools--commands"></a>Ferramentas e comandos

_Visão geral das ferramentas incluídas com o objetivo Sharpie e os argumentos de linha de comando para usá-los._

<style type="text/css"> azul .terminal {cor: rgb(10,96,254);} .terminal verde {cor: rgb(12,156,26);} .terminal magenta {cor: rgb(152,12,103);} </style>


Depois que o objetivo Sharpie com êxito é [instalado](~/cross-platform/macios/binding/objective-sharpie/get-started.md), abrir um terminal e familiarizar-se com o <em>comandos</em> objetivo Sharpie tem a oferecer:

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
|**xcode**|Fornece informações sobre a instalação atual do Xcode e as versões do iOS e Mac SDKs que estão disponíveis. Usaremos essas informações posteriormente quando geramos nosso associações.|
|**pod**|Procura, configura, instala (em um diretório local) e associa Objective-C [CocoaPod](https://cocoapods.org/) bibliotecas disponíveis do repositório especificações principal. Essa ferramenta avalia a CocoaPod instalado para deduzir automaticamente a entrada correta para passar para o `bind` ferramenta abaixo. No 3.0!|
|**bind**|Analisa os arquivos de cabeçalho (`*.h`) na biblioteca do Objective-C em inicial [ApiDefinition.cs e StructsAndEnums.cs](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) arquivos.|
|**update**|Verifica se há versões mais recentes do objetivo Sharpie e downloads e inicia o instalador, se houver um disponível.|
|**verify-docs**|Mostra informações detalhadas sobre `[Verify]` atributos.|
|**docs**|Navega para o documento em um navegador da web padrão.|

Para obter ajuda sobre uma ferramenta Sharpie objetivo específica, digite o nome da ferramenta e o `-help` opção. Por exemplo, `sharpie xcode -help` retorna a seguinte saída:

<pre>$ <b>sharpie xcode -help</b>
usage: sharpie xcode [OPTIONS]

Options:
  -h, -help        Show detailed help
  -v, -verbose     Be verbose with output

Xcode Options:
  -sdks            List all available Xcode SDKs. Pass -verbose for more details.</pre>

Para que possamos começar o processo de ligação, precisamos obter informações sobre nossos SDKs instalados atuais digitando o seguinte comando no Terminal `sharpie xcode -sdks`. A saída pode ser diferente dependendo de quais versões do Xcode que você instalou. Objetivo Sharpie procura SDKs instalados em qualquer `Xcode*.app` sob o `/Applications` diretório:

<pre>$ <b>sharpie xcode -sdks</b>
<span class="terminal-blue">sdk:</span> appletvos9.0    <span class="terminal-green">arch:</span> arm64
<span class="terminal-blue">sdk:</span> iphoneos9.1     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> iphoneos9.0     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> iphoneos8.4     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> macosx10.11     <span class="terminal-green">arch:</span> x86_64  i386
<span class="terminal-blue">sdk:</span> macosx10.10     <span class="terminal-green">arch:</span> x86_64  i386
<span class="terminal-blue">sdk:</span> watchos2.0      <span class="terminal-green">arch:</span> armv7</pre>

Acima, podemos ver que temos o `iphoneos9.1` SDK instalado na máquina e tem `arm64` suporte para a arquitetura. Vamos usar esse valor para todos os exemplos nesta seção. Com essas informações em vigor, você está pronto para analisar um arquivos de cabeçalho de biblioteca Objective-C em inicial `ApiDefinition.cs` e `StructsAndEnums.cs` para o projeto de associação.

