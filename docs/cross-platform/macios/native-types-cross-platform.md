---
title: Trabalhando com tipos nativos em aplicativos multiplataforma
description: Este artigo aborda o uso dos novos tipos nativos do iOS API Unificada (Nint, nuint, nfloat) em um aplicativo de plataforma cruzada em que o código é compartilhado com dispositivos não iOS, como Android ou Windows Phone sistemas operacionais.
ms.prod: xamarin
ms.assetid: B9C56C3B-E196-4ADA-A1DE-AC10D1001C2A
author: asb3993
ms.author: amburns
ms.date: 04/07/2016
ms.openlocfilehash: 9018c3e3c976ef7623835055ee989dfd86dc25e1
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70226229"
---
# <a name="working-with-native-types-in-cross-platform-apps"></a>Trabalhando com tipos nativos em aplicativos multiplataforma

_Este artigo aborda o uso dos novos tipos nativos do iOS API Unificada (Nint, nuint, nfloat) em um aplicativo de plataforma cruzada em que o código é compartilhado com dispositivos não iOS, como Android ou Windows Phone sistemas operacionais._


Os tipos nativos de 64 tipos funcionam com as APIs iOS e Mac. Se você estiver escrevendo um código compartilhado que é executado no Android ou no Windows também, será necessário gerenciar a conversão de tipos unificados em tipos .NET regulares que você pode compartilhar.

Este documento discute diferentes maneiras de interoperar com o API Unificada de seu código compartilhado/comum.

## <a name="when-to-use-the-native-types"></a>Quando usar os tipos nativos

APIs unificadas do xamarin. Ios e xamarin. Mac `int`ainda `uint` incluem `float` os tipos de `SizeF` dados e, bem `RectangleF`como os `PointF` tipos e. Esses tipos de dados existentes devem continuar a ser usados em qualquer código compartilhado e de plataforma cruzada. Os novos tipos de dados nativos só devem ser usados ao fazer uma chamada para uma API Mac ou iOS, onde o suporte para tipos com reconhecimento de arquitetura é necessário.

Dependendo da natureza do código que está sendo compartilhado, pode haver ocasiões em que o código de plataforma cruzada pode precisar lidar com os `nint`tipos `nuint` de `nfloat` dados e. Por exemplo: uma biblioteca que manipula transformações em dados retangulares que estavam usando `System.Drawing.RectangleF` anteriormente para compartilhar a funcionalidade entre versões do xamarin. Ios e xamarin. Android de um aplicativo precisa ser atualizada para lidar com tipos nativos no Ios.

A forma como essas alterações são tratadas depende do tamanho e da complexidade do aplicativo e da forma de compartilhamento de código que foi usada, como veremos nas seções a seguir.

## <a name="code-sharing-considerations"></a>Considerações de compartilhamento de código

Conforme indicado no documento [Opções de código de compartilhamento](~/cross-platform/app-fundamentals/code-sharing.md) , há duas maneiras principais de compartilhar código entre projetos de plataforma cruzada: Projetos compartilhados e bibliotecas de classes portáteis. Qual dos dois tipos foi usado, limitará as opções que temos ao lidar com os tipos de dados nativos no código de plataforma cruzada.

### <a name="portable-class-library-projects"></a>Projetos de biblioteca de classes portáteis

Uma PCL (biblioteca de classes portátil) permite que você direcione as plataformas às quais deseja dar suporte e use interfaces para fornecer funcionalidade específica da plataforma.

Como o tipo de projeto PCL é compilado para um `.DLL` e não tem sentido do API unificada, você será forçado a continuar usando os tipos de dados existentes (`int`, `uint`, `float`) no código-fonte PCL e o tipo converte as chamadas para o PCL classes e métodos nos aplicativos de front-end. Por exemplo:

