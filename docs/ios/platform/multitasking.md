---
title: Multitarefa para iPad
description: "iOS 9 dá suporte a dois aplicativos em execução ao mesmo tempo, usando o slide ou modo divisão. Ele também dá suporte a imagem na imagem de reprodução de vídeo."
ms.topic: article
ms.prod: xamarin
ms.assetid: 0F2266D7-21FF-404D-A148-0CFDE76B12AA
ms.technology: xamarin-ios
ms.custom: xamu-video
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 39c699b10280218223b6f6022d419f77aba875dc
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="multitasking-for-ipad"></a>Multitarefa para iPad

_iOS 9 dá suporte a dois aplicativos em execução ao mesmo tempo, usando o slide ou modo divisão. Ele também dá suporte a imagem na imagem de reprodução de vídeo._

![](multitasking-images/about02-sml.png "Exemplo de tela de dividir") ![ ] (multitasking-images/about03-sml.png "imagem na imagem exemplo")

iOS 9 adiciona suporte de multitarefa para dois aplicativos em execução ao mesmo tempo no iPad específicos de hardware. Multitarefa para iPad é suportada por meio dos seguintes recursos:

- [**Slide sobre** ](#Slide-Over) -permite que o usuário executar um aplicativo iOS segundo temporariamente em um slide out painel (ou no lado esquerdo ou direito da tela com base na direção de idioma) que abrange aproximadamente 25% do aplicativo principal em execução no momento. Slide sobre só está disponível em um iPad Pro, iPad ar, iPad 2 ar, iPad 2 Mini, iPad Mini 3 ou iPad Mini 4.
- [**Modo divisão** ](#Split-View) -em hardware de iPad com suporte (iPad 2 ar, iPad Mini 4 e iPad Pro somente), o usuário pode escolher um segundo aplicativo e executá-lo lado a lado com o aplicativo em execução no momento em um modo de tela de divisão. O usuário pode controlar o percentual da tela principal que ocupa de cada aplicativo.
- [**Figura Figura** ](#Picture-in-Picture) - para aplicativos que reproduzir conteúdo de vídeo, o vídeo pode ser executado em uma janela móvel e redimensionada que flutua sobre os outros aplicativos em execução no dispositivo iOS. O usuário tem controle total sobre o tamanho e a posição da janela. Imagem na imagem só está disponível em um iPad Pro, iPad ar, iPad 2 ar, iPad 2 Mini, iPad Mini 3 ou iPad Mini 4.

Há várias coisas a considerar quando [suporte multitarefas em seu aplicativo](#Supporting-Multitasking-in-your-App), incluindo:

- [Tamanho da tela e orientação](#Screen-Size-Considerations)
- [Atalhos de teclado de Hardware personalizado](#Custom-Hardware-Keyboard-Shortcuts)
- [Gerenciamento de recursos](#Resource-Management-Considerations)

Como desenvolvedor de aplicativos também é possível [recusar multitarefa](#Opting-Out-of-Multitasking), incluindo [desabilitando a reprodução de vídeo de PIP](#Disabling-PIP-Video-Playback).

Este artigo aborda as etapas necessárias para garantir que seu aplicativo xamarin funciona corretamente em um ambiente de multitarefa ou como recusar multitarefa se ele não é uma boa opção para seu aplicativo.

> [!VIDEO https://youtube.com/embed/GctYAozoLr8]

**Multitarefa para iPad, pelo [University Xamarin](https://university.xamarin.com)**


<a name="Multitasking-QuickStart" />

## <a name="multitasking-quickstart"></a>Início rápido de multitarefa

Para dar suporte a **Slide sobre** ou **modo divisão** seu aplicativo deve fazer o seguinte:

 - Ser desenvolvido com iOS 9 (ou superior).
 - Use um Storyboard para a tela Iniciar (e não ativos de imagem).
 - Use um Storyboard com Autolayout e Classes de tamanho de sua interface do usuário.
 - Suporte a todos os 4 iOS dispositivo orientações (retrato, invertidos retrato, paisagem esquerda e direita paisagem).

<a name="Multitasking" />

## <a name="about-multitasking-for-ipad"></a>Sobre multitarefa para iPad

iOS 9 oferece novas habilidades de multitarefa no iPad com a introdução de _Slide sobre_, _modo divisão_ (iPad 2 ar, iPad Mini 4 e iPad Pro apenas) e _imagem imagem_. Vamos dar uma Visão aprofundada esses recursos nas seções a seguir.

<a name="Slide-Over" />

### <a name="slide-over"></a>Slide sobre

O recurso de Slide sobre permite que o usuário selecione um segundo aplicativo e exibi-lo em um painel deslizante pequeno para fornecer a interação rápida. O painel de Slide sobre é temporário e será fechada quando o usuário voltar a trabalhar com o aplicativo principal novamente.

[![](multitasking-images/about01.png "O painel de Slide sobre")](multitasking-images/about01.png#lightbox)

O ponto principal a ser lembrado é que o usuário decide qual dois aplicativos serão executados lado a lado e que o desenvolvedor não tem controle sobre esse processo. Como resultado, há algumas coisas que você precisará fazer para garantir que seu aplicativo xamarin seja executado corretamente em um painel de Slide sobre:

- **Use Classes de tamanho e Autolayout** — porque seu aplicativo xamarin pode agora ser executado no painel do lado de fora do slide, você não poderá mais usar o dispositivo, seu tamanho de tela ou sua orientação para layout sua interface do usuário. Para garantir que seu aplicativo redimensiona sua interface corretamente, você precisará usar Autolayout e Classes de tamanho. Para obter mais informações, consulte nosso [Introdução ao Unified Storyboards](~/ios/user-interface/storyboards/unified-storyboards.md) documentação.
- **Use recursos com eficiência** — como seu aplicativo pode agora ser compartilhando o sistema com outro aplicativo em execução, é fundamental que o seu aplicativo usa recursos do sistema com eficiência. Quando a memória se torna esparsa, o sistema terminará automaticamente o aplicativo que está consumindo mais memória. Consulte da Apple [guia de eficiência de energia para aplicativos iOS](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) para obter mais detalhes.

Slide sobre só está disponível em um iPad Pro, iPad ar, iPad 2 ar, iPad 2 Mini, iPad Mini 3 ou iPad Mini 4. Para saber mais sobre como preparar seu aplicativo para Slide sobre, consulte da Apple [adotando aprimoramentos de multitarefa no iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145) documentação.

<a name="Split-View" />

### <a name="split-view"></a>Modo Divisão

No hardware de iPad com suporte (iPad 2 ar, iPad Mini 4 e iPad Pro somente), o usuário pode escolher um segundo aplicativo e executá-lo lado a lado com o aplicativo em execução no momento em um modo de tela de divisão. O usuário pode controlar o percentual de tela principal de que cada aplicativo ocupa arrastando uma tela divisor.

[![](multitasking-images/about02.png "O modo de exibição de divisão")](multitasking-images/about02.png#lightbox)

Como o Slide sobre o usuário decide qual dois aplicativos serão executados lado a lado e novamente, o desenvolvedor não tem controle sobre esse processo. Como resultado, o modo divisão coloca requisitos semelhantes em um aplicativo xamarin:

- **Use Classes de tamanho e Autolayout** — porque seu aplicativo xamarin agora pode ser executado em um modo de tela de divisão no tamanho especificado do usuário, você não poderá mais usar o dispositivo, seu tamanho de tela ou sua orientação para layout sua interface do usuário. Para garantir que seu aplicativo redimensiona sua interface corretamente, você precisará usar Autolayout e Classes de tamanho. Para obter mais informações, consulte nosso [Introdução ao Unified Storyboards](~/ios/user-interface/storyboards/unified-storyboards.md) documentação.
- **Use recursos com eficiência** — como seu aplicativo pode agora ser compartilhando o sistema com outro aplicativo em execução, é fundamental que o seu aplicativo usa recursos do sistema com eficiência. Quando a memória se torna esparsa, o sistema terminará automaticamente o aplicativo que está consumindo mais memória. Consulte da Apple [guia de eficiência de energia para aplicativos iOS](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) para obter mais detalhes.

Para saber mais sobre como preparar seu aplicativo para o modo de exibição de divisão, consulte da Apple [adotando aprimoramentos de multitarefa no iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145) documentação.

<a name="Picture-in-Picture" />

### <a name="picture-in-picture"></a>Figura Figura

A nova imagem no recurso de imagem (também conhecido como _PIP_) permite que o usuário assistir a um vídeo em uma janela flutuante pequeno que o usuário pode posicionar em qualquer lugar na tela acima de outros aplicativos em execução.

[![](multitasking-images/about03.png "Um exemplo de imagem na janela flutuante de imagem")](multitasking-images/about03.png#lightbox)

Como com o Slide sobre e modo de divisão, o usuário tem controle total sobre assistir um vídeo em uma imagem no modo de imagem. Se for Assista ao vídeo função principal do seu aplicativo, será necessário algumas modificações se comportem corretamente no modo PIP. Caso contrário, nenhuma alteração é necessária para dar suporte ao PIP.

Para seu aplicativo exibir o vídeo PIP mediante solicitação do usuário, você precisará estar usando um _AVKit_ ou _AV Foundation APIs_. A estrutura de Player de mídia tem foi depreciada no iOS 9 e não oferece suporte a PIP.

Imagem na imagem só está disponível em um iPad Pro, iPad ar, iPad 2 ar, iPad 2 Mini, iPad Mini 3 ou iPad Mini 4. Para obter mais informações, consulte nosso [aplicativo de exemplo PictureInPicture](https://developer.xamarin.com/samples/ios/iOS9/) da Apple [imagem no início rápido da imagem](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/QuickStartForPictureInPicture.html#//apple_ref/doc/uid/TP40015145-CH14) documentação.

<a name="Supporting-Multitasking-in-your-App" />

## <a name="supporting-multitasking-in-your-app"></a>Dando suporte a multitarefa em seu aplicativo

Para qualquer aplicativo xamarin existente, dando suporte a multitarefa é uma tarefa transparente, desde que o aplicativo já segue os guias de design da Apple e práticas recomendadas para iOS 8. Isso significa que o aplicativo deve usar storyboards com Autolayout e Classes de tamanho para seus layouts de Interface do usuário (consulte nossa [Introdução ao Unified Storyboards](~/ios/user-interface/storyboards/unified-storyboards.md) para obter mais informações).

Para esses aplicativos, pouca ou nenhuma alteração é necessárias para dar suporte a multitarefa e se comportam bem dentro dele. Se a interface do usuário do seu aplicativo, foi criado usando outros métodos, como posicionamento diretamente e elementos de interface do usuário no código c# ou se ele se baseia em tamanhos de tela do dispositivo específico ou orientações de dimensionamento, será necessário modificação significativa para dar suporte ao iOS 9 multitarefa corretamente.

Para oferecer suporte a iOS 9 multitarefa em qualquer novo aplicativo xamarin, novamente usar storyboards com Autolayout e Classes de tamanho para todos os layouts de Interface do usuário do aplicativo e implementar as instruções nas seções a seguir.

<a name="Screen-Size-Considerations" />

### <a name="screen-size-and-orientation-considerations"></a>Tamanho da tela e considerações de orientação

Antes de iOS 9, você pode projetar seu aplicativo em tamanhos de tela do dispositivo específico e orientações. Como um aplicativo agora pode ser executado em um painel de Slide Out ou no modo de exibição de divisão, ele pode encontrar se executado em uma classe de tamanho horizontal compact ou regulares no iPad, independentemente do tamanho físico de orientação ou de tela do dispositivo.

[![](multitasking-images/sizeclasses01.png "Tamanho da tela e considerações de orientação")](multitasking-images/sizeclasses01.png#lightbox)

Em um iPad, um aplicativo de tela inteira tem Classes regulares de tamanho horizontal e vertical. Todos os iPhones, mas o iPhone 6 Plus e iPhone 6s Plus, ter classes compacto em ambas as direções em qualquer orientação. O iPhone 6 Plus e iPhone 6s Plus no modo paisagem têm uma classe de tamanho horizontal Regular e uma classe de tamanho vertical Compact (muito semelhante um iPad Mini).

Em iPads que dão suporte a Slide sobre e modo divisão, você pode terminar com as seguintes combinações:

| **Orientação** | **Aplicativo primário** | **Aplicativo secundário** |
|--- |--- |--- |
| **Retrato** |75% da tela<br />Compact Horizontal<br />Regular Vertical|25% da tela<br />Compact Horizontal<br />Regular Vertical|
| **Landscape** |75% da tela<br />Regular Horizontal<br />Regular Vertical|25% da tela<br />Compact Horizontal<br />Regular Vertical|
| **Landscape** |50% da tela<br />Compact Horizontal<br />Regular Vertical|50% da tela<br />Compact Horizontal<br />Regular Vertical|

O exemplo [MuliTask](https://developer.xamarin.com/samples/monotouch/ios9/MultiTask/) aplicativo, se a tela inteira é executado em um iPad no modo paisagem, ele apresentará a lista e o modo de exibição de detalhes ao mesmo tempo:

[![](multitasking-images/sizeclasses03.png "A lista e o modo de exibição de detalhes apresentados ao mesmo tempo")](multitasking-images/sizeclasses03.png#lightbox)

Se o mesmo aplicativo é executado em um painel de Slide sobre, ele é apresentado como uma classe de tamanho Horizontal Compact e exibe somente a lista:

[![](multitasking-images/sizeclasses04.png "Somente a lista exibida quando o dispositivo é horizontal")](multitasking-images/sizeclasses04.png#lightbox)

Para garantir que seu aplicativo se comporta corretamente nessas situações, você deve adotar coleções característica junto com as Classes de tamanho e estar de acordo com o `IUIContentContainer` e `IUITraitEnvironment` interfaces. Consulte da Apple [referência de classe UITraitCollection](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITraitCollection_ClassReference/index.html#//apple_ref/doc/uid/TP40014202) e nossa [Introdução ao Unified Storyboards](~/ios/user-interface/storyboards/unified-storyboards.md) guia para obter mais informações.

Além disso, você não pode contar com os limites de tela de dispositivos para definir a área visível do aplicativo, você precisará usar os limites da janela do aplicativo em vez disso. Como os limites de janela são totalmente sob o controle do usuário, por meio de programação não é possível ajustá-las ou impedir que o usuário altere esses limites.

Por fim, seu aplicativo deve usar um arquivo de storyboard para apresentar a tela Iniciar, em vez de usar um conjunto de **. PNG** arquivos de imagem e dar suporte a todas as orientações de quatro interface (retrato, invertidos retrato, paisagem esquerda e direita paisagem) para ser considerado para a execução em um painel de Slide sobre ou no modo de exibição de divisão.

<a name="Custom-Hardware-Keyboard-Shortcuts" />

### <a name="custom-hardware-keyboard-shortcuts"></a>Atalhos de teclado de Hardware personalizado

No iOS 9 em execução em um iPad, Apple tem o suporte estendido para o teclado de hardware. iPads sempre incluíam suporte básico de teclado externo via Bluetooth e alguns teclados fabricantes criados de teclado que incluídas chaves físicas de específico do iOS.

Agora, com iOS 9, aplicativos podem criar seus próprios atalhos de teclado personalizados. Além disso, alguns atalhos de teclado básicas estão disponíveis como **comando-C** (cópia), **comando X** (Recortar) **comando V** (cole) e **comando-Shift-H**  (base), sem um aplicativo que está sendo especificamente escrito responder a eles.

**Guia comando** abrirá um alternador de aplicativo que permite ao usuário alternar rapidamente entre aplicativos do teclado, assim como o Mac OS:

[![](multitasking-images/keyboard01.png "O seletor de exibição do aplicativo")](multitasking-images/keyboard01.png#lightbox)

Se um aplicativo iOS 9 inclui atalhos de teclado, o usuário pode manter pressionada **comando**, **opção** ou **controle** chaves para exibi-los em um pop-up:

[![](multitasking-images/keyboard02.png "O pop-up atalhos de teclado")](multitasking-images/keyboard02.png#lightbox)

#### <a name="defining-custom-keyboard-shortcuts"></a>Definindo os atalhos de teclado personalizados

Se adicionar o código a seguir para um controlador de exibição ou exibição em nosso aplicativo, quando esse modo de exibição ou controlador estiver visível, um atalho de teclado personalizado estará disponível:

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

Primeiro, podemos substituir o `CanBecomeFirstResponder` propriedade e retornar `true` para a exibição ou exibição controlador pode receber entrada do teclado. 

Em seguida, podemos substituir o `KeyCommands` propriedade e criar um novo `UIKeyCommand` para o **comando N** pressionamento de tecla. Quando o pressionamento de tecla é ativado, podemos chamar o `NewEntry` método (que é expor ao iOS 9 usando o `Export` comando) para executar a ação solicitada.

Se executarmos esse aplicativo em um iPad com um teclado de hardware conectados e os tipos de usuário **comando N**, uma nova entrada será adicionada à lista. Se o usuário mantiver pressionada **comando** chave, a lista de atalhos será exibida:

[![](multitasking-images/keyboard03.png "O pop-up atalhos de teclado")](multitasking-images/keyboard03.png#lightbox)

Consulte o exemplo [MultiTask aplicativo](http://developer.xamarin.com/samples/monotouch/ios9/MultiTask/) para um exemplo de implementação.

<a name="Resource-Management-Considerations" />

### <a name="resource-management-considerations"></a>Considerações sobre gerenciamento de recursos

Mesmo para aplicativos que já estão usando guias de design e práticas recomendadas de iOS 8, o gerenciamento eficiente de recursos ainda pode ser um problema. No iOS 9, os aplicativos não têm uso exclusivo de memória, CPU ou outros recursos do sistema.

Como resultado, você deve ajustar seu aplicativo xamarin para usar os recursos do sistema efetivamente ou fica disponível para encerramento em situações de memória baixa. Isso é verdadeiro igualmente dos aplicativos de multitarefa, desde que um segundo aplicativo pode ser executado em um painel Slide sobre ou uma imagem na janela de imagem que exigem recursos extras ou fazendo com que a taxa de atualização para ficarem abaixo de 60 quadros por segundo.

Considere as seguintes ações de usuário e suas implicações:

- **Inserindo texto em um painel de Slide sobre** -mesmo se seu aplicativo não tiver nenhuma entrada de texto, o teclado de sistema agora pode ser exibido em sua interface de usuário. Como resultado, o aplicativo pode precisar responder às notificações de exibição de teclado (por exemplo, mostrando e ocultando o teclado).
- **Executando um aplicativo de segundo em um painel de Slide sobre** -o novo aplicativo agora está em execução em primeiro plano e concorrência com o aplicativo existente para recursos do sistema, como os ciclos de CPU e memória.
- **Reprodução de um vídeo em uma janela de PIP** - não somente pode esta janela cobrir parte da interface do seu aplicativo, mas o aplicativo que iniciou o vídeo ainda está em execução em segundo plano e consumo de recursos de CPU e memória.

Para garantir que seu aplicativo está usando recursos com eficiência, você deve fazer o seguinte:

- **Criar o perfil de aplicativo com instrumentos** -Verifique se há vazamentos de memória, explícitos de uso da CPU e áreas em que o aplicativo pode estar bloqueando o thread principal.
- **Responder aos métodos de transições de estado** - no seu **appdelegate. CS** substituição de arquivo e a resposta para o estado de alterar os métodos, como o aplicativo inserindo ou retornando do plano de fundo. Libere qualquer desnecessárias ativos como imagens, dados ou modos de exibição e controlador de exibição.
- **Testar lado a lado com aplicativos de uso intensivo de memória** - executar seu aplicativo usando o Slide Out e modo de divisão no hardware físico do iOS com um aplicativo de uso intensivo de memória, como mapas (no modo de exibição de satélite) e de teste que ambos os aplicativos continuem respondendo e não falhar.

Consulte da Apple [guia de eficiência de energia para aplicativos iOS](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) para obter mais informações sobre o gerenciamento de recursos.

<a name="Opting-Out-of-Multitasking" />

## <a name="opting-out-of-multitasking"></a>Aceitar-Out de multitarefa

Enquanto Apple sugere que todos os aplicativos do iOS 9 suportam multitarefa, pode haver razões muito específicas para um aplicativo não muito, como jogos ou aplicativos de câmera que exigem a tela inteira para funcionar corretamente.

Para o aplicativo xamarin recusar sendo executado em qualquer um Slide Out painel ou no modo de exibição de divisão, editar o projeto **Info. plist** de arquivos e verificar **requer a tela inteira**:

[![](multitasking-images/fullscreen01.png "Aceitar-Out de multitarefa")](multitasking-images/fullscreen01.png#lightbox)

> [!IMPORTANT]
> **Observação:** enquanto Opting-Out de multitarefa impede que o seu aplicativo está sendo executado no Slide Out ou no modo de divisão, ele faz **não** impedir que outro aplicativo está sendo executado em Slide Out ou uma imagem no vídeo de imagem da exibição junto com seu aplicativo.




<a name="Disabling-PIP-Video-Playback" />

### <a name="disabling-pip-video-playback"></a>Desabilitando a reprodução de vídeo de PIP

Na maioria das situações, seu aplicativo deve permitir que o usuário executar qualquer conteúdo de vídeo, ele exibe em uma imagem na janela flutuante de imagem. No entanto, pode haver situações em que isso pode não ser desejado, como vídeos de jogo cena recortado.

Para sair do PIP de reprodução de vídeo, faça o seguinte em seu aplicativo:

 - Se você estiver usando um `AVPlayerViewController` para exibir o vídeo, defina o `AllowsPictureInPicturePlayback` propriedade `false`.
 - Se você estiver usando o `AVPlayerLayer` para exibir o vídeo, não criar uma instância de um `AVPictureInPictureController`.
 - Se você estiver usando um `WKWebView` para exibir o vídeo, defina o `AllowsPictureInPictureMediaPlayback` propriedade `false`.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou as etapas necessárias para garantir que um aplicativo xamarin serão executados e se comportem corretamente no iOS da 9 nova capacidade de multitarefa para iPads. Além disso, ele coberto aceitar-out de multitarefa para aplicativos em que não é uma boa opção.



## <a name="related-links"></a>Links relacionados

- [Exemplos do iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Multitarefa (exemplo)](http://developer.xamarin.comhttps://developer.xamarin.com/samples/monotouch/ios9/MultiTask/)
- [Introdução ao Storyboards unificadas](~/ios/user-interface/storyboards/unified-storyboards.md)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Adotando multitarefa aprimoramentos no iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)
- [postagem do blog](https://blog.xamarin.com/using-auto-layouts-for-ios-9-splitview/)
