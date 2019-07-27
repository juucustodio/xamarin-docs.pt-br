---
ms.openlocfilehash: e383bbccd4e76be8a208f5680e5cf21e45a0dbc3
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511940"
---

A linha de comando a seguir para especificar uma compilação de versão da solução **SOLUTION_FILE. sln** para o iPhone. O local do IPA pode ser definido especificando a `IpaPackageDir` Propriedade na linha de comando:

- No Mac, usando **xbuild**:

        xbuild /p:Configuration="Release" \ 
           /p:Platform="iPhone" \ 
           /p:IpaPackageDir="$HOME/Builds" \
           /t:Build MyProject.sln

O comando **xbuild** normalmente é encontrado no diretório **/library/frameworks/mono.Framework/Commands**.

- No Windows, usando o **MSBuild**:

        msbuild /p:Configuration="Release" 
            /p:Platform="iPhone" 
            /p:IpaPackageDir="%USERPROFILE%\Builds" 
            /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser"  
            /t:Build MyProject.sln


o **MSBuild** não expandirá `$( )` automaticamente as expressões passadas pela linha de comando. Por esse motivo, é recomendável usar um caminho completo ao definir o `IpaPackageDir` na linha de comando.

Consulte as [notas de versão do IOS 9,8](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_9/xamarin.ios_9.8.md#new-msbuild-property-ipapackagedir-to-customize-ipa-output-location) para obter mais detalhes `IpaPackageDir` sobre a propriedade.
