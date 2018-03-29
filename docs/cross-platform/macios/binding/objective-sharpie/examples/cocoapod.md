---
title: Exemplo do mundo real usando CocoaPods
description: Este documento demonstra como usar Sharpie de objetivo de gerar automaticamente as definições de associação do c# de um CocoaPod.
ms.topic: article
ms.prod: xamarin
ms.assetid: 233B781D-5841-4250-9F63-0585231D2112
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/28/2018
ms.openlocfilehash: 24c796cb258578fdfc68c5b4aa1079d3c589da0f
ms.sourcegitcommit: 17a9cf246a4d33cfa232016992b308df540c8e4f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2018
---
# <a name="real-world-example-using-cocoapods"></a>Exemplo do mundo real usando CocoaPods

> [!NOTE]
> Este exemplo usa o [AFNetworking CocoaPod](https://cocoapods.org/pods/AFNetworking).

Novo na versão 3.0, o objetivo Sharpie oferece suporte à associação CocoaPods e mesmo inclui um comando (`sharpie pod`) para fazer o download, configurando e criando CocoaPods muito fácil. Você deve [se familiarizar com CocoaPods](https://cocoapods.org) em geral antes de usar esse recurso.

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

Depois que seu CocoaPod foi configurado, agora você pode criar a associação:

```bash
$ sharpie pod bind
```

Isso resultará no projeto CocoaPod Xcode sendo criado e, em seguida, avaliada e analisados por objetivo Sharpie. Um lote de saída do console será gerado, mas deve resultar na definição de associação no final:

```bash
(... lots of build output ...)

Parsing 19 header files...

Binding...
  [write] ApiDefinitions.cs
  [write] StructsAndEnums.cs

Done.
```

## <a name="next-steps"></a>Próximas etapas

Depois de gerar o **ApiDefinitions.cs** e **StructsAndEnums.cs** arquivos, veja a documentação a seguir para gerar um assembly para usar em seus aplicativos:

- [Visão geral da associação Objective-C](~/cross-platform/macios/binding/overview.md)
- [Associação de bibliotecas Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Passo a passo: Associação de uma biblioteca do iOS Objective-C](~/ios/platform/binding-objective-c/walkthrough.md)

