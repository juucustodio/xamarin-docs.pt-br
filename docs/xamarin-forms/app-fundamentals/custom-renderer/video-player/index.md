---
title: Implementar um player de vídeo
description: Este artigo explica como implementar um aplicativo de player de vídeo usando o Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 0CE9BEE7-4F81-4A00-B9B3-5E2535CD3050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 8e6f37fb607f4b0bc0ddfd41ad86dfe7ecbc03df
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68650535"
---
# <a name="implementing-a-video-player"></a>Implementar um player de vídeo

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

Às vezes é desejável reproduzir arquivos de vídeo em um aplicativo Xamarin.Forms. Esta série de artigos aborda como criar renderizadores personalizados para iOS, Android e o UWP (Plataforma Universal do Windows) para uma classe do Xamarin.Forms denominada `VideoPlayer`.

Na amostra [**VideoPlayerDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos), todos os arquivos que implementam e dão suporte a `VideoPlayer` estão em pastas denominadas `FormsVideoLibrary` e identificadas com o namespace `FormsVideoLibrary` ou namespaces que começam com `FormsVideoLibrary`. Essa organização e nomenclatura devem tornar fácil copiar os arquivos do player de vídeo para sua própria solução do Xamarin.Forms.

`VideoPlayer` pode reproduzir arquivos de vídeo de três tipos de fontes:

- A Internet, usando uma URL
- Um recurso inserido no aplicativo da plataforma
- A biblioteca de vídeos do dispositivo

Players de vídeo exigem *controles de transporte*, que são botões para reproduzir e pausar o vídeo e uma barra de posicionamento que mostra o andamento no vídeo e permite que o usuário passe rapidamente para um tempo diferente. `VideoPlayer` pode usar controles de transporte e a barra de posicionamento fornecidos pela plataforma (como mostrado abaixo), ou você pode fornecer controles de transporte e uma barra de posicionamento personalizados. Aqui está o programa em execução no iOS, no Android e na Plataforma Universal do Windows:

[![Reproduzir vídeo da Web](web-videos-images/playwebvideo-small.png "Reproduzir vídeo da Web")](web-videos-images/playwebvideo-large.png#lightbox "Reproduzir vídeo da Web")

É claro que você pode deitar o telefone para obter uma exibição ampliada.

Um player de vídeo mais sofisticado tem alguns recursos adicionais, como controle de volume e um mecanismo para interromper o vídeo quando chega uma chamada, bem como um meio de manter a tela ativa durante a reprodução.

A série de artigos a seguir mostra progressivamente como os renderizadores de plataforma e as classes de suporte são criados:

## <a name="creating-the-platform-video-playersplayer-creationmd"></a>[Criar os players de vídeo de plataforma](player-creation.md)

Cada plataforma requer uma classe `VideoPlayerRenderer` que cria e mantém um controle de player de vídeo compatível com a plataforma. Este artigo mostra a estrutura das classes do renderizador e o modo como os players são criados.

## <a name="playing-a-web-videoweb-videosmd"></a>[Reproduzir um vídeo da Web](web-videos.md)

A fonte mais comum de vídeos para um player de vídeo é provavelmente a Internet. Este artigo descreve como um vídeo da Web pode ser referenciado e usado como uma fonte para o player de vídeo.

## <a name="binding-video-sources-to-the-playersource-bindingsmd"></a>[Associando fontes de vídeo ao player](source-bindings.md)

Este artigo usa um `ListView` para apresentar uma coleção de vídeos para reproduzir. Um programa mostra como o arquivo code-behind pode definir a fonte de vídeo do player de vídeo, mas um segundo programa mostra como você pode usar a associação de dados entre o `ListView` e o player de vídeo.

## <a name="loading-application-resource-videosloading-resourcesmd"></a>[Carregar vídeos de recursos do aplicativo](loading-resources.md)

Vídeos podem ser inseridos como recursos nos projetos de plataforma. Este artigo mostra como armazenar esses recursos e depois carregá-los no programa a ser reproduzido pelo player de vídeo.

## <a name="accessing-the-devices-video-libraryaccessing-librarymd"></a>[Acessar a biblioteca de vídeos do dispositivo](accessing-library.md)

Quando um vídeo é criado usando a câmera do dispositivo, o arquivo de vídeo é armazenado na biblioteca de imagens do dispositivo. Este artigo mostra como acessar o seletor de imagem do dispositivo para selecionar o vídeo e, em seguida, executá-lo usando o player de vídeo.

## <a name="custom-video-transport-controlscustom-transportmd"></a>[Controles personalizados de transporte de vídeo](custom-transport.md)

Embora os players de vídeo em cada plataforma forneçam seus próprios controles de transporte na forma de botões para **Reproduzir** e **Pausar**, você pode suprimir a exibição desses botões e fornecer os seus próprios. Este artigo mostra como fazê-lo.

## <a name="custom-video-positioningcustom-positioningmd"></a>[Posicionamento de vídeo personalizado](custom-positioning.md)

Cada um dos players de vídeo da plataforma tem uma barra de posição que mostra o progresso do vídeo e permite que você avance ou retroceda para uma determinada posição. Este artigo demonstra como você pode substituir essa barra de posição com um controle personalizado.





## <a name="related-links"></a>Links relacionados

- [Demonstrações do player de vídeo (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)
