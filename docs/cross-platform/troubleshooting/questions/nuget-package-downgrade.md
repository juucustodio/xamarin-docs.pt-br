---
title: Como fazer downgrade de um pacote NuGet?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2375F833-A630-471E-B8E9-5AD2CB81F264
author: davidortinau
ms.author: daortin
ms.date: 05/08/2018
ms.openlocfilehash: 0c70859845915a821bb83b0f9d29528634b1a5de
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73013617"
---
# <a name="how-do-i-downgrade-a-nuget-package"></a>Como fazer downgrade de um pacote NuGet?

Visual Studio para Mac & o Visual Studio tem recursos para selecionar versões mais antigas de pacotes e instalá-los automaticamente; semelhante a como a atualização de pacotes funciona. Essas etapas são descritas abaixo.

## <a name="visual-studio"></a>Visual Studio

1. Vá para **ferramentas > Gerenciador de pacotes NuGet > console do Gerenciador de pacotes**
2. Definir o projeto em **projeto padrão**
3. Use esta sintaxe:

    > Install-Package [Pacotename]-versão [guia para o menu versão]

Você também pode copiar/colar o comando exato da página do NuGet do pacote. Exemplo para Xamarin. Forms: [https://www.nuget.org/packages/Xamarin.Forms/](https://www.nuget.org/packages/Xamarin.Forms/)

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. Em seu projeto, clique com o botão direito do mouse na pasta pacotes & selecione **adicionar pacotes**
2. No Searchbar, você pode usar a seguinte sintaxe para pesquisar os pacotes necessários:

    `[PackageName] version:*`

### <a name="examples"></a>Exemplos 

- Lista todos os pacotes Xamarin. Forms: 

    `Xamarin.Forms version:`

- Lista todos os pacotes Xamarin. Forms 1.4. x: 

    `Xamarin.Forms version:1.4`

> [!NOTE]
> Se você adicionar um espaço entre `version:` & o número de versão, a pesquisa se comportará como embora nenhuma versão tenha sido especificada.
