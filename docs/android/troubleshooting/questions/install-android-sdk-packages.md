---
title: Quais pacotes de SDK do Android devo instalar?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F136AAE0-C6D2-4B0F-8F8C-7A6A94877266
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/30/2018
ms.openlocfilehash: 24c70c2e869f59091a1519af6d1165dbea9cc467
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292457"
---
# <a name="which-android-sdk-packages-should-i-install"></a>Quais pacotes de SDK do Android devo instalar?

A instalação do SDK do Android não inclui automaticamente todos os pacotes mínimos necessários para o desenvolvimento. Embora as necessidades individuais do desenvolvedor variem, os seguintes pacotes geralmente serão necessários para o desenvolvimento com o Xamarin. Android:

## <a name="tools"></a>Ferramentas

Instale as ferramentas mais recentes da pasta ferramentas no Gerenciador do SDK:

- Ferramentas do SDK do Android
- Plataforma SDK do Android-ferramentas
- Build de SDK do Android-ferramentas

## <a name="android-platforms"></a>Plataforma (s) Android

Instale a "plataforma SDK" para as versões do Android que você definiu como destino de & mínimo.

Exemplos:

- API de destino 23
- API mínima 23

Só é necessário instalar a plataforma SDK para a API 23

- API de destino 23
- API mínima 15

É necessário instalar plataformas SDK para a API 15 e 23. Observe que você não precisa instalar os níveis de API entre o mínimo e o destino (mesmo se você estiver realizando a porta de volta para esses níveis de API).

## <a name="system-images"></a>Imagens do sistema

Eles só serão necessários se você quiser usar os emuladores Android prontos para uso do Google. Para obter mais informações, consulte [Android Emulator configuração](~/android/get-started/installation/android-emulator/index.md)

## <a name="extras"></a>Extras
Os SDK do Android extras geralmente não são necessários; Mas é útil estar atento a eles, pois eles podem ser necessários, dependendo do seu caso de uso.
