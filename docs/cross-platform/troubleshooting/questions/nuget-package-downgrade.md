---
title: Como fazer downgrade de um pacote do NuGet?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2375F833-A630-471E-B8E9-5AD2CB81F264
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 72fdf7246b148fa95ea312284957072ecda47121
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351210"
---
# <a name="how-do-i-downgrade-a-nuget-package"></a>Como fazer downgrade de um pacote do NuGet?

O Visual Studio para Mac e o Visual Studio têm recursos para selecionar as versões mais antigas de pacotes e instalá-los automaticamente. semelhante a como atualizar pacotes funciona. Essas etapas são descritas abaixo.

## <a name="visual-studio"></a>Visual Studio
1. Vá para **Ferramentas > Gerenciador de pacotes NuGet > Console do Gerenciador de pacotes**
2. Defina o projeto sob **projeto padrão**
3. Use a seguinte sintaxe:

    > Install-Package [PackageName]-versão [guia para o menu de versão]

Você pode também copiar/colar o comando exato da página do NuGet do pacote. Exemplo de xamarin. Forms: [https://www.nuget.org/packages/Xamarin.Forms/](https://www.nuget.org/packages/Xamarin.Forms/)

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac
1. Em seu projeto, clique com botão direito na pasta de pacotes & seleção **adicionar pacotes**
2. No searchbar, você pode usar a sintaxe a seguir para procurar os pacotes necessários:

    `[PackageName] version:*`

### <a name="examples"></a>Exemplos 
- Lista todos os pacotes de xamarin. Forms: 

    `Xamarin.Forms version:`
- Lista todos os pacotes de 1.4 do xamarin. Forms: 

    `Xamarin.Forms version:1.4`

*Observação: Se você adicionar um espaço entre `version:` & o número de versão, a pesquisa irá se comportar como se nenhuma versão foi especificada.*

