---
title: Trabalhando com tipos nativos em aplicativos multiplataforma
description: Este artigo aborda o uso do iOS novos tipos nativos de API unificada (nint, nuint, nfloat) em um aplicativo de plataforma cruzada em que o código é compartilhado com dispositivos não iOS, como Android ou sistemas operacionais do Windows Phone.
ms.prod: xamarin
ms.assetid: B9C56C3B-E196-4ADA-A1DE-AC10D1001C2A
author: asb3993
ms.author: amburns
ms.date: 04/07/2016
ms.openlocfilehash: 489d2a76e6eff661360b24d1872ed1343c74b85e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61261175"
---
# <a name="working-with-native-types-in-cross-platform-apps"></a>Trabalhando com tipos nativos em aplicativos multiplataforma

_Este artigo aborda o uso do iOS novos tipos nativos de API unificada (nint, nuint, nfloat) em um aplicativo de plataforma cruzada em que o código é compartilhado com dispositivos não iOS, como Android ou sistemas operacionais do Windows Phone._


Os tipos de 64 tipos nativos trabalham com as APIs de Mac e iOS. Se você estiver escrevendo o código compartilhado que é executado no Android ou Windows, bem, você precisará gerenciar a conversão de tipos unificado em tipos .NET regulares que pode ser compartilhado.

Este documento discute as diferentes maneiras de interoperar com a API unificada do seu código compartilhado/comum.

## <a name="when-to-use-the-native-types"></a>Quando usar os tipos nativos

APIs de Unificação de xamarin. Mac e xamarin. IOS ainda incluem o `int`, `uint` e `float` tipos de dados, bem como o `RectangleF`, `SizeF` e `PointF` tipos. Esses tipos de dados existentes devem continuar a ser usado em qualquer código compartilhado e de plataforma cruzada. Os novos tipos de dados nativos só devem ser usados ao fazer uma chamada para uma API do iOS ou Mac no qual o suporte para tipos de arquitetura são necessários.

Dependendo da natureza do código que está sendo compartilhado, pode haver momentos em que o código de plataforma cruzada talvez precise lidar com o `nint`, `nuint` e `nfloat` tipos de dados. Por exemplo: uma biblioteca que manipula as transformações nos dados retangulares que estava usando anteriormente `System.Drawing.RectangleF` compartilhar a funcionalidade entre versões do xamarin. IOS e xamarin. Android de um aplicativo, precisa ser atualizada para lidar com tipos nativos no iOS.

Como essas alterações são tratadas depende do tamanho e da complexidade do aplicativo e o formulário de código de compartilhamento que foi usado, como veremos as seções a seguir.

## <a name="code-sharing-considerations"></a>Considerações de compartilhamento de código

Conforme mencionado na [opções de compartilhamento de código](~/cross-platform/app-fundamentals/code-sharing.md) documento, há duas maneiras de compartilhar código entre projetos de plataforma cruzada: Projetos e bibliotecas de classes portáteis compartilhadas. Qual dos dois tipos foi usado, limitará as opções disponíveis ao lidar com os tipos de dados nativos no código de plataforma cruzada.

### <a name="portable-class-library-projects"></a>Projetos de biblioteca de classes portátil

Uma biblioteca de classe portátil (PCL) permite direcionar as plataformas que você deseja dar suporte e usar Interfaces para fornecer a funcionalidade específica da plataforma.

Uma vez que o tipo de projeto de PCL é compilado para baixo até um `.DLL` e ele tem nenhum sentido de que a API unificada, você será forçado a continuar a usar os tipos de dados existente (`int`, `uint`, `float`) na PCL código-fonte e o tipo convertem as chamadas para os PCLs classes e métodos em aplicativos de front-end. Por exemplo:

```csharp
using NativePCL;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

### <a name="shared-projects"></a>Projetos compartilhados

O tipo de projeto de ativo compartilhado permite organizar seu código-fonte em um projeto separado, em seguida, obtém incluído e compilado em aplicativos individuais específicos da plataforma de front-end e usar `#if` conforme necessário para gerenciar as diretivas de compilador requisitos específicos da plataforma.

O tamanho e a complexidade da frente encerrar aplicativos móveis que estão consumindo o código compartilhado, juntamente com o tamanho e a complexidade do código que está sendo compartilhado, precisa ser levada em conta ao escolher o método de suporte a dados nativos tipos de plataforma cruzada com o Tipo de projeto de ativo compartilhado.

Com base nesses fatores, os seguintes tipos de soluções podem ser implementados usando o `if __UNIFIED__ ... #endif` diretivas de compilador para lidar com as alterações específicas de API unificada no código.

#### <a name="using-duplicate-methods"></a>Usando métodos duplicados

Veja o exemplo de uma biblioteca que está fazendo a transformações nos dados retangulares fornecidos acima. Se a biblioteca contém apenas um ou dois métodos muito simples, você pode optar por criar versões duplicadas desses métodos para xamarin. IOS e xamarin. Android. Por exemplo:

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

