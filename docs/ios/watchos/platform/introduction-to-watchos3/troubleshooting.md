---
title: Solução de problemas do watchOS 3
description: Este documento fornece várias dicas de solução de problemas úteis ao trabalhar com o watchOS 3 no Xamarin. Dicas relacionadas a atividades, Apple Pay, atualização em segundo plano, NSURLConnection, privacidade e muito mais.
ms.prod: xamarin
ms.assetid: 5911D898-0E23-40CC-9F3C-5F61B4D50ADC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 093ac4a3242866413042de0b650433d4369ad35f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028251"
---
# <a name="watchos-3-troubleshooting"></a>Solução de problemas do watchOS 3

_Este artigo fornece várias dicas de solução de problemas para trabalhar com watchOS 3 em aplicativos Xamarin Apple Watch._

Esta página lista alguns problemas conhecidos que podem ocorrer ao usar o watchOS 3 com o Xamarin e a solução para esses problemas.

## <a name="activities"></a>Atividades

Para que o compartilhamento de atividades funcione corretamente, todos os relógios da Apple emparelhados devem estar executando o watchOS 3.

Problemas Conhecidos:

- A resposta a uma notificação de compartilhamento de atividade às vezes falha.
- A resposta a uma notificação de compartilhamento de atividades com uma mensagem pode falhar.
- O texto contextual acima de uma mensagem de notificação de compartilhamento de atividade estará incorreto.

## <a name="apple-pay"></a>Apple Pay

Problemas Conhecidos:

- Se uma data de expiração incorreta ou um código de PV for inserido para um novo atendimento de pagamento em Apple Pay, ao atingir a **próxima** , o processo em execução falhará.
- As compras Apple Pay no aplicativo que exigem um número de PIN podem falhar.

## <a name="auto-mac-unlock"></a>Desbloqueio de Mac automático

Usando o watchOS 3 Beta 2 (ou superior) e o macOS Sierra beta 2 (ou superior), se a autenticação de dois fatores estiver habilitada na conta do iCloud do usuário, ela poderá usar seus Apple Watch para desbloquear automaticamente seu Mac.

## <a name="background-refresh"></a>Atualização em segundo plano

A violação dos recursos do sistema resultará em uma falha de aplicativo watchOS 3 com os seguintes códigos de exceção:

- **0xc51bad01** -o aplicativo consumiu muito tempo de CPU.
- **0xc51bad02** -o aplicativo consumiu muito tempo de parede.
- **0xc51bad03** -o aplicativo não tinha tempo de execução suficiente para concluir a tarefa atual.

## <a name="clock"></a>Relógio

Complicações de aplicativos Apple Watch recentemente instalados podem aparecer como em branco. Reinicialize Apple Watch para corrigir esse problema.

## <a name="connectivity"></a>Conectividade

Problemas Conhecidos:

- watchOS não solicitará ao usuário permissão de acesso para dados de usuário protegidos no Apple Watch. Conceda acesso no aplicativo do iPhone antes de usar dados no aplicativo Watch.
- O Apple Watch pode inserir um estado em que todas as transmissões WatchConnectivity falham, reinicializam o Apple Watch para corrigir.

## <a name="notifications"></a>Notificações

Se um anexo de mídia for grande demais, ele será apresentado no iPhone do usuário, mas não em seu Apple Watch.

## <a name="nsurlconnection"></a>NSURLConnection

Quaisquer conexões `NSURLConnection` usando protocolos TLS mais antigos falharão. Para todas as conexões SSL/TLS, a cifra simétrica RC4 agora está desabilitada por padrão. Além disso, a API de transporte seguro não dá mais suporte a SSLv3 e é recomendável que o aplicativo pare de usar a criptografia SHA-1 e 3DES assim que possível.

A partir de watchOS 3, a segurança de conexões SSL/TLS está sendo estritamente imposta pela Apple. Os serviços e aplicativos afetados devem atualizar os servidores Web para usar as versões mais recentes do protocolo TLS.

## <a name="nsurlsession"></a>NSURLSession

A partir de watchOS 3, a propriedade `HTTPBodyStream` da classe `NSMutableURLRequest` deve ser definida como um fluxo não aberto, já que `NSURLConnection` e `NSURLSession` agora impõem estritamente esse requisito.

## <a name="privacy"></a>Privacidade

Problemas Conhecidos:

Ao trabalhar com URLs de `https://`, `NSURLSession` e `NSURLConnection` não oferecem mais suporte a pacotes de codificação RC4 durante o handshake TLS. Um dos códigos de erro a seguir pode ser gerado:

- **-1200 ou-98** -para erros de `NSURLErrorSecurityConnectionFailed` e SecureTransport.
- **-1200 [3:-9824]** -falha no carregamento de http.
- **-1200** - `NSURLConnection` concluído com erro.

A partir de watchOS 3, a segurança de conexões SSL/TLS está sendo estritamente imposta pela Apple. Os serviços e aplicativos afetados devem atualizar os servidores Web para usar as versões mais recentes do protocolo TLS. Consulte [NSURLConnection](#nsurlconnection) acima para obter mais informações.

## <a name="snapshots"></a>instantâneos

Os aplicativos WatchKit que não adotaram a nova API de `HandelBackgroundTask` não receberão mais atualizações periódicas no watchOS 3. 

## <a name="watchkit"></a>WatchKit

Os bastidores SpriteKit e SceneKit serão pausados quando um aplicativo entrar no plano de fundo no encaixe watchOS.

## <a name="related-links"></a>Links relacionados

- [Exemplos de watchOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+watchOS)
- [O que há de novo no watchOS 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
