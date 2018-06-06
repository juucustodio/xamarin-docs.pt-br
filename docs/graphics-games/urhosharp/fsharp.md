---
title: 'Programação UrhoSharp com F #'
description: 'Este documento descreve como criar um aplicativo de UrhoSharp simples hello world usando F # no Visual Studio para Mac.'
ms.prod: xamarin
ms.assetid: F976AB09-0697-4408-999A-633977FEFF64
author: charlespetzold
ms.author: chape
ms.date: 03/29/2017
ms.openlocfilehash: 64d69de70d6bc6f23b9907b498622b00c42b6f50
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783266"
---
# <a name="programming-urhosharp-with-f"></a>Programação UrhoSharp com F #

UrhoSharp pode ser programado com F # usando as mesmas bibliotecas e conceitos usados por programadores c#. O [UrhoSharp usando](~/graphics-games/urhosharp/using.md) artigo fornece uma visão geral do mecanismo de UrhoSharp e deve ser lida antes deste artigo.

Como muitas bibliotecas originadas do mundo do C++, muitas funções UrhoSharp retornam valores booleanos ou inteiros indicando êxito ou falha. Você deve usar `|> ignore` para ignorar esses valores.

O [programa de exemplo](https://github.com/xamarin/recipes/tree/master/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp) é um "Olá, mundo" para UrhoSharp do F #.

## <a name="creating-an-empty-project"></a>Criando um projeto vazio

Não existem modelos F # para UrhoSharp ainda disponível, portanto, para criar seu próprio projeto UrhoSharp você poderá iniciar com o [exemplo](https://github.com/xamarin/recipes/tree/master/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp) ou siga estas etapas:

1. No Visual Studio para Mac, crie um novo **solução**. Escolha **iOS > aplicativo > único aplicativo de exibição** e selecione **F #** como a linguagem de implementação. 
1. Excluir o **Main.storyboard** arquivo. Abra o **Info. plist** arquivo e, no **iPhone / iPod informações de implantação** painel, excluir o `Main` de cadeia de caracteres no **Interface principal** suspenso.
1. Excluir o **ViewController.fs** arquivo também.

## <a name="building-hello-world-in-urho"></a>Criando Hello World no Urho

Agora você está pronto para começar a definir as classes do jogo. No mínimo, você precisará definir uma subclasse de `Urho.Application` e substituir seu `Start` método. Para criar esse arquivo, clique com botão direito no projeto F #, escolha **adicionar novo arquivo...**  e adicione uma classe vazia do F # para seu projeto. O novo arquivo será adicionado ao final da lista de arquivos em seu projeto, mas você deve arrastá-la para que ela apareça *antes de* é usado em **AppDelegate.fs**.

1. Adicione uma referência para o pacote Urho NuGet.
1. Em um projeto existente do Urho, copie os diretórios (grandes) **CoreData /** e **dados /** em seu projeto **recursos /** directory. No seu projeto de F #, clique no **recursos** pasta e usar **adicionar / adicionar pasta existente** para adicionar esses arquivos ao seu projeto.

Estrutura do projeto agora deve parecer com:

![](fsharp-images/solutionpane.png "A estrutura do projeto deve ter aparência agora")

Definir sua classe recém criado como um subtipo de `Urho.Application` e substituir seu `Start` método:

```csharp
namespace HelloWorldUrho1

open Urho
open Urho.Gui
open Urho.iOS

type HelloWorld(o : ApplicationOptions) =
    inherit Urho.Application(o) 

override this.Start() = 
        let cache = this.ResourceCache
        let helloText = new Text()

        helloText.Value <- "Hello World from Urho3D, Mono, and F#"
        helloText.HorizontalAlignment <- HorizontalAlignment.Center
        helloText.VerticalAlignment <- VerticalAlignment.Center

        helloText.SetColor (new Color(0.f, 1.f, 0.f))
        let f = cache.GetFont("Fonts/Anonymous Pro.ttf")

        helloText.SetFont(f, 30) |> ignore
                  
        this.UI.Root.AddChild(helloText)
            
```

O código é muito simples. Ele usa o `Urho.Gui.Text` classe para exibir uma cadeia de caracteres centralizado com um determinado tamanho de fonte e cor. 

Antes de executar esse código, no entanto, UrhoSharp deve ser inicializado. 

Abra o arquivo AppDelegate.fs e modifique o `FinishedLaunching` método da seguinte maneira:

```csharp
namespace HelloWorldUrho1

open System
open UIKit
open Foundation
open Urho
open Urho.iOS

[<Register ("AppDelegate")>]
type AppDelegate () =
    inherit UIApplicationDelegate ()

    override this.FinishedLaunching (app, options) =
        let o = ApplicationOptions.Default
     
        let g = new HelloWorld(o)
        g.Run() |> ignore
       
        true
```

O `ApplicationOptions.Default` fornece as opções padrão para um aplicativo de modo paisagem. Passá-los `ApplicationOptions` para o construtor padrão para o `Application` subclasse (Observe que, quando você definiu o `HelloWorld` classe, a linha `inherit Application(o)` chama o construtor de classe base). 

O `Run` método de sua `Application` inicia o programa. Ele é definido como retornar um `int`, que pode ser transportado para `ignore`. 

O programa resultante deve parecer com:

![](fsharp-images/helloworldfsharp.png "O programa resultante deve ter aparência")








## <a name="related-links"></a>Links relacionados

- [Procure no GitHub (exemplo)](https://github.com/xamarinhttps://developer.xamarin.com/recipes/tree/master/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp)
