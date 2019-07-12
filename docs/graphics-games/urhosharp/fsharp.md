---
title: Programação de UrhoSharp comF#
description: Este documento descreve como criar um simples hello world UrhoSharp usando aplicativo F# no Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: F976AB09-0697-4408-999A-633977FEFF64
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 6269a7f2fa097136f492657d0ba7c6a1f056c38c
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832324"
---
# <a name="programming-urhosharp-with-f"></a>Programação de UrhoSharp comF#

UrhoSharp pode ser programado com o F# usando as mesmas bibliotecas e conceitos usados pelo C# os programadores. O [UrhoSharp usando](~/graphics-games/urhosharp/using.md) artigo fornece uma visão geral do mecanismo de UrhoSharp e devem ser lidos antes neste artigo.

Como em muitas bibliotecas que tenham sido originados no mundo do C++, muitas funções de UrhoSharp retornam boolianos ou inteiros indicando êxito ou falha. Você deve usar `|> ignore` para ignorar esses valores.

O [programa de exemplo](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp) é um "Hello World" para UrhoSharp do F#.

## <a name="creating-an-empty-project"></a>Criando um projeto vazio

Há nenhum F# modelos para UrhoSharp ainda disponível, portanto, para criar seu próprio projeto UrhoSharp poderá iniciar com o [amostra](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp) ou siga estas etapas:

1. No Visual Studio para Mac, crie um novo **solução**. Escolher **iOS > aplicativo > aplicativo de exibição única** e selecione **F#** como a linguagem de implementação. 
1. Excluir o **Main. Storyboard** arquivo. Abra o **Info. plist** arquivo e, na **iPhone / iPod informações de implantação** painel, excluir o `Main` de cadeia de caracteres no **Interface principal** lista suspensa.
1. Excluir o **ViewController.fs** também o arquivo.

## <a name="building-hello-world-in-urho"></a>Criando o Hello World no Urho

Agora você está pronto para começar a definir as classes do seu jogo. No mínimo, você precisará definir uma subclasse de `Urho.Application` e substituir seu `Start` método. Para criar esse arquivo, clique com botão direito no seu F# do projeto, escolha **adicionar novo arquivo...**  e adicione um vazio F# classe ao seu projeto. O novo arquivo será adicionado ao final da lista de arquivos em seu projeto, mas você deve arrastá-lo para que ele apareça *antes de* é usado em **AppDelegate.fs**.

1. Adicione uma referência ao pacote Urho NuGet.
1. De um projeto existente do Urho, copie os diretórios (grandes) **CoreData /** e **dados /** em seu projeto **recursos /** directory. No seu F# do projeto, clique com botão direito no **recursos** pasta e use **adicionar / adicionar pasta existente** para adicionar todos esses arquivos ao seu projeto.

A estrutura do projeto agora deve ser semelhante:

![](fsharp-images/solutionpane.png "A estrutura do projeto deve agora parecer como")

Definir sua classe recém-criado como um subtipo de `Urho.Application` e substituir seu `Start` método:

```fsharp
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

```fsharp
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

O `ApplicationOptions.Default` fornece as opções padrão para um aplicativo no modo paisagem. Passá-los `ApplicationOptions` para o construtor padrão para seu `Application` subclasse (Observe que, quando você definiu o `HelloWorld` classe, a linha `inherit Application(o)` chama o construtor de classe base).

O `Run` método de sua `Application` inicia o programa. Ele é definido como retornando um `int`, que pode ser transferido para `ignore`.

O programa resultante deve ser semelhante esta captura de tela:

![Captura de tela do programa resultante](fsharp-images/helloworldfsharp.png)

## <a name="related-links"></a>Links relacionados

- [Procurar no GitHub (amostra)](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp)
