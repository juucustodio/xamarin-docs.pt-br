---
title: Imagens e ícones no Xamarin. iOS
description: Esta seção inclui uma variedade de artigos que abordam o trabalho com imagens em um aplicativo Xamarin. iOS, como usá-los como ícones, iniciar telas ou incluí-los em controles e fornecer ícones para tipos de documento personalizados.
ms.prod: xamarin
ms.assetid: 0AB8CC07-11E4-0D75-4119-AED1A1252424
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 6048690bc68c3998b67dc89fdc191ea9158ae952
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437276"
---
# <a name="images-and-icons-in-xamarinios"></a>Imagens e ícones no Xamarin. iOS

_Esta seção inclui uma variedade de artigos que abordam o trabalho com imagens em um aplicativo Xamarin. iOS, como usá-los como ícones, iniciar telas ou incluí-los em controles e fornecer ícones para tipos de documento personalizados._

Há várias maneiras de os ativos de imagem serem usados dentro de um aplicativo iOS. De simplesmente exibir uma imagem como parte da interface do usuário de um aplicativo para, atribuí-la a um controle de interface do usuário, como um `UIButton` ou `UIImageView` , para fornecer ícones e telas de inicialização, o Xamarin. Ios facilita a adição de um grande trabalho artístico a um aplicativo IOS das seguintes maneiras: 

- **Imagens independentes de resolução** – use o suporte interno do IOS para trabalhar com imagens em diferentes resoluções e tipos de dispositivo (iPhone, iPad etc.).
- **Conjuntos de imagens do catálogo de ativos** – use **conjuntos de imagens do catálogo de ativos** para gerenciar e agrupar todas as versões de um determinado ativo de imagem exigido por um aplicativo.
- **Imagens no designer do IOS** – use o designer do IOS para definir imagens para controles.
- **Imagens no código** – use os `UIImage` métodos da classe para carregar e trabalhar com ativos de imagem e atribuí-los a controles de interface do usuário no código C#.
- **Ícone do aplicativo** – defina o ícone do aplicativo exigido por cada aplicativo Ios. Esse é o ícone que o usuário irá tocar na tela inicial do iOS para iniciar o aplicativo. Além disso, esse ícone é usado por Game Center, se aplicável.
- **Ícone de destaque** – defina o ícone de destaque do aplicativo. Sempre que o usuário inserir o nome de um aplicativo em uma pesquisa de destaque, esse ícone será exibido.
- **Ícone de configurações** – defina o ícone de **configurações** do aplicativo. Se o usuário inserir o aplicativo de **configurações** em seu dispositivo IOS, esse ícone será exibido no final da lista de configurações para o aplicativo. 
- **Telas de inicialização** – defina a tela de inicialização do aplicativo. Depois que o usuário toca no ícone do aplicativo e antes da primeira exibição aparecer, uma tela em branco será mostrada. Felizmente, o iOS inclui suporte para exibir uma imagem no lugar da tela em branco usando um Storyboard. 
- **ícone do iTunes** – forneça um ícone de iTune. Se você estiver usando o método ad hoc de entregar um aplicativo (para usuários corporativos ou para testes beta em dispositivos reais), o desenvolvedor também precisará incluir uma 512x512 e uma imagem 1024x1024 que serão usadas para representar o aplicativo no iTunes.
- **Ícones de documento** -use uma imagem como um ícone para qualquer tipo de documento específico ao qual um aplicativo Xamarin. Ios dá suporte ou cria.

Há várias considerações que devem ser levadas em conta ao criar ativos de imagem para um aplicativo iOS, bem como vários locais em que esses ativos serão usados. Cada um deles tem um efeito sobre não apenas quantos ativos de imagem serão necessários, mas como esses ativos são criados. Os tópicos a seguir abordam os tipos de ativos de imagens que serão necessários, como esses ativos são incluídos no grupo do aplicativo e como os ativos da imagem são consumidos para fornecer a funcionalidade necessária:

## <a name="displaying-an-image"></a>[Como exibir uma imagem](~/ios/app-fundamentals/images-icons/displaying-an-image.md)

Este artigo aborda a inclusão de um ativo de imagem em um aplicativo Xamarin. iOS e a exibição dessa imagem usando código C# ou atribuindo-a a um controle no designer do iOS.

## <a name="application-icons"></a>[Ícones de aplicativo](~/ios/app-fundamentals/images-icons/app-icons.md)

Este artigo aborda a inclusão e o gerenciamento de um ativo de imagem em um aplicativo Xamarin. iOS a ser usado como um ícone de aplicativo.

## <a name="alternate-app-icons"></a>[Ícones de aplicativos alternativos](~/ios/app-fundamentals/images-icons/alternate-app-icons.md)

A Apple adicionou vários aprimoramentos ao iOS 10,3 que permitem que um aplicativo Gerencie seu ícone:

- `ApplicationIconBadgeNumber` -Obtém ou define o emblema do ícone do aplicativo no Springboard.
- `SupportsAlternateIcons` -Se `true` o aplicativo tiver um conjunto alternativo de ícones.
- `AlternateIconName` -Retorna o nome do ícone alternativo selecionado no momento ou `null` se estiver usando o ícone primário.
- `SetAlternameIconName` -Use este método para alternar o ícone do aplicativo para o ícone alternativo fornecido.

## <a name="launch-screens"></a>[Telas de Inicialização](~/ios/app-fundamentals/images-icons/launch-screens.md)

Este artigo aborda o uso de um tipo especial de storyboard para fornecer uma tela de inicialização universal para cada tamanho e resolução do dispositivo iOS.

## <a name="custom-document-types"></a>[Tipos de documento personalizado](~/ios/app-fundamentals/images-icons/custom-document-types.md)

Este artigo aborda a inclusão e o gerenciamento de um ativo de imagem em um aplicativo Xamarin. iOS a ser usado como um ícone de tipo de documento personalizado.

## <a name="related-links"></a>Links Relacionados

- [Trabalhando com imagens (exemplo)](/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Diretrizes personalizadas de criação de imagem e ícone](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)