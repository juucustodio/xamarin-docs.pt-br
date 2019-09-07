---
title: Active Directory do Azure
description: Este documento descreve as etapas que devem ser seguidas para permitir que um aplicativo móvel se autentique com o Azure Active Directory.
ms.prod: xamarin
ms.assetid: 70B3C2AB-CB4D-420C-9CFA-20CCFA0E3C78
author: conceptdev
ms.author: crdun
ms.date: 03/23/2017
ms.openlocfilehash: b28fcea37d991879df0231609d09eeb2eca49505
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70766345"
---
# <a name="azure-active-directory"></a>Active Directory do Azure

_Registrar um aplicativo para usar Azure Active Directory_

Azure Active Directory permite que os desenvolvedores protejam recursos como arquivos, links e APIs Web usando a mesma conta institucional que os funcionários usam para entrar em seus sistemas ou verificar seus emails.

O desenvolvimento de aplicativos móveis que podem se autenticar com o Azure Active Directory envolve três etapas.
As duas primeiras etapas geralmente são as mesmas, independentemente dos serviços que você planeja usar. A terceira etapa é diferente para cada tipo de serviço:

  1. [Registrando com Azure Active Directory](~/cross-platform/data-cloud/active-directory/get-started/register.md) no portal do *WindowsAzure.com* , em seguida,
  2. [Configurar serviços](~/cross-platform/data-cloud/active-directory/get-started/configure.md).
  3. Desenvolva aplicativos móveis usando os serviços.

Entre os exemplos de serviços diferentes que você pode acessar estão:

- [API do Graph](~/cross-platform/data-cloud/active-directory/graph.md)
- API Web
- Office365

## <a name="conclusion"></a>Conclusão

Usando as etapas acima, você pode autenticar seus aplicativos móveis em Azure Active Directory. O Biblioteca de Autenticação do Active Directory (ADAL) torna muito mais fácil com menos linhas de código, ao mesmo tempo em que mantém a maioria do código o mesmo e, portanto, torna-o compartilhável entre plataformas.

## <a name="related-links"></a>Links relacionados

- [Exemplo do Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
