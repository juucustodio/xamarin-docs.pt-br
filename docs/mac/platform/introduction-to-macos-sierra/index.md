---
title: Introdução ao macOS Sierra
description: Este artigo apresenta todas as APIs e recursos novos e modificados disponíveis em macOS Sierra para desenvolvedores do Xamarin. Mac.
ms.prod: xamarin
ms.assetid: 71A8A737-F310-4320-BD23-743AA1E9033C
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 8715559db3f551773f41659a0c7c36adb29f0d1e
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437229"
---
# <a name="introduction-to-macos-sierra"></a>Introdução ao macOS Sierra

Com o novo macOS Sierra, o desenvolvedor pode aproveitar as novas APIs que permitem que o usuário final interaja com seus aplicativos e sites de maneiras anteriores indisponíveis. Por exemplo, a Apple agora permite que os sites forneçam aos clientes a opção de pagar com segurança por meio de Apple Pay e aprimoramentos para a estrutura de metal impulsionam os elementos gráficos de um aplicativo e o potencial de computação. 

Para obter mais informações sobre macOS Sierra, consulte a documentação do [MacOS + aplicativos](https://developer.apple.com/macos/) da Apple.

<a name="Whats-New-in-macOS-Sierra"></a>

## <a name="whats-new-in-macos-sierra"></a>O que há de novo no macOS Sierra

A Apple adicionou várias novas APIs e serviços em macOS Sierra junto com vários aprimoramentos aos recursos existentes, incluindo:

<a name="Apple-File-System"></a>

### <a name="apple-file-system"></a>Sistema de arquivos da Apple

Com o macOS Sierra, a Apple lançou o novo sistema de arquivos da Apple como um sistema de arquivos moderno para iOS, macOS, tvOS e watchOS. O sistema de arquivos da Apple foi otimizado para armazenamento flash e SSD e fornece os seguintes recursos: criptografia forte, metadados de cópia em gravação, compartilhamento de espaço, clonagem para arquivos e diretórios, instantâneos, dimensionamento rápido de diretório e primitivos de salvamento seguro atômico.

Para obter mais informações, consulte o [Guia do sistema de arquivos Apple](https://developer.apple.com/library/prerelease/content/documentation/FileManagement/Conceptual/APFS_Guide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40016999)da Apple.

<a name="Apple-Pay-Enhancements"></a>

### <a name="apple-pay-enhancements"></a>Aprimoramentos de Apple Pay

A Apple fez vários aprimoramentos para Apple Pay em macOS Sierra que permitem que o usuário faça pagamentos seguros de sites.

Com o macOS Sierra, foram adicionadas várias novas APIs que funcionam com macOS Sierra, iOS e watchOS para dar suporte a redes de pagamento dinâmico e um novo ambiente de teste da área restrita.

a macOS Sierra inclui a nova estrutura ApplePay JavaScript que permite ao desenvolvedor incorporar Apple Pay diretamente em sites do iOS e macOS baseados no Safari. Para sites que dão suporte a Apple Pay, o usuário pode autorizar o pagamento usando o iPhone ou o Apple Watch.

Para obter mais informações, consulte a referência da [estrutura do APPLEPAY js](https://developer.apple.com/reference/applepayjs) da Apple.

<a name="Building-Modern-macOS-Apps"></a>

### <a name="building-modern-macos-apps"></a>Compilando aplicativos macOS modernos

Os aplicativos macOS modernos, como o navegador da Web do Safari da Apple, páginas de processador de palavras e números distribuídos, usam muitas novas tecnologias para apresentar uma interface do usuário unificada e sensível ao contexto que se afasta de elementos de interface de usuário tradicionais, como painéis flutuantes e várias janelas abertas.

[![Um exemplo de uma janela Mac com guias](images/content08.png)](images/content08.png#lightbox)

Nosso guia [criando aplicativos MacOS modernos](~/mac/platform/introduction-to-macos-sierra/modern-cocoa-apps.md) aborda várias dicas, recursos e técnicas que um desenvolvedor pode usar para criar um aplicativo MacOS moderno no Xamarin. Mac.

<a name="CloudKit-Data-Sharing"></a>

### <a name="cloudkit-data-sharing"></a>Compartilhamento de dados CloudKit

A estrutura CloudKit foi expandida em macOS Sierra para permitir que o usuário Compartilhe registros de forma rápida e fácil a partir de seus bancos de dados de iCloud privados.

O CloudKit fornece uma interface do usuário completa para enviar e aceitar convites de registros compartilhados e o usuário tem controle de leitura/gravação completo sobre as pessoas que têm acesso aos registros.

Para obter mais informações, consulte referência da [estrutura do CloudKit](https://developer.apple.com/reference/clockkit) da Apple e [referência da estrutura do CloudKit js](https://developer.apple.com/reference/cloudkitjs).

> [!IMPORTANT]
> A Apple [fornece ferramentas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ajudar os desenvolvedores a lidar adequadamente com o GDPR (Regulamento Geral sobre a Proteção de Dados) da União Europeia.

<a name="Safari-App-Extensions-Support"></a>

### <a name="safari-app-extensions-support"></a>Suporte a extensões de aplicativo do Safari

As extensões de aplicativo do Safari permitem que o aplicativo estenda o comportamento do navegador da Web do Safari enquanto estiver intimamente integrado com macOS Sierra. Como as extensões de aplicativo do macOS Safari funcionam de forma semelhante às extensões de aplicativo do Safari do iOS, elas são fáceis de portar de um sistema para outro.

Para obter mais informações, consulte o [Guia de programação de extensões de aplicativo do Safari](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternetWeb/Conceptual/SafariAppExtension_PG/index.html#//apple_ref/doc/uid/TP40017319)da Apple.

<a name="Security-and-Privacy-Enhancements"></a>

### <a name="security-and-privacy-enhancements"></a>Aprimoramentos de segurança e privacidade

A Apple fez vários aprimoramentos na segurança e na privacidade em macOS Sierra que ajudarão o aplicativo a melhorar a segurança do aplicativo e a garantir a privacidade do usuário final, incluindo o seguinte:

- A nova `NSAllowsArbitraryLoadsInWebContent` chave pode ser adicionada ao arquivo do aplicativo `Info.plist` e permitirá que as páginas da Web sejam carregadas corretamente enquanto a proteção de ATS (segurança de transporte da Apple) ainda estiver habilitada para o restante do aplicativo.
- A API de CDSA (arquitetura de segurança de dados comum) foi preterida e deve ser substituída pela API SecKey para gerar chaves assimétricas.
- Para todas as conexões SSL/TLS, a cifra simétrica RC4 agora está desabilitada por padrão. Além disso, a API de transporte seguro não dá mais suporte a SSLv3 e é recomendável que o aplicativo pare de usar a criptografia SHA-1 e 3DES assim que possível.
- Como a nova área de transferência no iOS 10 e macOS Sierra permite que o usuário Copie e cole entre os dispositivos, a API foi expandida para permitir que uma área de transferência seja limitada a um dispositivo específico e tenha o carimbo de data/hora para ser limpo automaticamente em um determinado ponto. Além disso, os chamados pasteboards não são mais mantidos e devem ser substituídos pelos contêineres de área de ti compartilhados.
- Se o aplicativo acessar dados protegidos (como o calendário do usuário), ele _deverá_ declarar essa intenção com a chave de valor da cadeia de caracteres de finalidade correta em seu `Info.plist` arquivo ( `NSCalendarUsageDescription` no caso do calendário).
- Os aplicativos assinados pelo desenvolvedor que não são entregues por meio da Mac App Store agora podem aproveitar o CloudKit, o conjunto de chaves do iCloud, a unidade iCloud, as notificações por push remotos, os direitos MapKit e VPN.
- macOS Sierra não dá mais suporte ao fornecimento de código ou dados externos junto com o aplicativo de signatário de código em seu arquivo zip ou imagem de disco não assinado, pois o caminho de tempo de execução não é conhecido antes do tempo de execução.

Além disso, os aplicativos em execução no macOS Sierra (ou posterior) devem declarar estaticamente sua intenção de acessar recursos específicos ou informações do usuário inserindo uma ou mais chaves de privacidade específicas em seus `Info.plist` arquivos que explicam ao usuário por que o aplicativo deseja obter acesso.

Como o macOS Sierra compartilha essas alterações com o iOS 10, consulte nosso guia de [aprimoramentos de segurança e privacidade](~/ios/app-fundamentals/security-privacy.md) do IOS 10 para obter mais informações.

<a name="Smart-Card-Driver-Extension-Support"></a>

### <a name="smart-card-driver-extension-support"></a>Suporte à extensão de driver de cartão inteligente

Com o macOS Sierra, o aplicativo pode criar `NSExtension` drivers de cartão inteligente com base que permitem acesso somente leitura ao conteúdo de determinados tipos de cartões inteligentes. Essas informações são apresentadas dentro do conjunto de chaves do sistema (substituindo o método de arquitetura de segurança de dados comum preterido).

para obter mais informações, pleas consulte a [referência da estrutura CryptoTokenKit](https://developer.apple.com/reference/cryptotokenkit)da Apple.

<a name="Unified-Logging"></a>

### <a name="unified-logging"></a>Registro em log unificado

O registro em log unificado fornece ao aplicativo uma única API para mensagens eficientes em todos os níveis do sistema. Com o registro em log unificado, o aplicativo tem controle refinado sobre vários níveis de registro em log que incluem controles de privacidade e acompanhamento de atividades para uma depuração mais fácil. 

O registro em log fornece correlação automática de mensagens quando o rastreamento de atividades e o registro em log são usados juntos.

o macOS Sierra inclui um novo aplicativo de console (em aplicativos/utilitários) capaz de exibir dados de log de várias fontes, incluindo dispositivos conectados. Ele também dá suporte a pesquisas de token e salvas e exibe conexões entre mensagens relacionadas entre vários processos.

Além disso, as mensagens de log podem ser exibidas e mantidas usando ferramentas de linha de comando.

Para obter mais informações, consulte a [referência de registro em log](https://developer.apple.com/documentation/os/logging)da Apple.

<a name="Wide-Color"></a>

### <a name="wide-color"></a>Cores amplas

macOS Sierra estende o suporte para formatos de pixel de intervalo estendido e espaços de cores de ampla gama em todo o sistema, incluindo estruturas como gráficos principais, imagem principal, metal e AVFoundation. O suporte para dispositivos com monitores largos de cores é mais facilitou, fornecendo esse comportamento em toda a pilha gráfica.

Além disso, foi `AppKit` modificado para funcionar no novo colorspace **sRGB** estendido, facilitando a combinação de cores em escalas de cores amplas sem perda significativa de desempenho.

A Apple oferece as seguintes práticas recomendadas ao trabalhar com cores amplas:

- `NSColor` Agora, o usa o espaço de cores sRGB e não fixe mais valores para o `0.0` intervalo de to `1.0` . Se o aplicativo depender do comportamento anterior do fixe, será necessário modificá-lo para macOS Sierra.
- Ao usar uma API de nível baixo, como gráficos principal ou metal para fornecer processamento de imagens, o aplicativo deve usar um espaço de cores de intervalo estendido e um formato de pixel que dê suporte a valores de ponto flutuante de 16 bits. Quando necessário, o aplicativo terá que fixe manualmente os valores de componente de cor.
- Os principais gráficos, a imagem principal e os sombreadores de desempenho de metal fornecem novos métodos para conversão entre os dois espaços de cores.

Para obter mais informações, consulte nosso [introdução ao guia de cores largo](~/ios/platform/wide-color.md) .

<a name="Additional-Framework-Changes"></a>

## <a name="additional-framework-changes"></a>Alterações de estrutura adicionais

Além das principais alterações de estrutura e adições listadas acima, a Apple fez muitas alterações de estrutura secundárias adicionais no macOS Sierra.

Para saber mais, consulte nosso guia de [alterações de estrutura adicionais](~/mac/platform/introduction-to-macos-sierra/additional-framework-changes.md) .

<a name="Deprecated-APIs"></a>

## <a name="deprecated-apis"></a>APIs obsoletas

As seguintes APIs foram preteridas no macOS Sierra:

- Não há mais suporte para o sistema de arquivos padrão HFS.

Consulte a documentação de [diffs da API do MacOS v 10.12](https://developer.apple.com/library/archive/releasenotes/General/APIDiffsMacOS10_12/index.html) da Apple para obter uma lista completa de substituições e alterações.

## <a name="related-links"></a>Links Relacionados

- [Amostras de Mac](/samples/browse/?products=xamarin&term=Xamarin.Mac)
- [O que há de novo no macOS 10,12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)