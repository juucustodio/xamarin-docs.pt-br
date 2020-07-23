---
title: Multitarefa para iPad no Xamarin. iOS
description: o iOS 9 dá suporte a dois aplicativos em execução ao mesmo tempo, usando visualização de slides ou de divisão. Ele também dá suporte à reprodução de vídeo em imagem.
ms.prod: xamarin
ms.assetid: 0F2266D7-21FF-404D-A148-0CFDE76B12AA
ms.technology: xamarin-ios
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: b86f3a159a144f02ea13663bfddb41ed0100f740
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86931398"
---
# <a name="multitasking-for-ipad-in-xamarinios"></a>Multitarefa para iPad no Xamarin. iOS

_o iOS 9 dá suporte a dois aplicativos em execução ao mesmo tempo, usando visualização de slides ou de divisão. Ele também dá suporte à reprodução de vídeo em imagem._

![Exemplo de divisão de tela](multitasking-images/about02-sml.png) ![Exemplo de imagem em imagem](multitasking-images/about03-sml.png)

o iOS 9 adiciona suporte multitarefa para executar dois aplicativos ao mesmo tempo em um hardware de iPad específico. Há suporte para multitarefas para iPad por meio dos seguintes recursos:

- [**Deslizar**](#Slide-Over) para frente – permite que o usuário execute temporariamente um segundo aplicativo Ios em um painel de deslizamento (no lado direito ou esquerdo da tela com base na direção da linguagem) que abrange aproximadamente 25% do aplicativo principal em execução no momento. O deslizamento está disponível apenas em um iPad pro, iPad Air, iPad Air 2, iPad mini 2, iPad mini 3 ou iPad mini 4.
- [**Exibição dividida**](#Split-View) -em hardware do iPad com suporte (iPad Air 2, iPad mini 4 e iPad pro somente), o usuário pode escolher um segundo aplicativo e executá-lo lado a lado com o aplicativo em execução no momento em um modo de divisão de tela. O usuário pode controlar a porcentagem da tela principal ocupada por cada aplicativo.
- [**Imagem no Picture**](#Picture-in-Picture) -for apps que reproduzem conteúdo de vídeo, o vídeo agora pode ser reproduzido em uma janela móvel e redimensionável que flutua sobre os outros aplicativos em execução no momento no dispositivo IOS. O usuário tem controle total sobre o tamanho e a posição desta janela. A imagem na imagem está disponível apenas em um iPad pro, iPad Air, iPad Air 2, iPad mini 2, iPad mini 3 ou iPad mini 4.

Há várias coisas a serem consideradas ao [oferecer suporte a multitarefas em seu aplicativo](#Supporting-Multitasking-in-your-App), incluindo:

- [Tamanho e orientação da tela](#Screen-Size-Considerations)
- [Atalhos de teclado de hardware personalizados](#Custom-Hardware-Keyboard-Shortcuts)
- [Gerenciamento de recursos](#Resource-Management-Considerations)

Como desenvolvedor de aplicativos, você também pode [recusar a multitarefa](#Opting-Out-of-Multitasking), incluindo [desabilitar a reprodução de vídeo Pip](#Disabling-PIP-Video-Playback).

Este artigo abordará as etapas necessárias para garantir que seu aplicativo Xamarin. iOS seja executado corretamente em um ambiente multitarefa ou como recusar multitarefas se não for uma boa opção para seu aplicativo.

> [!VIDEO https://youtube.com/embed/GctYAozoLr8]

**Multitarefa para vídeo do iPad**

<a name="Multitasking-QuickStart"></a>

## <a name="multitasking-quickstart"></a>Início rápido de multitarefa

Para dar suporte à exibição de **slides** ou de **divisão** , seu aplicativo deve fazer o seguinte:

- Seja criado com base no iOS 9 (ou superior).
- Use um Storyboard para sua tela de inicialização (e não os ativos de imagem).
- Use um storyboard com classes de tamanho e AutoLayout para sua interface do usuário.
- Dê suporte a todas as 4 orientações de dispositivo iOS (retrato, virada para baixo, retrato à esquerda & paisagem à direita).

<a name="Multitasking"></a>

## <a name="about-multitasking-for-ipad"></a>Sobre multitarefas para iPad

o iOS 9 oferece novos recursos de multitarefa no iPad com a introdução do _deslizamento_, _modo divisão_ (iPad Air 2, iPad mini 4 e iPad pro somente) e _imagem no Picture_. Vamos examinar mais detalhadamente esses recursos nas seções a seguir.

<a name="Slide-Over"></a>

### <a name="slide-over"></a>Deslizar

O recurso deslizar sobre permite que o usuário escolha um segundo aplicativo e o exiba em um painel deslizante pequeno para fornecer interação rápida. O painel do slide acima é temporário e será fechado quando o usuário voltar para trabalhar com o aplicativo principal novamente.

[![O painel deslizante para cima](multitasking-images/about01.png)](multitasking-images/about01.png#lightbox)

O principal a ser lembrado é que o usuário decide quais dois aplicativos serão executados lado a lado e que o desenvolvedor não tem controle sobre esse processo. Como resultado, há algumas coisas que você precisará fazer para garantir que seu aplicativo Xamarin. iOS seja executado corretamente em um slide ao longo do painel:

- **Usar classes de tamanho e AutoLayout** — como seu aplicativo Xamarin. Ios agora pode ser executado no painel lateral de deslizamento, você não pode mais depender do dispositivo, seu tamanho de tela ou sua orientação para fazer o layout da interface do usuário. Para garantir que seu aplicativo dimensione sua interface corretamente, você precisará usar classes de tamanho e AutoLayout. Para obter mais informações, consulte nossa [introdução à documentação de storyboards unificados](~/ios/user-interface/storyboards/unified-storyboards.md) .
- **Use recursos com eficiência** — como seu aplicativo agora pode estar compartilhando o sistema com outro aplicativo em execução, é essencial que seu aplicativo use recursos do sistema com eficiência. Quando a memória se torna esparsa, o sistema encerra automaticamente o aplicativo que está consumindo mais memória. Consulte [Guia de eficiência de energia da Apple para aplicativos Ios](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) para obter mais detalhes.

O deslizamento está disponível apenas em um iPad pro, iPad Air, iPad Air 2, iPad mini 2, iPad mini 3 ou iPad mini 4. Para saber mais sobre como preparar seu aplicativo para o slide, consulte a documentação da Apple [adotando aprimoramentos de multitarefa no iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145) .

<a name="Split-View"></a>

### <a name="split-view"></a>Modo Divisão

Em hardware do iPad com suporte (iPad Air 2, iPad mini 4 e iPad pro somente), o usuário pode escolher um segundo aplicativo e executá-lo lado a lado com o aplicativo em execução no momento em um modo de divisão de tela. O usuário pode controlar a porcentagem da tela principal que cada aplicativo ocupa arrastando um divisor na tela.

[![O modo de exibição de divisão](multitasking-images/about02.png)](multitasking-images/about02.png#lightbox)

Como deslizar, o usuário decide quais dois aplicativos serão executados lado a lado e, novamente, o desenvolvedor não tem controle sobre esse processo. Como resultado, o modo de exibição de divisão coloca requisitos semelhantes em um aplicativo Xamarin. iOS:

- **Usar classes de tamanho e AutoLayout** — como seu aplicativo Xamarin. Ios agora pode ser executado em um modo de tela de divisão no tamanho especificado do usuário, você não pode mais depender do dispositivo, seu tamanho de tela ou sua orientação para fazer o layout da interface do usuário. Para garantir que seu aplicativo dimensione sua interface corretamente, você precisará usar classes de tamanho e AutoLayout. Para obter mais informações, consulte nossa [introdução à documentação de storyboards unificados](~/ios/user-interface/storyboards/unified-storyboards.md) .
- **Use recursos com eficiência** — como seu aplicativo agora pode estar compartilhando o sistema com outro aplicativo em execução, é essencial que seu aplicativo use recursos do sistema com eficiência. Quando a memória se torna esparsa, o sistema encerra automaticamente o aplicativo que está consumindo mais memória. Consulte [Guia de eficiência de energia da Apple para aplicativos Ios](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) para obter mais detalhes.

Para saber mais sobre como preparar seu aplicativo para o modo de exibição de divisão, consulte a documentação da Apple [adotando aprimoramentos de multitarefa no iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145) .

<a name="Picture-in-Picture"></a>

### <a name="picture-in-picture"></a>Imagem em imagem

A nova imagem no recurso de imagem (também conhecida como _Pip_) permite que o usuário Assista a um vídeo em uma janela pequena e flutuante que o usuário pode posicionar em qualquer lugar na tela acima de outros aplicativos em execução.

[![Uma imagem de exemplo na janela flutuante de imagem](multitasking-images/about03.png)](multitasking-images/about03.png#lightbox)

Como com o deslizamento e a exibição de divisão, o usuário tem controle total sobre como assistir a um vídeo na imagem no modo de imagem. Se a função principal do seu aplicativo for assistir a um vídeo, ele precisará de alguma modificação para se comportar corretamente no modo PIP. Caso contrário, nenhuma alteração será necessária para dar suporte a PIP.

Para que seu aplicativo exiba o vídeo PIP na solicitação do usuário, você precisará usar o _AVKit_ ou as APIs do _AV Foundation_. A estrutura do Media Player foi depreciada no iOS 9 e não dá suporte a PIP.

A imagem na imagem está disponível apenas em um iPad pro, iPad Air, iPad Air 2, iPad mini 2, iPad mini 3 ou iPad mini 4. Para obter mais informações, consulte nosso [aplicativo de exemplo PictureInPicture](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9) e a [imagem da Apple no Picture início rápido](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/QuickStartForPictureInPicture.html#//apple_ref/doc/uid/TP40015145-CH14) Documentation.

<a name="Supporting-Multitasking-in-your-App"></a>

## <a name="supporting-multitasking-in-your-app"></a>Suporte a multitarefas em seu aplicativo

Para qualquer aplicativo Xamarin. iOS existente, o suporte para multitarefas é uma tarefa transparente, desde que seu aplicativo já siga os guias de design da Apple e as práticas recomendadas para o iOS 8. Isso significa que o aplicativo deve usar storyboards com classes de tamanho e AutoLayout para seus layouts de interface do usuário (consulte nossa [introdução aos storyboards unificados](~/ios/user-interface/storyboards/unified-storyboards.md) para obter mais informações).

Para esses aplicativos, pouca ou nenhuma alteração é necessária para dar suporte a multitarefas e se comportar bem dentro dela. Se a interface do usuário do seu aplicativo foi criada usando outros métodos, como posicionar e dimensionar diretamente elementos da interface do usuário no código C# ou se ele depender de tamanhos ou orientações de tela específicos do dispositivo, ele precisará de uma modificação significativa para dar suporte a multitarefas do iOS 9 corretamente.

Para dar suporte à multitarefa do iOS 9 em qualquer novo aplicativo Xamarin. iOS, use novamente os storyboards com classes de tamanho e AutoLayout para todos os layouts de interface do usuário do aplicativo e implemente as instruções nas seções a seguir.

<a name="Screen-Size-Considerations"></a>

### <a name="screen-size-and-orientation-considerations"></a>Considerações de tamanho e orientação da tela

Antes do iOS 9, você poderia projetar seu aplicativo com relação a tamanhos e orientações de tela de dispositivo específicos. Como um aplicativo agora pode ser executado em um painel de deslizamento ou no modo de exibição de divisão, ele pode se encontrar em execução em uma classe de tamanho horizontal regular ou normal no iPad, independentemente da orientação física do dispositivo ou do tamanho da tela.

[![Considerações de tamanho e orientação da tela](multitasking-images/sizeclasses01.png)](multitasking-images/sizeclasses01.png#lightbox)

Em um iPad, um aplicativo de tela inteira tem classes regulares de tamanho horizontal e vertical. Todos os iPhones, mas o iPhone 6 Plus e iPhone 6s Plus, têm classes de tamanho compacto em ambas as direções em qualquer orientação. O iPhone 6 Plus e o iPhone 6s Plus no modo paisagem têm uma classe normal de tamanho horizontal e uma classe de tamanho vertical compacto (muito semelhante a um iPad mini).

Em iPads que dão suporte ao deslizamento e à exibição de divisão, você pode terminar com as seguintes combinações:

| **Direção** | **Aplicativo primário** | **Aplicativo secundário** |
|--- |--- |--- |
| **Retrato** |75% da tela<br />Compactar horizontalmente<br />Vertical regular|25% da tela<br />Compactar horizontalmente<br />Vertical regular|
| **Paisagem** |75% da tela<br />Horizontal regular<br />Vertical regular|25% da tela<br />Compactar horizontalmente<br />Vertical regular|
| **Paisagem** |50% da tela<br />Compactar horizontalmente<br />Vertical regular|50% da tela<br />Compactar horizontalmente<br />Vertical regular|

No aplicativo de exemplo [MuliTask](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-multitask) , se ele for executado em tela inteira em um iPad no modo paisagem, ele apresentará a lista e a exibição de detalhes ao mesmo tempo:

[![A lista e a exibição de detalhes apresentados ao mesmo tempo](multitasking-images/sizeclasses03.png)](multitasking-images/sizeclasses03.png#lightbox)

Se o mesmo aplicativo for executado em um slide sobre o painel, ele será apresentado como uma classe de tamanho horizontal compacto e exibirá apenas a lista:

[![Somente a lista apresentada quando o dispositivo é horizontal](multitasking-images/sizeclasses04.png)](multitasking-images/sizeclasses04.png#lightbox)

Para garantir que seu aplicativo se comporta corretamente nessas situações, você deve adotar coleções de características junto com as classes de tamanho e estar em conformidade com as `IUIContentContainer` `IUITraitEnvironment` interfaces e. Consulte a [referência de classe UITraitCollection](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITraitCollection_ClassReference/index.html#//apple_ref/doc/uid/TP40014202) da Apple e nosso guia [introdução ao storyboards unificado](~/ios/user-interface/storyboards/unified-storyboards.md) para obter mais informações.

Além disso, você não pode mais contar com os limites da tela de dispositivos para definir a área visível do aplicativo. você precisará usar os limites de janela do aplicativo em vez disso. Como os limites de janela estão totalmente sob o controle do usuário, não é possível ajustá-los programaticamente ou impedir que o usuário altere esses limites.

Por fim, seu aplicativo deve usar um arquivo de storyboard para apresentar sua tela de inicialização, em oposição ao uso de um conjunto de arquivos de imagem **. png** e dar suporte a todas as quatro orientações de interface (retrato, de cabeça para baixo, retrato à esquerda e paisagem à direita) a serem consideradas para execução em um slide sobre o painel ou no modo de exibição de divisão.

<a name="Custom-Hardware-Keyboard-Shortcuts"></a>

### <a name="custom-hardware-keyboard-shortcuts"></a>Atalhos de teclado de hardware personalizados

No iOS 9 em execução em um iPad, a Apple tem suporte estendido para teclados de hardware. o iPads sempre incluía suporte de teclado externo básico via Bluetooth e alguns fabricantes de teclado criaram teclas que incluíam chaves específicas de iOS com fio.

Agora, com o iOS 9, os aplicativos podem criar seus próprios atalhos de teclado personalizados. Além disso, alguns atalhos de teclado básicos estão disponíveis como **Command-C** (cópia), **Command-X** (recortar), **Command-V** (colar) e **Command-Shift-H** (Home), sem que um aplicativo que está sendo especificamente gravado responda a eles.

A **Guia de comando** abrirá um alternador de aplicativo que permite ao usuário alternar rapidamente entre aplicativos do teclado, assim como o Mac os:

[![O alternador de aplicativo](multitasking-images/keyboard01.png)](multitasking-images/keyboard01.png#lightbox)

Se um aplicativo iOS 9 incluir atalhos de teclado, o usuário poderá manter o **comando**, a **opção** ou as teclas de **controle** para exibi-los em um pop-up:

[![O pop-up atalhos de teclado](multitasking-images/keyboard02.png)](multitasking-images/keyboard02.png#lightbox)

#### <a name="defining-custom-keyboard-shortcuts"></a>Definindo atalhos de teclado personalizados

Se adicionarmos o código a seguir a um controlador de exibição ou exibição em nosso aplicativo, quando esse modo de exibição ou controlador estiver visível, um atalho de teclado personalizado estará disponível:

```csharp
#region Custom Keyboard Shortcut
public override bool CanBecomeFirstResponder {
    get { return true; }
}

public override UIKeyCommand[] KeyCommands {
    get {

        var keyCommand = UIKeyCommand.Create (new NSString("n"), UIKeyModifierFlags.Command, new Selector ("NewEntry"), new NSString("New Entry"));
        return new UIKeyCommand[]{ keyCommand };
    }
}

[Export("NewEntry")]
public void NewEntry() {

    // Add a new entry
    ...

}
#endregion
```

Primeiro, substituimos a `CanBecomeFirstResponder` propriedade e retornamos `true` para que o controlador de exibição ou exibição possa receber entrada de teclado. 

Em seguida, substituimos a `KeyCommands` propriedade e criamos uma nova `UIKeyCommand` para o pressionamento de tecla **Command-N** . Quando o pressionamento de teclas é ativado, chamamos o `NewEntry` método (que expõemos ao Ios 9 usando o `Export` comando) para executar a ação solicitada.

Se executarmos esse aplicativo em um iPad com um teclado de hardware anexado e o usuário digitar **Command-N**, uma nova entrada será adicionada à lista. Se o usuário mantiver a tecla de **comando** , a lista de atalhos será exibida:

[![O pop-up atalhos de teclado](multitasking-images/keyboard03.png)](multitasking-images/keyboard03.png#lightbox)

Consulte o aplicativo de [multitarefa](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-multitask) de exemplo para obter uma implementação de exemplo.

<a name="Resource-Management-Considerations"></a>

### <a name="resource-management-considerations"></a>Considerações sobre gerenciamento de recursos

Mesmo para aplicativos que já estão usando guias de design e práticas recomendadas do iOS 8, o gerenciamento eficiente de recursos ainda pode ser um problema. No iOS 9, os aplicativos não têm mais uso exclusivo de memória, CPU ou outros recursos do sistema.

Como resultado, você deve ajustar seu aplicativo Xamarin. iOS para usar os recursos do sistema com eficiência ou ele pode ser encerrado em situações de memória insuficiente. Isso é igualmente verdadeiro para aplicativos que recusam multitarefas, já que um segundo aplicativo ainda pode ser executado em um slide sobre o painel ou uma imagem na janela de imagem que requer recursos extras ou fazendo com que a taxa de atualização caia abaixo de 60 quadros por segundo.

Considere as seguintes ações do usuário e suas implicações:

- **Inserindo texto em um slide ao longo do painel** – mesmo que seu aplicativo não tenha entrada de texto, o teclado do sistema agora pode ser exibido em sua interface do usuário. Como resultado, o aplicativo pode precisar responder às notificações de vídeo do teclado (como mostrar e ocultar o teclado).
- **Executando um segundo aplicativo em um slide sobre o painel** – o novo aplicativo agora está em execução em primeiro plano e competindo com o aplicativo existente para recursos do sistema, como ciclos de memória e CPU.
- **Reproduzir um vídeo em uma janela PIP** – não só essa janela pode cobrir parte da interface do seu aplicativo, mas o aplicativo que iniciou o vídeo ainda está em execução em segundo plano e consumindo recursos de CPU e memória.

Para garantir que seu aplicativo esteja usando recursos com eficiência, você deve fazer o seguinte:

- **Criar perfil do aplicativo com instrumentos** -Verifique se há vazamentos de memória, uso excessivo da CPU e áreas em que o aplicativo pode estar bloqueando o thread principal.
- **Responder a métodos de transições de estado** – em sua substituição de arquivo **AppDelegate.cs** e resposta a métodos de alteração de estado, como o aplicativo entrando ou retornando do plano de fundo. Libere quaisquer ativos não necessários, como imagens, dados ou exibições e controlador de exibição.
- **Teste lado a lado com aplicativos com uso intensivo de memória** – execute seu aplicativo usando a exibição deslizante e divisão em hardware físico do IOS com um aplicativo com uso intensivo de memória, como mapas (no modo de exibição satélite) e teste se ambos os aplicativos permanecem responsivos e não falham.

Consulte o [Guia de eficiência de energia da Apple para aplicativos Ios](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) para obter mais informações sobre o gerenciamento de recursos.

<a name="Opting-Out-of-Multitasking"></a>

## <a name="opting-out-of-multitasking"></a>Recusando a multitarefa

Embora a Apple sugira que todos os aplicativos iOS 9 ofereçam suporte a multitarefas, pode haver motivos muito específicos para um aplicativo, como jogos ou aplicativos de câmera que exigem que a tela inteira funcione corretamente.

Para que seu aplicativo Xamarin. iOS opte por ser executado em um painel de deslizamento ou no modo de exibição de divisão, edite o arquivo **info. plist** do projeto e marque a opção **requer tela inteira**:

[![Recusando a multitarefa](multitasking-images/fullscreen01.png)](multitasking-images/fullscreen01.png#lightbox)

> [!IMPORTANT]
> Embora a recusa de multitarefas impeça que o aplicativo seja executado no deslizante ou no modo de exibição de divisão, ele não impede que outro aplicativo seja executado no deslizante ou uma imagem no vídeo de imagem seja exibida junto com seu aplicativo.

<a name="Disabling-PIP-Video-Playback"></a>

### <a name="disabling-pip-video-playback"></a>Desabilitando a reprodução de vídeo PIP

Na maioria das situações, o aplicativo deve permitir que o usuário reproduza qualquer conteúdo de vídeo exibido em uma imagem na janela flutuante de imagem. No entanto, pode haver situações em que isso pode não ser desejado, como vídeos de cena cortadas por jogos.

Para recusar a reprodução de vídeo PIP, faça o seguinte em seu aplicativo:

- Se você estiver usando um `AVPlayerViewController` para exibir vídeo, defina a `AllowsPictureInPicturePlayback` propriedade como `false` .
- Se você estiver usando o `AVPlayerLayer` para exibir vídeo, não crie uma instância de `AVPictureInPictureController` .
- Se você estiver usando um `WKWebView` para exibir vídeo, defina a `AllowsPictureInPictureMediaPlayback` propriedade como `false` .

<a name="Summary"></a>

## <a name="summary"></a>Resumo

Este artigo abordou as etapas necessárias para garantir que um aplicativo Xamarin. iOS seja executado e se comporte corretamente na nova capacidade de multitarefa do iOS 9 para iPads. Além disso, ele abordou a recusa de multitarefas para aplicativos em que não é uma boa opção.

## <a name="related-links"></a>Links Relacionados

- [Amostras do iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [Multitarefa (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-multitask)
- [Introdução aos storyboards unificados](~/ios/user-interface/storyboards/unified-storyboards.md)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Adotando aprimoramentos multitarefas no iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)
- [Postagem no blog](https://blog.xamarin.com/using-auto-layouts-for-ios-9-splitview/)
