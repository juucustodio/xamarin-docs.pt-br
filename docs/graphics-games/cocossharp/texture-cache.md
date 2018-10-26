---
title: Cache de textura usando CCTextureCache
description: Classe de CCTextureCache do CocosSharp fornece uma maneira padrão para organizar, cache e descarregar conteúdo. Ele é especialmente útil para grandes jogos que podem não caber inteiramente na RAM, simplificando o processo de agrupamento e descarte de texturas.
ms.prod: xamarin
ms.assetid: 1B5F3F85-9E68-42A7-B516-E90E54BA7102
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 232363d6ce1cb93499716b2c1247c48403cf6cea
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117376"
---
# <a name="texture-caching-using-cctexturecache"></a>Cache de textura usando CCTextureCache

_Classe de CCTextureCache do CocosSharp fornece uma maneira padrão para organizar, cache e descarregar conteúdo. Ele é especialmente útil para grandes jogos que podem não caber inteiramente na RAM, simplificando o processo de agrupamento e descarte de texturas._

O `CCTextureCache` classe é uma parte essencial do desenvolvimento de jogos CocosSharp. CocosSharp a maioria dos jogos de usar o `CCTextureCache` do objeto, mesmo se não, como muitos métodos de CocosSharp internamente usam explicitamente uma *compartilhado* cache de textura.

Este guia aborda o `CCTextureCache` e por isso é importante para o desenvolvimento de jogos. Especificamente, ele aborda:

 - Por que é importante de cache de textura
 - Tempo de vida de textura
 - Usando SharedTextureCache
 - Carregamento versus pré-carregamento com AddImage lento
 - Descarte as texturas


## <a name="why-texture-caching-matters"></a>Por que é importante de cache de textura

O cache de textura é uma consideração importante no desenvolvimento de jogos como o carregamento de textura é uma operação demorada e texturas exigem uma quantidade significativa de RAM em tempo de execução.

Assim como acontece com qualquer operação de arquivo, carregando texturas de disco pode ser uma operação cara. Carregamento de textura pode levar mais tempo se o arquivo que está sendo carregado requer processamento, como sendo descompactado (como é o caso para imagens png e jpg). Cache de textura pode reduzir o número de vezes que o aplicativo deve carregar os arquivos do disco.

Conforme mencionado acima, as texturas ocupam também uma grande quantidade de memória de tempo de execução. Por exemplo uma imagem de plano de fundo dimensionada para a resolução de um iPhone 6 (1344 x 750) seria ocupa 4 MB de RAM – mesmo se o arquivo PNG é somente alguns quilobytes de tamanho. O cache de textura fornece uma maneira de compartilhar as referências de textura dentro de um aplicativo e também uma maneira fácil para descarregar todo o conteúdo durante a transição entre estados diferentes de jogos.


## <a name="texture-lifespan"></a>Tempo de vida de textura

CocosSharp texturas podem ser mantidas na memória por toda a duração da execução do aplicativo ou eles podem ser de curta duração. Para minimizar a memória, uso de um aplicativo deve descartar das texturas quando não for mais necessário. Claro, isso significa que as texturas podem ser descartadas e recarregadas em um momento posterior, o que pode aumentar os tempos de carregamento ou prejudicar o desempenho durante cargas. 

Geralmente, o carregamento de textura requer um equilíbrio entre os tempos de carga e de uso de memória / desempenho de tempo de execução. Jogos que usam uma pequena quantidade de memória de textura podem manter todas as texturas na memória conforme necessário, mas talvez seja necessário descarregar texturas para liberar espaço em jogos de maiores.

O diagrama a seguir mostra um jogo simples que carrega as texturas conforme necessário e mantê-los na memória por toda a duração da execução:

![](texture-cache-images/image1.png "Este diagrama mostra um jogo simples que carrega as texturas conforme necessário e mantê-los na memória por toda a duração da execução")

As primeiras duas barras representam as texturas que são necessárias imediatamente após a execução do jogo. As seguintes três barras representam texturas para cada nível, carregados conforme necessário.

