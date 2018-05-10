---
title: Trabalhando com tipos nativos em aplicativos de plataforma cruzada
description: Este artigo aborda o uso do iOS novos tipos unificado API nativa (nint, nuint, nfloat) em um aplicativo de plataforma cruzada onde o código é compartilhado com dispositivos iOS não, como Android ou sistemas operacionais do Windows Phone.
ms.prod: xamarin
ms.assetid: B9C56C3B-E196-4ADA-A1DE-AC10D1001C2A
author: asb3993
ms.author: amburns
ms.date: 04/07/2016
ms.openlocfilehash: eb6979691eeef6dd7436d74fdfd501c747d9b3c6
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
---
# <a name="working-with-native-types-in-cross-platform-apps"></a>Trabalhando com tipos nativos em aplicativos de plataforma cruzada

_Este artigo aborda o uso do iOS novos tipos unificado API nativa (nint, nuint, nfloat) em um aplicativo de plataforma cruzada onde o código é compartilhado com dispositivos iOS não, como Android ou sistemas operacionais do Windows Phone._


Os tipos nativos de 64-tipos de trabalho com as APIs de Mac e iOS. Se você estiver escrevendo código compartilhado que é executado no Android ou Windows bem, você precisará gerenciar a conversão de tipos unificada em tipos de .NET regulares que você pode compartilhar.

Este documento aborda diferentes maneiras de interagir com a API unificada do seu código comum compartilhado /.

## <a name="when-to-use-the-native-types"></a>Quando usar os tipos nativos

Xamarin e Xamarin.Mac Unified APIs ainda incluem o `int`, `uint` e `float` tipos de dados, bem como o `RectangleF`, `SizeF` e `PointF` tipos. Esses tipos de dados existentes devem continuar a ser usado em qualquer código compartilhado e de plataforma cruzada. Os novos tipos de dados nativos só devem ser usados ao fazer uma chamada para uma API Mac ou iOS em que o suporte para tipos de arquitetura são necessários.

Dependendo da natureza do código que está sendo compartilhada, pode haver momentos em que o código de plataforma cruzada precisará lidar com a `nint`, `nuint` e `nfloat` tipos de dados. Por exemplo: uma biblioteca que manipula as transformações nos dados retangulares que estava usando anteriormente `System.Drawing.RectangleF` compartilhar recursos entre versões de um aplicativo xamarin e xamarin precisa ser atualizado para manipular tipos nativos no iOS.

Como essas alterações são tratadas depende do tamanho e complexidade do aplicativo e a forma de código de compartilhamento que foi usada, conforme veremos nas seções a seguir.

## <a name="code-sharing-considerations"></a>Considerações de compartilhamento de código

Conforme indicado no [opções de compartilhamento de código](~/cross-platform/app-fundamentals/code-sharing.md) documento, há duas maneiras de compartilhar código entre projetos de plataforma cruzada: projetos compartilhados e bibliotecas de classes portáteis. Os dois tipos de tiver sido usado, limitar as opções disponíveis ao lidar com os tipos de dados nativos no código de plataforma cruzada.

### <a name="portable-class-library-projects"></a>Projetos de biblioteca de classes portátil

Uma biblioteca de classe portátil (PCL) permite que você direcionar as plataformas que você deseja dar suporte e usar Interfaces para fornecer a funcionalidade específica de plataforma.

Como o tipo de projeto PCL é compilado para baixo até um `.DLL` e ele tem sem sentido da API unificada, você vai ser forçado a continuar usando os tipos de dados existente (`int`, `uint`, `float`) no PCL código-fonte e o tipo de converter as chamadas para os PCLs classes e métodos dos aplicativos front-end. Por exemplo:

```csharp
using NativePCL;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

### <a name="shared-projects"></a>Projetos compartilhados

O tipo de projeto de ativo compartilhado permite organizar seu código-fonte em um projeto separado, em seguida, obtém incluído e compilado em aplicativos individuais front-end específico da plataforma e usar `#if` conforme necessário para gerenciar as diretivas de compilador requisitos de plataforma específica.

O tamanho e a complexidade da frente encerrar aplicativos móveis que estão consumindo código compartilhado, juntamente com o tamanho e a complexidade do código que está sendo compartilhada, precisa ser levado em consideração ao escolher o método de suporte para dados nativos tipos de plataforma cruzada com o Tipo de projeto de ativo compartilhado.

Com base nesses fatores, os seguintes tipos de soluções podem ser implementados usando o `if __UNIFIED__ ... #endif` diretivas de compilador para lidar com as alterações específicas da API unificada para o código.

#### <a name="using-duplicate-methods"></a>Usando métodos duplicados

Veja o exemplo de uma biblioteca que está fazendo a transformações nos dados retangulares fornecidos acima. Se a biblioteca contém apenas um ou dois métodos muito simples, você poderá criar versões duplicadas desses métodos para xamarin e xamarin. Por exemplo:

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

