---
title: Ferramentas e comandos
description: "Visão geral das ferramentas incluídas com o objetivo Sharpie e os argumentos de linha de comando para usá-los."
ms.topic: article
ms.prod: xamarin
ms.assetid: A84E209B-8932-4CC1-BAD1-7FD51F798A97
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 10/05/2015
ms.openlocfilehash: 0d6e953a6a45b78d470c7ff73e1d6faa7444a683
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
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

<table>
  <thead>
    <tr><td>Ferramenta</td><td>Descrição</td>
  </thead>
  <tbody>
    <tr><td><b>xcode</b></td><td>Fornece informações sobre a instalação atual do Xcode e as versões do iOS e Mac SDKs que estão disponíveis. Usaremos essas informações posteriormente quando geramos nosso associações.</td></tr>
    <tr><td><b>pod</b></td><td>Procura, configura, instala (em um diretório local) e associa Objective-C <a href="https://cocoapods.org">CocoaPod</a> bibliotecas disponíveis do repositório especificações principal. Essa ferramenta avalia a CocoaPod instalado para deduzir automaticamente a entrada correta para passar para o <code>bind</code> ferramenta abaixo. <em><strong>No 3.0!</strong></em></td></tr>
    <tr><td><b>bind</b></td><td>Analisa os arquivos de cabeçalho (<code>*.h</code>) na biblioteca do Objective-C para o <a href="~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md">inicial <i>ApiDefinition.cs</i> e <i>StructsAndEnums.cs</i> arquivos</a>.</td></tr>
    <tr><td><b>update</b></td><td>Verifica se há versões mais recentes do objetivo Sharpie e downloads e inicia o instalador, se houver um disponível.</td></tr>
    <tr><td><b>verify-docs</b></td><td>Mostra informações detalhadas sobre <code>[Verify]</code> atributos.</td></tr>
    <tr><td><b>docs</b></td><td>Navega para o documento em um navegador da web padrão.</td></tr>
  </tbody>
</table>

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

