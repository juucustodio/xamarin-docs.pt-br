---
title: Introdução ao macOS Serra
description: Este artigo apresenta todas as APIs e recursos disponíveis no macOS Serra novos e modificados para desenvolvedores de Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 71A8A737-F310-4320-BD23-743AA1E9033C
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 5ab373d708d47ad7c3dbbf4507284be04a1f9934
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-macos-sierra"></a>Introdução ao macOS Serra

Com nova Serra macOS, o desenvolvedor pode tirar proveito de novas APIs que permitem que o usuário final interagir com seus aplicativos e sites de maneiras anteriormente não está disponíveis. Por exemplo, Apple agora permite que sites oferecer aos clientes a opção de pagamento com segurança por meio de pagamento da Apple e aprimoramentos para o aumento de Metal framework gráficos do aplicativo e computação em potencial. 

Para obter mais informações sobre macOS serra, consulte da Apple [macOS + aplicativos](https://developer.apple.com/macos/) documentação.

<a name="Whats-New-in-macOS-Sierra" />

## <a name="whats-new-in-macos-sierra"></a>O que há de novo no macOS Serra

Apple adicionou várias novas APIs e serviços macOS Serra junto com muitas melhorias em recursos existentes, incluindo:

<a name="Apple-File-System" />

### <a name="apple-file-system"></a>Sistema de arquivos da Apple

Com macOS serra, Apple lançou o novo sistema de arquivos de Apple como um sistema de arquivos modernos para iOS, macOS, tvOS e watchOS. O sistema de arquivos Apple foi otimizado para armazenamento SSD e Flash e fornece os seguintes recursos: criptografia forte, metadados de cópia na gravação de espaço de compartilhamento, clonagem de arquivos e diretórios, snapshots, dimensionamento rápido de diretório e atômicos primitivos de salvamento safe.

Para obter mais informações, consulte da Apple [guia de sistema de arquivo Apple](https://developer.apple.com/library/prerelease/content/documentation/FileManagement/Conceptual/APFS_Guide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40016999).

<a name="Apple-Pay-Enhancements" />

### <a name="apple-pay-enhancements"></a>Aprimoramentos de pagamento da Apple

Apple fez várias melhorias para pagamento da Apple macOS Serra que permitem que o usuário faça pagamentos seguros de sites.

Com macOS serra, várias novas APIs foram adicionadas que funcionam com macOS serra, iOS e watchOS para dar suporte a redes de pagamento dinâmico e um novo ambiente de teste de área restrita.

macOS Serra inclui a nova estrutura de ApplePay Javascript que permite ao desenvolvedor incorporar o pagamento da Apple diretamente no iOS e macOS sites baseados no Safari. Para sites que dão suporte ao pagamento da Apple, o usuário pode autorizar pagamento usando seu iPhone ou o Apple Watch.

Para obter mais informações, consulte da Apple [ApplePay JS Framework](https://developer.apple.com/reference/applepayjs) referência.

<a name="Building-Modern-macOS-Apps" />

### <a name="building-modern-macos-apps"></a>Criando macOS modernos aplicativos

Aplicativos de macOS modernos como da Apple navegador web Safari, processador de textos de páginas e uso de folha de espalhamento números muitas novas tecnologias para apresentar uma Interface unificada e sensível ao contexto do usuário que elimina com tradicionais elementos de interface do usuário, como painéis flutuantes e vários abrir Windows.

[![Um exemplo de uma janela com guias do Mac](images/content08.png)](images/content08.png#lightbox)

Nosso [macOS construção modernos aplicativos](~/mac/platform/introduction-to-macos-sierra/modern-cocoa-apps.md) guia abrange várias dicas, recursos e técnicas de um desenvolvedor pode usar para compilar um aplicativo moderno macOS em Xamarin.Mac.

<a name="CloudKit-Data-Sharing" />

### <a name="cloudkit-data-sharing"></a>Compartilhamento de dados CloudKit

A estrutura CloudKit foi expandida em macOS serra para permitir que usuário rapidamente e facilmente compartilhar registros ou conjuntos de registros de seus bancos de dados particular iCloud.

CloudKit fornece uma interface de usuário completo para enviar e aceitar convites de registro compartilhados e o usuário tem controle de leitura/gravação completa sobre as pessoas que têm acesso para os registros.

Para obter mais informações, consulte da Apple [CloudKit Framework referência](https://developer.apple.com/reference/clockkit) e [CloudKit JS Framework referência](https://developer.apple.com/reference/cloudkitjs).

<a name="Safari-App-Extensions-Support" />

### <a name="safari-app-extensions-support"></a>Suporte a extensões de aplicativo Safari

Extensões de aplicativo do Safari permitem que o aplicativo estender o comportamento do navegador da web Safari enquanto está sendo integrado com macOS Serra. Como extensões de aplicativo do Safari macOS funcionam semelhante para extensões de aplicativo do Safari do iOS, podem ser facilmente a porta de um sistema para outro.

Para obter mais informações, consulte da Apple [guia de programação de extensão do Safari aplicativo](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternetWeb/Conceptual/SafariAppExtension_PG/index.html#//apple_ref/doc/uid/TP40017319).

<a name="Security-and-Privacy-Enhancements" />

### <a name="security-and-privacy-enhancements"></a>Segurança e privacidade aprimoramentos

Apple fez diversos aprimoramentos de segurança e privacidade no macOS Serra que ajudará o aplicativo melhorar a segurança do aplicativo e garantir a privacidade do usuário final incluem o seguinte:

- O novo `NSAllowsArbitraryLoadsInWebContent` chave pode adicionar o aplicativo `Info.plist` de arquivo e permitirá que páginas da web para carregar corretamente enquanto a proteção de segurança de transporte da Apple (ATS) ainda está habilitada para o restante do aplicativo.
- O Common dados segurança arquitetura (CDSA) API foi preterido e deve ser substituído com a API SecKey para gerar chaves assimétricas.
- Para todas as conexões SSL/TLS, a criptografia simétrica RC4 agora é desabilitada por padrão. Além disso, a API de transporte seguro não oferece mais suporte SSLv3 e é recomendável que o aplicativo parar de usar criptografia SHA-1 e 3DES assim que possível.
- Como a nova área de transferência no iOS 10 e macOS Serra permite que o usuário copiar e colar entre dispositivos, a API foi expandida para permitir uma área de transferência ser limitada a um dispositivo específico e marca a ser limpo automaticamente em um determinado ponto. Além disso, áreas de trabalho nomeadas não são mais mantidas e devem ser substituídas com os contêineres de área de trabalho compartilhados.
- Se o aplicativo acessar dados protegidos (por exemplo, o calendário do usuário), ele _deve_ declarar essa intenção com a chave de valor de cadeia de caracteres de finalidade correta no seu `Info.plist` arquivo (`NSCalendarUsageDescription` no caso de calendário).
- Desenvolvedor Signed aplicativos que não são entregues por meio de Mac App Store agora podem tirar proveito de CloudKit, iCloud conjunto de chaves, iCloud unidade, notificações por push remoto, VPN e MapKit direitos.
- macOS Serra não suporta fornecendo dados junto com o aplicativo de signatário de código em seu arquivo zip ou imagem de disco não assinados ou código externo, como o caminho de tempo de execução não é conhecido antes do tempo de execução.

Além disso, os aplicativos executados em macOS Serra (ou posterior) estaticamente deve declarar sua intenção para acessar informações do usuário ou recursos específicos, inserindo uma ou mais chaves específica de privacidade em seus `Info.plist` arquivos explicam para o usuário por que o aplicativo deseja obter acesso.

Como macOS Serra compartilha essas alterações com iOS 10, consulte nosso iOS 10 [segurança e privacidade aprimoramentos](~/ios/app-fundamentals/security-privacy.md) guia para obter mais informações.

<a name="Smart-Card-Driver-Extension-Support" />

### <a name="smart-card-driver-extension-support"></a>Suporte à extensão de Driver de cartão inteligente

Com macOS serra, o aplicativo pode criar `NSExtension` com base em drivers de cartão inteligente que permite acesso somente leitura ao conteúdo de certos tipos de cartões inteligentes. Essas informações são apresentadas em seguida, em que o conjunto de chaves do sistema (substituindo o método de arquitetura comum de segurança de dados preterido).

Para obter mais informações, Pleas veja Apple [CryptoTokenKit Framework referência](https://developer.apple.com/reference/cryptotokenkit).

<a name="Unified-Logging" />

### <a name="unified-logging"></a>Registro em log unificado

Log unificado oferece o aplicativo com uma única API de mensagens eficiente em todos os níveis do sistema. Com log unificado do aplicativo tem controle refinado de vários níveis de log que incluem controles de privacidade e a atividade de rastreamento para depuração mais fácil. 

O log fornece correlação automática de mensagem quando a atividade de rastreamento e registro em log é usada juntos.

macOS Serra inclui um novo aplicativo de Console (em aplicativos/utilitários) que é capaz de exibir dados de log de várias fontes, incluindo dispositivos conectados. Ele também dá suporte a pesquisas salvas e editáveis e exibe as conexões entre as mensagens relacionadas entre vários processos.

Além disso, as mensagens de log podem ser exibidas e mantida usando as ferramentas de linha de comando.

Para obter mais informações, consulte da Apple [referência de log](https://developer.apple.com/reference/os/1891852-logging).

<a name="Wide-Color" />

### <a name="wide-color"></a>Cores de largura

macOS Serra estende o suporte para formatos de pixel do intervalo estendido e espaços de toda a gama de cores em todo o sistema, incluindo estruturas, como gráficos de núcleo, imagem principal, sistema operacional e AVFoundation. Suporte para dispositivos com telas de cores de largura é facilitado mais fornecendo esse comportamento em toda a pilha do gráfico inteiro.

Além disso, `AppKit` foi modificado para funcionar no novo estendida **sRGB** espaço de cor, tornando mais fácil misturar cores gamas de cor ampla sem perda de desempenho significativa.

Apple oferece as seguintes práticas recomendadas ao trabalhar com cores amplas:

- `NSColor` agora usa o sRGB espaço de cores e não poderá mais clamp valores para o `0.0` para `1.0` intervalo. Se o aplicativo utiliza o comportamento de fixação anterior, ele precisará ser modificada para macOS Serra.
- Ao usar uma API de nível inferior, como gráficos de núcleo ou sistema operacional para fornecer processamento de imagem, o aplicativo deve usar um intervalo estendido cor espaço e pixel formato que oferece suporte a valores de ponto flutuante de 16 bits. Quando necessário, o aplicativo precisará clamp manualmente os valores de componente de cor.
- Gráficos de núcleo, imagem principal e sombreadores de desempenho do sistema operacional fornecem novos métodos para converter entre os espaços de duas cores.

Para obter mais informações, consulte nosso [Introdução à cor ampla](~/ios/platform/wide-color.md) guia.

<a name="Additional-Framework-Changes" />

## <a name="additional-framework-changes"></a>Alterações de estrutura adicionais

Além das alterações de estrutura principais e adições listadas acima, a Apple fez muitas alterações de estrutura secundárias adicionais na macOS Serra.

Para obter mais informações, consulte nosso [alterações adicionais do Framework](~/mac/platform/introduction-to-macos-sierra/additional-framework-changes.md) guia.

<a name="Deprecated-APIs" />

## <a name="deprecated-apis"></a>APIs obsoletas

As seguintes APIs macOS Serra foram preteridas:

- Não há suporte para o sistema de arquivos padrão HFS.

Consulte da Apple [v10.12 OS X comparações API](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/APIDiffsMacOS10_12/index.html) documentação para obter uma lista completa de substituições e alterações.

## <a name="related-links"></a>Links relacionados

- [Amostras de Mac](https://developer.xamarin.com/samples/mac/)
- [O que há de novo nos X 10.12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
