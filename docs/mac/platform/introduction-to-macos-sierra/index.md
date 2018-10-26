---
title: Introdução ao macOS Sierra
description: Este artigo apresenta todas as APIs e recursos disponíveis no macOS Sierra novos e modificados para desenvolvedores do xamarin. Mac.
ms.prod: xamarin
ms.assetid: 71A8A737-F310-4320-BD23-743AA1E9033C
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 5a944fd8f7dcfdcbb3f025c92b4afac35673416f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111006"
---
# <a name="introduction-to-macos-sierra"></a>Introdução ao macOS Sierra

Com o novo macOS Sierra, o desenvolvedor pode tirar proveito das novas APIs que permitem que o usuário final interaja com seus aplicativos e sites de maneiras anteriormente indisponíveis. Por exemplo, Apple agora permite que os sites fornecer aos clientes a opção de pagamento com segurança por meio do Apple Pay e aprimoramentos para o aumento de Metal framework gráficos do aplicativo e computação em potencial. 

Para obter mais informações sobre o macOS Sierra, consulte da Apple [macOS + aplicativos](https://developer.apple.com/macos/) documentação.

<a name="Whats-New-in-macOS-Sierra" />

## <a name="whats-new-in-macos-sierra"></a>O que há de novo no macOS Sierra

Apple adicionou várias novas APIs e serviços no macOS Sierra junto com muitos aprimoramentos aos recursos existentes, incluindo:

<a name="Apple-File-System" />

### <a name="apple-file-system"></a>Sistema de arquivos da Apple

Com o macOS Sierra, Apple lançou o novo sistema de arquivos Apple como um sistema de arquivos modernos para iOS, macOS, tvOS e watchOS. O sistema de arquivos da Apple foi otimizado para armazenamento em Flash e SSD e fornece os seguintes recursos: criptografia forte, metadados de cópia na gravação, compartilhamento, a clonagem para arquivos e diretórios, instantâneos, dimensionamento rápido de diretório e primitivos de salvamento safe atômicos de espaço.

Para obter mais informações, consulte da Apple [guia de sistema de arquivos da Apple](https://developer.apple.com/library/prerelease/content/documentation/FileManagement/Conceptual/APFS_Guide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40016999).

<a name="Apple-Pay-Enhancements" />

### <a name="apple-pay-enhancements"></a>Aprimoramentos do Apple Pay

Apple fez várias melhorias para Apple Pay em macOS Serra que permitem ao usuário fazer pagamentos seguros de sites.

Com o macOS Sierra, diversas APIs novas foram adicionadas que funcionam com macOS Sierra, iOS e watchOS para dar suporte a redes de pagamento dinâmico e um novo ambiente de teste de área restrita.

macOS Sierra inclui a nova estrutura de ApplePay Javascript que permite que o desenvolvedor incorporar o Apple Pay diretamente no iOS e macOS sites da Web baseados no Safari. Para sites que dão suporte ao Apple Pay, o usuário pode autorizar o pagamento usando seu iPhone ou o Apple Watch.

Para obter mais informações, consulte da Apple [ApplePay JS Framework](https://developer.apple.com/reference/applepayjs) referência.

<a name="Building-Modern-macOS-Apps" />

### <a name="building-modern-macos-apps"></a>Compilar aplicativos macOS modernos

Aplicativos macOS modernos como o navegador Safari da Apple, processador de texto de páginas e uso de folha de espalhamento de números muitas novas tecnologias para apresentar uma Interface de usuário unificada, sensíveis ao contexto que acaba com elementos de interface do usuário tradicionais como painéis flutuantes e vários abrir Windows.

[![Um exemplo de uma janela com guias do Mac](images/content08.png)](images/content08.png#lightbox)

Nossos [aplicativos macOS modernos construção](~/mac/platform/introduction-to-macos-sierra/modern-cocoa-apps.md) guia abrange várias dicas, recursos e técnicas de um desenvolvedor pode usar para criar um aplicativo do macOS modernos no xamarin. Mac.

<a name="CloudKit-Data-Sharing" />

### <a name="cloudkit-data-sharing"></a>Compartilhamento de dados do CloudKit

A estrutura do CloudKit foi expandida no macOS Sierra para permitir que usuário rápida e facilmente compartilhar registros ou conjuntos de registros de seus bancos de dados privados do iCloud.

CloudKit fornece uma interface do usuário completa para enviar e aceitar convites de registros compartilhadas e o usuário tem controle de leitura/gravação completa sobre as pessoas que têm acesso aos registros.

Para obter mais informações, consulte da Apple [referência de estrutura do CloudKit](https://developer.apple.com/reference/clockkit) e [referência de estrutura do CloudKit JS](https://developer.apple.com/reference/cloudkitjs).

> [!IMPORTANT]
> A Apple [fornece ferramentas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ajudar os desenvolvedores a lidar adequadamente com o GDPR (Regulamento Geral sobre a Proteção de Dados) da União Europeia.

<a name="Safari-App-Extensions-Support" />

### <a name="safari-app-extensions-support"></a>Suporte de extensões de aplicativo do Safari

Extensões de aplicativo do Safari que o aplicativo estender o comportamento do navegador da web Safari enquanto está sendo integrado com o macOS Sierra. Uma vez que as extensões de aplicativo do Safari macOS funcionar semelhantes a extensões de aplicativo do Safari do iOS, eles são fáceis para a porta de um sistema para outro.

Para obter mais informações, consulte da Apple [guia de programação de extensão do Safari aplicativo](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternetWeb/Conceptual/SafariAppExtension_PG/index.html#//apple_ref/doc/uid/TP40017319).

<a name="Security-and-Privacy-Enhancements" />

### <a name="security-and-privacy-enhancements"></a>Aperfeiçoamentos de segurança e privacidade

Apple fez várias melhorias de segurança e privacidade no macOS Sierra que ajudará o aplicativo, melhorar a segurança do aplicativo e garantir a privacidade do usuário final incluindo o seguinte:

- O novo `NSAllowsArbitraryLoadsInWebContent` chave pode adicionar o aplicativo `Info.plist` de arquivo e permitirá que as páginas da web para carregar corretamente enquanto a proteção de segurança de transporte da Apple (ATS) ainda está habilitada para o restante do aplicativo.
- O Common Data Security arquitetura (CDSA) API foi preterida e deve ser substituído com a API SecKey para gerar as chaves assimétricas.
- Para todas as conexões SSL/TLS, a criptografia simétrica de RC4 agora é desabilitada por padrão. Além disso, a API de transporte seguro não oferece suporte a SSLv3 e é recomendável que o aplicativo parar de usar criptografia SHA-1 e 3DES assim que possível.
- Como a nova área de transferência no iOS 10 e no macOS Sierra permite que o usuário copiar e colar entre dispositivos, a API foi expandida para permitir uma área de transferência a ser limitado a um dispositivo específico e data/hora a ser limpa automaticamente em um determinado ponto. Além disso, áreas de trabalho nomeadas não são persistentes e devem ser substituídas com os contêineres de área de trabalho compartilhados.
- Se o aplicativo acessa os dados protegidos (por exemplo, o calendário do usuário), ele _devem_ declarar essa intenção com a chave de valor de cadeia de caracteres de finalidade correto no seu `Info.plist` arquivo (`NSCalendarUsageDescription` no caso de calendário).
- Desenvolvedor Signed aplicativos que não são entregues por meio do Mac App Store agora podem tirar proveito do CloudKit, iCloud ao conjunto de chaves, iCloud Drive, notificações por push remoto, VPN e MapKit direitos.
- macOS Sierra não oferece suporte a entrega de código externo ou dados junto com o aplicativo de signatário de código em seu arquivo zip ou a imagem de disco não assinado como o caminho de tempo de execução não é conhecido antes do tempo de execução.

Além disso, os aplicativos em execução no macOS Sierra (ou posterior) estaticamente deve declarar sua intenção para acessar informações do usuário ou recursos específicos, inserindo uma ou mais chaves específicas de privacidade em seus `Info.plist` arquivos explicam ao usuário por que o aplicativo deseja obter acesso.

Uma vez que o macOS Sierra compartilha essas alterações com iOS 10, consulte nosso iOS 10 [aperfeiçoamentos de segurança e privacidade](~/ios/app-fundamentals/security-privacy.md) guia para obter mais informações.

<a name="Smart-Card-Driver-Extension-Support" />

### <a name="smart-card-driver-extension-support"></a>Suporte à extensão de Driver de cartão inteligente

Com o macOS Sierra, o aplicativo pode criar `NSExtension` com base em drivers de cartão inteligente que permite acesso somente leitura ao conteúdo de determinados tipos de cartões inteligentes. Essas informações são apresentadas, em seguida, dentro do conjunto de chaves do sistema (substituindo o método de arquitetura comum de segurança de dados preterido).

Para obter mais informações, do Pleas consulte Apple [referência de estrutura CryptoTokenKit](https://developer.apple.com/reference/cryptotokenkit).

<a name="Unified-Logging" />

### <a name="unified-logging"></a>Registro em log unificado

Registro em log unificado fornece o aplicativo com uma única API de mensagens eficiente entre todos os níveis do sistema. Com o log unificado, o aplicativo tem um controle refinado sobre vários níveis de log que incluem controles de privacidade e a atividade de rastreamento para depuração mais fácil. 

Registro em log fornece correlação automática de mensagens quando a atividade de rastreamento e registro em log é usada juntos.

macOS Sierra inclui um novo aplicativo de Console (em aplicativos/utilitários) que é capaz de exibir dados de log de várias fontes, incluindo dispositivos conectados. Ele também dá suporte a pesquisas salvas e indexadas e exibe as conexões entre as mensagens relacionadas entre vários processos.

Além disso, as mensagens de log podem ser exibidas e mantidas por meio de ferramentas de linha de comando.

Para obter mais informações, consulte da Apple [referência de log](https://developer.apple.com/reference/os/1891852-logging).

<a name="Wide-Color" />

### <a name="wide-color"></a>Cores amplas

macOS Sierra estende o suporte para formatos de pixel de intervalo estendido e espaços de cores de toda a gama de cores em todo o sistema, incluindo estruturas como elementos gráficos principais, imagem principal, sistema operacional e AVFoundation. Suporte para dispositivos com telas de cores adicional é facilitado por fornecendo esse comportamento em toda a pilha inteira de gráficos.

Além disso, `AppKit` foi modificado funcionar no novo estendido **sRGB** espaço de cor, que facilita a combinação de cores em gamas de cores amplas sem perda de desempenho significativa.

A Apple oferece as seguintes práticas recomendadas ao trabalhar com cores amplas:

- `NSColor` agora usa o sRGB espaço de cores e não poderá mais clamp valores para o `0.0` para `1.0` intervalo. Se o aplicativo utiliza o comportamento de clamp anterior, ele precisará ser modificada para macOS Sierra.
- Ao usar uma API de nível inferior, como elementos gráficos principais ou Bare Metal para fornecer processamento de imagem, o aplicativo deve usar um intervalo estendido espaço e pixel formato de cor que dá suporte a valores de ponto flutuante de 16 bits. Quando for necessário, o aplicativo terá serem fixados manualmente os valores de componente de cor.
- Elementos gráficos principais, imagem principal e sombreadores de desempenho do sistema operacional fornecem novos métodos para converter entre os espaços de duas cores.

Para obter mais informações, consulte nosso [Introdução a cores amplas](~/ios/platform/wide-color.md) guia.

<a name="Additional-Framework-Changes" />

## <a name="additional-framework-changes"></a>Alterações de estrutura adicionais

Além das alterações de estrutura principais e adições listadas acima, Apple fez muitas alterações de estrutura secundárias adicionais no macOS Sierra.

Para obter mais informações, consulte nosso [alterações adicionais do Framework](~/mac/platform/introduction-to-macos-sierra/additional-framework-changes.md) guia.

<a name="Deprecated-APIs" />

## <a name="deprecated-apis"></a>APIs obsoletas

As seguintes APIs foram preteridas no macOS Sierra:

- Não há suporte para o sistema de arquivos padrão em HFS.

Consulte da Apple [OS X v10.12 diferenças de API](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/APIDiffsMacOS10_12/index.html) documentação para obter uma lista completa de alterações e reprovações.

## <a name="related-links"></a>Links relacionados

- [Amostras de Mac](https://developer.xamarin.com/samples/mac/)
- [O que há de novo nos X 10.12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
