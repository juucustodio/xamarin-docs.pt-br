---
title: Como fazer downgrade de um pacote NuGet?
ms.topic: article
ms.prod: xamarin
ms.assetid: 2375F833-A630-471E-B8E9-5AD2CB81F264
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 98d12bd93c50690909ac902a6f2498bcdb94960f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="how-do-i-downgrade-a-nuget-package"></a>Como fazer downgrade de um pacote NuGet?

O Visual Studio para Mac e o Visual Studio têm recursos para selecionar as versões mais antigas de pacotes e instalá-los automaticamente. semelhante a como atualizar pacotes funciona. Essas etapas são descritas abaixo.

## <a name="visual-studio"></a>Visual Studio
1. Vá para **Ferramentas > Gerenciador de pacotes do NuGet > Package Manager Console**
2. Defina o projeto em **projeto padrão**
3. Use a seguinte sintaxe:

    > Pacote de instalação [PackageName]-versão [tab para abrir o menu versão]

Você pode também copiar/colar o comando exato da página do NuGet do pacote. Exemplo de xamarin. Forms: [https://www.nuget.org/packages/Xamarin.Forms/](https://www.nuget.org/packages/Xamarin.Forms/)

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac
1. No seu projeto, clique na pasta de pacotes e selecione **adicionar pacotes**
2. Em searchbar, você pode usar a sintaxe a seguir para procurar os pacotes necessários:

    `[PackageName] version:*`

### <a name="examples"></a>Exemplos 
- Lista todos os pacotes de xamarin. Forms: 

    `Xamarin.Forms version:`
- Lista todos os pacotes do xamarin. Forms 1.4: 


    `Xamarin.Forms version:1.4`

*Observação: Se você adicionar um espaço entre `version:` & o número de versão, a pesquisa será se comportam como se nenhuma versão foi especificada.*

