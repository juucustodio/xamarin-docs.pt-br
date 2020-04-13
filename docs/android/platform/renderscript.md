---
title: Uma Introdução ao Renderscript
description: Este guia introduz o Renderscript e explica como usar as API renderizais intrínsecas em um aplicativo Xamarin.Android que tem como alvo o nível 17 da API ou superior.
ms.prod: xamarin
ms.assetid: 378793C7-5E3E-40E6-ABEE-BEAEF64E6A47
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 884b69b0cdecf4f979cec314b6440974c5bac97d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019805"
---
# <a name="an-introduction-to-renderscript"></a>Uma Introdução ao Renderscript

_Este guia introduz o Renderscript e explica como usar as API renderizais intrínsecas em um aplicativo Xamarin.Android que tem como alvo o nível 17 da API ou superior._

## <a name="overview"></a>Visão geral

Renderscript é uma estrutura de programação criada pelo Google com o objetivo de melhorar o desempenho de aplicativos Android que requerem amplos recursos computacionais. É uma API de baixo nível e alto desempenho baseada em [C99](https://en.wikipedia.org/wiki/C99). Por ser uma API de baixo nível que será executada em CPUs, GPUs ou DSPs, o Renderscript é adequado para aplicativos Android que podem precisar executar qualquer um dos seguintes:

- Gráficos
- Processamento de Imagem
- Criptografia
- Processamento de sinais
- Rotinas Matemáticas

O Renderscript `clang` usará e compilará os scripts para o código de byte LLVM que é empacotado no APK. Quando o aplicativo for executado pela primeira vez, o código de byte LLVM será compilado em código de máquina para os processadores do dispositivo. Esta arquitetura permite que um aplicativo Android explore as vantagens do código da máquina sem que os próprios desenvolvedores tenham que escrevê-lo para cada processador no próprio dispositivo.

Há dois componentes para uma rotina renderscript:

1. **O tempo de execução** &ndash; renderscript Estas são as APIs nativas que são responsáveis pela execução do Renderscript. Isso inclui quaisquer Renderscripts escritos para o aplicativo.

2. **Wrappers gerenciados das** &ndash; classes Android Framework Managed que permitem que um aplicativo Android controle e interaja com o tempo de execução do Renderscript e scripts. Além da estrutura fornecida classes para controlar o tempo de execução do Renderscript, a cadeia de ferramentas do Android examinará o código-fonte do Renderscript e gerará classes de invólucro gerenciadas para uso pelo aplicativo Android.

O diagrama a seguir ilustra como esses componentes se relacionam:

![Diagrama ilustrando como o Android Framework interage com o Renderscript Runtime](renderscript-images/renderscript-01.png)

Existem três conceitos importantes para usar renderscripts em um aplicativo Android:

1. **Um contexto** &ndash; Uma API gerenciada fornecida pelo Android SDK que aloca recursos para o Renderscript e permite que o aplicativo Android passe e receba dados do Renderscript.

2. Um &ndash; **kernel _de computação_ ** Também conhecido como _kernel raiz_ ou _kernel,_ esta é uma rotina que faz o trabalho. O kernel é muito semelhante a uma função C; é uma rotina paraleletrizável que será executada sobre todos os dados na memória alocada .

3. **Os** &ndash; dados de memória alocados são passados para e a partir de um kernel através de uma _[alocação](xref:Android.Renderscripts.Allocation)_. Um kernel pode ter uma entrada e/ou uma alocação de saída.

O [namespace do Android.Renderscripts](xref:Android.Renderscripts) contém as classes para interagir com o tempo de execução do Renderscript. Em particular, [`Renderscript`](xref:Android.Renderscripts.RenderScript) a classe gerenciará o ciclo de vida e os recursos do mecanismo Renderscript. O aplicativo para Android deve inicializar um ou mais[`Android.Renderscripts.Allocation`](xref:Android.Renderscripts.Allocation)
banco de dados. Uma Alocação é uma API gerenciada que é responsável pela alocação e acesso à memória compartilhada entre o aplicativo Android e o tempo de execução do Renderscript. Normalmente, uma alocação é criada para entrada e, opcionalmente, outra alocação é criada para segurar a saída do kernel. O mecanismo de tempo de execução renderscript e as classes de invólucro gerenciado saqueadas gerenciarão o acesso à memória mantida pelas Alocações, não há necessidade de um desenvolvedor de aplicativos Android fazer qualquer trabalho extra.

Uma alocação conterá um ou mais [Android.Renderscripts.Elements](xref:Android.Renderscripts.Element).
Os elementos são um tipo especializado que descreve dados em cada alocação.
Os tipos de elemento da alocação de saída devem corresponder aos tipos do elemento de entrada. Ao executar, um Renderscript iterará sobre cada elemento na alocação de entrada em paralelo e escreverá os resultados na alocação de saída. Existem dois tipos de elementos:

- **tipo** &ndash; simples Conceitualmente isso é o mesmo `float` que `char`um tipo de dados C, ou a .

- **tipo** &ndash; complexo Este tipo é `struct`semelhante a um C .

O mecanismo Renderscript executará uma verificação de tempo de execução para garantir que os Elementos em cada Alocação sejam compatíveis com o que é exigido pelo kernel. Se o tipo de dados dos Elementos na Alocação não corresponder ao tipo de dados que o kernel está esperando, uma exceção será lançada.

Todos os núcleos Renderscript serão embrulhados por um tipo que é um descendente do[`Android.Renderscripts.Script`](xref:Android.Renderscripts.Script)
. A `Script` classe é usada para definir parâmetros `Allocations`para um Renderscript, definir o apropriado e executar o Renderscript. Existem `Script` duas subclasses no Android SDK:

- **`Android.Renderscripts.ScriptIntrinsic`**&ndash; Algumas das tarefas mais comuns do Renderscript são empacotadas no Android SDK e são acessíveis por uma subclasse da classe [ScriptIntrínseca.](xref:Android.Renderscripts.ScriptIntrinsic) Não há necessidade de um desenvolvedor tomar quaisquer medidas extras para usar esses scripts em seu aplicativo, pois eles já são fornecidos.

- **`ScriptC_XXXXX`**&ndash; Também conhecidos como _scripts de usuário,_ estes são scripts que são escritos por desenvolvedores e embalados no APK. Na hora da compilação, a cadeia de ferramentas do Android gerará classes de invólucro gerenciados que permitirão que os scripts sejam usados no aplicativo para Android.
  O nome dessas classes geradas é o nome do `ScriptC_`arquivo Renderscript, prefixado com . Escrever e incorporar scripts de usuário não é suportado oficialmente pelo Xamarin.Android e além do escopo deste guia.

Desses dois tipos, `StringIntrinsic` apenas o é suportado pelo Xamarin.Android. Este guia discutirá como usar scripts intrínsecos em um aplicativo Xamarin.Android.

## <a name="requirements"></a>Requisitos

Este guia é para aplicativos Xamarin.Android que visam o nível 17 ou superior da API. O uso de _scripts_ de usuário não está coberto neste guia.

A [Biblioteca de Suporte Xamarin.Android V8](https://www.nuget.org/packages/Xamarin.Android.Support.v8.RenderScript/) backports a API renderizais instrínseca para aplicativos que visam versões mais antigas do Android SDK. Adicionar este pacote a um projeto Xamarin.Android deve permitir que aplicativos que visam versões mais antigas do SDK do Android aproveitem os scripts intrínsecos.

## <a name="using-intrinsic-renderscripts-in-xamarinandroid"></a>Usando Renderscripts Intrínsecos em Xamarin.Android

Os scripts intrínsecos são uma ótima maneira de executar tarefas intensivas de computação com uma quantidade mínima de código adicional. Eles foram ajustados à mão para oferecer um desempenho ideal em uma grande seção transversal de dispositivos.
Não é incomum que um script intrínseco execute 10x mais rápido do que o código gerenciado e 2-3x vezes depois de uma implementação C personalizada. Muitos dos cenários típicos de processamento são cobertos pelos scripts intrínsecos. Esta lista dos scripts intrínsecos descreve os scripts atuais em Xamarin.Android:

- [ScriptIntrinsic3DLUT](xref:Android.Renderscripts.ScriptIntrinsic3DLUT) &ndash; converte RGB para RGBA usando uma tabela de aparência 3D. 

- [ScriptIntrínsicBLAS](https://developer.android.com/reference/android/renderscript/ScriptIntrinsicBLAS.html) &ndash; fornece APIs de renderização de alto desempenho para [BLAS](http://www.netlib.org/blas/). Os BLAS (Basic Linear Algebra Subprograms) são rotinas que fornecem blocos de construção padrão para a realização de operações básicas de vetor e matriz. 

- [ScriptIntrinsicBlend](xref:Android.Renderscripts.ScriptIntrinsicBlend) &ndash; Mistura duas alocações juntas.

- [ScriptIntrínsicBlur](xref:Android.Renderscripts.ScriptIntrinsicBlur) &ndash; aplica um desfoque gaussiano a uma alocação.

- [ScriptIntrínsicColorMatrix](xref:Android.Renderscripts.ScriptIntrinsicColorMatrix) &ndash; aplica uma matriz de cores a uma alocação (ou seja, alterar cores, ajustar a tonalidade).

- [ScriptIntrinsicConvolve3x3](xref:Android.Renderscripts.ScriptIntrinsicConvolve3x3) &ndash; aplica uma matriz de cores 3x3 a uma alocação.

- [ScriptIntrinsicConvolve5x5](xref:Android.Renderscripts.ScriptIntrinsicConvolve5x5) &ndash; aplica uma matriz de cores 5x5 a uma alocação.

- [ScriptIntrinsicHistograma](xref:Android.Renderscripts.ScriptIntrinsicHistogram) &ndash; Um filtro de histograma intrínseco.

- [ScriptIntrínsicLUT](xref:Android.Renderscripts.ScriptIntrinsicLUT) &ndash; Aplica uma tabela de aparência por canal a um buffer.

- [ScriptIntrinsicResize](xref:Android.Renderscripts.ScriptIntrinsicResize) &ndash; Script para executar o redimensionamento de uma alocação 2D.

- [ScriptIntrinsicYuvToRGB](xref:Android.Renderscripts.ScriptIntrinsicYuvToRGB) &ndash; Converte um buffer YUV para RGB.

Consulte a documentação da API para obter detalhes sobre cada um dos scripts intrínsecos.

As etapas básicas para usar o Renderscript em um aplicativo Android são descritas a seguir.

**Criar um contexto renderscript** &ndash; o[`Renderscript`](xref:Android.Renderscripts.RenderScript)
class é um invólucro gerenciado em torno do contexto Renderscript e controlará a inicialização, o gerenciamento de recursos e a limpeza. O objeto Renderscript é `RenderScript.Create` criado usando o método de fábrica, que toma um contexto Android (como uma atividade) como parâmetro. A seguinte linha de código demonstra como inicializar o contexto renderscript:

```csharp
Android.Renderscripts.RenderScript renderScript = RenderScript.Create(this);
```

**Criar alocações** &ndash; Dependendo do script intrínseco, pode ser `Allocation`necessário criar um ou dois s. O[`Android.Renderscripts.Allocation`](xref:Android.Renderscripts.Allocation)
classe tem vários métodos de fábrica para ajudar a instanciar uma alocação para um intrínseco. Como exemplo, o seguinte trecho de código demonstra como criar alocação para Bitmaps.

```csharp
Android.Graphics.Bitmap originalBitmap;
Android.Renderscripts.Allocation inputAllocation = Allocation.CreateFromBitmap(renderScript,
                                                     originalBitmap,
                                                     Allocation.MipmapControl.MipmapFull,
                                                     AllocationUsage.Script);
```

Muitas vezes, será necessário `Allocation` criar um para conter os dados de saída de um script. Este trecho a seguir mostra `Allocation.CreateTyped` como usar o ajudante `Allocation` para instanciar um segundo que o mesmo tipo do original:

```csharp
Android.Renderscripts.Allocation outputAllocation = Allocation.CreateTyped(renderScript, inputAllocation.Type);
```

**Instanciar o invólucro** &ndash; script Cada uma das classes intrínsecas `Create`do invólucro de script deve ter métodos auxiliares (tipicamente chamados) para instanciar um objeto de invólucro para esse script. O seguinte trecho de código é um exemplo `ScriptIntrinsicBlur` de como instanciar um objeto desfocado. O `Element.U8_4` método auxiliar criará um elemento que descreve um tipo de dados que é de 4 campos de `Bitmap` valores inteiros não assinados de 8 bits, adequados para manter os dados de um objeto:

```csharp
Android.Renderscripts.ScriptIntrinsicBlur blurScript = ScriptIntrinsicBlur.Create(renderScript, Element.U8_4(renderScript));
```

**Atribuição de alocação, parâmetros de configuração & executar script** &ndash; A `Script` classe fornece um `ForEach` método para realmente executar o Renderscript. Este método irá iterar sobre cada um `Element` na retenção `Allocation` dos dados de entrada. Em alguns casos, pode ser `Allocation` necessário fornecer um que segure a saída.
`ForEach`substituirá o conteúdo da alocação de saída. Para continuar com os trechos de código das etapas anteriores, este exemplo mostra como atribuir uma alocação de entrada, definir um parâmetro e, finalmente, executar o script (copiando os resultados para a alocação de saída):

```csharp
blurScript.SetInput(inputAllocation);
blurScript.SetRadius(25);  // Set a pamaeter
blurScript.ForEach(outputAllocation);
```

Você pode querer conferir a receita [Desfoque de uma Imagem com Renderscript,](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript) é um exemplo completo de como usar um script intrínseco no Xamarin.Android.

## <a name="summary"></a>Resumo

Este guia introduziu o Renderscript e como usá-lo em um aplicativo Xamarin.Android. Ele discutiu brevemente o que é renderscript e como ele funciona em um aplicativo Android. Ele descreveu alguns dos principais componentes no Renderscript e a diferença entre _scripts de usuário_ e _scripts instrínsecos_. Finalmente, este guia discutiu os passos no uso de um script intrínseco em um aplicativo Xamarin.Android.

## <a name="related-links"></a>Links relacionados

- [Espaço de nome Android.Renderscripts](xref:Android.Renderscripts)
- [Desfoque uma imagem com renderscript](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript)
- [Renderscript](https://developer.android.com/guide/topics/renderscript/compute.html)
- [Tutorial: Começando com o Renderscript](https://software.intel.com/en-us/articles/renderscript-basic-sample-for-android-os)