```csharp
using NativePCL;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

### <a name="shared-projects"></a>Projetos compartilhados

O tipo de projeto de ativo compartilhado permite organizar o código-fonte em um projeto separado que, em seguida, é incluído e compilado nos aplicativos individuais de front-end específicos `#if` da plataforma e usa as diretivas do compilador conforme necessário para gerenciar requisitos específicos da plataforma.

O tamanho e a complexidade dos aplicativos móveis de front-end que estão consumindo código compartilhado, juntamente com o tamanho e a complexidade do código que está sendo compartilhado, precisam ser levados em conta ao escolher o método de suporte para tipos de dados nativos em uma plataforma cruzada Projeto de ativo compartilhado.

Com base nesses fatores, os seguintes tipos de soluções podem ser implementados usando `if __UNIFIED__ ... #endif` as diretivas do compilador para lidar com as API unificada alterações específicas ao código.

#### <a name="using-duplicate-methods"></a>Usando métodos duplicados

Veja o exemplo de uma biblioteca que está fazendo transformações em dados retangulares fornecidos acima. Se a biblioteca contiver apenas um ou dois métodos muito simples, você poderá optar por criar versões duplicadas desses métodos para Xamarin. iOS e Xamarin. Android. Por exemplo:

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
using CoreGraphics;
#endif

namespace NativeShared
{
    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        #if __UNIFIED__
            public static nfloat CalculateArea(CGRect rect) {

                // Calculate area...
                return (rect.Width * rect.Height);

            }
        #else
            public static float CalculateArea(RectangleF rect) {

                // Calculate area...
                return (rect.Width * rect.Height);

            }
        #endif
        #endregion
    }
}
```

No código acima, como a `CalculateArea` rotina é muito simples, usamos a compilação condicional e criamos uma versão API unificada separada do método. Por outro lado, se a biblioteca contivesse muitas rotinas ou várias rotinas complexas, essa solução não seria viável, pois ela apresentaria um problema que mantém todos os métodos em sincronia para modificações ou correções de bugs.

#### <a name="using-method-overloads"></a>Usando sobrecargas de método

Nesse caso, a solução pode ser criar uma versão de sobrecarga dos métodos usando tipos de dados de 32 bits para que eles agora executem `CGRect` como um parâmetro e/ou um valor de retorno, converta esse valor `RectangleF` em um (sabendo que `nfloat` converter de para `float` é uma conversão com perdas) e chama a versão original da rotina para fazer o trabalho real. Por exemplo:

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
using CoreGraphics;
#endif

namespace NativeShared
{
    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        #if __UNIFIED__
            public static nfloat CalculateArea(CGRect rect) {

                // Call original routine to calculate area
                return (nfloat)CalculateArea((RectangleF)rect);

            }
        #endif

        public static float CalculateArea(RectangleF rect) {

            // Calculate area...
            return (rect.Width * rect.Height);

        }

        #endregion
    }
}

```

Novamente, essa é uma boa solução, desde que a perda de precisão não afete os resultados das necessidades específicas do seu aplicativo.

#### <a name="using-alias-directives"></a>Usando diretivas de alias

Para áreas em que a perda de precisão é um problema, outra solução possível é usar `using` diretivas para criar um alias para tipos de dados nativos e CoreGraphics incluindo o código a seguir na parte superior dos arquivos de código-fonte compartilhados e convertendo qualquer necessário `int`, `uint` ou valorespara`nint` ,`nfloat`ou: `float` `nuint`

```csharp
#if __UNIFIED__
    // Mappings Unified CoreGraphic classes to MonoTouch classes
    using RectangleF = global::CoreGraphics.CGRect;
    using SizeF = global::CoreGraphics.CGSize;
    using PointF = global::CoreGraphics.CGPoint;
#else
    // Mappings Unified types to MonoTouch types
    using nfloat = global::System.Single;
    using nint = global::System.Int32;
    using nuint = global::System.UInt32;
#endif
```

