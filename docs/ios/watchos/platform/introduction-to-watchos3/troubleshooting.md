---
title: watchOS 3 solução de problemas
description: Este documento fornece várias dicas de solução de problemas útil ao trabalhar com o watchOS 3 no Xamarin. Dicas estão relacionados às atividades, Apple Pay, atualização em segundo plano, NSURLConnection, privacidade e muito mais.
ms.prod: xamarin
ms.assetid: 5911D898-0E23-40CC-9F3C-5F61B4D50ADC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 497ce4f0ecbd5e4d49d2ff88855ff68dd9a63e43
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123193"
---
# <a name="watchos-3-troubleshooting"></a>watchOS 3 solução de problemas

_Este artigo fornece várias dicas de solução de problemas para trabalhar com o watchOS 3 em aplicativos Xamarin Apple Watch._

Esta página lista alguns problemas conhecidos que podem ocorrer ao usar o watchOS 3 com o Xamarin e a solução para esses problemas.

## <a name="activities"></a>Atividades

Para a atividade de compartilhamento para funcionar corretamente, todas as Apple Watches emparelhados deve estar executando o watchOS 3.

Problemas Conhecidos:

- Respondendo a uma notificação de compartilhamento de atividade, às vezes, falhará.
- Respondendo a uma notificação de compartilhamento de atividade com uma mensagem pode falhar.
- Texto contextual acima de uma mensagem de notificação de compartilhamento de atividade estarão incorreto.

## <a name="apple-pay"></a>Apple Pay

Problemas Conhecidos:

- Se um código CW ou data de validade incorreto for inserido para um novo cuidado de pagamento no Apple Pay, ao atingir **próxima** o processo em execução falhará.
- Compras no aplicativo Apple Pay que exigem um número PIN podem falhar.

## <a name="auto-mac-unlock"></a>Desbloqueio automático Mac

Usando watchOS 3 beta 2 (ou posterior) e macOS beta de serra 2 (ou superior), se a autenticação de dois fatores é ativada na conta do iCloud do usuário, eles podem usar o seu Apple Watch automaticamente desbloquear seu Mac.

## <a name="background-refresh"></a>Atualização em segundo plano

Violação de recursos do sistema resultará em uma falha de aplicativo do watchOS 3 com os seguintes códigos de exceção:

- **0xc51bad01** -o aplicativo consumidos muito tempo de CPU.
- **0xc51bad02** -o aplicativo consumidos muito tempo de parede.
- **0xc51bad03** -o aplicativo não tem tempo de execução suficientes para concluir a tarefa atual.

## <a name="clock"></a>Relógio

Complicações de aplicativos do Apple Watch recém-instalado podem aparecer como espaço em branco. Reinicialize o Apple Watch para corrigir esse problema.

## <a name="connectivity"></a>conectividade

Problemas Conhecidos:

- watchOS não solicitará ao usuário permissão de acesso de dados de usuário protegido na Apple Watch. Conceder acesso no aplicativo do iPhone antes de usar dados no aplicativo de inspeção.
- O Apple Watch pode entrar em um estado em que todas as transmissões de WatchConnectivity falharem, reinicialize o Apple Watch para corrigir.

## <a name="notifications"></a>Notificações

Se um anexo de mídia for muito grande, ele será apresentado no iPhone do usuário, mas não seu Apple Watch.

## <a name="nsurlconnection"></a>NSURLConnection

Qualquer `NSURLConnection` conexões usando protocolos mais antigos do TLS falhará. Para todas as conexões SSL/TLS, a criptografia simétrica de RC4 agora é desabilitada por padrão. Além disso, a API de transporte seguro não oferece suporte a SSLv3 e é recomendável que o aplicativo parar de usar criptografia SHA-1 e 3DES assim que possível.

A partir do watchOS 3, segurança de conexões SSL/TLS está sendo imposta estritamente pela Apple. Aplicativos e serviços afetados deve atualizado os servidores web para usar as versões mais recentes do protocolo TLS.

## <a name="nsurlsession"></a>NSURLSession

A partir do watchOS 3, o `HTTPBodyStream` propriedade do `NSMutableURLRequest` classe deve ser definida como um fluxo não aberto desde `NSURLConnection` e `NSURLSession` agora estritamente impor esse requisito.

## <a name="privacy"></a>Privacidade

Problemas Conhecidos:

Ao trabalhar com `https://` ambas as URLs `NSURLSession` e `NSURLConnection` não há mais suporte a conjuntos de criptografia RC4 durante o handshake TLS. Um dos seguintes códigos de erro pode ser gerado:

- **-1200 ou-98** - para `NSURLErrorSecurityConnectionFailed` e SecureTransport erros.
- **-1200 [3:-9824]** -falha de carregamento de http.
- **-1200**  -  `NSURLConnection` concluída com erro.

A partir do watchOS 3, segurança de conexões SSL/TLS está sendo imposta estritamente pela Apple. Aplicativos e serviços afetados deve atualizado os servidores web para usar as versões mais recentes do protocolo TLS. Ver [NSURLConnection](#NSURLConnection) acima para obter mais informações.

## <a name="snapshots"></a>Instantâneos

Aplicativos do WatchKit que não adotaram o novo `HandelBackgroundTask` API não receberá mais atualizações periódicas no watchOS 3. 

## <a name="watchkit"></a>WatchKit

Cenas de SpriteKit e SceneKit ficará em pausa quando um aplicativo entra em segundo plano no watchOS Dock.

## <a name="related-links"></a>Links relacionados

- [Exemplos de watchOS](https://developer.xamarin.com/samples/watchos/all/)
- [O que há de novo no watchOS 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
