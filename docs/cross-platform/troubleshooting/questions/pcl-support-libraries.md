---
title: Como exibir as bibliotecas que têm suporte em um PCL?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 14FF03BD-AF41-4DB1-B307-2349C13DE7E4
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 0c3e727ea0bd72efb03ed21c1b42de1023e36f93
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="how-can-i-view-what-libraries-are-supported-in-a-pcl"></a>Como exibir as bibliotecas que têm suporte em um PCL?

- Você pode encontrar uma visão geral de vários recursos com suporte as várias plataformas de destino PCL sob o *recursos com suporte* parte desta página: [http://msdn.microsoft.com/en-us/library/gg597391.aspx](https://msdn.microsoft.com/en-us/library/gg597391.aspx)

- Outra opção é usar o [.NET portabilidade analisador](https://visualstudiogallery.msdn.microsoft.com/1177943e-cfb7-4822-a8a6-e56c7905292b) para avaliar se sua biblioteca existente pode ser convertida em um perfil PCL.

- Uma terceira possibilidade é procurar o conteúdo do perfil real que você pode usar. Usando o perfil 78 por exemplo, você pode ir aqui: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\Profile\Profile78\` e exibir todos os assemblies dentro dele.

Independentemente do método que você escolheu, por favor, observe que algumas funcionalidades tem a serem baixados por meio do NuGet e a biblioteca Microsoft BCL.
