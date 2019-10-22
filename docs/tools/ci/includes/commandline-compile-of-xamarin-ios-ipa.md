---
ms.openlocfilehash: 31c8d1b781648f2d9c69062c52e71478fc7a496b
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "69529129"
---

A linha de comando a seguir para especificar uma compilação de versão da solução **SOLUTION_FILE. sln** para o iPhone. O local do IPA pode ser definido especificando a propriedade `IpaPackageDir` na linha de comando:

- No Mac, usando **xbuild**:

  ```
  xbuild /p:Configuration="Release" \ 
         /p:Platform="iPhone" \ 
         /p:IpaPackageDir="$HOME/Builds" \
         /t:Build MyProject.sln
  ```

O comando **xbuild** normalmente é encontrado no diretório **/library/frameworks/mono.Framework/Commands**.

- No Windows, usando o **MSBuild**:

  ```
  msbuild /p:Configuration="Release" 
          /p:Platform="iPhone" 
          /p:IpaPackageDir="%USERPROFILE%\Builds" 
          /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser"  
          /t:Build MyProject.sln
  ```

o **MSBuild** não expandirá automaticamente `$( )` expressões passadas pela linha de comando. Por esse motivo, é recomendável usar um caminho completo ao definir o `IpaPackageDir` na linha de comando.

Consulte as [notas de versão do iOS 9,8](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_9/xamarin.ios_9.8.md#new-msbuild-property-ipapackagedir-to-customize-ipa-output-location) para obter mais detalhes sobre a propriedade `IpaPackageDir`.
