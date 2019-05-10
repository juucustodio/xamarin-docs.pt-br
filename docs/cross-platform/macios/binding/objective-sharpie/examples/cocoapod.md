---
title: Exemplo do mundo real usando o CocoaPods
description: Este documento demonstra como usar o objetivo Sharpie para gerar automaticamente o C# definições de um CocoaPod de associação.
ms.prod: xamarin
ms.assetid: 233B781D-5841-4250-9F63-0585231D2112
author: asb3993
ms.author: amburns
ms.date: 03/28/2018
ms.openlocfilehash: 266f2bca1f4a96242b17080e60c9f43cb956a5fd
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64977876"
---
# <a name="real-world-example-using-cocoapods"></a>Exemplo do mundo real usando o CocoaPods

> [!NOTE]
> Este exemplo usa o [AFNetworking CocoaPod](https://cocoapods.org/pods/AFNetworking).

Novo na versão 3.0, objetivo Sharpie oferece suporte à associação CocoaPods e até mesmo inclui um comando (`sharpie pod`) para fazer o download, configurando e CocoaPods muito fácil de criar. Você deve [você se familiarizar com os CocoaPods](https://cocoapods.org) em geral antes de usar esse recurso.

## <a name="creating-a-binding-for-a-cocoapod"></a>Criando uma associação para um CocoaPod

O `sharpie pod` comando tem uma opção global e dois subcomandos:

```bash
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

```bash
$ sharpie pod init -help
usage: sharpie pod init [INIT_OPTIONS] TARGET_SDK POD_SPEC_NAMES

Init Options:
  -f, -force       Initialize a new Podfile and run actions against
                   it even if one already exists
```

Vários nomes de CocoaPod e subspec nomes podem ser fornecidos para `init`.

```bash
$ sharpie pod init ios AFNetworking
** Setting up CocoaPods master repo ...
   (this may take a while the first time)
** Searching for requested CocoaPods ...
** Working directory:
**   - Writing Podfile ...
**   - Installing CocoaPods ...
**     (running `pod install --no-integrate --no-repo-update`)
Analyzing dependencies
Downloading dependencies
Installing AFNetworking (2.6.0)
Generating Pods project
Sending stats
** 🍻 Success! You can now use other `sharpie podn`  commands.
```

Depois que o CocoaPod foi configurado, agora você pode criar a associação:

```bash
$ sharpie pod bind
```

Isso resultará no projeto CocoaPod Xcode que está sendo criado e, em seguida, avaliados e analisadas pelo Sharpie objetivo. Um lote de saída do console será gerado, mas deve resultar na definição de associação no final:

```bash
(... lots of build output ...)

Parsing 19 header files...

Binding...
  [write] ApiDefinitions.cs
  [write] StructsAndEnums.cs

Done.
```

## <a name="next-steps"></a>Próximas etapas

Depois de gerar o **ApiDefinitions.cs** e **StructsAndEnums.cs** arquivos, examine a documentação a seguir para gerar um assembly para usar em seus aplicativos:

- [Visão geral da associação de Objective-C](~/cross-platform/macios/binding/overview.md)
- [Associação de bibliotecas de Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Passo a passo: Associação de uma biblioteca do iOS Objective-C](~/ios/platform/binding-objective-c/walkthrough.md)
