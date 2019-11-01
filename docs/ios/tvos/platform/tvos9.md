---
title: Introdução ao tvOS 9
description: Este artigo apresenta todas as APIs e recursos novos e modificados disponíveis no tvOS 9 para desenvolvedores do Xamarin. tvOS.
ms.prod: xamarin
ms.assetid: A7E738E1-9F94-489B-918F-7DF8F0810987
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/07/2016
ms.openlocfilehash: 34f332eb712f479f9f9565a3894212e3cdd5aaf6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030539"
---
# <a name="introduction-to-tvos-9"></a>Introdução ao tvOS 9

_Este artigo apresenta todas as APIs e recursos novos e modificados disponíveis no tvOS 9 para desenvolvedores do Xamarin. tvOS._

A Apple lançou a 4ª geração do hardware da Apple TV com um remoto reprojetado, habilitado para toque, executando o novo sistema operacional tvOS (com base no iOS 9).

Pela primeira vez, o tvOS abre a plataforma de TV da Apple para o desenvolvedor, permitindo que você crie aplicativos ricos e envolventes e libere-os por meio da loja de aplicativos interna da Apple TV em um processo semelhante à experiência de escrever e lançar aplicativos para iOS usando o aplicativo iTunes Armazenadas.

Se você estiver familiarizado com o desenvolvimento do Xamarin. iOS, deverá encontrar a transição para tvOS bastante simples. A maioria das APIs e recursos são os mesmos, no entanto, muitas APIs comuns não estão disponíveis (como WebKit). Além disso, trabalhar com o com o Siri Remote apresenta alguns desafios de design que não estão presentes em dispositivos iOS baseados em tela de toque.

