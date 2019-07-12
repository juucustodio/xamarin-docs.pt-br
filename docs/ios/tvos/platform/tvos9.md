---
title: Introdução ao tvOS 9
description: Este artigo apresenta todas as APIs e recursos disponíveis no tvOS 9 novos e modificados para desenvolvedores do xamarin. tvos.
ms.prod: xamarin
ms.assetid: A7E738E1-9F94-489B-918F-7DF8F0810987
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/07/2016
ms.openlocfilehash: c4aea5a35fd5db46c9e7ca245b852c988e82f53e
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830501"
---
# <a name="introduction-to-tvos-9"></a>Introdução ao tvOS 9

_Este artigo apresenta todas as APIs e recursos disponíveis no tvOS 9 novos e modificados para desenvolvedores do xamarin. tvos._

Apple lançou a 4ª geração do hardware da Apple TV apresentando reprojetada, habilitar toque remota, executando o novo sistema operacional de tvOS (com base no iOS 9).

Pela primeira vez, tvOS abre a plataforma de Apple TV ao desenvolvedor, permitindo que você crie aplicativos avançados e imersivos e liberá-los por meio de Store de aplicativo interno a Apple TV em um processo semelhante à experiência de escrever e liberar aplicativos para iOS usando o aplicativo do iTunes Store.

Se você estiver familiarizado com o desenvolvimento do xamarin. IOS, você deve encontrar a transição para tvOS bastante simples. A maioria das APIs e recursos é os mesmos, no entanto, muitas APIs comuns estão disponíveis (como WebKit). Além disso, trabalhar com o com o Siri remoto apresenta alguns desafios de design que não estão presentes nos dispositivos iOS de tela sensível ao toque com base.

