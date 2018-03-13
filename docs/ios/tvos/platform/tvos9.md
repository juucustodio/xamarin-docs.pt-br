---
title: "Introdução às tvOS 9"
description: "Este artigo apresenta todas as APIs e recursos disponíveis no tvOS 9 novos e modificados para desenvolvedores de Xamarin.tvOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: A7E738E1-9F94-489B-918F-7DF8F0810987
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/07/2016
ms.openlocfilehash: 55e83658e09bc7e5c12bb3ef3f508497651ec46c
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="introduction-to-tvos-9"></a>Introdução às tvOS 9

_Este artigo apresenta todas as APIs e recursos disponíveis no tvOS 9 novos e modificados para desenvolvedores de Xamarin.tvOS._


Apple lançou a 4º geração do hardware Apple TV, apresentando remoto reprojetado, habilitar toque, com o novo sistema operacional de tvOS (com base no iOS 9).

Pela primeira vez, tvOS abre a plataforma Apple TV de desenvolvedor, permitindo que você crie aplicativos sofisticados e envolventes e liberá-los por meio da loja de aplicativo interno da Apple TV em um processo semelhante à experiência de escrever e liberação de aplicativos para iOS usando a iTunes App Repositório.

Se você estiver familiarizado com o desenvolvimento de xamarin, você deve considerar a transição para tvOS bastante simples. A maioria das APIs e recursos é os mesmos, no entanto, várias APIs comuns estão disponíveis (como WebKit). Além disso, trabalhando com o com o computador remoto Siri apresenta alguns desafios de design que não estão presentes nos dispositivos do iOS com base em tela sensível ao toque.

