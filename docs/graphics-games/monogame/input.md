---
title: "Referência de GamePad MonoGame"
description: "GamePad é uma classe padrão e de plataforma cruzada para acessar dispositivos de entrada em MonoGame."
ms.topic: article
ms.prod: xamarin
ms.assetid: 1F71F3E8-2397-4C6A-8163-6731ECFB7E03
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 14a38c07b2ae5552cd9fb67d0cec581eafbf61cb
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="monogame-gamepad-reference"></a>Referência de GamePad MonoGame

_GamePad é uma classe padrão e de plataforma cruzada para acessar dispositivos de entrada em MonoGame._

`GamePad` pode ser usado para ler a entrada de dispositivos de entrada em várias plataformas de MonoGame. Este guia mostra como trabalhar com a classe GamePad. Desde que cada dispositivo de entrada é diferente no layout e o número de botões que ela fornece, este guia inclui diagramas que mostram os mapeamentos de vários dispositivos.


# <a name="gamepad-as-a-replacement-for-xbox360gamepad"></a>GamePad como uma substituição para Xbox360GamePad

A API do XNA original fornecida a `Xbox360GamePad` classe para a entrada de leitura de um controlador de jogo no Xbox 360 ou PC. MonoGame foi substituída com um `GamePad` classe como Xbox 360 controladores não podem ser usados na maioria das plataformas de MonoGame (por exemplo, iOS ou Xbox One). Apesar da alteração do nome, o uso do `GamePad` classe é semelhante de `Xbox360GamePad` classe.


# <a name="reading-input-from-gamepad"></a>Entrada de leitura de GamePad

O `GameController` classe fornece uma maneira padronizada de entrada de leitura em qualquer plataforma MonoGame. Ele fornece informações por meio de dois métodos:

 - `GetState` -Retorna o estado atual dos botões do controlador, cartões analógicos e direcional.
 - `GetCapabilities` – Retorna informações sobre os recursos de hardware, como se o controlador tem determinados botões ou oferece suporte a vibração.


## <a name="example-moving-a-character"></a>Exemplo: Mover um caractere

O código a seguir mostra como o cartão thumb esquerdo pode ser usado para mover um caractere definindo seu `XVelocity` e `YVelocity` propriedades. Esse código supõe que `characterInstance` é uma instância de um objeto que tem `XVelocity` e `YVelocity` propriedades:


```csharp
// In Update, or some code called every frame:
var gamePadState = GamePad.GetState(PlayerIndex.One);
// Use gamePadState to move the character
characterInstance.XVelocity = gamePadState.ThumbSticks.Left.X * characterInstance.MaxSpeed;
characterInstance.YVelocity = gamePadState.ThumbSticks.Left.Y * characterInstance.MaxSpeed;
```


## <a name="example-detecting-pushes"></a>Exemplo: Detectando envios por push

`GamePadState` Fornece informações sobre o estado atual do controlador, como se um determinado botão é pressionado. Determinadas ações, como fazer com que um caractere de salto, exigem verificação se o botão foi pressionado (não estava inativo último quadro, mas está inativo este quadro) ou liberado (foi pressionada último quadro, mas não para este quadro). 

Para executar esse tipo de lógica, variáveis locais que armazenam o quadro anterior `GamePadState` e o quadro atual `GamePadState` devem ser criados. O exemplo a seguir mostra como armazenar e usar o quadro anterior `GamePadState` implementar saltar:


```csharp
// At class scope:
// Store the last frame's and this frame's GamePadStates.
// "new" them so that code doesn't have to perform null checks:
GamePadState lastFrameGamePadState = new GamePadState();
GamePadState currentGamePadState = new GamePadState();
protected override void Update(GameTime gameTime)
{
    // store off the last state before reading the new one:
    lastFrameGamePadState = currentGamePadState;
    currentGamePadState = GamePad.GetState(PlayerIndex.One);
    bool wasAButtonPushed = 
currentGamePadState.Buttons.A == ButtonState.Pressed
        && lastFrameGamePadState.Buttons.A == ButtonState.Released;
    if(wasAButtonPushed)
    {
        MakeCharacterJump();
    }
...
}
```


## <a name="example-checking-for-buttons"></a>Exemplo: Verificação de botões

`GetCapabilities` pode ser usado para verificar se um controlador tem determinado hardware, como um botão específico ou um cartão analógico. O código a seguir mostra como verificar os botões B e Y em um controlador em um jogo que exige a presença de ambos os botões:


```csharp
var capabilities = GamePad.GetCapabilities(PlayerIndex.One);
bool hasBButton = capabilities.HasBButton;
bool hasXButton = capabilities.HasXButton;
if(!hasBButton || !hasXButton)
{
    NotifyUserOfMissingButtons();
}
```


# <a name="ios"></a>iOS

aplicativos iOS dão suporte a entrada do controlador de jogo sem fio.

> [!IMPORTANT]
> Os pacotes do NuGet para MonoGame 3.5 não incluem suporte para controladores de jogo sem fio. Usando a classe GamePad no iOS requer a criação 3.5 MonoGame da fonte ou usando os binários MonoGame 3.6 NuGet. 



## <a name="ios-game-controller"></a>Controlador de jogo do iOS

O `GamePad` classe retorna propriedades de leitura de controladores sem fio. As propriedades de `GamePad` oferecem uma boa cobertura para o iOS padrão controlador hardware, conforme mostrado no diagrama a seguir:

![](input-images/image1.png "As propriedades a GamePad fornecem uma boa cobertura para o iOS padrão hardware do controlador, conforme mostrado neste diagrama")


# <a name="apple-tv"></a>TV da Apple

Jogos Apple TV podem usar o Siri remoto ou controladores de jogo sem fio para entrada.


## <a name="siri-remote"></a>Siri remoto

*Siri remoto* é o dispositivo nativo de entrada para Apple TV. Embora os valores do Siri remoto podem ser lidos por meio de eventos (conforme mostrado no [guia Siri remoto e controladores Bluetooth](~/ios/tvos/platform/remote-bluetooth.md)), o `GamePad` classe pode retornar valores de Siri remoto.

Observe que `GamePad` somente pode ler entrada no botão play e toque superfície: 

![](input-images/image2.png "Observe que GamePad somente pode ler a entrada de botão Reproduzir e toque superfície")

Desde o toque movimentação superfície é ler o `DPad` propriedade, a movimentação de valores são relatados usando o `ButtonState` classe. Em outras palavras, os valores só estão disponíveis como `ButtonState.Pressed` ou `ButtonState.Released`, em vez de gestos ou valores numéricos.


## <a name="apple-tv-game-controller"></a>Apple TV jogo controlador

Controladores de jogo para Apple TV se comportam de forma idêntica para controladores de jogo para aplicativos iOS. Para obter mais informações, consulte o [iOS o controlador de jogo](#iOS_Game_Controller). 


# <a name="xbox-one"></a>Xbox One

O console Xbox One oferece suporte a entrada de leitura de um controlador de jogo Xbox One.


## <a name="xbox-one-game-controller"></a>Controlador de jogos do Xbox One

O controlador de jogo Xbox One é o dispositivo de entrada mais comuns para o Xbox One. O `GamePad` classe fornece valores de entrada do hardware do controlador de jogo.

![](input-images/image3.png "A classe GamePad fornece valores de entrada do hardware do controlador de jogo")


# <a name="summary"></a>Resumo

Este guia fornecida uma visão geral do MonoGame `GamePad` classe, como implementar a lógica de leitura de entrada e diagramas comuns `GamePad` implementações.

## <a name="related-links"></a>Links relacionados

- [MonoGame GamePad](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_GamePad)
