---
title: Cache de textura usando CCTextureCache
description: Classe de CCTextureCache do CocosSharp fornece uma maneira padrão para organizar, cache e descarregar conteúdo. É especialmente útil para grandes jogos que não caber inteiramente na RAM, simplificando o processo de agrupamento e descarte de texturas.
ms.prod: xamarin
ms.assetid: 1B5F3F85-9E68-42A7-B516-E90E54BA7102
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: c217d8a935ae971aab472b05968c0251366362b2
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783682"
---
# <a name="texture-caching-using-cctexturecache"></a>Cache de textura usando CCTextureCache

_Classe de CCTextureCache do CocosSharp fornece uma maneira padrão para organizar, cache e descarregar conteúdo. É especialmente útil para grandes jogos que não caber inteiramente na RAM, simplificando o processo de agrupamento e descarte de texturas._

O `CCTextureCache` classe é uma parte essencial do desenvolvimento de jogos CocosSharp. CocosSharp a maioria dos jogos use o `CCTextureCache` do objeto, mesmo se não, como muitos métodos de CocosSharp internamente usam explicitamente um *compartilhado* cache de textura.

Este guia aborda o `CCTextureCache` e por que é importante para o desenvolvimento de jogos. Especificamente, ele abrange:

 - Por questões de cache de textura
 - Tempo de vida de textura
 - Usando SharedTextureCache
 - Versus pré-carregar AddImage de carregamento lento
 - Descartando texturas


## <a name="why-texture-caching-matters"></a>Por questões de cache de textura

O cache de textura é uma consideração importante no desenvolvimento de jogos, como o carregamento de textura é uma operação demorada e texturas exigem uma quantidade significativa de RAM em tempo de execução.

Assim como acontece com qualquer operação de arquivo, carregar texturas do disco pode ser uma operação dispendiosa. Carregamento de textura pode levar mais tempo se o arquivo que está sendo carregado requer processamento, como sendo descompactado (como é o caso de imagens png e jpg). Cache de textura pode reduzir o número de vezes que o aplicativo deve carregar arquivos do disco.

Conforme mencionado acima, texturas também ocupam uma grande quantidade de memória de tempo de execução. Por exemplo uma imagem de plano de fundo dimensionada para a resolução de um iPhone 6 (1344 x 750) seria ocupam 4 MB de RAM – mesmo se o arquivo PNG somente alguns quilobytes de tamanho. O cache de textura fornece uma maneira de compartilhar referências de textura dentro de um aplicativo e também uma maneira fácil para descarregar todo o conteúdo ao realizar a transição entre estados diferentes de jogos.


## <a name="texture-lifespan"></a>Tempo de vida de textura

Texturas CocosSharp podem ser mantidas na memória por toda a duração da execução do aplicativo, ou podem estar vida curta. Para minimizar a memória deve descartar o uso de um aplicativo de texturas quando forem mais necessários. Obviamente, isso significa que texturas podem ser descartadas e carregadas novamente mais tarde, que pode aumentar o tempo de carregamento ou prejudicar o desempenho durante cargas. 

Carregamento de textura geralmente requer um equilíbrio entre horários de carga e de uso de memória / desempenho de tempo de execução. Jogos que usam uma pequena quantidade de memória de textura podem manter todas as texturas na memória conforme necessário, mas jogos maior talvez precise descarregar texturas para liberar espaço.

O diagrama a seguir mostra um jogo simple que carrega texturas conforme necessário e os mantém em memória por toda a duração da execução:

![](texture-cache-images/image1.png "Este diagrama mostra um jogo simple que carrega texturas conforme necessário e os mantém em memória por toda a duração da execução")

As primeiras duas barras representam texturas que são necessários imediatamente após a execução do jogo. As seguintes três barras representam texturas para cada nível, carregados conforme necessário.

Se o jogo era grande o suficiente, ele carrega eventualmente suficiente texturas para preencher toda a RAM fornecida pelo dispositivo e sistema operacional. Para resolver isso, um jogo pode carregar dados de textura quando ele não for mais necessário. Por exemplo, o diagrama a seguir mostra um jogo que descarrega Level1Texture quando não for mais necessário, em seguida, carrega Level2Texture para o próximo nível. O resultado final é que apenas três texturas são mantidas na memória a qualquer momento: 

