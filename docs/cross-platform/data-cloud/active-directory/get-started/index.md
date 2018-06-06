---
title: Azure Active Directory
description: Este documento descreve as etapas que devem ser seguidas para permitir que um aplicativo móvel autenticar com o Active Directory do Azure.
ms.prod: xamarin
ms.assetid: 70B3C2AB-CB4D-420C-9CFA-20CCFA0E3C78
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: ca33422817f19dbb0a04e8870800d3f5efa8af2a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780055"
---
# <a name="azure-active-directory"></a>Azure Active Directory

_Registrar um aplicativo para usar o Active Directory do Azure_

Active Directory do Azure permite aos desenvolvedores recursos seguros, como arquivos, links e APIs da Web usando a mesma conta institucional que os funcionários usam para entrar em seus sistemas ou verificar seus emails.

Desenvolvimento de aplicativos móveis que podem se autenticar com o Azure Active Directory envolve três etapas.
As duas primeiras etapas geralmente são o mesmo, independentemente de quais serviços você planeja usar. A terceira etapa é diferente para cada tipo de serviço:

  1. [Registrar com o Active Directory do Azure](~/cross-platform/data-cloud/active-directory/get-started/register.md) no *windowsazure.com* portal, em seguida,
  2. [Configurar serviços](~/cross-platform/data-cloud/active-directory/get-started/configure.md).
  3. Desenvolva aplicativos móveis usando os serviços.

Exemplos de diferentes serviços, que você pode acessar:

- [API do Graph](~/cross-platform/data-cloud/active-directory/graph.md)
- API Web
- Office365


## <a name="conclusion"></a>Conclusão

Usando as etapas acima que você possa autenticar seus aplicativos móveis no Active Directory do Azure. A biblioteca de autenticação Active Directory (ADAL) torna muito mais fácil com menos linhas de código, mantendo a maioria do código do mesmo e tornando-o, portanto, podem ser compartilhadas entre plataformas.



## <a name="related-links"></a>Links relacionados

- [Exemplo de NativeClient Microsoft](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