No código acima, uma vez que o `CalculateArea` rotina é muito simple, temos usado compilação condicional e criado uma separado, a versão de API unificada do método. Por outro lado, se a biblioteca continha muitas rotinas ou várias rotinas complexas, essa solução não seria viável, como o que ele deve apresentar um problema, mantendo todos os métodos em sincronia para modificações ou correções de bugs.

#### <a name="using-method-overloads"></a>Usar o método Overloads

Nesse caso, a solução pode ser criar uma versão de sobrecarga dos métodos usando tipos de dados de 32 bits para que eles tomem agora `CGRect` como parâmetro e/ou valor de retorno, converter esse valor em uma `RectangleF` (sabendo que a conversão de `nfloat` para `float` é uma conversão sem perdas) e chamar a versão original da rotina para fazer o trabalho real. Por exemplo:

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

Novamente, isso é uma boa solução, desde que a perda de precisão não afeta os resultados para necessidades específicas do seu aplicativo.

#### <a name="using-alias-directives"></a>Usando diretivas de Alias

Para áreas em que a perda de precisão for um problema, outra solução possível é usar `using` diretivas para criar um alias para tipos de dados nativo e CoreGraphics, incluindo o código a seguir na parte superior dos arquivos de código do código-fonte compartilhado e convertendo algum necessário `int`, `uint` ou `float` valores de `nint`, `nuint` ou `nfloat`:

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

Para que, em seguida, torna-se nosso código de exemplo:

```csharp
using System;
using System.Drawing;

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

Observe que aqui, alteramos a `CalculateArea` retorno de método uma `nfloat` em vez do padrão `float`. Isso foi feito para que não seria possível obter um erro de compilação ao tentar _implicitamente_ converter o `nfloat` resultado de nosso cálculo (uma vez que são os dois valores que está sendo multiplicados `nfloat`) em um `float` valor de retorno.

Se o código é compilado e executado em um dispositivo não API unificada, o `using nfloat = global::System.Single;` mapeia os `nfloat` para um `Single` que será convertido implicitamente em um `float` permitindo que o aplicativo de consumo de front-end chamar o `CalculateArea` método sem modificação.


#### <a name="using-type-conversions-in-the-front-end-app"></a>Usando conversões de tipo no aplicativo front-End

No caso em que seus aplicativos de front-end apenas fazem um punhado de chamadas para sua biblioteca de código compartilhado, outra solução poderia ser deixar a biblioteca inalterada e conversão de tipo no aplicativo xamarin. IOS ou xamarin. Mac ao chamar a rotina existente. Por exemplo:

```csharp
using NativeShared;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

Se o aplicativo de consumo torna centenas de chamadas para a biblioteca de código compartilhado, isso mais uma vez, pode não ser uma boa solução.

Com base na arquitetura do nosso aplicativo, podemos terminar usando uma ou mais das soluções acima para dar suporte a dados nativos tipos (onde for necessário) no nosso código de plataforma cruzada.


## <a name="xamarinforms-applications"></a>Aplicativos xamarin. Forms

O exemplo a seguir é necessária para usar o xamarin. Forms para interfaces do usuário de plataforma cruzada que também serão compartilhadas com um aplicativo de API unificada:

- A solução inteira deve estar usando a versão 1.3.1 (ou superior) do pacote do NuGet xamarin. Forms.
- Para qualquer renderizações personalizadas do xamarin. IOS, use os mesmos tipos de soluções apresentadas acima com base em como o código de interface do usuário tiver sido compartilhado (projeto compartilhado ou PCL).

Como em um aplicativo de plataforma cruzada padrão, 32 bits existentes dados tipos devem ser usados em qualquer código compartilhado e de plataforma cruzada para a maioria das situações de todos os. Novos tipos de dados nativo só deve ser usados ao fazer uma chamada para uma API do iOS ou Mac no qual o suporte para tipos de arquitetura são necessários.

Para obter mais detalhes, consulte nosso [Atualizando aplicativos existentes do xamarin. Forms](https://developer.xamarin.com/guides/cross-platform/macios/updating-xamarin-forms-apps/) documentação.

## <a name="summary"></a>Resumo

Neste artigo, nós temos ver quando devemos usar os tipos de dados nativos em um aplicativo de API unificada e sua implicações multiplataforma. Apresentamos várias soluções que podem ser usadas em situações em que os novos tipos de dados nativo deve ser usados em bibliotecas de plataforma cruzada. E vimos um guia rápido para dar suporte a APIs unificadas em aplicativos de plataforma cruzada do xamarin. Forms.



## <a name="related-links"></a>Links relacionados

- [API Unificada](~/cross-platform/macios/unified/index.md)
- [Tipos nativos](~/cross-platform/macios/nativetypes.md)
- [Opções de compartilhamento de código](~/cross-platform/app-fundamentals/code-sharing.md)
- [Exemplo de compartilhamento de código](https://developer.xamarin.com/samples/mobile/SharingCode/)
