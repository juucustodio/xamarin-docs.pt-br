---
title: Quais pacotes de SDK do Android deve instalar?
ms.topic: article
ms.prod: xamarin
ms.assetid: F136AAE0-C6D2-4B0F-8F8C-7A6A94877266
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/02/2018
ms.openlocfilehash: e2b0736a9ccc4dde5c1dcdf2d99f527247040560
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="which-android-sdk-packages-should-i-install"></a>Quais pacotes de SDK do Android deve instalar?

Instalar o SDK do Android não inclui automaticamente todos os pacotes necessários mínimos para o desenvolvimento. Enquanto precisa variam de desenvolvedor individuais, os seguintes pacotes geralmente serão necessários para o desenvolvimento com xamarin:

## <a name="tools"></a>Ferramentas

Instale as ferramentas mais recentes na pasta Ferramentas no Gerenciador de SDK:

- Ferramentas do SDK do Android
- Ferramentas de plataforma do SDK do Android
- Ferramentas de compilação do SDK do Android

## <a name="android-platforms"></a>Plataformas Android

Instale o "Platform SDK" para as versões Android que você definiu como destino & mínima. 

Exemplos:

- API 23 de destino
- 23 de API mínima

Só precisa instalar o SDK de plataforma para API 23

- API 23 de destino
- 15 de API mínima

Necessário instalar o SDK plataformas para 15 da API e 23. Observe que você não precisa instalar os níveis de API entre o mínimo e o destino (mesmo se você estiver pacotes para os níveis de API).

## <a name="system-images"></a>Imagens do sistema
Eles apenas são necessários se você deseja usar os emuladores Android fora da caixa do Google. 

- [Como configurar o emulador padrão](~/android/get-started/installation/android-emulator/index.md)

- [Como acelerar o emulador padrão](~/android/get-started/installation/android-emulator/index.md)

## <a name="extras"></a>Extras
O Android Extras SDK geralmente não são necessários; mas é útil estar ciente delas, pois eles podem ser necessários dependendo de seu caso de uso.

## <a name="further-reading"></a>Leitura adicional
O guia a seguir aborda essas opções e apresenta mais detalhes sobre os diferentes pacotes SDK manager tem disponível: [guia de instalação do Gerenciador de SDK do Android](http://www.themethodology.net/2015/02/android-sdk-manager-setup-for.html?m=1)