Este guia fornecerá uma introdução a todas as APIs e recursos novos e modificados disponíveis no tvOS 9 para desenvolvedores do Xamarin. tvOS. Para obter mais informações sobre o tvOS, consulte [o desenvolvimento da Apple para a nova documentação da Apple TV](https://developer.apple.com/tvos/) .

<a name="Supported-and-Unsupported-Capabilities" />

## <a name="supported-and-unsupported-capabilities"></a>Recursos com e sem suporte

os aplicativos do tvOS em execução na Apple TV têm os seguintes recursos e recursos com suporte:

- Grupos de Aplicativos
- Modos em tela de fundo
- Proteção de Dados
- Game Center
- Controladores de jogo
- iCloud
- Compras no aplicativo
- Compartilhamento de conjunto de chaves

Os seguintes recursos e funcionalidades não têm suporte:

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

Consulte nossa documentação de [assemblies com](~/ios/tvos/internals/assemblies.md) suporte e [estruturas com suporte](~/ios/tvos/internals/frameworks.md) para obter mais informações.

<a name="Apple-TV-Hardware" />

## <a name="apple-tv-hardware"></a>Hardware da Apple TV

A nova Apple TV tem as seguintes especificações de hardware:

- processador de 64 bits a8
- 32 GB ou 64 GB de armazenamento
- 2GB de RAM
- 10/100 Mbps Ethernet
- WiFi 802.11 a/b/g/n/AC
- resolução de 1080p
- HDMI
- Porta C USB (para uso somente para desenvolvedores e diagnósticos)
- Novo Siri remoto ou Apple TV remoto (com base na região)

### <a name="siri-remote"></a>Siri remoto

Com base na região, o controle remoto da Apple TV fornecido terá uma das duas configurações: Siri remoto ou Apple TV remota.

O Siri remoto está disponível atualmente nos seguintes países:

- Austrália
- Canadá
- France
- Germany
- Japão
- Espanha
- Reino Unido
- Estados Unidos

Todos os outros países receberão o remoto da Apple TV, que substitui o botão Siri por um botão de pesquisa que abre a tela de pesquisa padrão com entrada de texto para pesquisa:

[![](tvos9-images/remote02.png "Siri Remote")](tvos9-images/remote02.png#lightbox)

Para obter mais informações, consulte nossa documentação de [controladores Siri remotos e Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) .

<a name="Apple-TV-Provisioning" />

## <a name="apple-tv-provisioning"></a>Provisionamento da Apple TV

Assim como o desenvolvimento para iOS, o novo tvOS exigirá o perfil de provisionamento adequado para desenvolvimento e distribuição com base na associação da equipe e identidades de assinatura que você já estabeleceu com a Apple.

O provisionamento adequado também é necessário para acessar recursos do tvOS como armazenamentos de dados do iCloud KVS ou CloudKit. Consulte nossos [recursos e armazenamento de dados](~/ios/tvos/app-fundamentals/resources-data-storage.md) para obter informações sobre como dar suporte ao icloud em seus aplicativos Xamarin. tvOS.

Os perfis de provisionamento são criados e instalados da mesma maneira que o trabalho com aplicativos Xamarin. iOS. Como tal, consulte nossa documentação de [provisionamento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md) Ios para obter mais detalhes.

<a name="Apple-TV-Apps" />

## <a name="apple-tv-apps"></a>Aplicativos da Apple TV

O novo hardware da Apple TV e o tvOS 9 dão suporte a dois tipos de aplicativos: aplicativos tradicionais e de cliente-servidor.

<a name="Traditional-Apps" />

### <a name="traditional-apps"></a>Aplicativos tradicionais

Os aplicativos tradicionais são adquiridos da loja de aplicativos da Apple TV e instalados diretamente no dispositivo. Esses aplicativos podem ser jogos, utilitários ou aplicativos de mídia desenvolvidos usando as mesmas estruturas e técnicas que os aplicativos Xamarin. iOS.

Os aplicativos da Apple TV têm um tamanho máximo de 200 MB e podem baixar mais de 2GB de conteúdo usando recursos sob demanda. Consulte nossos [recursos e armazenamento de dados](~/ios/tvos/app-fundamentals/resources-data-storage.md) para obter mais informações.

Veja nossa [Olá, tvOS guia de início rápido](~/ios/tvos/get-started/hello-tvos.md) para se familiarizar com as ferramentas e os conceitos necessários para desenvolver aplicativos tvOS usando o Xamarin. tvOS.

<a name="Summary" />

### <a name="client-server-apps"></a>Aplicativos cliente-servidor

Além dos aplicativos tradicionais instalados, a Apple TV facilita a criação de aplicativos de streaming de mídia cliente-servidor baseados na Web usando tecnologias da Web (HTTPS, XML e JavaScript). Você criará a interface do usuário usando a linguagem de marcação TVML da Apple e usará o JavaScript para definir os comportamentos do aplicativo usando o TVMLKit.

Para obter mais informações, consulte sobre a documentação referência da Apple [TV Markup Language](https://developer.apple.com/library/prerelease/tvos/documentation/LanguagesUtilities/Conceptual/ATV_Template_Guide/index.html#//apple_ref/doc/uid/TP40015064)da Apple, [referência do TVJS Framework](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLJS/Reference/TVJSFrameworkReference/index.html#//apple_ref/doc/uid/TP40016076), [referência da estrutura do TVMLKit](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLKit/Reference/TVMLKit_Collection/index.html#//apple_ref/doc/uid/TP40016429), [sobre http Live streaming](https://developer.apple.com/library/prerelease/tvos/referencelibrary/GettingStarted/AboutHTTPLiveStreaming/about/about.html#//apple_ref/doc/uid/TP40013978) e [especificação de criação de HLS para Apple TV](https://developer.apple.com/services-account/download?path=/Documentation/HLS_Authoring_Specification_for_Apple_TV/HLS_Authoring_Specification_for_Apple_TV.pdf).

<a name="User-Interface-Challenges" />

## <a name="user-interface-challenges"></a>Desafios da interface do usuário

Diferentemente do iOS ou do OS X, a Apple TV não tem uma tela touch ou mouse que permite ao usuário selecionar e interagir diretamente com um aplicativo ou seu conteúdo. Em vez disso, eles acessam o novo Siri remoto ou um controlador de jogo Bluetooth para navegar pela interface do usuário de um aplicativo. Para obter mais informações, consulte nossa documentação de [controladores Siri remotos e Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) .

Além disso, a experiência geral do usuário é drasticamente diferente de aplicativos iOS ou Mac que tendem a ser experiências de usuário único. Com a Apple TV, as experiências do usuário tendem a ser mais sociais por natureza, onde várias pessoas podem estar sentado no sofá interagindo com um único aplicativo e entre si. Para criar uma experiência de aplicativo de TV da Apple bem-sucedida (um novo aplicativo ou portando um existente), essas alterações devem ser levadas em consideração. 

<a name="Summary" />

### <a name="working-with-focus-and-parallax-images"></a>Trabalhando com imagens de foco e da Parallax

Conforme mencionado acima, os usuários do seu aplicativo Xamarin. tvOS não serão interagindo com a interface diretamente como com o iOS, no qual tocam as imagens na tela do dispositivo, mas indiretamente de toda a sala usando o Siri remoto. Para apresentar e lidar com essa interação do usuário, a Apple TV usa um modelo baseado em foco. 

Conforme as alterações de foco, animações e efeitos sutis (como o efeito de da Parallax em imagens) são usadas para identificar claramente o item de interface do usuário que tem foco no momento.

Se o usuário fizer um gesto circular e lento no Siri remoto, o item focalizado irá Sway em tempo real em resposta a esse movimento. À medida que o Sway ocorre, um Sheen iluminado é aplicado à sua imagem, fazendo com que a superfície pareça brilhar. Após uma determinada quantidade de inatividade, qualquer conteúdo fora do foco será esmaecido e o item focado aumentará ainda mais.

Para obter mais informações, consulte nossa documentação [trabalhando com navegação e foco](~/ios/tvos/app-fundamentals/navigation-focus.md) e [trabalhando com ícones e imagens](~/ios/tvos/app-fundamentals/icons-images.md) .

<a name="The-Home-Screen" />

### <a name="the-home-screen"></a>A tela inicial

A tela inicial da Apple TV mostra todos os aplicativos que estão instalados e fornece uma maneira de acessar as preferências do usuário:

[![](tvos9-images/home01.png "The Home Screen")](tvos9-images/home01.png#lightbox)

O usuário navega em uma grade de ícones de aplicativo usando gestos de toque no Siri remoto usando o foco para selecionar um aplicativo e iniciá-lo. O ícone do aplicativo é a sua primeira chance de fazer uma boa impressão sobre o usuário potencial e deve comunicar a finalidade do seu aplicativo em um relance.

Cada aplicativo deve fornecer uma versão pequena e uma grande de seu ícone de aplicativo. O ícone pequeno será usado na tela inicial da Apple TV quando o aplicativo for instalado. A versão grande é usada pela loja de aplicativos. O ícone do aplicativo grande deve imitar a aparência da versão do ícone pequeno.

Para obter mais informações, consulte nossa documentação sobre [como trabalhar com ícones e imagens](~/ios/tvos/app-fundamentals/icons-images.md) .

<a name="The-Top-Shelf" />

### <a name="the-top-shelf"></a>A prateleira superior

Se o usuário tiver colocado seu aplicativo Xamarin. tvOS na linha superior da tela inicial da Apple TV, uma imagem de prateleira grande será exibida quando seu aplicativo for selecionado pelo usuário. Essa imagem deve destacar os recursos do seu aplicativo ou fornecer links diretos para seu conteúdo.

[![](tvos9-images/topshelf01.png "The Top Shelf")](tvos9-images/topshelf01.png#lightbox)

A imagem da prateleira superior pode ser fornecida como um único arquivo estático `.png` ou `.lsr` ou pode ser criada dinamicamente em tempo de execução como uma única linha de itens com foco.

Em vez de exibir uma imagem de prateleira superior estática, ela pode conter uma linha dinâmica ou itens de foco ou um conjunto dinâmico de faixas de rolagem. Ambos os estilos dinâmicos permitem que você realce o conteúdo fornecido pelo seu aplicativo ou salte para seus recursos mais usados.

Para obter mais informações, consulte nossa documentação sobre [como trabalhar com ícones e imagens](~/ios/tvos/app-fundamentals/icons-images.md) e a [referência da estrutura TVServices](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412) da Apple para obter mais informações sobre como adicionar uma extensão de prateleira superior ao seu aplicativo para fornecer conteúdo de prateleira superior dinâmico.

## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [Criando aplicativos para tvOS com Xamarin (vídeo)](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
