---
title: ''
description: Este artigo explica como implementar um aplicativo de player de vídeo usando o Xamarin.Forms .
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 08bfb86f040bfbce834df5a5d98231afae92e78d
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84133753"
---
# <a name="implementing-a-video-player"></a>Implementar um player de vídeo

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

Às vezes, é desejável reproduzir arquivos de vídeo em um Xamarin.Forms aplicativo. Esta série de artigos discute como escrever renderizadores personalizados para iOS, Android e o Plataforma Universal do Windows (UWP) para uma Xamarin.Forms classe denominada `VideoPlayer` .

Na amostra [**VideoPlayerDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos), todos os arquivos que implementam e dão suporte a `VideoPlayer` estão em pastas denominadas `FormsVideoLibrary` e identificadas com o namespace `FormsVideoLibrary` ou namespaces que começam com `FormsVideoLibrary`. Essa organização e nomenclatura devem facilitar a cópia dos arquivos do player de vídeo em sua própria Xamarin.Forms solução.

`VideoPlayer` pode reproduzir arquivos de vídeo de três tipos de fontes:

- A Internet, usando uma URL
- Um recurso inserido no aplicativo da plataforma
- A biblioteca de vídeos do dispositivo

Players de vídeo exigem *controles de transporte*, que são botões para reproduzir e pausar o vídeo e uma barra de posicionamento que mostra o andamento no vídeo e permite que o usuário passe rapidamente para um tempo diferente. `VideoPlayer` pode usar controles de transporte e a barra de posicionamento fornecidos pela plataforma (como mostrado abaixo), ou você pode fornecer controles de transporte e uma barra de posicionamento personalizados. Aqui está o programa em execução no iOS, no Android e na Plataforma Universal do Windows:

[![Reproduzir vídeo da Web](web-videos-images/playwebvideo-small.png "Reproduzir vídeo da Web")](web-videos-images/playwebvideo-large.png#lightbox "Reproduzir vídeo da Web")

É claro que você pode deitar o telefone para obter uma exibição ampliada.

Um player de vídeo mais sofisticado tem alguns recursos adicionais, como controle de volume e um mecanismo para interromper o vídeo quando chega uma chamada, bem como um meio de manter a tela ativa durante a reprodução.

A série de artigos a seguir mostra progressivamente como os renderizadores de plataforma e as classes de suporte são criados:

## <a name="creating-the-platform-video-players"></a>[Criando os players de vídeo da plataforma](player-creation.md)

Cada plataforma requer uma classe `VideoPlayerRenderer` que cria e mantém um controle de player de vídeo compatível com a plataforma. Este artigo mostra a estrutura das classes do renderizador e o modo como os players são criados.

## <a name="playing-a-web-video"></a>[Reprodução de um vídeo da Web](web-videos.md)

A fonte mais comum de vídeos para um player de vídeo é provavelmente a Internet. Este artigo descreve como um vídeo da Web pode ser referenciado e usado como uma fonte para o player de vídeo.

## <a name="binding-video-sources-to-the-player"></a>[Associando fontes de vídeo ao Player](source-bindings.md)

Este artigo usa um `ListView` para apresentar uma coleção de vídeos para reproduzir. Um programa mostra como o arquivo code-behind pode definir a fonte de vídeo do player de vídeo, mas um segundo programa mostra como você pode usar a associação de dados entre o `ListView` e o player de vídeo.

## <a name="loading-application-resource-videos"></a>[Carregando vídeos de recursos do aplicativo](loading-resources.md)

Vídeos podem ser inseridos como recursos nos projetos de plataforma. Este artigo mostra como armazenar esses recursos e depois carregá-los no programa a ser reproduzido pelo player de vídeo.

## <a name="accessing-the-devices-video-library"></a>[Acessando a biblioteca de vídeos do dispositivo](accessing-library.md)

Quando um vídeo é criado usando a câmera do dispositivo, o arquivo de vídeo é armazenado na biblioteca de imagens do dispositivo. Este artigo mostra como acessar o seletor de imagem do dispositivo para selecionar o vídeo e, em seguida, executá-lo usando o player de vídeo.

## <a name="custom-video-transport-controls"></a>[Controles de transporte de vídeo personalizados](custom-transport.md)

Embora os players de vídeo em cada plataforma forneçam seus próprios controles de transporte na forma de botões para **Reproduzir** e **Pausar**, você pode suprimir a exibição desses botões e fornecer os seus próprios. Este artigo mostra como fazer isso.

## <a name="custom-video-positioning"></a>[Posicionamento de vídeo personalizado](custom-positioning.md)

Cada um dos players de vídeo da plataforma tem uma barra de posição que mostra o progresso do vídeo e permite que você avance ou retroceda para uma determinada posição. Este artigo demonstra como você pode substituir essa barra de posição com um controle personalizado.

## <a name="related-links"></a>Links relacionados

- [Demonstrações do player de vídeo (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)
