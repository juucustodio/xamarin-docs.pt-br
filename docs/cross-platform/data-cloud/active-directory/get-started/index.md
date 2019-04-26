---
title: Azure Active Directory
description: Este documento descreve as etapas que devem ser seguidas para permitir que um aplicativo móvel autenticar com o Azure Active Directory.
ms.prod: xamarin
ms.assetid: 70B3C2AB-CB4D-420C-9CFA-20CCFA0E3C78
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: ca33422817f19dbb0a04e8870800d3f5efa8af2a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61318292"
---
# <a name="azure-active-directory"></a>Azure Active Directory

_Registrar um aplicativo para usar o Azure Active Directory_

O Azure Active Directory permite aos desenvolvedores recursos seguros, como arquivos, links e APIs Web usando a mesma conta institucional que os funcionários usam para entrar em seus sistemas ou verificar seus emails.

Desenvolvimento de aplicativos móveis, que podem autenticar com o Azure Active Directory envolve três etapas.
As duas primeiras etapas geralmente são os mesmos, independentemente de quais serviços você planeja usar. A terceira etapa é diferente para cada tipo de serviço:

  1. [Registrando com o Azure Active Directory](~/cross-platform/data-cloud/active-directory/get-started/register.md) sobre o *windowsazure.com* portal, em seguida,
  2. [Configurar serviços](~/cross-platform/data-cloud/active-directory/get-started/configure.md).
  3. Desenvolva aplicativos móveis usando os serviços.

Exemplos de diferentes serviços, que você pode acessar:

- [API do Graph](~/cross-platform/data-cloud/active-directory/graph.md)
- API Web
- Office365


## <a name="conclusion"></a>Conclusão

Usando as etapas acima, você pode autenticar seus aplicativos móveis no Azure Active Directory. O Active Directory Authentication Library (ADAL) torna muito mais fácil com menos linhas de código, enquanto mantém a maior parte do código no mesmo e tornando-o, portanto, podem ser compartilhadas entre plataformas.



## <a name="related-links"></a>Links relacionados

- [Exemplo de Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
