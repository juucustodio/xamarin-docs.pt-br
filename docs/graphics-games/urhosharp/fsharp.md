---
title: Programação de UrhoSharp com F#
description: 'Este documento descreve como criar um aplicativo Hello World UrhoSharp simples usando F # no Visual Studio para Mac.'
ms.prod: xamarin
ms.assetid: F976AB09-0697-4408-999A-633977FEFF64
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: af9619ace957a47282cbf9fdefea4e81e7eace13
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86940004"
---
# <a name="programming-urhosharp-with-f"></a>Programação UrhoSharp com F\#

UrhoSharp pode ser programado com F # usando as mesmas bibliotecas e conceitos usados por programadores de C#. O artigo [usando UrhoSharp](~/graphics-games/urhosharp/using.md) fornece uma visão geral do mecanismo de UrhoSharp e deve ser lido antes deste artigo.

Como muitas bibliotecas originadas no mundo C++, muitas funções UrhoSharp retornam boolianos ou inteiros indicando êxito ou falha. Você deve usar `|> ignore` para ignorar esses valores.

O [programa de exemplo](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp) é um "Olá, mundo" para UrhoSharp da F #.

## <a name="creating-an-empty-project"></a>Criando um projeto vazio

Não há modelos F # para UrhoSharp ainda disponíveis, portanto, para criar seu próprio projeto UrhoSharp, você pode começar com o [exemplo](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp) ou seguir estas etapas:

1. Em Visual Studio para Mac, crie uma nova **solução**. Escolha aplicativo **iOS > > aplicativo de exibição única** e selecione **F #** como linguagem de implementação. 
1. Exclua o arquivo **Main. Storyboard** . Abra o arquivo **info. plist** e, no painel de **informações de implantação do iPhone/iPod** , exclua a cadeia de `Main` caracteres no menu suspenso da **interface principal** .
1. Exclua o arquivo **ViewController. FS** também.

## <a name="building-hello-world-in-urho"></a>Criando Olá, Mundo em Urho

Agora você está pronto para começar a definir as classes do jogo. No mínimo, será necessário definir uma subclasse de `Urho.Application` e substituir seu `Start` método. Para criar esse arquivo, clique com o botão direito do mouse no projeto F #, escolha **Adicionar novo arquivo...** e adicione uma classe F # vazia ao seu projeto. O novo arquivo será adicionado ao final da lista de arquivos em seu projeto, mas você deve arrastá-lo para que ele apareça *antes* de ser usado em **AppDelegate. FS**.

1. Adicione uma referência ao pacote NuGet Urho.
1. Em um projeto Urho existente, copie os diretórios (grandes) **CoreData/** e **Data/** para o diretório **/recursos** do projeto. No projeto F #, clique com o botão direito do mouse na pasta **recursos** e use **Adicionar/Adicionar pasta existente** para adicionar todos esses arquivos ao seu projeto.

A estrutura do projeto agora deve ser semelhante a:

![A estrutura do projeto agora deve ser parecida com](fsharp-images/solutionpane.png)

Defina a sua classe recém-criada como um subtipo de `Urho.Application` e substitua seu `Start` método:

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

O código é muito simples. Ele usa a `Urho.Gui.Text` classe para exibir uma cadeia de caracteres alinhada ao centro com uma determinada fonte e tamanho de cor. 

No entanto, antes que esse código possa ser executado, UrhoSharp deve ser inicializado. 

Abra o arquivo AppDelegate. FS e modifique o `FinishedLaunching` método da seguinte maneira:

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

O `ApplicationOptions.Default` fornece as opções padrão para um aplicativo de modo paisagem. Passe-os `ApplicationOptions` para o construtor padrão para sua `Application` subclasse (Observe que, quando você definiu a `HelloWorld` classe, a linha `inherit Application(o)` chama o construtor de classe base).

O `Run` método de seu `Application` inicia o programa. Ele é definido como retornando um `int` , que pode ser canalizado para `ignore` .

O programa resultante deve ser semelhante a esta captura de tela:

![Captura de tela do programa resultante](fsharp-images/helloworldfsharp.png)

## <a name="related-links"></a>Links Relacionados

- [Procurar no GitHub (exemplo)](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp)