![](texture-cache-images/image2.png "O resultado final é que apenas três texturas são mantidas na memória a qualquer momento")


O diagrama acima indica que o uso de memória de textura pode ser reduzido através do descarregamento, mas isso pode exigir que os tempos de carregamento adicional se um jogador optar por repetir um nível. Também vale a pena observar que as texturas UITexture e MainCharacter são carregadas e descarregadas nunca. Isso significa que esses texturas são necessários em todos os níveis, para que elas sempre são mantidas na memória. 


## <a name="using-sharedtexturecache"></a>Usando SharedTextureCache

CocosSharp automaticamente armazena em cache texturas ao carregá-los por meio de `CCSprite` construtor. Por exemplo, o código a seguir só cria uma instância de textura:


```csharp
for (int i = 0; i < 100; i++)
{
    CCSprite starSprite = new CCSprite ("star.png");
    starSprite.PositionX = i * 32;
    this.AddChild (starSprite);
} 
```

CocosSharp automaticamente armazena em cache o `star.png` textura para evitar a possibilidade de cara de criar vários idênticos `CCTexture2D` instâncias. Isso é feito pela `AddImage` que está sendo chamado em um compartilhado `CCTextureCache` instância especificamente `CCTextureCache.SharedTextureCache.Shared`. Para entender como o `SharedTextureCache` usado podemos ver o seguinte código que é funcionalmente idêntico ao chamar o `CCSprite` construtor com um parâmetro de cadeia de caracteres:


```

CCSprite starSprite = new CCSprite ();
 starSprite.Texture = CCTextureCache.SharedTextureCache.AddImage ("star.png");
```

`AddImage` verifica se o arquivo de argumento (neste caso `star.png`) já foi carregado. Nesse caso, a instância de cache é retornada. Se não, em seguida, ele é carregado do sistema de arquivos e uma referência para a textura é armazenada internamente para subsequentes `AddImage` chamadas. Em outras palavras a `star.png` imagem só é carregada uma vez e chamadas subsequentes não exigem nenhum acesso ao disco adicionais ou memória adicional de textura.


## <a name="lazy-loading-vs-pre-loading-with-addimage"></a>Versus pré-carregar AddImage de carregamento lento

`AddImage` permite que o código a ser gravado o mesmo se a textura solicitada já está carregada ou não. Isso significa que o conteúdo não será carregada até que ela for necessária; No entanto, isso também pode causar problemas de desempenho em tempo de execução devido a carregamento de conteúdo imprevisível.

Por exemplo, considere um jogo onde arma do player pode ser atualizada. Quando atualizado, o arma e projéteis visivelmente mudará, resultando em novas texturas que está sendo usadas. Se o conteúdo for carregado lento, em seguida, as texturas associadas armas atualizadas não serão carregadas inicialmente, mas mais tarde quando o player adquire as atualizações. 

Esse carregamento médio jogos pode fazer com que o jogo para *pop*, que é um congelamento curto mas perceptível em execução. Para evitar isso, o código pode prever qual texturas podem ser necessários com antecedência e carregá-los previamente. Por exemplo, o seguinte pode ser usado para pré-carregar texturas:


```csharp
void PreLoadImages()
{
    var cache = CCTextureCache.SharedTextureCache;

    cache.AddImage ("powerup1.png");
    cache.AddImage ("powerup2.png");
    cache.AddImage ("powerup3.png");

    cache.AddImage ("enemy1.png");
    cache.AddImage ("enemy2.png");
    cache.AddImage ("enemy3.png");

    // pre-load any additional content here to 
    // prevent pops at runtime
} 
```

Este pré-carregamento pode resultar em perda memória e pode aumentar o tempo de inicialização. Por exemplo, o player, na verdade, nunca poderá obter uma inicialização representada pelo `powerup3.png` de textura, para que ele seja carregado desnecessariamente. Obviamente, isso pode ser um custo necessário para pagar para evitar uma potencial pop em jogos, portanto, é melhor conteúdo pré-carregamento se ele se ajuste na memória RAM.


