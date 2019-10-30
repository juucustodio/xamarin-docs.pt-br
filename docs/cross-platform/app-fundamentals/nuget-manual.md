---
title: Criando manualmente pacotes NuGet para o Xamarin
description: Este documento contém dicas para ajudar a criar pacotes NuGet direcionados para a plataforma Xamarin. Ele descreve os perfis do Xamarin Package do NuGet, PCL NuGets com dependências de plataforma e links para vários exemplos de código-fonte aberto.
ms.prod: xamarin
ms.assetid: a5964686-5fc6-4280-b087-7ba27cc1c8bf
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 583b1cb9e53ef0fb1002bc73ba53d063f99eff7c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016773"
---
# <a name="manually-creating-nuget-packages-for-xamarin"></a>Criando manualmente pacotes NuGet para o Xamarin

_Esta página contém algumas dicas para ajudar a criar pacotes NuGet direcionados para a plataforma Xamarin._

> [!NOTE]
> Xamarin Studio 6,2 (e Visual Studio para Mac) inclui a capacidade de gerar _automaticamente_ pacotes do NuGet por meio de projetos PCL, .net Standard ou compartilhados. Consulte o guia de [compartilhamento de bibliotecas multiplataforma para o código](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md) para obter mais detalhes.

## <a name="nuget-package-xamarin-profiles"></a>Perfis do Xamarin Package do NuGet

O suporte do site do NuGet para [várias versões e perfis de .NET Framework](https://docs.nuget.org/create/enforced-package-conventions) discute como dar suporte a diferentes estruturas e perfis da Microsoft, mas não inclui os nomes de estrutura de destino usados pelo Xamarin.

As principais estruturas de destino do Xamarin em uso hoje são:

- **Monoandroid** -Xamarin. Android
- **Xamarin. Ios** -Xamarin. Ios [API unificada](~/cross-platform/macios/unified/index.md) (dá suporte a 64 bits)
- **Xamarin. Mac** – o perfil móvel do Xamarin. Mac, que é equivalente à superfície da API do Xamarin. Ios e Xamarin. Android.

Também há um destino para o [API clássica](~/cross-platform/macios/unified/index.md)do Ios mais antigo:

- API Clássica do **MonoTouch** – Ios

Um arquivo **. nuspec** direcionado a todos eles teria uma aparência semelhante a:

```xml
<files>
    <file src="Mac\bin\Release\*.dll" target="lib\Xamarin.Mac20" />
    <file src="iOS\bin\Release\*.dll" target="lib\Xamarin.iOS10" />
    <file src="Android\bin\Release\*.dll" target="lib\MonoAndroid10" />
    <file src="iOSClassic\bin\Release\*.dll" target="lib\MonoTouch10" />
</files>
```

O acima ignora todas as bibliotecas de classes portáteis.

Os arquivos mais **. nuspec** especificam o número de versão da estrutura de destino, mas é opcional se o assembly funcionar com todas as versões da estrutura de destino. Portanto, se o seu destino foi **lib\MonoAndroid** isso significa que ele funciona com qualquer versão do Xamarin. Android.

Você pode especificar a versão com um conjunto de números sem um ponto decimal ou pode especificá-la usando pontos decimais. Sem o ponto decimal, o NuGet simplesmente pegará cada número e o transformará em uma versão inserindo um '. ' entre cada dígito.

Na acima, "MonoAndroid10" significa "Android 1,0". Isso apenas significa que a [estrutura de destino](~/android/app-fundamentals/android-api-levels.md) do projeto precisa ser o monoandroid versão 1,0 ou superior. A versão é especificada no elemento `<TargetFrameworkVersion>` no arquivo de projeto.

Para esclarecer:

- **MonoAndroid403** corresponde ao Android 4.0.3 e mais recente (API do IE nível 15)
- O **xamarin. iOS10** corresponde ao Xamarin. Ios 1,0 e mais recente
- O **xamarin. Ios 1.0** também corresponde ao Xamarin. Ios 1,0 e mais recente

## <a name="pcl-nugets-with-platform-dependencies"></a>NuGets PCL com dependências de plataforma

Os perfis PCL são limitados em quais APIs do .NET Framework eles podem acessar e certamente não podem acessar o código específico da plataforma. Esses links de terceiros abordam abordagens diferentes para a criação de pacotes NuGet que usam o PCL e APIs nativas para fornecer compatibilidade com o Xamarin e outras plataformas:

- [Como tornar as bibliotecas de classes portáteis funcionam para você](https://blogs.msdn.com/b/dsplaisted/archive/2012/08/27/how-to-make-portable-class-libraries-work-for-you.aspx)
- [O truque Bait e switch PCL](https://log.paulbetts.org/the-bait-and-switch-pcl-trick/)
- [Criando uma PCL do NuGet que funciona com o Xamarin. iOS](https://www.jimbobbennett.io/creating-a-nuget-pcl-that-works-with-xamarin-ios/)

Essa [lista externa de perfis PCL com o nome de destino do NuGet](https://portablelibraryprofiles.stephencleary.com) também é uma referência útil.

## <a name="examples"></a>Exemplos

Alguns exemplos de código-fonte aberto para os quais você pode se referir:

- [**ModernHttpClient**](https://www.nuget.org/packages/modernhttpclient/) – escreva seu aplicativo usando System .net. http, mas remova essa biblioteca no e ela será drasticamente mais rápida (Exibir [fonte](https://github.com/paulcbetts/ModernHttpClient)).
- [**Fragmentação**](https://www.nuget.org/packages/Splat/) – uma biblioteca para tornar as coisas entre plataformas que devem ser (Exibir [fonte](https://github.com/paulcbetts/Splat)).
- [**NGraphics**](https://www.nuget.org/packages/NGraphics/) -uma biblioteca de plataforma cruzada para renderizar gráficos vetoriais no .net (Exibir [fonte](https://github.com/praeclarum/NGraphics/blob/master/NGraphics.nuspec)).

## <a name="related-links"></a>Links relacionados

- [Criação automatizada de NuGet Nugetizer-3000](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)       
- [Incluindo um NuGet em seu projeto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