Este guia fornecerá uma introdução para todas as APIs e recursos disponíveis no tvOS 9 novos e modificados para desenvolvedores Xamarin.tvOS. Para obter mais informações sobre tvOS, consulte da Apple [desenvolvendo para o novo Apple TV](https://developer.apple.com/tvos/) documentação.

<a name="Supported-and-Unsupported-Capabilities" />

## <a name="supported-and-unsupported-capabilities"></a>Recursos compatíveis e sem suportados

tvOS aplicativos em execução no Apple TV tem suporte para o seguinte recursos e capacidades:

 - Grupos de Aplicativos
 - Modos em tela de fundo
 - Proteção de Dados
 - Game Center
 - Controladores de jogo
 - iCloud
 - Compras no aplicativo
 - Compartilhamento de conjunto de chaves

Não há suporte para os seguintes recursos e capacidades:

 - Apple Pay
 - Área restrita do aplicativo
 - Domínios Associados
 - HealthKit
 - HomeKit
 - Áudio entre aplicativos
 - Mapas
 - VPN pessoal
 - Notificações por Push
 - Carteira
 - Configuração de Acessório sem Fio

Confira nosso [suporte para Assemblies](~/ios/tvos/internals/assemblies.md) e [estruturas de suporte](~/ios/tvos/internals/frameworks.md) documentação para obter mais informações.

<a name="Apple-TV-Hardware" />

## <a name="apple-tv-hardware"></a>Apple TV Hardware

O novo Apple TV tem as seguintes especificações de hardware:

 - processador de A8 de 64 bits
 - 32 ou 64GB de armazenamento
 - 2GB de RAM
 - 10/100 Mbps Ethernet
 - 802.11a/b/g/n/ac Wi-Fi
 - resolução de 1080p
 - HDMI
 - Porta C (para o desenvolvedor e uso de diagnóstico somente)
 - Novo Siri remoto ou Apple TV remotos (com base na região)

### <a name="siri-remote"></a>Siri remoto

Com base na região, fornecido Apple TV remoto virão em configurações com um um: Siri remoto ou Apple TV remoto.

Siri remoto está disponível no momento nos seguintes países:

 - Austrália
 - Canadá
 - França
 - Alemanha
 - Japão
 - Espanha
 - Reino Unido
 - Estados Unidos

Todos os outros países receberá o Apple TV remoto que substitui o botão Siri com um botão de pesquisa que exibe a tela de pesquisa padrão com entrada de texto de pesquisa:

[![](tvos9-images/remote02.png "Siri remoto")](tvos9-images/remote02.png#lightbox)

Para obter mais informações, consulte nosso [Siri remoto e controladores Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) documentação.

<a name="Apple-TV-Provisioning" />

## <a name="apple-tv-provisioning"></a>Provisionamento do Apple TV

Assim como o desenvolvimento para iOS, o novo tvOS exigirá o perfil de provisionamento apropriado para desenvolvimento e distribuição com base na associação de equipe e assinatura identidades já estabelecidas com a Apple.

Também é necessário para acessar recursos de tvOS como iCloud KVS ou repositórios de dados CloudKit provisionamento apropriado. Confira nosso [armazenamento de dados e recursos](~/ios/tvos/app-fundamentals/resources-data-storage.md) para obter informações sobre suporte iCloud em seus aplicativos Xamarin.tvOS.

Perfis de provisionamento são criados e instalados da mesma maneira como trabalhar com aplicativos xamarin. Como tal, veja nosso iOS [aprovisionamento](~/ios/get-started/installation/device-provisioning/index.md) documentação para obter mais detalhes.

<a name="Apple-TV-Apps" />

## <a name="apple-tv-apps"></a>Apple TV aplicativos

O novo hardware Apple TV e tvOS 9 dá suporte a dois tipos de aplicativos: tradicional e aplicativos de cliente-servidor.

<a name="Traditional-Apps" />

### <a name="traditional-apps"></a>Aplicativos tradicionais

Aplicativos tradicionais são adquiridos da loja de aplicativos Apple TV e são instalados diretamente no dispositivo. Esses aplicativos podem ser jogos, utilitários ou aplicativos de mídia que são desenvolvidos usando a mesma estrutura e técnicas como aplicativos xamarin.

Aplicativos da Apple TV têm um tamanho máximo de 200MB e podem baixar um adicional de 2GB de conteúdo usando os recursos sob demanda. Confira nosso [armazenamento de dados e recursos](~/ios/tvos/app-fundamentals/resources-data-storage.md) para obter mais informações.

Consulte nossa [Hello, tvOS Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md) para se familiarizar com as ferramentas e os conceitos necessários para desenvolver aplicativos de tvOS usando Xamarin.tvOS.

<a name="Summary" />

### <a name="client-server-apps"></a>Aplicativos de cliente-servidor

Além dos aplicativos tradicionais instalados, Apple TV torna fácil criar aplicativos que usam tecnologias da web (HTTPS, XML e JavaScript) de streaming de mídia do servidor de cliente baseado na web. Você cria a interface do usuário usando a linguagem de marcação TVML da Apple e usar JavaScript para definir os comportamentos do aplicativo usando TVMLKit.

Para obter mais informações, consulte da Apple [referência de linguagem de marcação Apple TV](https://developer.apple.com/library/prerelease/tvos/documentation/LanguagesUtilities/Conceptual/ATV_Template_Guide/index.html#//apple_ref/doc/uid/TP40015064), [TVJS Framework referência](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLJS/Reference/TVJSFrameworkReference/index.html#//apple_ref/doc/uid/TP40016076), [TVMLKit Framework referência](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLKit/Reference/TVMLKit_Collection/index.html#//apple_ref/doc/uid/TP40016429), [sobre HTTP Live Streaming](https://developer.apple.com/library/prerelease/tvos/referencelibrary/GettingStarted/AboutHTTPLiveStreaming/about/about.html#//apple_ref/doc/uid/TP40013978) e [especificação de criação de HLS da Apple TV](https://developer.apple.com/services-account/download?path=/Documentation/HLS_Authoring_Specification_for_Apple_TV/HLS_Authoring_Specification_for_Apple_TV.pdf) documentação.

<a name="User-Interface-Challenges" />

## <a name="user-interface-challenges"></a>Desafios de Interface do usuário

Ao contrário do iOS ou OS X, Apple TV não tem uma tela sensível ao toque ou mouse que permitem que o usuário selecione diretamente e interagir com um aplicativo ou seu conteúdo. Em vez disso, eles usuário remoto Siri novo ou um controlador de jogo Bluetooth para navegar de Interface do usuário do aplicativo. Para obter mais informações, consulte nosso [Siri remoto e controladores Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) documentação.

Além disso, a experiência geral do usuário é drasticamente diferente de aplicativos iOS ou Mac que tendem a ser experiências de usuário único. Com o Apple TV, as experiências do usuário tendem a ser mais social por natureza, em que várias pessoas podem estar localizado no sofá interagir com um único aplicativo e entre si. Para criar uma experiência de aplicativo Apple TV bem-sucedida (um novo aplicativo ou movimentando existente), essas alterações devem ser levadas em consideração. 

<a name="Summary" />

### <a name="working-with-focus-and-parallax-images"></a>Trabalhando com foco e imagens da Parallax

Como mencionado acima, os usuários do seu aplicativo Xamarin.tvOS não interagirá com sua interface diretamente com iOS onde eles tocar imagens na tela do dispositivo, mas indiretamente de dentro de uma sala usando Siri remoto. Para apresentar e lidar com essa interação do usuário, a Apple TV usa um modelo de foco com base. 

Como as alterações de foco, animações sutis e efeitos (como o efeito da Parallax em imagens) são usados para identificar claramente o item de Interface do usuário que tem o foco no momento.

Se o usuário fizer um gesto lento, circular no controle remoto Siri, o Item focalizado será sway em tempo real em resposta a essa movimentação. Como o sway ocorre, um sheen iluminado é aplicada à sua imagem de fazer com que a superfície de aparecer ser exibido. Depois de uma determinada quantidade de inatividade, qualquer conteúdo fora de foco escurece e o item focalizada aumentará ainda maior.

Para obter mais informações, consulte nosso [trabalhando com foco e navegação](~/ios/tvos/app-fundamentals/navigation-focus.md) e [trabalhando com imagens e ícones](~/ios/tvos/app-fundamentals/icons-images.md) documentação.

<a name="The-Home-Screen" />

### <a name="the-home-screen"></a>Tela inicial

A tela inicial para Apple TV mostra todos os aplicativos que estão instalados e fornece uma maneira de acessar as preferências do usuário:

[![](tvos9-images/home01.png "Tela inicial")](tvos9-images/home01.png#lightbox)

O usuário navega uma grade de ícones de aplicativo usando gestos de toque em Siri remoto usando o foco para selecionar um aplicativo e iniciá-lo. O ícone do aplicativo é a sua primeira chance de fazer uma boa impressão em seu usuário potencial e devem se comunicar a finalidade do seu aplicativo em um relance.

Cada aplicativo deve fornecer uma pequena e uma versão grande do seu ícone de aplicativo. O ícone pequeno será usado na tela de início para Apple TV, quando o aplicativo é instalado. A versão grande é usada pela loja de aplicativos. O ícone de aplicativo grande deve simular a aparência da versão do ícone pequeno.

Para obter mais informações, consulte nosso [trabalhando com imagens e ícones](~/ios/tvos/app-fundamentals/icons-images.md) documentação.

<a name="The-Top-Shelf" />

### <a name="the-top-shelf"></a>Prateleira superior

Se o usuário tiver inserido o seu aplicativo Xamarin.tvOS na linha superior na tela de início para Apple TV, uma imagem grande de prateleira superior será exibida quando seu aplicativo é selecionado pelo usuário. Essa imagem deve realçar os recursos do seu aplicativo ou forneça links diretos para o seu conteúdo.

[![](tvos9-images/topshelf01.png "Prateleira superior")](tvos9-images/topshelf01.png#lightbox)

A imagem de prateleira superior pode ser fornecida como um único static `.png` ou `.lsr` arquivo ou podem ser criada dinamicamente em tempo de execução como uma única linha de itens de controle.

Em vez de exibir uma imagem estática de prateleira superior, ele pode conter uma linha dinâmica ou itens de controle ou um conjunto dinâmico de faixas de rolagem. Ambos esses estilos dinâmica permitem que você realce o conteúdo fornecido pelo seu aplicativo ou um salto para seus recursos mais usados.

Para obter mais informações, consulte nosso [trabalhando com imagens e ícones](~/ios/tvos/app-fundamentals/icons-images.md) documentação e da Apple [TVServices Framework referência](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412) para obter mais informações sobre como adicionar uma extensão de prateleira superior ao seu aplicativo para fornecer conteúdo dinâmico de validade da parte superior.






## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guias de Interface Humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [Criando aplicativos para tvOS com Xamarin (vídeo)](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