No código acima, pois o `CalculateArea` rotina é muito simple, ter usado a compilação condicional e criado um separado, a versão de API unificada do método. Por outro lado, se a biblioteca continha muitos rotinas ou várias rotinas complexas, essa solução não seria for viável, pois ela poderia apresentar um problema manter todos os métodos de sincronização para modificações ou correções de bugs.

#### <a name="using-method-overloads"></a>Usar o método Overloads

Nesse caso, a solução pode ser criar uma versão de sobrecarga dos métodos usando tipos de dados de 32 bits que agora `CGRect` como parâmetro e/ou valor de retorno, converter esse valor em uma `RectangleF` (saber que a conversão de `nfloat` para `float` uma conversão com perdas) e chamar a versão original da rotina para fazer o trabalho real. Por exemplo:

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

Novamente, isso é uma boa solução como a perda de precisão não afeta os resultados para as necessidades específicas do seu aplicativo.

#### <a name="using-alias-directives"></a>Usando diretivas de Alias

Para as áreas onde a perda de precisão é um problema, uma outra solução possível é usar `using` diretivas para criar um alias para tipos de dados nativo e CoreGraphics, incluindo o código a seguir para a parte superior dos arquivos de código fonte compartilhada e converter qualquer necessário `int`, `uint` ou `float` valores `nint`, `nuint` ou `nfloat`:

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

Para que o nosso código de exemplo torna-se:

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

Observe que aqui alteramos o `CalculateArea` método retorno um `nfloat` em vez do padrão `float`. Isso foi feito para que não se um erro de compilação ao tentar _implicitamente_ converter o `nfloat` resultado de nosso cálculo (já que ambos os valores que está sendo multiplicados são `nfloat`) em um `float` valor de retorno.

Se o código é compilado e executado em um dispositivo não API unificada, o `using nfloat = global::System.Single;` mapeia o `nfloat` para um `Single` que serão implicitamente convertidos para um `float` permitindo que o aplicativo de consumo de front-end chamar o `CalculateArea` método sem modificação.


#### <a name="using-type-conversions-in-the-front-end-app"></a>Usando conversões de tipo no aplicativo Front-End

Que seus aplicativos de front-end apenas fazem uma série de chamadas para a biblioteca de código compartilhado, outra solução poderia ser deixar a biblioteca inalterada e tipo de conversão no aplicativo xamarin ou Xamarin.Mac ao chamar a rotina existente. Por exemplo:

```csharp
using NativeShared;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

Se o aplicativo de consumo centenas de chamadas para a biblioteca de código compartilhado isso novamente, pode não ser uma boa solução.

Com base na arquitetura do nosso aplicativo, estamos pode acabar usando uma ou mais das soluções acima para dar suporte a dados nativos tipos (se necessário) em nosso código de plataforma cruzada.


## <a name="xamarinforms-applications"></a>Aplicativos xamarin. Forms

O exemplo a seguir é necessário para usar o xamarin. Forms para interfaces de plataforma cruzada também serão compartilhadas com um aplicativo de API unificada:

- Toda a solução deve estar usando a versão 1.3.1 (ou superior) do pacote do NuGet xamarin. Forms.
- Para qualquer processa personalizada xamarin, use os mesmos tipos de soluções apresentados a seguir com base em como o código de interface do usuário foi compartilhado (projeto compartilhado ou PCL).

Como um aplicativo de plataforma cruzada padrão, de 32 bits existentes tipos de dados devem ser usados em qualquer código compartilhado e de plataforma cruzada para todas as situações mais. Novos tipos de dados nativo só deve ser usados ao fazer uma chamada para uma API Mac ou iOS em que o suporte para tipos de arquitetura são necessários.

Para obter mais detalhes, consulte nosso [atualizar aplicativos existentes do xamarin. Forms](http://developer.xamarin.com/guides/cross-platform/macios/updating-xamarin-forms-apps/) documentação.

## <a name="summary"></a>Resumo

Neste artigo, nós temos consulte quando devemos usar os tipos de dados nativos em um aplicativo de API unificada e suas implicações entre plataformas. Podemos ter apresentado várias soluções que podem ser usadas em situações em que os novos tipos de dados nativo deve ser usados em bibliotecas de plataforma cruzada. E que vimos um guia rápido para dar suporte a APIs unificado em aplicativos de plataforma cruzada do xamarin. Forms.



## <a name="related-links"></a>Links relacionados

- [API Unificada](~/cross-platform/macios/unified/index.md)
- [Tipos nativos](~/cross-platform/macios/nativetypes.md)
- [Opções de compartilhamento de código](~/cross-platform/app-fundamentals/code-sharing.md)
- [Exemplo de compartilhamento de código](https://developer.xamarin.com/samples/mobile/SharingCode/)
