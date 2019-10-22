---
title: Parte 1 – Criando um monojogo de plataforma cruzada
description: Este tutorial mostra como criar um novo projeto para iOS e Android usando monogame. O resultado é uma solução de Visual Studio para Mac com um projeto de código compartilhado de plataforma cruzada, bem como um projeto para cada plataforma. Este projeto exibirá uma tela azul vazia quando executado.
ms.prod: xamarin
ms.assetid: FC69E69B-04D4-45DF-9BBF-2A6CDEAD9B2F
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: d72c428bb4b8c88365180c5c3c50b107eed2b21d
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "68978454"
---
# <a name="part-1--creating-a-cross-platform-monogame"></a>Parte 1 – Criando um monojogo de plataforma cruzada

_Este tutorial mostra como criar um novo projeto para iOS e Android usando monogame. O resultado é uma solução de Visual Studio para Mac com um projeto de código compartilhado de plataforma cruzada, bem como um projeto para cada plataforma. Este projeto exibirá uma tela azul vazia quando for executado._

O monogame permite o desenvolvimento de jogos de plataforma cruzada com grande parte da reutilização de código. Este guia de explicação abordará a configuração de uma solução que contém projetos para iOS e Android, bem como um projeto de código compartilhado para código de plataforma cruzada.

Ao concluir, o projeto tem a estrutura adequada para executar a lógica de atualização de jogos e a lógica de desenho de jogos em 30 quadros por segundo. Ele pode ser usado como o projeto base para qualquer projeto de monojogo. O projeto terá a seguinte aparência quando executado:

![Tela azul em branco](part1-images/image1.png)

## <a name="adding-monogame-to-visual-studio"></a>Adicionando monogames ao Visual Studio

> [!IMPORTANT]
> O monogames não é instalado por padrão no Visual Studio 2019 ou Visual Studio para Mac.
>
> Você deve baixar e instalar manualmente a versão mais recente do http://www.monogame.net/downloads/ executar o instalador. Talvez seja necessário reiniciar o Visual Studio para que os modelos sejam exibidos.
>
> A seção **desenvolvimento de jogos** deve aparecer no Gerenciador de **suplementos**.

Para habilitar o suplemento monogame para Visual Studio para Mac, selecione **Visual Studio para Mac**  >  Gerenciador de**suplementos...** . Para o Visual Studio 2019 no Windows, selecione **ferramentas**  > **Gerenciador de suplementos...** . Selecione a guia **Galeria** , expanda a categoria **desenvolvimento de jogos** e selecione **suplemento monogame**e clique em **instalar...** :

![Galeria de extensões de Visual Studio para Mac selecionando monojogo](part1-images/image2.png)

Uma vez instalado, os modelos de jogos serão exibidos no Visual Studio para Mac, como veremos na próxima seção.

## <a name="creating-a-new-solution"></a>Criando uma nova solução

Em Visual Studio para Mac selecione **arquivo > nova solução**. Na caixa de diálogo **novo projeto** , clique em **diversos**, role até a seção **geral** , selecione a opção **aplicativo móvel de monojogo universal** e clique em Avançar.

![Caixa de diálogo novo projeto criando um aplicativo de monojogo](part1-images/image3.png)

Nomeie o projeto WalkingGame e clique em criar:

![Caixa de diálogo novo projeto escolhendo um nome e um local](part1-images/image4.png)

Agora, nosso projeto será executado assim como qualquer outro projeto do iOS ou Android. O projeto deve ser executado para exibir um plano de fundo azul-centáurea:

![Segundo plano em branco do aplicativo azul](part1-images/image5.png)

## <a name="fixing-android-compile-errors"></a>Corrigindo erros de compilação do Android

A versão atual dos modelos de monojogo inclui alguns erros de sintaxe no arquivo de `Activity1.cs` do Android. Para corrigir esses problemas, substitua a função `OnCreate` pelo seguinte:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    var g = new Game1();
    SetContentView((View)g.Services.GetService(typeof(View)));
    g.Run();
}
```

## <a name="summary"></a>Resumo

Este passo a passo abordou como criar um projeto Multijogo de plataforma cruzada usando o Visual Studio para Mac. O resultado disso é uma tela azul vazia. Esse projeto pode ser usado como o ponto de partida para qualquer jogo iOS e Android.

## <a name="related-links"></a>Links relacionados

- [NuGet do Android para monojogo](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [NuGet do iOS para monojogo](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [Documentação da API monogame](http://www.monogame.net/documentation/?page=main)
