---
title: Multitarefa para iPad em xamarin. IOS
description: iOS 9 oferece suporte a dois aplicativos em execução ao mesmo tempo, usando o slide ou modo divisão. Ele também dá suporte a imagem na imagem de reprodução do vídeo.
ms.prod: xamarin
ms.assetid: 0F2266D7-21FF-404D-A148-0CFDE76B12AA
ms.technology: xamarin-ios
ms.custom: xamu-video
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 78f9aa47cce2fe0059bd7f4d4ff637d14fb8c669
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112500"
---
# <a name="multitasking-for-ipad-in-xamarinios"></a>Multitarefa para iPad em xamarin. IOS

_iOS 9 oferece suporte a dois aplicativos em execução ao mesmo tempo, usando o slide ou modo divisão. Ele também dá suporte a imagem na imagem de reprodução do vídeo._

![](multitasking-images/about02-sml.png "Exemplo de tela de dividir") ![](multitasking-images/about03-sml.png "imagem na imagem exemplo")

iOS 9 adiciona suporte de multitarefa para dois aplicativos em execução ao mesmo tempo em um hardware específico iPad. Multitarefa para iPad é suportada pelos seguintes recursos:

- [**Slide sobre** ](#Slide-Over) -permite que o usuário executar temporariamente um segundo aplicativo do iOS em um slide do painel (seja no lado esquerdo ou direito da tela com base na direção de idioma) que cobre aproximadamente 25% do aplicativo principal em execução no momento. Deslizar sobre está disponível somente em um iPad Pro, iPad Air, iPad Air 2, iPad 2 Mini, iPad Mini 3 ou 4 Mini do iPad.
- [**Modo de exibição dividido** ](#Split-View) -no hardware do iPad com suporte (iPad Air 2, iPad Mini 4 e iPad Pro somente), o usuário pode escolher um segundo aplicativo e executá-lo lado a lado com o aplicativo em execução no momento em um modo de tela de divisão. O usuário pode controlar o percentual da tela principal que cada aplicativo ocupa.
- [**Figura Figura** ](#Picture-in-Picture) – para aplicativos que reproduzir conteúdo de vídeo, o vídeo pode agora ser reproduzido em uma janela redimensionável e móvel que flutua sobre os outros aplicativos em execução no momento no dispositivo iOS. O usuário tem controle total sobre o tamanho e a posição dessa janela. A imagem na imagem está disponível somente em um iPad Pro, iPad Air, iPad Air 2, iPad 2 Mini, iPad Mini 3 ou 4 Mini do iPad.

Há várias coisas a considerar quando [que dão suporte a multitarefa em seu aplicativo](#Supporting-Multitasking-in-your-App), incluindo:

- [Tamanho da tela e a orientação](#Screen-Size-Considerations)
- [Atalhos de teclado de Hardware personalizado](#Custom-Hardware-Keyboard-Shortcuts)
- [Gerenciamento de recursos](#Resource-Management-Considerations)

Como desenvolvedor de aplicativos também é possível [recusar multitarefa](#Opting-Out-of-Multitasking), incluindo [desabilitando a reprodução de vídeo de PIP](#Disabling-PIP-Video-Playback).

Este artigo abordará as etapas necessárias para garantir que seu aplicativo xamarin. IOS seja executado corretamente em um ambiente de execução multitarefa ou como recusar a multitarefa se ele não é um bom ajuste para seu aplicativo.

> [!VIDEO https://youtube.com/embed/GctYAozoLr8]

**Multitarefa para iPad, por [Xamarin University](https://university.xamarin.com)**


<a name="Multitasking-QuickStart" />

## <a name="multitasking-quickstart"></a>Guia de início rápido multitarefa

Para dar suporte à **Deslizar ao longo** ou **modo de exibição dividido** seu aplicativo deve fazer o seguinte:

 - Ser criado em relação a iOS 9 (ou posterior).
 - Usar um Storyboard para sua tela de inicialização (e não ativos de imagem).
 - Use um Storyboard com layout automático e Classes de tamanho para sua interface do usuário.
 - Suporte a todos os 4 iOS orientações do dispositivo (retrato, retrato de cabeça para baixo, esquerda paisagem e paisagem direita).

<a name="Multitasking" />

## <a name="about-multitasking-for-ipad"></a>Sobre a execução multitarefa para iPad

iOS 9 oferece novas habilidades de multitarefa no iPad com a introdução da _Deslizar ao longo_, _modo de exibição dividido_ (2 de ar, iPad Mini 4 e iPad Pro somente iPad) e _Picture in Picture_. Vamos dar examinar esses recursos mais detalhadamente nas seções a seguir.

<a name="Slide-Over" />

### <a name="slide-over"></a>Slide sobre

O recurso de Slide sobre permite ao usuário escolher um segundo aplicativo e exibi-lo em um painel deslizante pequeno para propiciar interação rápida. O painel de Slide sobre é temporário e será fechada quando o usuário voltar a trabalhar com o aplicativo principal novamente.

[![](multitasking-images/about01.png "O painel Deslizar sobre")](multitasking-images/about01.png#lightbox)

O ponto principal a lembrar é que o usuário decide quais aplicativos de duas e serão executados lado a lado que o desenvolvedor não tem controle sobre esse processo. Como resultado, há algumas coisas que você precisará fazer para garantir que seu aplicativo xamarin. IOS seja executado corretamente em um painel de Slide sobre:

- **Usar layout automático e Classes de tamanho** — porque o aplicativo xamarin. IOS agora pode ser executado no painel do lado de fora do slide, você não poderá mais usar no dispositivo, seu tamanho de tela ou sua orientação para o layout de sua interface do usuário. Para garantir que seu aplicativo dimensiona sua interface corretamente, você precisará usar layout automático e Classes de tamanho. Para obter mais informações, consulte nosso [Introdução ao Storyboards unificados](~/ios/user-interface/storyboards/unified-storyboards.md) documentação.
- **Usar recursos de maneira eficiente** — como seu aplicativo pode agora ser compartilhando o sistema com outro aplicativo em execução, é essencial que seu aplicativo usa recursos do sistema com eficiência. Quando a memória se torna esparsa, o sistema automaticamente encerrará o aplicativo que está consumindo mais memória. Consulte da Apple [guia de eficiência de energia para aplicativos iOS](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) para obter mais detalhes.

Deslizar sobre está disponível somente em um iPad Pro, iPad Air, iPad Air 2, iPad 2 Mini, iPad Mini 3 ou 4 Mini do iPad. Para saber mais sobre como preparar seu aplicativo para deslizar sobre, consulte da Apple [adotando os aprimoramentos de multitarefa no iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145) documentação.

<a name="Split-View" />

### <a name="split-view"></a>Modo Divisão

Em um hardware de iPad com suporte (iPad Air 2, iPad Mini 4 e iPad Pro somente), o usuário pode escolher um segundo aplicativo e executá-lo lado a lado com o aplicativo em execução no momento em um modo de tela de divisão. O usuário pode controlar o percentual da tela principal que cada aplicativo ocupa arrastando na tela divisor.

[![](multitasking-images/about02.png "O modo divisão")](multitasking-images/about02.png#lightbox)

Como o Slide sobre o usuário decide quais dois aplicativos serão executados lado a lado e novamente, o desenvolvedor não tem controle sobre esse processo. Como resultado, o modo divisão coloca requisitos semelhantes em um aplicativo xamarin. IOS:

- **Usar layout automático e Classes de tamanho** — porque o aplicativo xamarin. IOS agora pode ser executado em um modo de tela de divisão em tamanho especificado e do usuário, você não poderá mais usar no dispositivo, seu tamanho de tela ou sua orientação para o layout de sua interface do usuário. Para garantir que seu aplicativo dimensiona sua interface corretamente, você precisará usar layout automático e Classes de tamanho. Para obter mais informações, consulte nosso [Introdução ao Storyboards unificados](~/ios/user-interface/storyboards/unified-storyboards.md) documentação.
- **Usar recursos de maneira eficiente** — como seu aplicativo pode agora ser compartilhando o sistema com outro aplicativo em execução, é essencial que seu aplicativo usa recursos do sistema com eficiência. Quando a memória se torna esparsa, o sistema automaticamente encerrará o aplicativo que está consumindo mais memória. Consulte da Apple [guia de eficiência de energia para aplicativos iOS](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) para obter mais detalhes.

Para saber mais sobre como preparar seu aplicativo para o modo divisão, consulte da Apple [adotando os aprimoramentos de multitarefa no iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145) documentação.

<a name="Picture-in-Picture" />

### <a name="picture-in-picture"></a>Picture in Picture

A nova imagem no recurso de imagem (também conhecido como _PIP_) permite que o usuário assistir a um vídeo em uma janela flutuante pequeno que o usuário pode posicionar em qualquer lugar na tela acima de outros aplicativos em execução.

[![](multitasking-images/about03.png "Um exemplo de imagem na janela flutuante de imagem")](multitasking-images/about03.png#lightbox)

Como com Deslizar ao longo e modo de exibição de divisão, o usuário tem controle total sobre assistir a um vídeo em uma imagem no modo de imagem. Se a função de principal do seu aplicativo é assistir a vídeo, será necessário algumas modificações para se comportar corretamente no modo PIP. Caso contrário, nenhuma alteração é necessária para dar suporte ao PIP.

Para seu aplicativo exibir o vídeo PIP mediante solicitação do usuário, você precisará estar usando um _AVKit_ ou o _AV Foundation APIs_. A estrutura de Player de mídia tem sido depreciada no iOS 9 e não suporta o PIP.

A imagem na imagem está disponível somente em um iPad Pro, iPad Air, iPad Air 2, iPad 2 Mini, iPad Mini 3 ou 4 Mini do iPad. Para obter mais informações, consulte nosso [aplicativo de exemplo PictureInPicture](https://developer.xamarin.com/samples/ios/iOS9/) da Apple [imagem no início rápido da imagem](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/QuickStartForPictureInPicture.html#//apple_ref/doc/uid/TP40015145-CH14) documentação.

<a name="Supporting-Multitasking-in-your-App" />

## <a name="supporting-multitasking-in-your-app"></a>Suporte a multitarefa em seu aplicativo

Para qualquer aplicativo existente do xamarin. IOS, que dão suporte a multitarefa é uma tarefa transparente, desde que seu aplicativo já segue os guias de design da Apple e práticas recomendadas para o iOS 8. Isso significa que o aplicativo deve usar storyboards com layout automático e Classes de tamanho para seus layouts de Interface do usuário (consulte nosso [Introdução ao Storyboards unificados](~/ios/user-interface/storyboards/unified-storyboards.md) para obter mais informações).

Para esses aplicativos, pouca ou nenhuma alteração é necessárias para dar suporte à multitarefa e se comportem bem dentro dele. Se seu aplicativo a interface do usuário, foi criado usando outros métodos, como diretamente posicionar e dimensionar elementos de interface do usuário no C# de código ou se ele se baseia em tamanhos de tela do dispositivo específico ou orientações, ele precisará ser modificada significativa para dar suporte a iOS 9 multitarefa corretamente.

Para dar suporte a iOS 9 multitarefa em qualquer novo aplicativo xamarin. IOS, novamente usar storyboards com layout automático e Classes de tamanho para todos os layouts de Interface do usuário do aplicativo e implementar as instruções nas seções a seguir.

<a name="Screen-Size-Considerations" />

### <a name="screen-size-and-orientation-considerations"></a>Tamanho da tela e considerações de orientação

Antes do iOS 9, você poderia projetar seu aplicativo em tamanhos de tela do dispositivo específico e orientações. Porque um aplicativo agora pode ser executado em um painel de Slide Out ou no modo de exibição de divisão, ele pode se localizar executado em uma classe de tamanho horizontal do compact ou regular no iPad, independentemente do tamanho físico de orientação ou a tela do dispositivo.

[![](multitasking-images/sizeclasses01.png "Tamanho da tela e considerações de orientação")](multitasking-images/sizeclasses01.png#lightbox)

Em um iPad, um aplicativo de tela inteira tem Classes regulares horizontal e vertical de tamanho. Todos os iPhones, mas o iPhone 6 Plus e iPhone 6s Plus, ter classes de tamanho compacto em ambas as direções em qualquer orientação. O iPhone 6 Plus e iPhone 6s Plus no modo paisagem, ter uma classe de tamanho Regular horizontal e uma classe de tamanho vertical Compact (muito semelhante um Mini iPad).

Em iPads que dão suporte ao deslizar ao longo e modo de exibição dividido, pode acabar com as seguintes combinações:

| **Orientação** | **Aplicativo primário** | **Aplicativo secundário** |
|--- |--- |--- |
| **Retrato** |75% da tela<br />Compact Horizontal<br />Vertical regular|25% da tela<br />Compact Horizontal<br />Vertical regular|
| **Paisagem** |75% da tela<br />Horizontal regular<br />Vertical regular|25% da tela<br />Compact Horizontal<br />Vertical regular|
| **Paisagem** |50% da tela<br />Compact Horizontal<br />Vertical regular|50% da tela<br />Compact Horizontal<br />Vertical regular|

No exemplo [MuliTask](https://developer.xamarin.com/samples/monotouch/ios9/MultiTask/) aplicativo, se ele é executado a tela inteira em um iPad no modo paisagem, ele apresentará a lista e a exibição de detalhes ao mesmo tempo:

[![](multitasking-images/sizeclasses03.png "A lista e o modo de exibição de detalhes apresentados ao mesmo tempo")](multitasking-images/sizeclasses03.png#lightbox)

Se o mesmo aplicativo é executado em um painel de Deslizar ao longo, ele é apresentado como uma classe de tamanho Horizontal Compact e exibe apenas a lista:

[![](multitasking-images/sizeclasses04.png "Somente a lista apresentada quando o dispositivo é horizontal")](multitasking-images/sizeclasses04.png#lightbox)

Para garantir que seu aplicativo se comporta corretamente nessas situações, você deve adotar as coleções de característica junto com as Classes de tamanho e estar de acordo com o `IUIContentContainer` e `IUITraitEnvironment` interfaces. Consulte da Apple [referência de classe UITraitCollection](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITraitCollection_ClassReference/index.html#//apple_ref/doc/uid/TP40014202) e em nosso [Introdução ao Storyboards unificados](~/ios/user-interface/storyboards/unified-storyboards.md) guia para obter mais informações.

Além disso, você não pode contar com os limites da tela de dispositivos para definir a área visível do aplicativo, você precisará usar limites de janela do seu aplicativo em vez disso. Como os limites da janela são totalmente sob o controle do usuário, por meio de programação não é possível ajustá-los ou impedir que o usuário altere esses limites.

Por fim, seu aplicativo deve usar um arquivo de storyboard para apresentar sua tela de inicialização em vez de usar um conjunto de **PNG** arquivos de imagem e dar suporte a todas as orientações de interface de quatro (retrato, retrato de cabeça para baixo, esquerda paisagem e paisagem direita) para ser considerado para em execução em um Slide sobre painel ou no modo de exibição de divisão.

<a name="Custom-Hardware-Keyboard-Shortcuts" />

### <a name="custom-hardware-keyboard-shortcuts"></a>Atalhos de teclado de Hardware personalizado

No iOS 9 em execução em um iPad, Apple tem o suporte estendido para teclados de hardware. iPads sempre ter incluído o suporte básico de teclado externo via Bluetooth e alguns teclados de fabricantes criados de teclado que incluídos cabeadas chaves do iOS específicas.

Agora, com iOS 9, aplicativos podem criar seus próprios atalhos de teclado personalizados. Além disso, alguns atalhos de teclado básicas estão disponíveis como **comando-C** (cópia), **comando-X** (Recortar) **comando V** (colar) e **comando-Shift-H**  (home), sem um aplicativo que está sendo especificamente escrita responda a eles.

**Guia de comandos** abrirá um alternador de aplicativo que permite ao usuário alternar rapidamente entre aplicativos usando o teclado, assim como o Mac OS:

[![](multitasking-images/keyboard01.png "O seletor de aplicativo")](multitasking-images/keyboard01.png#lightbox)

Se um aplicativo iOS 9 inclui os atalhos de teclado, o usuário pode manter pressionada **comando**, **opção** ou **controle** chaves para exibi-las em um pop-up:

[![](multitasking-images/keyboard02.png "O pop-up de atalhos de teclado")](multitasking-images/keyboard02.png#lightbox)

#### <a name="defining-custom-keyboard-shortcuts"></a>Definir atalhos de teclado personalizados

Se adicionar o código a seguir para uma exibição ou controlador de exibição em nosso aplicativo, quando esse modo de exibição ou controlador estiver visível, um atalho de teclado personalizados estarão disponível:

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

Primeiro, podemos substituir o `CanBecomeFirstResponder` propriedade e retorno `true` para que a exibição ou controlador de exibição possa receber entrada do teclado. 

Em seguida, substituímos o `KeyCommands` propriedade e crie um novo `UIKeyCommand` para o **comando N** pressionamento de tecla. Quando o pressionamento de tecla é ativado, podemos chamar o `NewEntry` método (que é expor ao iOS 9 usando o `Export` comando) para executar a ação solicitada.

Se executarmos esse aplicativo em um iPad com um teclado de hardware conectado e o usuário digita **comando N**, uma nova entrada será adicionada à lista. Se o usuário mantiver pressionada **comando** chave, a lista de atalhos será exibida:

[![](multitasking-images/keyboard03.png "O pop-up de atalhos de teclado")](multitasking-images/keyboard03.png#lightbox)

Consulte a amostra [MultiTask aplicativo](http://developer.xamarin.com/samples/monotouch/ios9/MultiTask/) para um exemplo de implementação.

<a name="Resource-Management-Considerations" />

### <a name="resource-management-considerations"></a>Considerações de gerenciamento de recursos

Até mesmo para aplicativos que já estão usando guias de design e práticas recomendadas de iOS 8, o gerenciamento eficiente de recursos ainda pode ser um problema. No iOS 9, os aplicativos não têm uso exclusivo de memória, CPU ou outros recursos do sistema.

Como resultado, você deve ajustar seu aplicativo xamarin. IOS para usar recursos do sistema com eficiência ou ela fique voltada para encerramento em situações de pouca memória. Isso também é verdadeiro de aplicativos que se recusar a execução multitarefa, desde um segundo aplicativo pode ser executado em um painel Deslizar ao longo ou uma imagem na janela de imagem que exigem recursos extras ou fazendo com que a taxa de atualização esteja abaixo de 60 quadros por segundo.

Considere as seguintes ações de usuário e suas implicações:

- **Inserindo texto em um painel de Slide sobre** -mesmo que seu aplicativo não tem nenhuma entrada de texto, o teclado de sistema agora pode ser exibido ao longo de sua interface do usuário. Como resultado, o aplicativo pode precisar responder a notificações de exibição de teclado (por exemplo, mostrando e ocultando o teclado).
- **Executando um aplicativo de segundo em um painel de Slide sobre** -o novo aplicativo agora está em execução em primeiro plano e competindo com o aplicativo existente para recursos do sistema, como memória e ciclos de CPU.
- **Reproduzir um vídeo em uma janela de PIP** – não apenas pode esta janela abranger parte da interface do seu aplicativo, mas o aplicativo que iniciou o vídeo ainda está em execução em segundo plano e consumo de recursos de CPU e memória.

Para garantir que seu aplicativo está usando recursos com eficiência, você deve fazer o seguinte:

- **Criar o perfil de aplicativo com o instrumentos** -Verifique se há vazamentos de memória, explícitos de uso da CPU e áreas em que o aplicativo pode estar bloqueando o thread principal.
- **Responder aos métodos de transições de estado** - no seu **AppDelegate.cs** substituição de arquivo e a resposta para o estado de alterar os métodos, como o aplicativo inserindo ou retornando do plano de fundo. Libere qualquer desnecessários ativos como imagens, dados ou modos de exibição e controlador de exibição.
- **Testar lado a lado com aplicativos com uso intensivo de memória** - executar seu aplicativo usando o Slide Out e exibição de divisão no hardware físico com iOS com um aplicativo com uso intensivo de memória, como mapas (no modo de exibição de satélite) e que ambos os aplicativos permaneçam responsivos e causa a falha de teste.

Consulte da Apple [guia de eficiência de energia para aplicativos iOS](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) para obter mais informações sobre o gerenciamento de recursos.

<a name="Opting-Out-of-Multitasking" />

## <a name="opting-out-of-multitasking"></a>Recusando a multitarefa

Enquanto a Apple sugere que todos os aplicativos do iOS 9 dá suporte a multitarefa, pode haver motivos muito específicos para um aplicativo não é muito, como jogos ou aplicativos de câmera que exigem a tela inteira para funcionar corretamente.

Para o aplicativo xamarin. IOS recusar a que está sendo executado em qualquer um Slide Out painel ou no modo de exibição de divisão, edição do projeto **Info. plist** do arquivo e verifique **requer tela inteira**:

[![](multitasking-images/fullscreen01.png "Recusando a multitarefa")](multitasking-images/fullscreen01.png#lightbox)

> [!IMPORTANT]
> Enquanto a recusa de multitarefa impede que seu aplicativo está sendo executado em um Slide Out ou o modo divisão, ele não impede que outro aplicativo sendo executado no Slide Out ou uma imagem na imagem de vídeo sejam exibidos junto com seu aplicativo.

<a name="Disabling-PIP-Video-Playback" />

### <a name="disabling-pip-video-playback"></a>Desabilitando a reprodução de vídeo de PIP

Na maioria das situações, seu aplicativo deve permitir que o usuário executar qualquer conteúdo de vídeo que ele exibe em uma imagem na janela flutuante da imagem. No entanto, pode haver situações em que isso pode não ser desejado, como vídeos de jogo cena de recorte.

Para recusar a reprodução de vídeo do PIP, faça o seguinte em seu aplicativo:

 - Se você estiver usando um `AVPlayerViewController` para exibir o vídeo, defina a `AllowsPictureInPicturePlayback` propriedade `false`.
 - Se você estiver usando o `AVPlayerLayer` para exibir o vídeo, não instancie um `AVPictureInPictureController`.
 - Se você estiver usando um `WKWebView` para exibir o vídeo, defina a `AllowsPictureInPictureMediaPlayback` propriedade `false`.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou as etapas necessárias para garantir que um aplicativo xamarin. IOS serão executados e se comportem corretamente no iOS 9, a nova capacidade de multitarefa para iPads. Além disso, ele abordou a aceitar-out de multitarefa para aplicativos em que não é uma boa opção.



## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Multitarefa (amostra)](https://developer.xamarin.com/samples/monotouch/ios9/MultiTask/)
- [Introdução ao Storyboards unificadas](~/ios/user-interface/storyboards/unified-storyboards.md)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Adotar melhorias de multitarefa no iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)
- [Postagem de blog](https://blog.xamarin.com/using-auto-layouts-for-ios-9-splitview/)