Se o jogo era grande o suficiente, ela eventualmente carregaria suficiente texturas para preencher toda a RAM fornecido pelo dispositivo e do sistema operacional. Para resolver isso, um jogo pode carregar dados de textura quando ele não for mais necessário. Por exemplo, o diagrama a seguir mostra um jogo que descarrega Level1Texture quando não for mais necessário, em seguida, ele carrega Level2Texture para o próximo nível. O resultado final é que apenas três texturas são mantidas na memória em um determinado momento: 

![](texture-cache-images/image2.png "O resultado final é que apenas três texturas são mantidas na memória a qualquer momento determinado")


O diagrama acima indica que o uso de memória de textura pode ser reduzido através do descarregamento, mas isso pode exigir tempo de carregamento adicionais, se um jogador optar por repetir um nível. Também vale a pena observar que as texturas UITexture e MainCharacter são carregadas e descarregadas nunca. Isso significa que essas texturas são necessárias em todos os níveis, para que eles sempre são mantidos na memória. 


## <a name="using-sharedtexturecache"></a>Usando SharedTextureCache

CocosSharp automaticamente armazena em cache as texturas quando carregá-los por meio de `CCSprite` construtor. Por exemplo, o código a seguir só cria uma instância de textura:


```csharp
for (int i = 0; i < 100; i++)
{
    CCSprite starSprite = new CCSprite ("star.png");
    starSprite.PositionX = i * 32;
    this.AddChild (starSprite);
} 
```

CocosSharp armazena automaticamente o `star.png` textura para evitar a alternativa cara de criar vários idênticos `CCTexture2D` instâncias. Isso é conseguido `AddImage` que está sendo chamado em um compartilhado `CCTextureCache` da instância, especificamente `CCTextureCache.SharedTextureCache.Shared`. Para entender como o `SharedTextureCache` é usado, pode examinar o código a seguir que é funcionalmente idêntico ao chamar o `CCSprite` construtor com um parâmetro de cadeia de caracteres:


```

CCSprite starSprite = new CCSprite ();
 starSprite.Texture = CCTextureCache.SharedTextureCache.AddImage ("star.png");
```

`AddImage` verifica se o arquivo de argumento (nesse caso, `star.png`) já foi carregado. Nesse caso, a instância armazenada em cache é retornada. Se não, em seguida, ele é carregado do sistema de arquivos e uma referência para a textura é armazenada internamente para subsequentes `AddImage` chamadas. Em outras palavras a `star.png` imagem é carregada apenas uma vez, e as chamadas subsequentes não exigem nenhum acesso ao disco adicionais ou memória adicional de textura.


## <a name="lazy-loading-vs-pre-loading-with-addimage"></a>Carregamento versus pré-carregamento com AddImage lento

`AddImage` permite que o código a ser gravado o mesmo se a textura solicitada já está carregada ou não. Isso significa que o conteúdo não será carregada até que ela é necessária; No entanto, isso também pode causar problemas de desempenho em tempo de execução devido ao carregamento de conteúdo imprevisível.

Por exemplo, considere um jogo onde arma do player pode ser atualizada. Quando atualizado, os projéteis e armas visivelmente mudará, resultando em novas texturas que está sendo usadas. Se o conteúdo é o carregamento lento, em seguida, as texturas associadas armas atualizadas não serão carregadas inicialmente, mas em um momento posterior, quando o jogador adquire as atualizações. 

Este carregamento médio de jogo pode fazer com que o jogo para *pop-up*, que é um congelamento curto, mas perceptível em execução. Para evitar isso, o código pode prever quais texturas podem ser necessários com antecedência e pré-carregá-los. Por exemplo, a seguir pode ser usados para pré-carregar texturas:


```csharp
void PreLoadImages()
{
    var cache = CCTextureCache.SharedTextureCache;

    cache.AddImage ("powerup1.png");
    cache.AddImage ("powerup2.png");
    cache.AddImage ("powerup3.png");

    cache.AddImage ("enemy1.png");
    cache.AddImage ("enemy2.png");
    cache.AddImage ("enemy3.png");

    // pre-load any additional content here to 
    // prevent pops at runtime
} 
```

Esse pré-carregamento pode resultar em desperdício de memória e pode aumentar o tempo de inicialização. Por exemplo, o player, na verdade, nunca poderá obter um geradores de energia representado pelo `powerup3.png` de textura, para que ele seja carregado desnecessariamente. É claro, isso pode ser um custo necessário pagar evitar uma potencial pop em jogo, portanto, é geralmente melhor ao conteúdo pré-carregamento se ela caiba na RAM.


