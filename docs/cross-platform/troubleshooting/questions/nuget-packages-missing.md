---
title: Erro de pacotes ausentes após atualizar os pacotes do Nuget
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: D61CC966-1D4A-49A5-8A6F-41572E28329B
author: conceptdev
ms.author: crdun
ms.date: 05/08/2018
ms.openlocfilehash: 6ea175859055420780463619d0ae7fe9ec85c857
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70288210"
---
# <a name="missing-packages-error-after-updating-nuget-packages"></a>Erro de pacotes ausentes após atualizar os pacotes do Nuget

Esse problema foi relatado principalmente em soluções de aplicativo de exemplo do Xamarin. Forms, mas o potencial para esse problema pode ocorrer em qualquer projeto que usa pacotes NuGet.

Se depois de atualizar os pacotes do NuGet em seu projeto ou solução, você verá um erro que faz referência aos números de versão do pacote antigo, como:

```csharp
Error: This project references NuGet package(s) that are missing on this computer.
Enable NuGet Package Restore to download them.
For more information, see http://go.microsoft.com/fwlink/?LinkID=322105

The missing file is ../../packages/Xamarin.Forms.1.3.1.6296/build/portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10/Xamarin.Forms.targets. (FormsGallery)
```

Neste exemplo, *Xamarin. Forms. 1.3.1.6296* é o número de versão antigo que foi removido com a atualização do pacote NuGet.

Isso pode acontecer se os elementos XML no arquivo. csproj que fazem referência ao número de versão do pacote antigo tiverem sido adicionados ou editados manualmente, o NuGet não os removerá ou atualizará se tiverem sido adicionados/editados manualmente, portanto, o projeto está procurando pacotes que foram excluí.

Para corrigir esse problema, edite manualmente os arquivos. csproj e exclua todos os elementos que fazem referência ao número de versão antigo.

Elementos de exemplo a serem removidos (se tiverem o número de versão do pacote antigo):

```xml
<Reference Include="Xamarin.Forms.Maps">
    <HintPath>..\..\packages\Xamarin.Forms.Maps.1.3.1.6296\lib\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.Maps.dll</HintPath>
</Reference>

<Import Project="..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets" Condition="Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" />
<Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets'))" />
```
