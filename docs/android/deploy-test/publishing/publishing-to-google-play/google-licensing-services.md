---
title: Licenciamento de Serviços do Google
ms.prod: xamarin
ms.assetid: E96BDCC3-454A-A797-5819-905E2BB1AC41
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 12/20/2017
ms.openlocfilehash: eedfcfe2ed274ddf541addec67e66250deab7899
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114620"
---
# <a name="google-licensing-services"></a>Licenciamento de Serviços do Google

Antes do Google Play, os aplicativos Android dependiam da Proteção Contra Cópia herdada, fornecida pelo Google Market para garantir que somente usuários autorizados poderiam executar aplicativos em seus dispositivos. As limitações do mecanismo de Proteção Contra Cópia o tornaram uma solução inferior ao ideal para a proteção do aplicativo.

O Licenciamento do Google é uma substituição desse mecanismo de Proteção Contra Cópia herdado.
O Licenciamento do Google é um serviço flexível, seguro e baseado em rede, que os aplicativos Android podem consultar para determinar se um aplicativo está licenciado para ser executado em um determinado dispositivo.

O Licenciamento do Google é flexível de forma que os aplicativos Android têm controle total sobre quando verificar a licença, com que frequência verificar a licença e como lidar com a resposta do servidor de licenças.

O Licenciamento do Google é seguro de maneira que cada resposta é assinada usando um par de chaves RSA compartilhadas exclusivamente entre o servidor do Google Play e o aplicativo. O Google Play fornece uma chave pública para os desenvolvedores que é incorporada no aplicativo Android e é usada para autenticar as respostas. O servidor do Google Play guarda a chave privada internamente.

Um aplicativo que implementou o Licenciamento do Google faz uma solicitação a um serviço hospedado pelo aplicativo Google Play no dispositivo. O Google Play, em seguida, envia essa solicitação ao servidor de Licenciamento do Google, que responde com o status da licença: 

[![Diagrama de fluxo de trabalho do servidor de licenciamento](google-licensing-services-images/gp-licensing-service-overview.png)](google-licensing-services-images/gp-licensing-service-overview.png#lightbox)

O diagrama acima ilustra esse fluxo de trabalho: 

-   O aplicativo fornece o nome do pacote, um *nonce* (um autenticador criptográfico) que é usado para validar a resposta do servidor e um retorno de chamada que pode manipular a resposta de forma assíncrona. 

-   O Google Play fornece informações como a conta do Google e o próprio dispositivo, como o número IMSI. 

O serviço de Licenciamento do Google também é um componente fundamental dos arquivos de expansão de APK (que serão discutidos mais adiante neste documento). Os arquivos de expansão de APK utilizam serviços de Licenciamento do Google para obter as URLs dos arquivos de expansão que serão baixados.


## <a name="requirements"></a>Requisitos

Aplicativos que não são adquiridos através do Google Play não receberão nenhum benefício dos serviços de Licenciamento do Google. Se o Google Play não estiver instalado em um dispositivo, os aplicativos que usam Serviços de Licenciamento continuarão funcionando normalmente nesse dispositivo.

O Google Play requer acesso à Internet para funcionar. Um aplicativo pode armazenar a licença em cache para acomodar cenários nos quais o dispositivo não tenha acesso aos servidores de Licenciamento do Google Play.

Aplicativos gratuitos exigem o Licenciamento do Google somente quando o aplicativo usa arquivos de expansão APK.
