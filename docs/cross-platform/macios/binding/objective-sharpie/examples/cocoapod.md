---
title: Exemplo do mundo real usando CocoaPods
ms.topic: article
ms.prod: xamarin
ms.assetid: 233B781D-5841-4250-9F63-0585231D2112
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: ae92b491e6186371f1fc1ead835f918a94f18f86
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="real-world-example-using-cocoapods"></a>Exemplo do mundo real usando CocoaPods


**Este exemplo usa o [AFNetworking CocoaPod](https://cocoapods.org/pods/AFNetworking).**

Novo na versão 3.0, o objetivo Sharpie oferece suporte à associação CocoaPods e tem até mesmo um comando de front-end (`sharpie pod`) para fazer o download, configurando e criando CocoaPods muito fácil. Você deve [faimilarize com CocoaPods](https://cocoapods.org) em geral antes de usar esse recurso.

O `sharpie pod` comando tem uma opção global e dois subcomandos:

```csharp
$ sharpie pod -help
usage: sharpie pod [OPTIONS] COMMAND [COMMAND_OPTIONS]

Pod Options:
  -d, -dir DIR     Use DIR as the CocoaPods binding directory,
                   defaulting to the current directory

Available Commands:
  init         Initialize a new Xamarin C# CocoaPods binding project
  bind         Bind an existing Xamarin C# CocoaPods project
```

O `init` subcomando também tem alguma ajuda úteis:

```csharp
$ sharpie pod init -help
usage: sharpie pod init [INIT_OPTIONS] TARGET_SDK POD_SPEC_NAMES

Init Options:
  -f, -force       Initialize a new Podfile and run actions against
                   it even if one already exists
```

Vários nomes de CocoaPod e subspec nomes podem ser fornecidos para `init`.

<pre>$ <b>sharpie pod init ios AFNetworking</b>
<span class="terminal-green">**</span> Setting up CocoaPods master repo ...
   (this may take a while the first time)
<span class="terminal-green">**</span> Searching for requested CocoaPods ...
<span class="terminal-green">**</span> Working directory:
<span class="terminal-green">**</span>   - Writing Podfile ...
<span class="terminal-green">**</span>   - Installing CocoaPods ...
<span class="terminal-green">**</span>     (running `<span class="terminal-blue">pod install --no-integrate --no-repo-update</span>`)
Analyzing dependencies
Downloading dependencies
Installing AFNetworking (2.6.0)
Generating Pods project
Sending stats
<span class="terminal-green">**</span> 🍻 Success! You can now use other `<span class="terminal-green">sharpie pod</span>`  commands.</pre>

Depois que seu CocoaPod foi configurado, agora você pode criar a associação:

<pre>$ <b>sharpie pod bind</b></pre>

Isso resultará no projeto CocoaPod Xcode sendo criado e, em seguida, avaliada e analisados por objetivo Sharpie. Um lote de saída do console será gerado, mas deve resultar na definição de associação no final:

<pre><em>(... lots of build output ...)</em>

<span class="terminal-blue">Parsing 19 header files...</span>

<span class="terminal-magenta">Binding...</span>
  <span class="terminal-magenta">[write]</span> ApiDefinitions.cs
  <span class="terminal-magenta">[write]</span> StructsAndEnums.cs

<span class="terminal-green">Done.</span></pre>

