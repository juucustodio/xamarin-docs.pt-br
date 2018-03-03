---
title: "watchOS 3 solução de problemas"
description: "Este artigo fornece várias dicas de solução de problemas para trabalhar com watchOS 3 em aplicativos Xamarin Apple Watch."
ms.topic: article
ms.prod: xamarin
ms.assetid: 5911D898-0E23-40CC-9F3C-5F61B4D50ADC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 02ec4c3c9827f7cfac48184eb12491faaa0c92c8
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="watchos-3-troubleshooting"></a>watchOS 3 solução de problemas

_Este artigo fornece várias dicas de solução de problemas para trabalhar com watchOS 3 em aplicativos Xamarin Apple Watch._

Esta página lista alguns problemas conhecidos que podem ocorrer ao usar watchOS 3 com o Xamarin e a solução para esses problemas.

## <a name="activities"></a>Atividades

Para a atividade de compartilhamento para funcionar corretamente, todas as inspeções Apple emparelhados deve estar executando o watchOS 3.

Problemas Conhecidos:

- Responder às vezes, uma notificação de compartilhamento de atividade falha.
- Responder a uma notificação de compartilhamento de atividade com uma mensagem pode falhar.
- Texto contextual acima de uma mensagem de notificação de compartilhamento de atividade estará incorreto.


## <a name="apple-pay"></a>Apple Pay

Problemas Conhecidos:

- Se uma data de validade incorreto ou o código CW for inserido para um novo cuidado de pagamento ao pagamento da Apple, quando atingir **próximo** o processo em execução falhará.
- Compras no aplicativo pagamento da Apple, exigindo um número PIN podem falhar.



## <a name="auto-mac-unlock"></a>Desbloqueio automático Mac

Usando watchOS 3 beta 2 (ou posterior) e macOS beta de serra 2 (ou superior), se a autenticação de dois fatores é ativada na conta do iCloud do usuário, ele podem usar seu Apple Watch automaticamente desbloquear seu Mac.



## <a name="background-refresh"></a>Atualização em segundo plano

Violação de recursos do sistema resultará em uma falha de aplicativo watchOS 3 com os seguintes códigos de exceção:

- **0xc51bad01** -o aplicativo consumido muito tempo de CPU.
- **0xc51bad02** -o aplicativo consumido muito tempo na parede.
- **0xc51bad03** -o aplicativo não teve tempo de execução suficientes para concluir a tarefa atual.



## <a name="clock"></a>Relógio

Complicações de aplicativos do Apple Watch recém-instalado podem aparecer como espaço em branco. Reinicialize o Apple Watch para corrigir esse problema.


## <a name="connectivity"></a>Conectividade

Problemas Conhecidos:

- watchOS não solicitará ao usuário permissão de acesso protegido para dados de usuário sobre o Apple Watch. Conceda o acesso no aplicativo do iPhone antes de usar dados no aplicativo de inspeção.
- O Apple Watch pode entrar em um estado em que todas as transmissões de WatchConnectivity falharem, reinicialize o Apple Watch para corrigir.


## <a name="notifications"></a>Notificações

Se um anexo de mídia for muito grande, ele será apresentado no iPhone do usuário, mas não seu Apple Watch.


## <a name="nsurlconnection"></a>NSURLConnection

Qualquer `NSURLConnection` conexões usando os protocolos TLS mais antigos irá falhar. Para todas as conexões SSL/TLS, a criptografia simétrica RC4 agora é desabilitada por padrão. Além disso, a API de transporte seguro não oferece mais suporte SSLv3 e é recomendável que o aplicativo parar de usar criptografia SHA-1 e 3DES assim que possível.

A partir de watchOS 3, segurança de conexões SSL/TLS está sendo imposta estritamente pela Apple. Aplicativos e serviços afetados deve atualizar servidores web para usar as versões mais recentes do protocolo TLS.


## <a name="nsurlsession"></a>NSURLSession

A partir de watchOS 3, o `HTTPBodyStream` propriedade o `NSMutableURLRequest` classe deve ser definida como um fluxo não aberto desde `NSURLConnection` e `NSURLSession` agora estritamente impõe esse requisito.


## <a name="privacy"></a>Privacidade

Problemas Conhecidos:

Ao trabalhar com `https://` ambas as URLs `NSURLSession` e `NSURLConnection` não suportar conjuntos de criptografia RC4 durante o handshake TLS. Um dos seguintes códigos de erro pode ser gerado:

- **-1200 ou-98** - para `NSURLErrorSecurityConnectionFailed` e SecureTransport erros.
- **-1200 [3:-9824]** -falhado de carregamento de http.
- **-1200**  -  `NSURLConnection` concluída com erro.

A partir de watchOS 3, segurança de conexões SSL/TLS está sendo imposta estritamente pela Apple. Aplicativos e serviços afetados deve atualizar servidores web para usar as versões mais recentes do protocolo TLS. Consulte [NSURLConnection](#NSURLConnection) acima para obter mais informações.


## <a name="snapshots"></a>Instantâneos

Aplicativos WatchKit que não adotaram o novo `HandelBackgroundTask` API não receberá mais atualizações periódicas no watchOS 3. 


## <a name="watchkit"></a>WatchKit

Nos bastidores SpriteKit e SceneKit serão pausados quando um aplicativo insere o plano de fundo no watchOS encaixe.


## <a name="related-links"></a>Links relacionados

- [Exemplos de watchOS](https://developer.xamarin.com/samples/watchos/all/)
- [O que há de novo no watchOS 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