Então, nosso código de exemplo se torna:

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
    // Map Unified CoreGraphic classes to MonoTouch classes
    using RectangleF = global::CoreGraphics.CGRect;
    using SizeF = global::CoreGraphics.CGSize;
    using PointF = global::CoreGraphics.CGPoint;
#else
    // Map Unified types to MonoTouch types
    using nfloat = global::System.Single;
    using nint = global::System.Int32;
    using nuint = global::System.UInt32;
#endif

namespace NativeShared
{

    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        public static nfloat CalculateArea(RectangleF rect) {

            // Calculate area...
            return (rect.Width * rect.Height);

        }
        #endregion
    }
}
```

Observe que, aqui, alteramos `CalculateArea` o método para retornar `nfloat` um, em vez `float`do padrão. Isso foi feito para que não tenhamos um erro de compilação ao tentar converter _implicitamente_ o `nfloat` resultado do nosso cálculo (já que ambos os valores que estão sendo multiplicados `nfloat`são do tipo `float` ) em um valor de retorno.

Se o código for compilado e executado em um dispositivo não API unificada, o `using nfloat = global::System.Single;` mapeará `nfloat` o para `Single` um que será convertido implicitamente em `float` um que permita que o aplicativo de front-end `CalculateArea` de consumo chame o método sem modificado.


#### <a name="using-type-conversions-in-the-front-end-app"></a>Usando conversões de tipo no aplicativo de front-end

Caso os aplicativos de front-end façam apenas algumas chamadas para sua biblioteca de códigos compartilhados, outra solução poderia ser deixar a biblioteca inalterada e fazer a conversão de tipo no aplicativo Xamarin. iOS ou Xamarin. Mac ao chamar a rotina existente. Por exemplo:

```csharp
using NativeShared;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

Se o aplicativo de consumo fizer centenas de chamadas para a biblioteca de código compartilhada, isso poderá não ser uma boa solução.

Com base na arquitetura de nosso aplicativo, podemos acabar usando uma ou mais das soluções acima para dar suporte a tipos de dados nativos (quando necessário) em nosso código de plataforma cruzada.


## <a name="xamarinforms-applications"></a>Aplicativos Xamarin. Forms

O seguinte é necessário para usar o Xamarin. Forms para UIs de plataforma cruzada que também serão compartilhadas com um aplicativo API Unificada:

- A solução inteira deve estar usando a versão 1.3.1 (ou superior) do pacote NuGet do Xamarin. Forms.
- Para qualquer renderização personalizada do Xamarin. iOS, use os mesmos tipos de soluções apresentadas acima com base em como o código da interface do usuário foi compartilhado (projeto compartilhado ou PCL).

Como em um aplicativo de plataforma cruzada padrão, os tipos de dados existentes de 32 bits devem ser usados em qualquer código compartilhado e de plataforma cruzada para a maioria das situações. Os novos tipos de dados nativos só devem ser usados ao fazer uma chamada para uma API Mac ou iOS, onde o suporte para tipos com reconhecimento de arquitetura é necessário.

Para obter mais detalhes, consulte nossa documentação atualizando os [aplicativos Xamarin. Forms existentes](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md) .

## <a name="summary"></a>Resumo

Neste artigo, vimos quando usar os tipos de dados nativos em um aplicativo API Unificada e suas implicações entre plataformas. Apresentamos várias soluções que podem ser usadas em situações em que os novos tipos de dados nativos devem ser usados em bibliotecas de plataforma cruzada. Além disso, vimos um guia rápido para dar suporte a APIs unificadas em aplicativos de plataforma cruzada do Xamarin. Forms.



## <a name="related-links"></a>Links relacionados

- [API Unificada](~/cross-platform/macios/unified/index.md)
- [Tipos nativos](~/cross-platform/macios/nativetypes.md)
- [Opções de código de compartilhamento](~/cross-platform/app-fundamentals/code-sharing.md)
- [Exemplo de compartilhamento de código](https://docs.microsoft.com/samples/xamarin/mobile-samples/sharingcode/)