## <a name="disposing-textures"></a>Descartando texturas

Se um jogo não exige mais memória de textura que está disponível no dispositivo especificações mínimo texturas não precisa ser descartado. Por outro lado, jogos maior podem ser necessário liberar a memória de textura para liberar espaço para o novo conteúdo. Por exemplo, um jogo pode usar uma grande quantidade de memória texturas para um ambiente de armazenamento. Se o ambiente é usado somente em um nível específico, em seguida, ele deve ser descarregado quando o nível de termina.


### <a name="disposing-a-single-texture"></a>Descartando uma textura única

Primeiro a remoção de uma textura único requer a chamada a `Dispose` método e, em seguida, remoção manual do `CCTextureCache`.

O exemplo a seguir mostra como remover completamente uma entidade de gráfica do plano de fundo junto com sua textura:


```csharp
void DisposeBackground()
{
    // Assuming this is called from a CCLayer:
    this.RemoveChild (backgroundSprite);

    CCTextureCache.SharedTextureCache.RemoveTexture (backgroundsprite.Texture);

    backgroundSprite.Texture.Dispose ();
} 
```

Descartar diretamente texturas pode ser eficaz ao lidar com um número pequeno de texturas, mas isso pode se tornar propensas a erros ao lidar com grandes conjuntos de textura.

Texturas podem ser agrupadas em personalizado (não compartilhada) `CCTextureCache` instâncias para simplificar a limpeza de textura.

Por exemplo, considere um exemplo em que o conteúdo esteja pré-carregado usando um determinado nível `CCTextureCache` instância. O `CCTextureCache` instância pode ser definida na classe que define o nível (que pode ser um `CCLayer` ou `CCScene`):


```csharp
CCTextureCache levelTextures; 
```

O `levelTextures` instância pode ser usada para pré-carregar as texturas nível específico: 


```

void PreloadLevelTextures(CCApplication application)
{
    levelTextures = new CCTextureCache (application);

    levelTextures.AddImage ("Background.png");
    levelTextures.AddImage ("Foreground.png");
    levelTextures.AddImage ("Enemy1.png");
    levelTextures.AddImage ("Enemy2.png");
    levelTextures.AddImage ("Enemy3.png");

    levelTextures.AddImage ("Powerups.png");
    levelTextures.AddImage ("Particles.png");
} 
```

Por fim quando o nível termina, as texturas podem ser descartadas todos de uma vez por meio de `CCTextureCache`:


```csharp
void EndLevel()
{
    levelTextures.Dispose ();
    // Perform any other end-level cleanup
} 
```

O método Dispose será descartar todas as texturas internas, limpar a memória usada por essas texturas. Combinando `CCTextureCache.Shared` com uma nível ou jogo modo específica `CCTextureCache` instância resulta em algumas texturas persistentes por meio de todo o jogo e alguns que está sendo descarregado como níveis terminar, semelhante ao diagrama apresentado no começo deste guia: 

![](texture-cache-images/image2.png "Combinando CCTextureCache.Shared com um nível ou jogo específicos do modo CCTextureCache instância resulta em algumas texturas persistentes por meio de todo o jogo e alguns que está sendo descarregado como níveis terminar, semelhante ao diagrama apresentado no começo deste guia")




## <a name="summary"></a>Resumo

Este guia mostra como usar o `CCTextureCache` classe para desempenho de tempo de execução e o uso de memória saldo. `CCTexturCache.SharedTextureCache` pode ser explicitamente ou implicitamente, usada para carregar e armazenar em cache texturas durante a vida útil do aplicativo, enquanto `CCTextureCache` instâncias podem ser usadas para descarregar texturas para reduzir o uso de memória.

## <a name="related-links"></a>Links relacionados

- [https://github.com/mono/CocosSharp](https://github.com/mono/CocosSharp)
- [/api/type/CocosSharp.CCTextureCache/](https://developer.xamarin.com/api/type/CocosSharp.CCTextureCache/)