Este guia lhe dará uma introdução a todas as APIs e recursos disponíveis no tvOS 9 novos e modificados para desenvolvedores do xamarin. tvos. Para obter mais informações sobre tvOS, consulte da Apple [desenvolvendo para o novo Apple TV](https://developer.apple.com/tvos/) documentação.

<a name="Supported-and-Unsupported-Capabilities" />

## <a name="supported-and-unsupported-capabilities"></a>Recursos compatíveis e sem suportados

tvOS aplicativos que são executados na Apple TV tê suporte para o seguinte recursos e capacidades:

- Grupos de Aplicativos
- Modos em tela de fundo
- Proteção de Dados
- Game Center
- Controladores de jogos
- iCloud
- Compras no aplicativo
- Compartilhamento de conjunto de chaves

Não há suporte para os seguintes recursos e capacidades:

- Apple Pay
- Área restrita de aplicativo
- Domínios Associados
- HealthKit
- HomeKit
- Áudio entre aplicativos
- Mapas
- VPN pessoal
- Notificações por Push
- Carteira
- Configuração de Acessório sem Fio

Consulte nosso [suporte para Assemblies](~/ios/tvos/internals/assemblies.md) e [estruturas com suporte](~/ios/tvos/internals/frameworks.md) documentação para obter mais informações.

<a name="Apple-TV-Hardware" />

## <a name="apple-tv-hardware"></a>Hardware do Apple TV

O novo Apple TV tem as seguintes especificações de hardware:

- processador de A8 de 64 bits
- GB de 32 ou 64GB de armazenamento
- 2GB de RAM
- 10/100Mbps Ethernet
- WiFi 802.11a/b/g/n/ac
- resolução de 1080p
- HDMI
- Porta de C de USB (para o desenvolvedor e o uso do diagnóstico somente)
- Novo Siri remoto ou Apple TV remotos (com base na região)

### <a name="siri-remote"></a>Siri remoto

Com base na região, fornecido da Apple TV remoto virão em qualquer um configurações: Siri remoto ou Apple TV remoto.

Siri remoto está disponível nos seguintes países:

- Austrália
- Canadá
- França
- Alemanha
- Japão
- Espanha
- Reino Unido
- Estados Unidos

Todos os outros países receberá a Apple TV remoto que substitui o botão de Siri com um botão de pesquisa que abre a tela de pesquisa padrão com entrada de texto de pesquisa:

[![](tvos9-images/remote02.png "Siri remoto")](tvos9-images/remote02.png#lightbox)

Para obter mais informações, consulte nosso [Siri remoto e controladores de Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) documentação.

<a name="Apple-TV-Provisioning" />

## <a name="apple-tv-provisioning"></a>Provisionamento do Apple TV

Assim como desenvolver para iOS, tvOS o novo exigirá o perfil de provisionamento adequada para desenvolvimento e distribuição com base na participação na equipe e identidades de assinatura que você já tiver estabelecido com a Apple.

Também é necessário para acessar recursos de tvOS como iCloud KVS ou armazenamentos de dados do CloudKit provisionamento adequado. Consulte nosso [armazenamento de dados e recursos](~/ios/tvos/app-fundamentals/resources-data-storage.md) para obter informações sobre suporte a iCloud em seus aplicativos xamarin. tvos.

Perfis de provisionamento são criados e instalados da mesma forma como trabalhar com aplicativos xamarin. IOS. Como tal, consulte nosso iOS [aprovisionamento](~/ios/get-started/installation/device-provisioning/index.md) documentação para obter mais detalhes.

<a name="Apple-TV-Apps" />

## <a name="apple-tv-apps"></a>Apple TV aplicativos

O novo hardware de Apple TV e tvOS 9 oferece suporte a dois tipos de aplicativos: tradicionais e aplicativos de cliente-servidor.

<a name="Traditional-Apps" />

### <a name="traditional-apps"></a>Aplicativos tradicionais

Aplicativos tradicionais são comprados de Store de aplicativo da Apple TV e são instalados diretamente no dispositivo. Esses aplicativos podem ser games, utilitários ou aplicativos de mídia que são desenvolvidos usando as mesmas estruturas e técnicas que os aplicativos xamarin. IOS.

Aplicativos da Apple TV têm um tamanho máximo de 200MB e podem baixar um adicional de 2GB de conteúdo usando os recursos sob demanda. Consulte nosso [armazenamento de dados e recursos](~/ios/tvos/app-fundamentals/resources-data-storage.md) para obter mais informações.

Consulte nosso [Olá, guia de início rápido do tvOS](~/ios/tvos/get-started/hello-tvos.md) de se familiarizar com as ferramentas e os conceitos necessários para desenvolver aplicativos de tvOS usando o xamarin. tvos.

<a name="Summary" />

### <a name="client-server-apps"></a>Aplicativos de cliente-servidor

Além dos aplicativos tradicionais instalados, Apple TV torna fácil criar aplicativos usando tecnologias da web (HTTPS, XML e JavaScript) de streaming de mídia do cliente-servidor baseada na web. Você projetar a interface do usuário usando a linguagem de marcação TVML da Apple e usar JavaScript para definir os comportamentos do aplicativo usando TVMLKit.

Para obter mais informações, consulte da Apple [referência de linguagem de marcação da Apple TV](https://developer.apple.com/library/prerelease/tvos/documentation/LanguagesUtilities/Conceptual/ATV_Template_Guide/index.html#//apple_ref/doc/uid/TP40015064), [referência de estrutura TVJS](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLJS/Reference/TVJSFrameworkReference/index.html#//apple_ref/doc/uid/TP40016076), [TVMLKit Framework referência](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLKit/Reference/TVMLKit_Collection/index.html#//apple_ref/doc/uid/TP40016429), [sobre HTTP Live Streaming](https://developer.apple.com/library/prerelease/tvos/referencelibrary/GettingStarted/AboutHTTPLiveStreaming/about/about.html#//apple_ref/doc/uid/TP40013978) e [HLS criação especificação para Apple TV](https://developer.apple.com/services-account/download?path=/Documentation/HLS_Authoring_Specification_for_Apple_TV/HLS_Authoring_Specification_for_Apple_TV.pdf) documentação.

<a name="User-Interface-Challenges" />

## <a name="user-interface-challenges"></a>Desafios de Interface do usuário

Ao contrário do iOS ou OS X, Apple TV não tem uma tela sensível ao toque ou mouse que permitem ao usuário selecionar diretamente e interagir com um aplicativo ou seu conteúdo. Em vez disso, eles usuário novo Siri remoto ou um controlador de jogo de Bluetooth para navegar a Interface do usuário do aplicativo. Para obter mais informações, consulte nosso [Siri remoto e controladores de Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) documentação.

Além disso, a experiência geral do usuário é drasticamente diferente de aplicativos iOS ou Mac que tendem a ser experiências de usuário único. Com a Apple TV, experiências do usuário tendem a ser mais a natureza social, em que várias pessoas podem estar localizado no sofá interagindo com um único aplicativo e entre si. Para criar uma experiência de aplicativo do Apple TV bem-sucedida (um novo aplicativo ou portabilidade de um existente), essas alterações devem ser levadas em consideração. 

<a name="Summary" />

### <a name="working-with-focus-and-parallax-images"></a>Trabalhando com foco e imagens da Parallax

Como mencionado acima, os usuários do seu aplicativo tvos não interagirá com sua interface diretamente como com o iOS onde eles tocar em imagens na tela do dispositivo, mas indiretamente de dentro de uma sala usando Siri remoto. Para apresentar e lidar com essa interação do usuário, a Apple TV usa um modelo de foco com base. 

Como as alterações de foco, animações sutis e efeitos (como o efeito da Parallax em imagens) são usados para identificar claramente o item de Interface do usuário que tem foco no momento.

Se o usuário faz um gesto lento, circular no Siri remoto, o Item com foco será sway em tempo real em resposta a esse movimento. Enquanto o sway ocorre, um trabalho iluminado é aplicado à sua imagem fazendo com que a superfície parecem se destacam. Após um determinado período de inatividade, qualquer conteúdo fora de foco escurece e o item focalizado aumentará ainda maior.

Para obter mais informações, consulte nosso [trabalhando com navegação e fogo](~/ios/tvos/app-fundamentals/navigation-focus.md) e [trabalhando com imagens e ícones](~/ios/tvos/app-fundamentals/icons-images.md) documentação.

<a name="The-Home-Screen" />

### <a name="the-home-screen"></a>A tela inicial

A tela inicial para Apple TV mostra todos os aplicativos que estão instalados e fornece uma maneira de acessar as preferências do usuário:

[![](tvos9-images/home01.png "A tela inicial")](tvos9-images/home01.png#lightbox)

O usuário navega de uma grade de ícones do aplicativo usando gestos de toque no controle Siri remoto usando o foco para selecionar um aplicativo e iniciá-lo. O ícone do aplicativo é a sua primeira chance de fazer uma boa impressão em seu usuário possíveis e preciso comunicar-se a finalidade do seu aplicativo em um relance.

Cada aplicativo deve fornecer uma pequena e uma versão grande do seu ícone de aplicativo. Quando o aplicativo é instalado, o ícone pequeno será usado na tela de início para Apple TV. A versão grande é usada pelo Store do aplicativo. O ícone grande do aplicativo deve simular a aparência da versão do ícone pequeno.

Para obter mais informações, consulte nosso [trabalhando com imagens e ícones](~/ios/tvos/app-fundamentals/icons-images.md) documentação.

<a name="The-Top-Shelf" />

### <a name="the-top-shelf"></a>Prateleira superior

Se o usuário tiver colocado o seu aplicativo tvos na linha superior na tela de início para Apple TV, uma imagem da prateleira superior grande será exibida quando seu aplicativo é selecionado pelo usuário. Essa imagem deve realçam os recursos do seu aplicativo ou fornecem links diretos para o seu conteúdo.

[![](tvos9-images/topshelf01.png "Prateleira superior")](tvos9-images/topshelf01.png#lightbox)

A imagem da prateleira superior pode ser fornecida como um único estático `.png` ou `.lsr` arquivo ou ele pode ser criado dinamicamente em tempo de execução como uma única linha de itens focalizável.

Em vez de exibir uma imagem da prateleira superior estático, ele pode conter uma linha dinâmica ou focalizável itens ou um conjunto dinâmico de faixas de rolagem. Ambos esses estilos dinâmica permitem que você realce o conteúdo fornecido pelo seu aplicativo ou um salto para seus recursos mais usados.

Para obter mais informações, consulte nosso [trabalhando com imagens e ícones](~/ios/tvos/app-fundamentals/icons-images.md) documentação e da Apple [referência de estrutura TVServices](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412) para obter mais informações sobre como adicionar uma extensão da prateleira superior ao seu aplicativo para fornecer conteúdo dinâmico da prateleira superior.






## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de Interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [Criação de aplicativos para tvOS com o Xamarin (vídeo)](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
