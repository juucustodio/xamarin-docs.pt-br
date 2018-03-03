---
title: "Imagens e ícones"
description: "Esta seção inclui uma variedade de artigos que abrangem a trabalhar com imagens em um aplicativo xamarin, como usá-los como ícones, inicie telas ou incluindo-os em controles e fornecer ícones para tipos de documento personalizadas."
ms.topic: article
ms.prod: xamarin
ms.assetid: 0AB8CC07-11E4-0D75-4119-AED1A1252424
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 904ba5db84101651d10605fadf8e8861db0ddc1f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="images-and-icons"></a>Imagens e ícones

_Esta seção inclui uma variedade de artigos que abrangem a trabalhar com imagens em um aplicativo xamarin, como usá-los como ícones, inicie telas ou incluindo-os em controles e fornecer ícones para tipos de documento personalizadas._

Há várias maneiras de imagem ativos são usados dentro de um aplicativo iOS. De simplesmente exibir uma imagem como parte da interface do usuário do aplicativo, atribuí-la a um controle de interface do usuário, como um `UIButton` ou `UIImageView`, ao fornecer ícones e telas de inicialização, xamarin torna mais fácil adicionar arte grande para um aplicativo iOS das seguintes maneiras: 

- **Imagens de resolução independente** – Use o suporte interno do iOS para trabalhar com imagens em resoluções de diferentes dispositivos e tipos (iPhone, iPad, etc.).
- **Conjuntos de imagem do catálogo de ativos** -Use **conjuntos de imagem do catálogo de ativos** para gerenciar e agrupar todos os versão de um ativo de imagem exigido por um aplicativo.
- **Imagens no iOS Designer** -usar o Designer do iOS para definir imagens para controles.
- **Imagens em código** – Use o `UIImage` métodos da classe para carregar e trabalhar com os ativos de imagem e atribuí-los a controles de interface do usuário em código c#.
- **Ícone do aplicativo** -definir o ícone de aplicativo necessário para cada aplicativo iOS. Esse é o ícone que o usuário será tocar na tela inicial do iOS para iniciar o aplicativo. Além disso, esse ícone é usado pelo Centro de jogo, se aplicável.
- **Ícone de destaque** -definir ícone de destaque do aplicativo. Sempre que o usuário insere o nome de um aplicativo em uma pesquisa do Spotlight, esse ícone é exibido.
- **Ícone de configurações** -definir o aplicativo **configurações** ícone. Se o usuário insere o **configurações** aplicativo em seu dispositivo iOS, esse ícone será exibido no final da lista de configurações para o aplicativo. 
- **Iniciar telas** -definir a tela de inicialização do aplicativo. Depois que o usuário toca o ícone do aplicativo e antes da primeira exibição aparece, uma tela em branco será mostrada. Felizmente, o iOS inclui suporte para exibir uma imagem em vez da tela em branco usando um Storyboard. 
- **Ícone do iTunes** -forneça um ícone iTune. Se usar o método Ad Hoc de fornecimento de um aplicativo (para usuários corporativos ou de teste beta dispositivos reais), o desenvolvedor precisa também incluem um 512 x 512 e uma imagem de 1024 x 1024 que será usada para representar o aplicativo na iTunes.
- **Ícones de documento** -usar uma imagem como um ícone para qualquer tipo de documento específico que um aplicativo xamarin dá suporte a ou cria.

Há várias considerações que devem ser levadas em conta ao criar ativos de imagem para um aplicativo iOS, bem como vários lugares onde esses ativos serão usados. Cada um deles tem um impacto sobre não apenas o número de ativos de imagem, será necessários, mas como esses ativos são criados. Os tópicos a seguir abordam os tipos de ativos de imagens que serão necessários, como esses ativos são incluídos no pacote do aplicativo e como os ativos de imagem são consumidos para fornecer a funcionalidade necessária:


## <a name="displaying-an-imageiosapp-fundamentalsimages-iconsdisplaying-an-imagemd"></a>[Exibir uma imagem](~/ios/app-fundamentals/images-icons/displaying-an-image.md)

Este artigo aborda incluindo um ativo de imagem em um aplicativo xamarin e exibir essa imagem, usando o código c# ou atribuindo a ele a um controle no Designer de iOS.

## <a name="application-iconsiosapp-fundamentalsimages-iconsapp-iconsmd"></a>[Ícones de aplicativo](~/ios/app-fundamentals/images-icons/app-icons.md)

Cobre este artigo incluindo e gerenciando um ativo de imagem em um aplicativo xamarin para ser usado como um ícone de aplicativo.

## <a name="alternate-app-iconsiosapp-fundamentalsimages-iconsalternate-app-iconsmd"></a>[Ícones de aplicativo alternativo](~/ios/app-fundamentals/images-icons/alternate-app-icons.md)

Apple tenha adicionado vários aprimoramentos para o iOS 10.3 que permitem que um aplicativo gerenciar seu ícone:

 - `ApplicationIconBadgeNumber` -Obtém ou define a notificação do ícone de aplicativo no Springboard.
 - `SupportsAlternateIcons` -Se `true` o aplicativo tem um conjunto alternativo de ícones.
 - `AlternateIconName` -Retorna o nome do ícone alternativo selecionado no momento ou `null` se usando o ícone do primário.
 - `SetAlternameIconName` -Use esse método para alternar o ícone do aplicativo para o ícone alternativo fornecido.


## <a name="launch-screensiosapp-fundamentalsimages-iconslaunch-screensmd"></a>[Inicie a telas](~/ios/app-fundamentals/images-icons/launch-screens.md)

Este artigo aborda o uso de um tipo especial de Storyboard para fornecer uma tela de inicialização universal para cada tamanho de dispositivo do iOS e a resolução.

## <a name="custom-document-typesiosapp-fundamentalsimages-iconscustom-document-typesmd"></a>[Tipos de documento personalizadas](~/ios/app-fundamentals/images-icons/custom-document-types.md)

Cobre este artigo incluindo e gerenciando um ativo de imagem em um aplicativo xamarin para ser usado como um ícone de tipo de documento personalizadas.



## <a name="related-links"></a>Links relacionados

- [Trabalhando com imagens (exemplo)](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Ícone personalizado e diretrizes de criação de imagem](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