## <a name="disposing-textures"></a>Descarte as texturas

Se um jogo não exige mais memória de textura que está disponível no dispositivo especificações mínimo texturas não precisam ser descartados. Por outro lado, jogos maiores, talvez seja necessário liberar a memória de textura para liberar espaço para o novo conteúdo. Por exemplo, um jogo pode usar uma grande quantidade de memória que armazena as texturas para um ambiente. Se o ambiente é usado somente em um nível específico, em seguida, ele deve ser descarregado quando termina o nível.


### <a name="disposing-a-single-texture"></a>Descartando uma textura única

Remover uma textura única primeiro requer uma chamada a `Dispose` método e, em seguida, a remoção manual do `CCTextureCache`.

O exemplo a seguir mostra como remover completamente um sprite de plano de fundo, juntamente com sua textura de:


```csharp
void DisposeBackground()
{
    // Assuming this is called from a CCLayer:
    this.RemoveChild (backgroundSprite);

    CCTextureCache.SharedTextureCache.RemoveTexture (backgroundsprite.Texture);

    backgroundSprite.Texture.Dispose ();
} 
```

Descartar diretamente as texturas pode ser eficiente ao lidar com um número pequeno de texturas, mas isso pode se tornar propenso a erro ao lidar com grandes conjuntos de textura.

As texturas podem ser agrupadas em personalizado (não compartilhada) `CCTextureCache` instâncias para simplificar a limpeza de textura.

Por exemplo, considere um exemplo em que o conteúdo esteja pré-carregado usando um nível de determinado `CCTextureCache` instância. O `CCTextureCache` instância pode ser definida na classe que define o nível (que pode ser um `CCLayer` ou `CCScene`):


```csharp
CCTextureCache levelTextures; 
```

O `levelTextures` instância, em seguida, pode ser usada para pré-carregar as texturas específicas de nível: 


```

void PreloadLevelTextures(CCApplication application)
{
    levelTextures = new CCTextureCache (application);

    levelTextures.AddImage ("Background.png");
    levelTextures.AddImage ("Foreground.png");
    levelTextures.AddImage ("Enemy1.png");
    levelTextures.AddImage ("Enemy2.png");
    levelTextures.AddImage ("Enemy3.png");

    levelTextures.AddImage ("Powerups.png");
    levelTextures.AddImage ("Particles.png");
} 
```

Finalmente quando termina o nível, as texturas podem ser descartadas tudo ao mesmo tempo por meio de `CCTextureCache`:


```csharp
void EndLevel()
{
    levelTextures.Dispose ();
    // Perform any other end-level cleanup
} 
```

O método Dispose descartará todas as texturas internas, eliminando a memória usada por essas texturas. Combinando `CCTextureCache.Shared` com um específicos do modo de jogo ou nível `CCTextureCache` instância resulta em algumas texturas persistência por meio de todo o jogo e alguns que está sendo descarregado como níveis de encerrar, semelhante ao diagrama apresentado no início deste guia: 

![](texture-cache-images/image2.png "Combinando CCTextureCache.Shared com um nível ou jogos específicos do modo CCTextureCache instância resulta em algumas texturas persistência por meio de todo o jogo e alguns que está sendo descarregado como níveis de encerrar, semelhante ao apresentado no início deste guia diagrama")




## <a name="summary"></a>Resumo

Este guia mostra como usar o `CCTextureCache` classe para o desempenho de tempo de execução e o uso de memória de saldo. `CCTexturCache.SharedTextureCache` pode ser explicitamente ou implicitamente, usada para carregar e armazenar em cache as texturas durante a vida útil do aplicativo, enquanto `CCTextureCache` instâncias podem ser usadas para descarregar texturas para reduzir o uso de memória.

## <a name="related-links"></a>Links relacionados

- [https://github.com/mono/CocosSharp](https://github.com/mono/CocosSharp)
- [/api/type/CocosSharp.CCTextureCache/](https://developer.xamarin.com/api/type/CocosSharp.CCTextureCache/)
