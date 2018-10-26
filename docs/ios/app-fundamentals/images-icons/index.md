---
title: Imagens e ícones no xamarin. IOS
description: Esta seção inclui uma variedade de artigos que abrangem a trabalhar com imagens em um aplicativo xamarin. IOS, como usá-las como ícones, telas de lançamento ou incluindo-os em controles e fornecendo os ícones para tipos de documento personalizados.
ms.prod: xamarin
ms.assetid: 0AB8CC07-11E4-0D75-4119-AED1A1252424
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 09afcac8dcecd5a1d05961c2981c0940fff00a01
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102354"
---
# <a name="images-and-icons-in-xamarinios"></a>Imagens e ícones no xamarin. IOS

_Esta seção inclui uma variedade de artigos que abrangem a trabalhar com imagens em um aplicativo xamarin. IOS, como usá-las como ícones, telas de lançamento ou incluindo-os em controles e fornecendo os ícones para tipos de documento personalizados._

Há várias maneiras de imagem ativos são usados dentro de um aplicativo iOS. De simplesmente exibir uma imagem como parte da interface do usuário um aplicativo, atribuindo-o a um controle de interface do usuário, como um `UIButton` ou `UIImageView`, ao fornecer ícones e telas de inicialização, xamarin. IOS torna mais fácil adicionar arte excelente para um aplicativo iOS das seguintes maneiras: 

- **Imagens independentes de resolução** – usar o suporte interno do iOS para trabalhar com imagens em resoluções de dispositivos diferentes e tipos (iPhone, iPad, etc.).
- **Conjuntos de imagem do catálogo de ativos** -Use **conjuntos de imagem do catálogo de ativos** para gerenciar e agrupar todas as versões de um ativo de determinada imagem exigido por um aplicativo.
- **Imagens no iOS Designer** -usar o Designer do iOS para definir imagens para controles.
- **Imagens no código** – Use o `UIImage` métodos da classe para carregar e trabalhar com ativos de imagem e atribuí-los aos controles de interface do usuário no C# código.
- **Ícone do aplicativo** -definir o ícone do aplicativo necessário para cada aplicativo iOS. Esse é o ícone que o usuário deverá tocar da tela inicial do iOS para iniciar o aplicativo. Além disso, esse ícone é usado pela Central de jogo, se aplicável.
- **Ícone de destaque** -definir o ícone do aplicativo em destaque. Sempre que o usuário insere o nome de um aplicativo em uma pesquisa de destaque, esse ícone é exibido.
- **Ícone de configurações** -definir o aplicativo **configurações** ícone. Se o usuário insere o **configurações** aplicativo em seu dispositivo iOS, esse ícone será exibido no final da lista de configurações para o aplicativo. 
- **Iniciar telas** -definir tela de inicialização do aplicativo. Depois que o usuário toca o ícone do aplicativo e antes da primeira exibição é exibida, uma tela em branco será mostrada. Felizmente, o iOS inclui suporte para exibir uma imagem no lugar da tela em branco usando um Storyboard. 
- **Ícone do iTunes** -forneça um ícone iTune. Se usar o método Ad Hoc do fornecimento de um aplicativo (ou para usuários corporativos ou de teste beta em dispositivos reais), o desenvolvedor também precisa incluir um 512 x 512 e uma imagem de 1024 x 1024 que será usada para representar o aplicativo no iTunes.
- **Ícones de documento** -usar uma imagem como um ícone para qualquer tipo de documento específico que um aplicativo xamarin. IOS dá suporte a ou cria.

Há várias considerações que devem ser levadas em conta ao criar ativos de imagem para um aplicativo iOS, bem como vários lugares onde esses ativos serão usados. Cada uma delas tem um impacto sobre não somente quantas ativos de imagem será necessários, mas como esses ativos são criados. Os tópicos a seguir abordam os tipos de ativos de imagens que serão necessários, como esses ativos são incluídos no pacote do aplicativo e como os ativos de imagem são consumidos para fornecer a funcionalidade necessária:


## <a name="displaying-an-imageiosapp-fundamentalsimages-iconsdisplaying-an-imagemd"></a>[Exibir uma imagem](~/ios/app-fundamentals/images-icons/displaying-an-image.md)

Este artigo aborda incluindo um ativo de imagem em um aplicativo xamarin. IOS e exibir essa imagem, usando o código c# ou atribuindo-a um controle no Designer do iOS.

## <a name="application-iconsiosapp-fundamentalsimages-iconsapp-iconsmd"></a>[Ícones de aplicativo](~/ios/app-fundamentals/images-icons/app-icons.md)

Aborda esse artigo incluindo e gerenciar um ativo de imagem em um aplicativo xamarin. IOS para ser usado como um ícone de aplicativo.

## <a name="alternate-app-iconsiosapp-fundamentalsimages-iconsalternate-app-iconsmd"></a>[Ícones de aplicativos alternativos](~/ios/app-fundamentals/images-icons/alternate-app-icons.md)

Apple adicionou várias melhorias para o iOS 10.3 que permitem que um aplicativo gerenciar seu ícone:

 - `ApplicationIconBadgeNumber` -Obtém ou define o selo do ícone do aplicativo em do Springboard.
 - `SupportsAlternateIcons` -Se `true` o aplicativo tem um conjunto alternativo de ícones.
 - `AlternateIconName` -Retorna o nome do ícone alternativo selecionado no momento ou `null` se usando o ícone principal.
 - `SetAlternameIconName` -Use esse método para alternar o ícone do aplicativo para o ícone alternativo especificado.


## <a name="launch-screensiosapp-fundamentalsimages-iconslaunch-screensmd"></a>[Telas de Inicialização](~/ios/app-fundamentals/images-icons/launch-screens.md)

Este artigo aborda o uso de um tipo especial de Storyboard para fornecer uma tela de inicialização universal para cada tamanho de dispositivo iOS e a resolução.

## <a name="custom-document-typesiosapp-fundamentalsimages-iconscustom-document-typesmd"></a>[Tipos de documento personalizado](~/ios/app-fundamentals/images-icons/custom-document-types.md)

Aborda esse artigo incluindo e gerenciar um ativo de imagem em um aplicativo xamarin. IOS para ser usado como um ícone de tipo de documento personalizado.



## <a name="related-links"></a>Links relacionados

- [Trabalhando com imagens (amostra)](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Ícone personalizado e diretrizes de criação de imagem](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
