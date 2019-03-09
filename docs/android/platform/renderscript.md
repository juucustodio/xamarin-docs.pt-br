---
title: Uma introdução ao Renderscript
description: Este guia apresenta Renderscript e explica como usar o intrínseco Renderscript da API em um aplicativo xamarin. Android esse nível de API de destinos 17 ou superior.
ms.prod: xamarin
ms.assetid: 378793C7-5E3E-40E6-ABEE-BEAEF64E6A47
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 8364310d23739c05ff97ea8aa8fa4c56f89ea40c
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670721"
---
# <a name="an-introduction-to-renderscript"></a>Uma introdução ao Renderscript

_Este guia apresenta Renderscript e explica como usar o intrínseco Renderscript da API em um aplicativo xamarin. Android esse nível de API de destinos 17 ou superior._

## <a name="overview"></a>Visão geral

RenderScript é uma estrutura de programação criada pelo Google para fins de aprimorar o desempenho de aplicativos Android que exigem muitos recursos computacionais. É um nível inferior, de alto desempenho API com base em [C99](https://en.wikipedia.org/wiki/C99). Porque ele é um nível baixo API que serão executados no DSPs, GPUs ou CPUs, Renderscript é adequado para aplicativos Android que talvez seja necessário executar qualquer um dos seguintes:

* Gráficos
* Processamento de imagens
* Criptografia
* Processamento de sinais
* Rotinas de matemáticas

RenderScript usará `clang` e compilar os scripts de código de byte LLVM, que é fornecido no APK. Quando o aplicativo é executado pela primeira vez, o código de byte LLVM será compilado em código de computador para os processadores no dispositivo. Essa arquitetura permite que um aplicativo do Android explorar as vantagens do código de máquina sem os desenvolvedores tendo que criá-lo para cada processador no dispositivo em si.

Há dois componentes para uma rotina Renderscript:

1. **O tempo de execução Renderscript** &ndash; trata as APIs nativas que são responsáveis por executar o Renderscript. Isso inclui qualquer Renderscripts escritos para o aplicativo.

2. **Invólucros gerenciados do Android Framework** &ndash; gerenciados classes que permitem que um aplicativo do Android controlar e interagir com o tempo de execução Renderscript e scripts. Além das classes de estrutura fornecida para controlar o tempo de execução Renderscript, a cadeia de ferramentas do Android examinaremos o código-fonte Renderscript e gerar classes de wrapper gerenciado para uso pelo aplicativo Android.

O diagrama a seguir ilustra como esses componentes se relacionam:

![Diagrama ilustrando como o Framework Android interage com o Renderscript Runtime](renderscript-images/renderscript-01.png)

Há três conceitos importantes para usar Renderscripts em um aplicativo do Android:

1. **Um contexto** &ndash; um gerenciado API fornecida pelo SDK do Android que aloca recursos para Renderscript e permite que o aplicativo Android transmitir e receber dados do Renderscript.

2. **Um _kernel de computação_**  &ndash; também conhecido como o _kernel raiz_ ou _kernel_, esse uma rotina que faz o trabalho. O kernel é muito semelhante a uma função C; é uma rotina paralelizável que executará todos os dados na memória alocada.

3. **A memória alocada** &ndash; dados são passados de e para um kernel por meio de uma  _[alocação](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)_. Um kernel pode ter uma entrada e/ou uma alocação de saída.

O [Android.Renderscripts](https://developer.xamarin.com/api/namespace/Android.Renderscripts/) namespace contém as classes para interagir com o tempo de execução Renderscript. Em particular, o [ `Renderscript` ](https://developer.xamarin.com/api/type/Android.Renderscripts.RenderScript/) classe irá gerenciar o ciclo de vida e os recursos do mecanismo de Renderscript. O aplicativo do Android deve inicializar um ou mais [`Android.Renderscripts.Allocation`](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)
Objetos. Uma alocação é uma API gerenciada que é responsável pela alocação e acessar a memória que é compartilhada entre o aplicativo do Android e o tempo de execução Renderscript. Normalmente, uma alocação é criada para entrada e alocação, opcionalmente, outra é criada para manter a saída do kernel. O mecanismo de tempo de execução Renderscript e as classes de wrapper gerenciado associado irá gerenciar o acesso à memória mantida pelas alocações, não é necessário para um desenvolvedor de aplicativo do Android para fazer qualquer trabalho adicional.

Uma alocação conterá um ou mais [Android.Renderscripts.Elements](https://developer.xamarin.com/api/type/Android.Renderscripts.Element/).
Elementos são um tipo especializado que descrevem dados em cada alocação.
Os tipos de elemento da saída de alocação deve corresponder os tipos de elemento de entrada. Durante a execução, um Renderscript iterar sobre cada elemento na alocação de entrada em paralelo e gravar os resultados para a saída de alocação. Há dois tipos de elementos:

- **tipo simples** &ndash; conceitualmente isso é o mesmo que um tipo de dados C `float` ou um `char`.

- **tipo complexo** &ndash; esse tipo é semelhante a um C `struct`.

O mecanismo Renderscript executará uma verificação de tempo de execução para garantir que os elementos em cada alocação são compatíveis com o que é exigido pelo kernel. Se o tipo de dados dos elementos na alocação de não coincidem com o tipo de dados que o kernel está esperando, uma exceção será lançada.

Todos os kernels de Renderscript serão colocados dentro de um tipo que é um descendente das [`Android.Renderscripts.Script`](https://developer.xamarin.com/api/type/Android.Renderscripts.Script/)
. O `Script` classe é usada para definir parâmetros para um Renderscript, defina apropriado `Allocations`, e execute o Renderscript. Há dois `Script` subclasses no SDK do Android:


- **`Android.Renderscripts.ScriptIntrinsic`** &ndash; Algumas das tarefas mais comuns Renderscript estão incluídas no SDK do Android e podem ser acessadas por uma subclasse do [ScriptIntrinsic](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsic/) classe. Não é necessário para um desenvolvedor executar quaisquer etapas adicionais para usar esses scripts em seus aplicativos, pois eles já são fornecidos.

- **`ScriptC_XXXXX`** &ndash; Também conhecido como _scripts de usuário_, esses são scripts que são escritos por desenvolvedores e empacotados no APK. Em tempo de compilação, a cadeia de ferramentas do Android irá gerar classes de wrapper gerenciado que permite que os scripts a ser usado no aplicativo do Android.
  O nome dessas classes gerado é o nome do arquivo Renderscript, prefixado com `ScriptC_`. Escrevendo e incorporação de scripts do usuário não tem suporte oficialmente pelo xamarin. Android e além do escopo deste guia.

Desses dois tipos, somente o `StringIntrinsic` é compatível com o xamarin. Android. Este guia discute como usar o intrínseco scripts em um aplicativo xamarin. Android.

## <a name="requirements"></a>Requisitos

Este guia é para aplicativos xamarin. Android, esse nível de API de destino 17 ou superior. O uso de _scripts do usuário_ não é abordada neste guia.

O [biblioteca de suporte do xamarin. Android V8](https://www.nuget.org/packages/Xamarin.Android.Support.v8.RenderScript/) backports o instrinsic Renderscript da API para aplicativos destinados a versões mais antigas do SDK do Android. Adicionando esse pacote a um projeto do xamarin. Android deve permitir que aplicativos destinados a versões anteriores do SDK do Android para aproveitar os scripts intrínseco.

## <a name="using-intrinsic-renderscripts-in-xamarinandroid"></a>Usando Renderscripts intrínseco no xamarin. Android

Os scripts intrínsecos são uma ótima maneira de executar tarefas com uso intensivo de computação com uma quantidade mínima de código adicional. Eles foram ajustado para oferecer o desempenho ideal em uma seção cruzada grande de dispositivos de mão.
Não é incomum para um script intrínseco seja executado 10 vezes mais rápido do que o código gerenciado e 2 a 3 vezes a depois que uma implementação personalizada de C. Muitos dos cenários típicos de processamento são cobertos pelos scripts intrínseco. Esta lista dos scripts intrínseco descreve os scripts atuais no xamarin. Android:

- [ScriptIntrinsic3DLUT](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsic3DLUT//) &ndash; converte RGB para RGBA usando uma tabela de pesquisa 3D. 

- [ScriptIntrinsicBLAS](https://developer.android.com/reference/android/renderscript/ScriptIntrinsicBLAS.html) &ndash; Provideshigh desempenho Renderscript APIs para [BLAS](http://www.netlib.org/blas/). BLAS (subprogramas de álgebra Linear básica) são rotinas que fornecem os blocos de construção padrão para executar operações de matriz e vetor básico. 

- [ScriptIntrinsicBlend](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicBlend) &ndash; combina duas alocações juntos.

- [ScriptIntrinsicBlur](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicBlur) &ndash; aplica um Desfoque Gaussiano para uma alocação.

- [ScriptIntrinsicColorMatrix](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicColorMatrix/) &ndash; aplica-se a uma matriz de cores para uma alocação (ou seja, alterar cores, ajuste o matiz).

- [ScriptIntrinsicConvolve3x3](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicConvolve3x3/) &ndash; aplica-se a uma matriz de cores de 3x3 para uma alocação.

- [ScriptIntrinsicConvolve5x5](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicConvolve5x5/) &ndash; aplica-se a uma matriz de 5x5 cores para uma alocação.

- [ScriptIntrinsicHistogram](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicHistogram/) &ndash; um filtro de histograma intrínseco.

- [ScriptIntrinsicLUT](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicLUT/) &ndash; aplica-se a uma tabela de pesquisa por canal para um buffer.

- [ScriptIntrinsicResize](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicResize/) &ndash; Script para executar o redimensionamento de uma alocação de 2D.

- [ScriptIntrinsicYuvToRGB](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicYuvToRGB/) &ndash; converte um buffer YUV para RGB.

Consulte a documentação da API para obter detalhes sobre cada um dos scripts intrínseco.

As etapas básicas para usar Renderscript em um aplicativo do Android são descritas a seguir.

**Criar um contexto de Renderscript** &ndash; o [`Renderscript`](https://developer.xamarin.com/api/type/Android.Renderscripts.RenderScript/)
classe é um invólucro gerenciado em contexto Renderscript e controlam a inicialização, gerenciamento de recursos e limpar. O objeto Renderscript é criado usando o `RenderScript.Create` método de fábrica, que usa um contexto Android (por exemplo, uma atividade) como um parâmetro. A seguinte linha de código demonstra como inicializar o contexto de Renderscript:

```csharp
Android.Renderscripts.RenderScript renderScript = RenderScript.Create(this);
```

**Criar alocações** &ndash; , dependendo do script intrínseco, pode ser necessário criar um ou dois `Allocation`s. O [`Android.Renderscripts.Allocation`](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)
classe tem vários métodos de fábrica para ajudá-lo criando uma instância de uma alocação para um intrínseco. Por exemplo, o trecho de código a seguir demonstra como criar alocação para Bitmaps.

```csharp
Android.Graphics.Bitmap originalBitmap;
Android.Renderscripts.Allocation inputAllocation = Allocation.CreateFromBitmap(renderScript,
                                                     originalBitmap,
                                                     Allocation.MipmapControl.MipmapFull,
                                                     AllocationUsage.Script);
```

Muitas vezes, será necessário criar um `Allocation` para manter os dados de saída de um script. Este trecho de código a seguir mostra como usar o `Allocation.CreateTyped` auxiliar para instanciar um segundo `Allocation` que o mesmo tipo que o original:

```csharp
Android.Renderscripts.Allocation outputAllocation = Allocation.CreateTyped(renderScript, inputAllocation.Type);
```

**Criar uma instância do wrapper de Script** &ndash; cada uma das classes de wrapper de script intrínseco deve ter métodos auxiliares (normalmente chamado `Create`) para instanciar um objeto wrapper para esse script. O trecho de código a seguir está um exemplo de como criar uma instância de um `ScriptIntrinsicBlur` desfoque do objeto. O `Element.U8_4` método auxiliar criará um elemento que descreve um tipo de dados que é 4 campos de valores de inteiro de 8 bits sem sinal, adequadas para conter os dados de um `Bitmap` objeto:

```csharp
Android.Renderscripts.ScriptIntrinsicBlur blurScript = ScriptIntrinsicBlur.Create(renderScript, Element.U8_4(renderScript));
```

**Atribuir Allocation(s), definir parâmetros de & execução de Script** &ndash; as `Script` classe fornece um `ForEach` método a ser executado, na verdade, o Renderscript. Esse método irá iterar sobre cada `Element` no `Allocation` mantendo os dados de entrada. Em alguns casos, pode ser necessário fornecer um `Allocation` que contém a saída.
`ForEach` substituirá o conteúdo da saída de alocação. Para continuar com os trechos de código das etapas anteriores, este exemplo mostra como atribuir uma alocação de entrada, defina um parâmetro e, em seguida, por fim, execute o script (copiando os resultados para a saída de alocação):

```csharp
blurScript.SetInput(inputAllocation);
blurScript.SetRadius(25);  // Set a pamaeter
blurScript.ForEach(outputAllocation);
```

Talvez você queira fazer check-out a [desfoque uma imagem com Renderscript](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript) receita, é um exemplo completo de como usar um script intrínseco no xamarin. Android.

## <a name="summary"></a>Resumo

Este guia apresentou Renderscript e como usá-lo em um aplicativo xamarin. Android. Discuti brevemente o que é Renderscript e como ele funciona em um aplicativo Android. Ele descreveu alguns dos principais componentes Renderscript e a diferença entre _scripts de usuário_ e _instrinsic scripts_. Por fim, este guia abordou as etapas usando um script intrínseco em um aplicativo xamarin. Android.



## <a name="related-links"></a>Links relacionados

- [Namespace Android.Renderscripts](https://developer.xamarin.com/api/namespace/Android.Renderscripts/)
- [Uma imagem com Renderscript de desfoque](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript)
- [Renderscript](https://developer.android.com/guide/topics/renderscript/compute.html)
- [Tutorial: Introdução ao Renderscript](https://software.intel.com/en-us/articles/renderscript-basic-sample-for-android-os)
