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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292457"
---
# <a name="which-android-sdk-packages-should-i-install"></a>Quais pacotes de SDK do Android devo instalar?

A instalação do Android SDK não inclui automaticamente todos os pacotes mínimos necessários para o desenvolvimento. Embora as necessidades individuais do desenvolvedor variem, os seguintes pacotes geralmente serão necessários para desenvolver com xamarin.Android:

## <a name="tools"></a>Ferramentas

Instale as ferramentas mais recentes da pasta Ferramentas no gerenciador de SDK:

- Ferramentas do SDK do Android
- Ferramentas de plataforma Android SDK
- Ferramentas de compilação do Android SDK

## <a name="android-platforms"></a>Plataforma Android(s)

Instale a "Plataforma SDK" para as versões android que você definiu como meta mínima &.

Exemplos:

- API alvo 23
- API mínima 23

Só precisa instalar a plataforma SDK para a API 23

- API alvo 23
- API mínima 15

Necessário instalar plataformas SDK para API 15 e 23. Observe que você não precisa instalar os níveis de API entre o mínimo e o alvo (mesmo se você estiver se voltando para esses níveis de API).

## <a name="system-images"></a>Imagens do sistema

Estes só são necessários se você quiser usar os emuladores android fora da caixa do Google. Para obter mais informações, consulte [configuração do emulador do Android](~/android/get-started/installation/android-emulator/index.md)

## <a name="extras"></a>Extras
Os Extras do Android SDK geralmente não são necessários; mas é útil estar ciente deles, pois eles podem ser necessários dependendo do seu caso de uso.
