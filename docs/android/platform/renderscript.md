---
title: Uma introdução ao renderscript
description: Este guia apresenta o renderscript e explica como usar as APIs intrínsecas do renderscript em um aplicativo Xamarin. Android que tem como destino o nível de API 17 ou superior.
ms.prod: xamarin
ms.assetid: 378793C7-5E3E-40E6-ABEE-BEAEF64E6A47
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 884b69b0cdecf4f979cec314b6440974c5bac97d
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73019805"
---
# <a name="an-introduction-to-renderscript"></a>Uma introdução ao renderscript

_Este guia apresenta o renderscript e explica como usar as APIs intrínsecas do renderscript em um aplicativo Xamarin. Android que tem como destino o nível de API 17 ou superior._

## <a name="overview"></a>Visão geral

Renderscript é uma estrutura de programação criada pelo Google com a finalidade de melhorar o desempenho de aplicativos Android que exigem recursos computacionais extensivos. É uma API de nível baixo e de alto desempenho baseada em [C99](https://en.wikipedia.org/wiki/C99). Como é uma API de nível baixo que será executada em CPUs, GPUs ou DSPs, o renderscript é adequado para aplicativos Android que talvez precisem executar qualquer um dos seguintes:

- Gráficos
- Processamento de imagens
- Criptografia
- Processamento de sinais
- Rotinas matemáticas

O renderscript usará `clang` e compilará os scripts para o código de byte LLVM, que é agrupado no APK. Quando o aplicativo é executado pela primeira vez, o código de byte LLVM será compilado no código do computador para os processadores no dispositivo. Essa arquitetura permite que um aplicativo Android Explore as vantagens do código do computador sem que os próprios desenvolvedores precisem escrevê-lo para cada processador no próprio dispositivo.

Há dois componentes para uma rotina renderscript:

1. **O tempo de execução do Renderscript** &ndash; as APIs nativas que são responsáveis por executar o renderscript. Isso inclui qualquer Renderscripts gravado para o aplicativo.

2. **Os wrappers gerenciados da estrutura do Android** &ndash; classes gerenciadas que permitem que um aplicativo Android controle e interaja com o tempo de execução e os scripts do renderscript. Além das classes fornecidas pelo Framework para controlar o tempo de execução do renderscript, o ferramentas do Android examinará o código-fonte renderscript e gerará classes wrapper gerenciadas para uso pelo aplicativo do Android.

O diagrama a seguir ilustra como esses componentes se relacionam:

![Diagrama ilustrando como a estrutura do Android interage com o tempo de execução do renderscript](renderscript-images/renderscript-01.png)

Há três conceitos importantes para usar o Renderscripts em um aplicativo Android:

1. **Um contexto** &ndash; uma API gerenciada fornecida pelo SDK do Android que aloca recursos para renderscript e permite que o aplicativo Android passe e receba dados do renderscript.

2. **Um _kernel de computação_**  &ndash; também conhecido como _kernel ou kernel_ _raiz_ , essa é uma rotina que faz o trabalho. O kernel é muito semelhante a uma função C; é uma rotina paralelizáveis que será executada em todos os dados na memória alocada.

3. A **memória alocada** &ndash; dados são passados de e para um kernel por meio de uma _[alocação](xref:Android.Renderscripts.Allocation)_ . Um kernel pode ter uma entrada e/ou uma alocação de saída.

O namespace [Android. Renderscripts](xref:Android.Renderscripts) contém as classes para interagir com o tempo de execução do renderscript. Em particular, a classe [`Renderscript`](xref:Android.Renderscripts.RenderScript) gerenciará o ciclo de vida e os recursos do mecanismo renderscript. O aplicativo do Android deve inicializar um ou mais [`Android.Renderscripts.Allocation`](xref:Android.Renderscripts.Allocation)
banco de dados. Uma alocação é uma API gerenciada que é responsável pela alocação e pelo acesso à memória que é compartilhada entre o aplicativo Android e o tempo de execução do renderscript. Normalmente, uma alocação é criada para entrada e, opcionalmente, outra alocação é criada para manter a saída do kernel. O mecanismo de tempo de execução do renderscript e as classes de wrapper gerenciados associadas gerenciarão o acesso à memória mantida pelas alocações, não haverá necessidade de um desenvolvedor de aplicativos Android para realizar qualquer trabalho extra.

Uma alocação conterá um ou mais [Android. Renderscripts. Elements](xref:Android.Renderscripts.Element).
Os elementos são um tipo especializado que descreve os dados em cada alocação.
Os tipos de elemento da alocação de saída devem corresponder aos tipos do elemento de entrada. Durante a execução, um renderscript será iterado em cada elemento na alocação de entrada em paralelo e gravará os resultados na alocação de saída. Há dois tipos de elementos:

- o **tipo simples** &ndash; conceitualmente é o mesmo que um tipo de dados C, `float` ou um `char`.

- **tipo complexo** &ndash; esse tipo é semelhante a um `struct`de C.

O mecanismo renderscript executará uma verificação de tempo de execução para garantir que os elementos em cada alocação sejam compatíveis com o que é exigido pelo kernel. Se o tipo de dados dos elementos na alocação não corresponder ao tipo de dados esperado pelo kernel, uma exceção será lançada.

Todos os kernels renderscript serão encapsulados por um tipo que é um descendente do [`Android.Renderscripts.Script`](xref:Android.Renderscripts.Script)
. A classe `Script` é usada para definir parâmetros para um renderscript, definir o `Allocations`apropriado e executar o renderscript. Há duas subclasses `Script` no SDK do Android:

- **`Android.Renderscripts.ScriptIntrinsic`** &ndash; algumas das tarefas renderscript mais comuns são agrupadas no SDK do Android e podem ser acessadas por uma subclasse da classe [ScriptIntrinsic](xref:Android.Renderscripts.ScriptIntrinsic) . Não há necessidade de um desenvolvedor realizar etapas adicionais para usar esses scripts em seu aplicativo, pois eles já foram fornecidos.

- **`ScriptC_XXXXX`** &ndash; também conhecidos como _scripts de usuário_, esses são scripts que são escritos por desenvolvedores e empacotados no apk. No momento da compilação, o ferramentas do Android gerará classes wrapper gerenciadas que permitirão que os scripts sejam usados no aplicativo do Android.
  O nome dessas classes geradas é o nome do arquivo renderscript, prefixado com `ScriptC_`. Não há suporte oficialmente para a gravação e a incorporação de scripts de usuário pelo Xamarin. Android e além do escopo deste guia.

Desses dois tipos, somente o `StringIntrinsic` é suportado pelo Xamarin. Android. Este guia explicará como usar scripts intrínsecos em um aplicativo Xamarin. Android.

## <a name="requirements"></a>Requisitos

Este guia é para aplicativos Xamarin. Android que visam a API de nível 17 ou superior. O uso de _scripts de usuário_ não é abordado neste guia.

O [Xamarin. Android V8 dá suporte à biblioteca](https://www.nuget.org/packages/Xamarin.Android.Support.v8.RenderScript/) de back ports das APIs do instrinsic renderscript para aplicativos direcionados a versões mais antigas do SDK do Android. Adicionar este pacote a um projeto Xamarin. Android deve permitir aplicativos direcionados a versões mais antigas do SDK do Android para aproveitar os scripts intrínsecos.

## <a name="using-intrinsic-renderscripts-in-xamarinandroid"></a>Usando Renderscripts intrínsecos no Xamarin. Android

Os scripts intrínsecos são uma ótima maneira de executar tarefas de computação intensivas com uma quantidade mínima de código adicional. Eles foram ajustados para oferecer um desempenho ideal em uma grande seção cruzada de dispositivos.
Não é incomum um script intrínseco ser executado 10 vezes mais rápido do que o código gerenciado e 2 vezes depois de uma implementação personalizada do C. Muitos dos cenários de processamento típicos são cobertos pelos scripts intrínsecos. Esta lista de scripts intrínsecos descreve os scripts atuais no Xamarin. Android:

- [ScriptIntrinsic3DLUT](xref:Android.Renderscripts.ScriptIntrinsic3DLUT) &ndash; converte RGB em RGBA usando uma tabela de pesquisa 3D. 

- [ScriptIntrinsicBLAS](https://developer.android.com/reference/android/renderscript/ScriptIntrinsicBLAS.html) &ndash; Provideshigh desempenho renderscript APIs [Blas](http://www.netlib.org/blas/). O BLAS (subprogramas de algebra linear básico) são rotinas que fornecem blocos de construção padrão para executar operações básicas de vetor e matriz. 

- [ScriptIntrinsicBlend](xref:Android.Renderscripts.ScriptIntrinsicBlend) &ndash; combina duas alocações juntas.

- [ScriptIntrinsicBlur](xref:Android.Renderscripts.ScriptIntrinsicBlur) &ndash; aplica um desfoque gaussiano a uma alocação.

- [ScriptIntrinsicColorMatrix](xref:Android.Renderscripts.ScriptIntrinsicColorMatrix) &ndash; aplica uma matriz de cores a uma alocação (ou seja, alterar as cores, ajustar o matiz).

- [ScriptIntrinsicConvolve3x3](xref:Android.Renderscripts.ScriptIntrinsicConvolve3x3) &ndash; aplica uma matriz de cores 3x3 a uma alocação.

- [ScriptIntrinsicConvolve5x5](xref:Android.Renderscripts.ScriptIntrinsicConvolve5x5) &ndash; aplica uma matriz de cores 5x5 a uma alocação.

- [ScriptIntrinsicHistogram](xref:Android.Renderscripts.ScriptIntrinsicHistogram) &ndash; um filtro de histograma intrínseco.

- [ScriptIntrinsicLUT](xref:Android.Renderscripts.ScriptIntrinsicLUT) &ndash; aplica uma tabela de pesquisa por canal a um buffer.

- [ScriptIntrinsicResize](xref:Android.Renderscripts.ScriptIntrinsicResize) &ndash; script para executar o redimensionamento de uma alocação 2D.

- [ScriptIntrinsicYuvToRGB](xref:Android.Renderscripts.ScriptIntrinsicYuvToRGB) &ndash; converte um buffer YUV em RGB.

Consulte a documentação da API para obter detalhes sobre cada um dos scripts intrínsecos.

As etapas básicas para usar o renderscript em um aplicativo Android são descritas a seguir.

**Criar um contexto Renderscript** &ndash; o [`Renderscript`](xref:Android.Renderscripts.RenderScript)
a classe é um wrapper gerenciado em volta do contexto renderscript e controlará a inicialização, o gerenciamento de recursos e a limpeza. O objeto renderscript é criado usando o método de fábrica `RenderScript.Create`, que usa um contexto do Android (como uma atividade) como um parâmetro. A linha de código a seguir demonstra como inicializar o contexto renderscript:

```csharp
Android.Renderscripts.RenderScript renderScript = RenderScript.Create(this);
```

**Criar alocações** &ndash; dependendo do script intrínseco, pode ser necessário criar uma ou duas `Allocation`s. O [`Android.Renderscripts.Allocation`](xref:Android.Renderscripts.Allocation)
a classe tem vários métodos de fábrica para ajudar na instanciação de uma alocação para um intrínseco. Por exemplo, o trecho de código a seguir demonstra como criar a alocação para bitmaps.

```csharp
Android.Graphics.Bitmap originalBitmap;
Android.Renderscripts.Allocation inputAllocation = Allocation.CreateFromBitmap(renderScript,
                                                     originalBitmap,
                                                     Allocation.MipmapControl.MipmapFull,
                                                     AllocationUsage.Script);
```

Geralmente, será necessário criar um `Allocation` para manter os dados de saída de um script. Este trecho de código a seguir mostra como usar o `Allocation.CreateTyped` auxiliar para criar uma instância de um segundo `Allocation` que o mesmo tipo do original:

```csharp
Android.Renderscripts.Allocation outputAllocation = Allocation.CreateTyped(renderScript, inputAllocation.Type);
```

**Instancie o wrapper de script** &ndash; cada uma das classes de wrapper de script intrínsecas deve ter métodos auxiliares (normalmente chamados de `Create`) para instanciar um objeto wrapper para esse script. O trecho de código a seguir é um exemplo de como criar uma instância de um objeto de desfoque `ScriptIntrinsicBlur`. O método auxiliar `Element.U8_4` criará um elemento que descreve um tipo de dados que é de quatro campos de valores inteiros de 8 bits sem sinal, adequados para manter os dados de um objeto de `Bitmap`:

```csharp
Android.Renderscripts.ScriptIntrinsicBlur blurScript = ScriptIntrinsicBlur.Create(renderScript, Element.U8_4(renderScript));
```

**Atribuir alocação (ões), definir parâmetros, & executar Script** &ndash; a classe `Script` fornece um método `ForEach` para realmente executar o renderscript. Esse método fará iteração em cada `Element` no `Allocation` que contém os dados de entrada. Em alguns casos, pode ser necessário fornecer um `Allocation` que contém a saída.
`ForEach` substituirá o conteúdo da alocação de saída. Para continuar com os trechos de código das etapas anteriores, este exemplo mostra como atribuir uma alocação de entrada, definir um parâmetro e, finalmente, executar o script (copiando os resultados para a alocação de saída):

```csharp
blurScript.SetInput(inputAllocation);
blurScript.SetRadius(25);  // Set a pamaeter
blurScript.ForEach(outputAllocation);
```

Talvez você queira conferir o [Desfoque de uma imagem com a receita renderscript](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript) , é um exemplo completo de como usar um script intrínseco no Xamarin. Android.

## <a name="summary"></a>Resumo

Este guia introduziu o renderscript e como usá-lo em um aplicativo Xamarin. Android. Ele discutiu brevemente o que é o renderscript e como ele funciona em um aplicativo Android. Ele descreveu alguns dos principais componentes em renderscript e a diferença entre _scripts de usuário_ e _scripts instrinsic_. Por fim, este guia abordou as etapas de uso de um script intrínseco em um aplicativo Xamarin. Android.

## <a name="related-links"></a>Links relacionados

- [Namespace Android. Renderscripts](xref:Android.Renderscripts)
- [Desfocar uma imagem com renderscript](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript)
- [Renderscript](https://developer.android.com/guide/topics/renderscript/compute.html)
- [Tutorial: Introdução com renderscript](https://software.intel.com/en-us/articles/renderscript-basic-sample-for-android-os)
