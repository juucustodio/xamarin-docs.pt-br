---
title: Implementando um player de vídeo
description: Este artigo explica como implementar um aplicativo de player de vídeo usando xamarin. Forms.
ms.prod: xamarin
ms.assetid: 0CE9BEE7-4F81-4A00-B9B3-5E2535CD3050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 00697ca0adf3a34abec90c2f96d9fd9c273d06bb
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35239778"
---
# <a name="implementing-a-video-player"></a>Implementando um player de vídeo

Às vezes é desejável para reproduzir arquivos de vídeo em um aplicativo xamarin. Forms. Esta série de artigos discute como escrever renderizadores personalizados para iOS, Android e o Windows UWP (plataforma Universal) para uma classe xamarin. Forms denominada `VideoPlayer`.

No [ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) de exemplo, todos os arquivos que implementam o suportam e `VideoPlayer` estão em pastas denominadas `FormsVideoLibrary` e identificado com o namespace `FormsVideoLibrary` ou namespaces que começam `FormsVideoLibrary`. Essa organização e nomenclatura facilita copiar os arquivos de player de vídeo em sua própria solução xamarin. Forms.

`VideoPlayer` pode executar arquivos de vídeos de três tipos de fontes:

- Internet usando uma URL
- Um recurso incorporado do aplicativo de plataforma
- Biblioteca de vídeos do dispositivo

Players de vídeo requerem *controles de transporte*, que são botões para reproduzir e pausar o vídeo e um posicionamento de barras que mostra o progresso através do vídeo e permite que o usuário ignore rapidamente para um local diferente. `VideoPlayer` pode usar os controles de transporte e posicionamento barra fornecida pela plataforma (conforme mostrado abaixo), ou você pode fornecer uma barra de posicionamento e controles de transporte personalizado. Aqui está o programa em execução no iOS, Android e a plataforma Universal do Windows:

[![Reproduzir o vídeo do Web](web-videos-images/playwebvideo-small.png "reproduzir o vídeo do Web")](web-videos-images/playwebvideo-large.png#lightbox "reproduzir o vídeo de Web")

Obviamente, você pode ativar o telefone para os lados de uma exibição maior.

Um player de vídeo mais sofisticado tem alguns recursos adicionais, como controle de volume e um mecanismo para interromper o vídeo quando uma chamada telefônica é fornecido por meio de uma maneira de manter a tela ativa durante a reprodução.

A seguinte série de artigos progressivamente mostra como os renderizadores de plataforma e classes de suporte são criados:

## <a name="creating-the-platform-video-playersplayer-creationmd"></a>[Criando os players de vídeo de plataforma](player-creation.md)

Cada plataforma requer um `VideoPlayerRenderer` classe que cria e mantém um controle de player de vídeo com suporte na plataforma. Este artigo mostra a estrutura do renderizador de classes e como os players são criados.

## <a name="playing-a-web-videoweb-videosmd"></a>[Reprodução de um vídeo de Web](web-videos.md)

Provavelmente, a origem mais comum de vídeos para um player de vídeo é a Internet. Este artigo descreve como um vídeo da Web pode ser referenciado e usado como uma fonte para o player de vídeo.

## <a name="binding-video-sources-to-the-playersource-bindingsmd"></a>[Fontes de vídeo de associação para o player](source-bindings.md)

Este artigo usa um `ListView` para apresentar uma coleção de vídeos para reproduzir. Um programa mostra como o arquivo code-behind pode definir a fonte de vídeo do player de vídeo, mas um segundo programa mostra como você pode usar dados de associação entre o `ListView` e o player de vídeo.

## <a name="loading-application-resource-videosloading-resourcesmd"></a>[Vídeos de recursos do aplicativo de carregamento](loading-resources.md)

Vídeos podem ser inseridos como recursos nos projetos de plataforma. Este artigo mostra como armazenar esses recursos e depois carregá-los para o programa a ser executado, o player de vídeo.

## <a name="accessing-the-devices-video-libraryaccessing-librarymd"></a>[Acessar a biblioteca de vídeos do dispositivo](accessing-library.md)

Quando um vídeo é criado usando a câmera do dispositivo, o arquivo de vídeo é armazenado na biblioteca de imagens do dispositivo. Este artigo mostra como acessar o seletor de imagem do dispositivo para selecionar o vídeo e, em seguida, executá-lo usando o player de vídeo.

## <a name="custom-video-transport-controlscustom-transportmd"></a>[Controles personalizados de transporte de vídeo](custom-transport.md)

Embora os players de vídeo em cada plataforma fornecem seus próprios controles de transporte na forma de botões para **reproduzir** e **pausar**, você pode suprimir a exibição desses botões e fornecer sua própria. Este artigo mostra como fazer isso.

## <a name="custom-video-positioningcustom-positioningmd"></a>[Posicionamento de vídeo personalizado](custom-positioning.md)

Cada um dos players de vídeo a plataforma tem uma barra de posição que mostra o andamento do vídeo e permite que você vá em frente ou para trás para uma posição específica. Este artigo demonstra como você pode substituir essa barra de posição com um controle personalizado.





## <a name="related-links"></a>Links relacionados

- [Demonstrações em vídeo Player (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
