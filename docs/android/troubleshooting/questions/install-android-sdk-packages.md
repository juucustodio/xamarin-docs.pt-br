---
title: Quais pacotes de SDK do Android devo instalar?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F136AAE0-C6D2-4B0F-8F8C-7A6A94877266
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/30/2018
ms.openlocfilehash: 04a07d5b7f37222515136e5592f31a4583b02fe3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61157358"
---
# <a name="which-android-sdk-packages-should-i-install"></a>Quais pacotes de SDK do Android devo instalar?

Instalar o SDK do Android não inclui automaticamente todos os pacotes necessários mínimos para o desenvolvimento. Enquanto o desenvolvedor individual precisa variam, os seguintes pacotes geralmente serão necessários para desenvolver com o xamarin. Android:

## <a name="tools"></a>Ferramentas

Instale as ferramentas mais recentes na pasta Ferramentas no Gerenciador do SDK:

- Ferramentas do SDK do Android
- Ferramentas da plataforma SDK do Android
- Ferramentas de Build do SDK do Android

## <a name="android-platforms"></a>Plataformas Android

Instale a "plataforma do SDK" para as versões de Android que você definiu como o mínimo de & destino. 

Exemplos:

- API de 23 de destino
- 23 de API mínimo

Só precisa instalar o SDK de plataforma para a API 23

- API de 23 de destino
- API mínimo 15

Necessário instalar o SDK de plataformas para API 15 e 23. Observe que não é necessário instalar os níveis de API entre o mínimo e o destino (mesmo se você for o transporte para esses níveis de API).

## <a name="system-images"></a>Imagens do sistema

Só são necessários se você deseja usar os emuladores de Android da caixa do Google. Para obter mais informações, consulte [configuração do Android Emulator](~/android/get-started/installation/android-emulator/index.md)

## <a name="extras"></a>Extras
Os Extras de SDK do Android geralmente não são necessários; mas é útil estar ciente deles, pois eles podem ser necessários, dependendo do seu caso de uso.

## <a name="further-reading"></a>Leitura adicional
O guia a seguir aborda essas opções e apresenta mais detalhes sobre os diferentes pacotes SDK manager tem disponível: [Guia de instalação do Gerenciador de SDK do Android](http://www.themethodology.net/2015/02/android-sdk-manager-setup-for.html?m=1)

