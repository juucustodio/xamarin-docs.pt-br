---
title: Referência de GamePad MonoGame
description: Este documento descreve GamePad, uma classe de plataforma cruzada para acessar dispositivos de entrada no MonoGame. Ele discute como ler dados provenientes do gamepad e fornece exemplos de código.
ms.prod: xamarin
ms.assetid: 1F71F3E8-2397-4C6A-8163-6731ECFB7E03
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 235166b78dfbd4998086a2925a54137f1922f5d1
ms.sourcegitcommit: a7170494e1975f0f1be547a45444752fd8e57819
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58507000"
---
# <a name="monogame-gamepad-reference"></a>Referência de GamePad MonoGame

_GamePad é uma classe padrão, de plataforma cruzada para acessar dispositivos de entrada no MonoGame._

`GamePad` pode ser usado para ler a entrada de dispositivos de entrada em várias plataformas de MonoGame. Este guia mostra como trabalhar com a classe GamePad. Uma vez que cada dispositivo de entrada é diferente no layout e o número de botões que ele fornece, este guia inclui diagramas que mostram os mapeamentos de vários dispositivos.

## <a name="gamepad-as-a-replacement-for-xbox360gamepad"></a>GamePad como uma substituição para Xbox360GamePad

A API do XNA original fornecido a `Xbox360GamePad` classe para ler a entrada de um controlador de jogo no PC ou Xbox 360. MonoGame substituiu isso com um `GamePad` classe já que controladores do Xbox 360 não podem ser usados na maioria das plataformas de MonoGame (por exemplo, iOS ou no Xbox One). Apesar da alteração do nome, o uso do `GamePad` classe é semelhante ao `Xbox360GamePad` classe.

## <a name="reading-input-from-gamepad"></a>Ler a entrada de GamePad

O `GamePad` classe fornece uma maneira padronizada de ler a entrada em qualquer plataforma MonoGame. Ele fornece informações por meio de dois métodos:

- `GetState` – Retorna o estado atual dos botões do controlador, cartões analógicos e direcional.
- `GetCapabilities` – Retorna informações sobre os recursos de hardware, como se o controlador tem determinados botões ou dá suporte a vibração.

### <a name="example-moving-a-character"></a>Exemplo: Mover um caractere

O código a seguir mostra como o pen drive de thumb esquerdo pode ser usado para mover um caractere, definindo sua `XVelocity` e `YVelocity` propriedades. Esse código supõe que `characterInstance` é uma instância de um objeto que tem `XVelocity` e `YVelocity` propriedades:

```csharp
// In Update, or some code called every frame:
var gamePadState = GamePad.GetState(PlayerIndex.One);
// Use gamePadState to move the character
characterInstance.XVelocity = gamePadState.ThumbSticks.Left.X * characterInstance.MaxSpeed;
characterInstance.YVelocity = gamePadState.ThumbSticks.Left.Y * characterInstance.MaxSpeed;
```

### <a name="example-detecting-pushes"></a>Exemplo: Detecção de envios por push

`GamePadState` Fornece informações sobre o estado atual do controlador, como se um determinado botão é pressionado. Determinadas ações, como fazer com que um caractere de pular, exigem a verificação se o botão foi pressionado (não era para baixo do último quadro, mas está desativado neste quadro) ou liberado (era para baixo do último quadro, mas não para baixo deste quadro).

Para executar esse tipo de lógica, variáveis locais que armazenam o quadro anterior `GamePadState` e o quadro atual `GamePadState` deve ser criado. O exemplo a seguir mostra como armazenar e usar o quadro anterior `GamePadState` implementar saltar:

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

### <a name="example-checking-for-buttons"></a>Exemplo: Verificação de botões

`GetCapabilities` pode ser usado para verificar se um controlador tem determinado hardware, como um botão específico ou pen drive analógico. O código a seguir mostra como verificar se há os botões de B e Y em um controlador em um jogo que exige a presença de ambos os botões:

```csharp
var capabilities = GamePad.GetCapabilities(PlayerIndex.One);
bool hasBButton = capabilities.HasBButton;
bool hasXButton = capabilities.HasXButton;
if(!hasBButton || !hasXButton)
{
    NotifyUserOfMissingButtons();
}
```

## <a name="ios"></a>iOS

aplicativos iOS oferecem suporte a entrada do controlador de jogo sem fio.

> [!IMPORTANT]
> Os pacotes do NuGet 3.5 MonoGame não incluem o suporte sem fio para controladores de jogos. Usando a classe de GamePad no iOS requer a criação 3.5 MonoGame de fonte ou usando os binários do NuGet do MonoGame 3.6.

### <a name="ios-game-controller"></a>Controlador de jogo do iOS

O `GamePad` classe retorna propriedades de leitura de controladores sem fio. As propriedades no `GamePad` oferecem uma boa cobertura para o iOS padrão controlador hardware, conforme mostrado no diagrama a seguir:

![](input-images/image1.png "As propriedades de GamePad fornecem uma boa cobertura para o iOS padrão hardware de controlador, conforme mostrado neste diagrama")

## <a name="apple-tv"></a>Apple TV

Jogos da Apple TV podem usar o Siri remoto ou controladores de jogos sem fio para a entrada.

### <a name="siri-remote"></a>Siri remoto

*Siri remoto* é o dispositivo de entrada nativo para Apple TV. Embora os valores da Siri remoto podem ser lidos por meio de eventos (conforme mostrado na [guia Siri remoto e controladores de Bluetooth](~/ios/tvos/platform/remote-bluetooth.md)), o `GamePad` classe pode retornar valores de Siri remoto.

Observe que `GamePad` só pode ler a entrada do botão Reproduzir e toque superfície:

![](input-images/image2.png "Observe que GamePad só pode ler a entrada do botão Reproduzir e superfície de toque")

Desde o toque superfície movimentação é lida por meio de `DPad` propriedade, movimentação de valores são relatados usando o `ButtonState` classe. Em outras palavras, os valores só estão disponíveis como `ButtonState.Pressed` ou `ButtonState.Released`, em vez de valores numéricos ou de gestos.

### <a name="apple-tv-game-controller"></a>Controlador de jogo de TV da Apple

Controladores de jogo para Apple TV se comportam de forma idêntica para controladores de jogo para aplicativos iOS. Para obter mais informações, consulte o [controlador de jogo do iOS](#ios-game-controller) seção. 

## <a name="xbox-one"></a>Xbox One

O console Xbox One dá suporte a ler a entrada de um controlador de jogo Xbox One.

### <a name="xbox-one-game-controller"></a>Controlador de jogo do Xbox One

O controlador de jogo Xbox One é o dispositivo de entrada mais comum para o Xbox One. O `GamePad` classe fornece valores de entrada do hardware do controlador de jogo.

![](input-images/image3.png "A classe de GamePad fornece valores de entrada do hardware do controlador de jogo")

## <a name="summary"></a>Resumo

Este guia fornece uma visão geral do MonoGame `GamePad` classe, como implementar a lógica de leitura de entrada e diagramas do comum `GamePad` implementações.

## <a name="related-links"></a>Links relacionados

- [MonoGame GamePad](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_GamePad)
