---
title: Criação manual de pacotes do NuGet para Xamarin
description: Este documento contém dicas para ajudar a criar pacotes do NuGet que se destinam a plataforma Xamarin. Ele descreve os perfis de Xamarin de pacote do NuGet, PCL NuGets com dependências de plataforma e links para vários exemplos de código-fonte aberto.
ms.prod: xamarin
ms.assetid: a5964686-5fc6-4280-b087-7ba27cc1c8bf
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 4f4ca327479a7f4eb4a7dc7feafdd71291c1b7fe
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61186133"
---
# <a name="manually-creating-nuget-packages-for-xamarin"></a>Criação manual de pacotes do NuGet para Xamarin

_Esta página contém algumas dicas para ajudar a criar pacotes do NuGet que se destinam a plataforma Xamarin._

> [!NOTE]
> Xamarin Studio 6.2 (e o Visual Studio para Mac) incluem a capacidade _automaticamente_ gerar pacotes do NuGet de PCL, .NET Standard ou projetos compartilhados. Consulte a [bibliotecas de multiplataforma para o compartilhamento de código](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md) guia para obter mais detalhes.

## <a name="nuget-package-xamarin-profiles"></a>Perfis de Xamarin de pacote do NuGet

O site do NuGet [que dão suporte a várias versões do .NET Framework e perfis](https://docs.nuget.org/create/enforced-package-conventions) discute como dar suporte a diferentes estruturas da Microsoft e perfis, mas não inclui os nomes de estrutura de destino usados pelo Xamarin.

As estruturas de destino principais do Xamarin em uso atualmente são:

* **MonoAndroid** -xamarin. Android
* **Xamarin. IOS** -xamarin. IOS [API unificada](~/cross-platform/macios/unified/index.md) (dá suporte a 64 bits)
* **Xamarin. Mac** -perfil móvel do xamarin. Mac, que é equivalente à superfície de API de xamarin. Android e xamarin. IOS.

Também é um destino para o iOS mais antigas [API clássica](~/cross-platform/macios/unified/index.md):

* **MonoTouch** -iOS API clássica

Um **. NuSpec** arquivo que o destino todos esses seria algo parecido com:

```xml
<files>
    <file src="Mac\bin\Release\*.dll" target="lib\Xamarin.Mac20" />
    <file src="iOS\bin\Release\*.dll" target="lib\Xamarin.iOS10" />
    <file src="Android\bin\Release\*.dll" target="lib\MonoAndroid10" />
    <file src="iOSClassic\bin\Release\*.dll" target="lib\MonoTouch10" />
</files>
```

As opções acima ignora todas as bibliotecas de classes portátil.

A maioria dos **. NuSpec** arquivos especificam o número de versão do framework de destino, mas é opcional se o seu assembly funciona com todas as versões dessa estrutura de destino. Portanto, se o destino foi **lib\MonoAndroid** isso significaria que ele funciona com qualquer versão do xamarin. Android.

Você pode especificar a versão com um conjunto de números sem um ponto decimal, ou você pode especificá-lo usando pontos decimais. Sem o ponto decimal NuGet apenas será levar cada número e transformá-lo em uma versão com a inserção de um '.' entre cada dígito.

Na acima "MonoAndroid10" significa "Android 1.0". Isso apenas significa que o projeto [estrutura de destino](~/android/app-fundamentals/android-api-levels.md) precisa ser MonoAndroid versão 1.0 ou superior. A versão é especificada no `<TargetFrameworkVersion>` elemento no arquivo de projeto.

Para esclarecer:

- **MonoAndroid403** corresponde ao Android 4.0.3 e mais recente (ie API nível 15)
- **Xamarin.iOS10** corresponde ao xamarin. IOS 1.0 e mais recentes
- **Xamarin.iOS1.0** também corresponde ao xamarin. IOS 1.0 e mais recentes

## <a name="pcl-nugets-with-platform-dependencies"></a>NuGets PCL com dependências de plataforma

Os perfis de PCL estão limitados nas quais APIs eles podem acessar do .NET framework, e certamente não poderão acessar o código específico da plataforma. Esses links 3ª parte discutem diferentes abordagens para a criação de pacotes do NuGet que usam o PCL e APIs nativas para fornecer compatibilidade para Xamarin e outras plataformas:

- [Como fazer as bibliotecas de classes portáteis trabalharem para você](http://blogs.msdn.com/b/dsplaisted/archive/2012/08/27/how-to-make-portable-class-libraries-work-for-you.aspx)
- [O truque PCL de isca](http://log.paulbetts.org/the-bait-and-switch-pcl-trick/)
- [Criando uma PCL do NuGet que funciona com xamarin. IOS](http://www.jimbobbennett.io/creating-a-nuget-pcl-that-works-with-xamarin-ios/)

Este externo [lista de perfis de PCL com seu nome de destino do NuGet](http://embed.plnkr.co/03ck2dCtnJogBKHJ9EjY) também é uma referência útil.

## <a name="examples"></a>Exemplos

Alguns exemplos de código-fonte aberto que você pode fazer referência a:

- [**ModernHttpClient** ](https://www.nuget.org/packages/modernhttpclient/) – gravar seu aplicativo usando HTTP, mas remova essa biblioteca e ele irá drasticamente mais rápido (modo de exibição [origem](https://github.com/paulcbetts/ModernHttpClient)).
- [**Splat** ](https://www.nuget.org/packages/Splat/) – uma biblioteca para tornar as coisas de plataforma cruzada que deve ser (modo de exibição [origem](https://github.com/paulcbetts/Splat)).
- [**NGraphics** ](https://www.nuget.org/packages/NGraphics/) -uma biblioteca de plataforma cruzada para a renderização de gráficos vetoriais no .NET (modo de exibição [origem](https://github.com/praeclarum/NGraphics/blob/master/NGraphics.nuspec)).

## <a name="related-links"></a>Links relacionados

- [Nugetizer 3000 automatizada de criação do Nuget](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)
- [Atualizando NuGets para iOS 64 bits](https://blog.xamarin.com/how-to-update-nuget-packages-for-64-bit/)
- [Incluindo um NuGet em seu projeto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
