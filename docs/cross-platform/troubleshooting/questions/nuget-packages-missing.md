---
title: Erro de pacotes ausentes após atualizar os pacotes do Nuget
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: D61CC966-1D4A-49A5-8A6F-41572E28329B
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 7cb802dd60d4e4879a260ff56d4f94ea5acb2965
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674841"
---
# <a name="missing-packages-error-after-updating-nuget-packages"></a>Erro de pacotes ausentes após atualizar os pacotes do Nuget

Esse problema foi relatado principalmente em soluções de aplicativo de exemplo do xamarin. Forms, mas o potencial para esse problema pode ocorrer em qualquer projeto que usa pacotes do NuGet. 

Se depois de atualizar pacotes do Nuget em seu projeto ou solução, você verá um erro que referencia os números de versão do pacote antigo, tais como:

```csharp
Error: This project references NuGet package(s) that are missing on this computer.
Enable NuGet Package Restore to download them.  
For more information, see http://go.microsoft.com/fwlink/?LinkID=322105

The missing file is ../../packages/Xamarin.Forms.1.3.1.6296/build/portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10/Xamarin.Forms.targets. (FormsGallery)
```

Neste exemplo *Xamarin.Forms.1.3.1.6296* é o número de versão antiga que foi removido com a atualização de pacote do Nuget.

Isso pode acontecer se os elementos XML no arquivo. csproj que referenciam o número de versão do pacote antigo tinham adicionados manualmente ou editados, o Nuget não remover ou atualizá-los se eles tivessem sido adicionadas/editado manualmente, para que o projeto agora está procurando por pacotes que foram excluído. 

Para corrigir esse problema, edite os arquivos. csproj manualmente e excluir todos os elementos que fazem referência o número da versão antiga. 

Elementos de exemplo para remover (se eles têm o número de versão do pacote antigo):

```xml
<Reference Include="Xamarin.Forms.Maps">
    <HintPath>..\..\packages\Xamarin.Forms.Maps.1.3.1.6296\lib\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.Maps.dll</HintPath>
</Reference>

<Import Project="..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets" Condition="Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" />
<Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets'))" />
```