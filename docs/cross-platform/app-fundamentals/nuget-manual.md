---
title: Criar manualmente os pacotes do NuGet para Xamarin
description: Esta página contém algumas dicas para ajudar a criar pacotes do NuGet que destinar a plataforma do Xamarin.
ms.prod: xamarin
ms.assetid: a5964686-5fc6-4280-b087-7ba27cc1c8bf
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 510c27cb54b91c837ca6f6b7a93f944f13097f0c
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
---
# <a name="manually-creating-nuget-packages-for-xamarin"></a>Criar manualmente os pacotes do NuGet para Xamarin

_Esta página contém algumas dicas para ajudar a criar pacotes do NuGet que destinar a plataforma do Xamarin._

> [!NOTE]
> Xamarin Studio 6.2 (e o Visual Studio para Mac) incluem a capacidade de _automaticamente_ gerar pacotes NuGet do PCL, .NET padrão ou projetos compartilhados. Consulte o [Multiplatform bibliotecas para compartilhar código](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md) guia para obter mais detalhes.

## <a name="nuget-package-xamarin-profiles"></a>Perfis de Xamarin de pacote do NuGet

O site do NuGet [dando suporte a várias versões do .NET Framework e perfis](https://docs.nuget.org/create/enforced-package-conventions) discute como dar suporte a diferentes estruturas da Microsoft e perfis, mas não inclui os nomes do framework de destino usados pelo Xamarin.

A estrutura de destino principal Xamarin em uso hoje em dia é:

* **MonoAndroid** -xamarin
* **Xamarin** -xamarin [API unificada](~/cross-platform/macios/unified/index.md) (dá suporte a 64 bits)
* **Xamarin.Mac** -perfil móvel do Xamarin.Mac, que é equivalente à superfície de API de xamarin e xamarin.

Também é um destino para o iOS mais antigas [API clássica](~/cross-platform/macios/unified/index.md):

* **MonoTouch** -API clássica para iOS

Um **. NuSpec** arquivo de destino todos esses seria algo como:

```xml
<files>
    <file src="Mac\bin\Release\*.dll" target="lib\Xamarin.Mac20" />
    <file src="iOS\bin\Release\*.dll" target="lib\Xamarin.iOS10" />
    <file src="Android\bin\Release\*.dll" target="lib\MonoAndroid10" />
    <file src="iOSClassic\bin\Release\*.dll" target="lib\MonoTouch10" />
</files>
```

As opções acima ignora qualquer biblioteca de classes portátil.

A maioria dos **. NuSpec** arquivos especificam o número de versão do framework de destino, mas é opcional se o seu assembly funciona com todas as versões do framework de destino. Portanto, se o destino foi **lib\MonoAndroid** isso significaria que ele funciona com qualquer versão do xamarin.

Você pode especificar a versão com um conjunto de números sem um ponto decimal, ou você pode especificá-lo usando pontos decimais. Sem o ponto decimal NuGet apenas se cada número e transformá-lo em uma versão inserindo um '.' entre cada dígito.

No acima "MonoAndroid10" significa "Android 1.0". Isso significa que o projeto [framework de destino](~/android/app-fundamentals/android-api-levels.md) precisa ser MonoAndroid versão 1.0 ou posterior. A versão é especificada no `<TargetFrameworkVersion>` elemento no arquivo de projeto.

Para esclarecer:

- **MonoAndroid403** corresponde Android 4.0.3 e mais recente (ie API nível 15)
- **Xamarin.iOS10** corresponde xamarin 1.0 e mais recentes
- **Xamarin.iOS1.0** também corresponda xamarin 1.0 e mais recentes


## <a name="pcl-nugets-with-platform-dependencies"></a>PCL NuGets com dependências de plataforma

Perfis de PCL são limitados no que acessarem de APIs do .NET framework, e certamente não poderão acessar o código específico da plataforma. Esses links 3ª parte discutem diferentes abordagens para a criação de pacotes do NuGet que usam PCL e APIs nativas para fornecer compatibilidade para Xamarin e outras plataformas:

- [Como fazer o trabalho de bibliotecas de classes portátil para você](http://blogs.msdn.com/b/dsplaisted/archive/2012/08/27/how-to-make-portable-class-libraries-work-for-you.aspx)
- [O truque PCL isco](http://log.paulbetts.org/the-bait-and-switch-pcl-trick/)
- [Criando um PCL NuGet que funciona com xamarin](http://www.jimbobbennett.io/creating-a-nuget-pcl-that-works-with-xamarin-ios/)

Este externo [lista de perfis de PCL com seu nome de destino do NuGet](http://embed.plnkr.co/03ck2dCtnJogBKHJ9EjY) também é uma referência útil.

## <a name="examples"></a>Exemplos

Alguns exemplos de código-fonte aberto que você pode fazer referência a:

- [**ModernHttpClient** ](https://www.nuget.org/packages/modernhttpclient/) – gravar seu aplicativo usando o System, mas remover esta biblioteca no e ele irá consideravelmente mais rápido (exibição [origem](https://github.com/paulcbetts/ModernHttpClient)).
- [**Splat** ](https://www.nuget.org/packages/Splat/) – uma biblioteca para tornar as coisas entre plataformas que deve ser (exibição [origem](https://github.com/paulcbetts/Splat)).
- [**NGraphics** ](https://www.nuget.org/packages/NGraphics/) -uma biblioteca de plataforma cruzada para a renderização de gráficos vetoriais no .NET (exibição [origem](https://github.com/praeclarum/NGraphics/blob/master/NGraphics.nuspec)).


## <a name="related-links"></a>Links relacionados

- [3000 Nugetizer automatizada Nuget criação](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)
- [Atualizando NuGets para 64 bits do iOS](http://blog.xamarin.com/how-to-update-nuget-packages-for-64-bit/)
- [Incluindo um NuGet em seu projeto](/visualstudio/mac/nuget-walkthrough/index.md)
