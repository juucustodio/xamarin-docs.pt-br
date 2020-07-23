---
title: Referência de GamePad em monojogo
description: Este documento descreve o GamePad, uma classe de plataforma cruzada para acessar dispositivos de entrada em monojogo. Ele aborda como ler a entrada do gamepad e fornece código de exemplo.
ms.prod: xamarin
ms.assetid: 1F71F3E8-2397-4C6A-8163-6731ECFB7E03
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 11b1cfda435e97b09f9d1eded22f11f912d1783d
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86934024"
---
# <a name="monogame-gamepad-reference"></a>Referência de GamePad em monojogo

_O GamePad é uma classe padrão de plataforma cruzada para acessar dispositivos de entrada em monojogo._

`GamePad`pode ser usado para ler a entrada de dispositivos de entrada em várias plataformas monogames. Este guia mostra como trabalhar com a classe GamePad. Como cada dispositivo de entrada difere no layout e no número de botões que ele fornece, este guia inclui diagramas que mostram os vários mapeamentos de dispositivo.

## <a name="gamepad-as-a-replacement-for-xbox360gamepad"></a>GamePad como uma substituição para Xbox360GamePad

A API do XNA original forneceu a `Xbox360GamePad` classe para ler a entrada de um controlador de jogo no Xbox 360 ou PC. O monogame substituiu isso por uma `GamePad` classe, uma vez que os controladores do Xbox 360 não podem ser usados na maioria das plataformas de monojogo (como Ios ou Xbox One). Apesar da alteração do nome, o uso da `GamePad` classe é semelhante à `Xbox360GamePad` classe.

## <a name="reading-input-from-gamepad"></a>Lendo a entrada do GamePad

A `GamePad` classe fornece uma maneira padronizada de leitura de entrada em qualquer plataforma monogame. Ele fornece informações por meio de dois métodos:

- `GetState`– Retorna o estado atual dos botões, dos pentes analógicos e do d-Pad do controlador.
- `GetCapabilities`– Retorna informações sobre os recursos de hardware, como se o controlador tem determinados botões ou oferece suporte a vibração.

### <a name="example-moving-a-character"></a>Exemplo: movendo um caractere

O código a seguir mostra como o painel de polegar à esquerda pode ser usado para mover um caractere definindo suas `XVelocity` `YVelocity` Propriedades e. Esse código pressupõe que `characterInstance` é uma instância de um objeto que tem `XVelocity` `YVelocity` as propriedades e:

```csharp
// In Update, or some code called every frame:
var gamePadState = GamePad.GetState(PlayerIndex.One);
// Use gamePadState to move the character
characterInstance.XVelocity = gamePadState.ThumbSticks.Left.X * characterInstance.MaxSpeed;
characterInstance.YVelocity = gamePadState.ThumbSticks.Left.Y * characterInstance.MaxSpeed;
```

### <a name="example-detecting-pushes"></a>Exemplo: detectando Pushes

`GamePadState`fornece informações sobre o estado atual do controlador, como se um determinado botão é pressionado. Determinadas ações, como fazer um salto de caractere, exigem a verificação se o botão foi enviado por push (não estava no último quadro, mas está abaixo desse quadro) ou liberado (estava no último quadro, mas não abaixo desse quadro).

Para executar esse tipo de lógica, as variáveis locais que armazenam o quadro anterior `GamePadState` e o quadro atual `GamePadState` devem ser criadas. O exemplo a seguir mostra como armazenar e usar o quadro anterior `GamePadState` para implementar a salto:

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

### <a name="example-checking-for-buttons"></a>Exemplo: verificando botões

`GetCapabilities`pode ser usado para verificar se um controlador tem determinado hardware, como um botão específico ou um pente analógico. O código a seguir mostra como verificar os botões B e Y em um controlador em um jogo que requer a presença de ambos os botões:

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

os aplicativos iOS dão suporte à entrada do controlador de jogo sem fio.

> [!IMPORTANT]
> Os pacotes do NuGet para o monogame 3,5 não incluem suporte para controladores de jogos sem fio. Usar a classe GamePad no iOS requer a criação de monogame 3,5 da origem ou usando os binários do NuGet de 3,6 de jogos.

### <a name="ios-game-controller"></a>Controlador de jogo do iOS

A `GamePad` classe retorna as propriedades lidas de controladores sem fio. As propriedades no `GamePad` fornecem uma boa cobertura para o hardware do controlador Ios padrão, conforme mostrado no diagrama a seguir:

![As propriedades no GamePad fornecem uma boa cobertura para o hardware do controlador iOS padrão, conforme mostrado neste diagrama](input-images/image1.png)

## <a name="apple-tv"></a>Apple TV

Os jogos de TV da Apple podem usar os controladores de jogo Siri remoto ou sem fio para entrada.

### <a name="siri-remote"></a>Siri remoto

*Siri Remote* é o dispositivo de entrada nativo para o Apple TV. Embora os valores do Siri remoto possam ser lidos por meio de eventos (conforme mostrado no [Guia de controladores remotos e Bluetooth do Siri](~/ios/tvos/platform/remote-bluetooth.md)), a `GamePad` classe pode retornar valores do Siri remoto.

Observe que `GamePad` só é possível ler a entrada do botão Play e da superfície de toque:

![Observe que o GamePad só pode ler a entrada do botão reproduzir e da superfície de toque](input-images/image2.png)

Como a movimentação da superfície de toque é lida pela `DPad` propriedade, os valores de movimento são relatados usando a `ButtonState` classe. Em outras palavras, os valores só estão disponíveis como `ButtonState.Pressed` ou `ButtonState.Released` , em oposição a valores numéricos ou gestos.

### <a name="apple-tv-game-controller"></a>Controlador de jogo da Apple TV

Os controladores de jogos para o Apple TV se comportam de forma idêntica aos controladores de jogos para aplicativos iOS. Para obter mais informações, consulte a seção [controlador de jogo do IOS](#ios-game-controller) . 

## <a name="xbox-one"></a>Xbox One

O console do Xbox One dá suporte à leitura de entrada de um controlador de jogo do Xbox One.

### <a name="xbox-one-game-controller"></a>Controlador de jogo do Xbox One

O controlador de jogo do Xbox One é o dispositivo de entrada mais comum para o Xbox One. A `GamePad` classe fornece valores de entrada do hardware do controlador de jogo.

![A classe GamePad fornece valores de entrada do hardware do controlador de jogo](input-images/image3.png)

## <a name="summary"></a>Resumo

Este guia forneceu uma visão geral da classe de monojogo `GamePad` , como implementar a lógica de leitura de entrada e diagramas de `GamePad` implementações comuns.

## <a name="related-links"></a>Links Relacionados

- [GamePad com monojogo](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_GamePad)
