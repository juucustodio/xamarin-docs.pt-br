---
title: Cores de largura
description: Este artigo aborda as cores de largura e como ele pode ser usado em um aplicativo xamarin ou Xamarin.Mac.
ms.topic: article
ms.prod: xamarin
ms.assetid: 576E978A-F182-489A-83E4-D8CDC6890B24
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 34f2e249c5474997b2e2e179285f455c425739cf
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2018
---
# <a name="wide-color"></a>Cores de largura

_Este artigo aborda as cores de largura e como ele pode ser usado em um aplicativo xamarin ou Xamarin.Mac._

iOS 10 e macOS Serra aprimora o suporte para formatos de pixel do intervalo estendido e espaços de toda a gama de cores em todo o sistema, incluindo estruturas, como gráficos de núcleo, imagem principal, sistema operacional e AVFoundation. Suporte para dispositivos com telas de cores de largura é facilitado mais fornecendo esse comportamento em toda a pilha do gráfico inteiro.

## <a name="about-wide-color"></a>Sobre cores de largura

Como mencionado acima, o iOS 10 e macOS Serra aprimora o suporte para formatos de pixel do intervalo estendido e espaços de toda a gama de cores do sistema, incluindo estruturas, como gráficos de núcleo, imagem principal, sistema operacional e AVFoundation. Suporte para dispositivos com telas de cores de largura é facilitado mais fornecendo esse comportamento em toda a pilha do gráfico inteiro.

No 90 Apple criado ColorSync para lidar com a cor de processamento no Mac. Eles também ajudaram a encontrar o internacional cor Consortium ICC () para criar e promover um conjunto de padrões para lidar com cores no hardware do computador. Trabalho da Apple com o ICC foi incluído no ColorSync e foi compilado no núcleo dos X (agora chamado macOS).

Apple também está à frente da tecnologia de exibição com hardware, como a exibição de Retina, o novo P3 exibir e exibir P3 cor espaço (lançado no iMac no 2015) e o TrueTone exibe na profissionais iPad, iPhone 7 e iPhone 7 Plus.

Com cores de largura no macOS Serra e iOS 10, Apple está mudando a maneira macOS e iOS lidar com cores para tirar proveito dessas novas tecnologias de exibição.

## <a name="core-color-concepts"></a>Principais conceitos de cor

Os seguintes conceitos de cor core precisam ser abordadas antes de fazer uma análise mais profunda cor macOS e iOS:

### <a name="color-space"></a>Espaço de cor

Um espaço de cor é um ambiente no qual cores podem ser representadas e comparadas. Pode ser um espaço de uma a quatro dimensional que é definido pela intensidade de seus componentes de cor. 

[![](wide-color-images/color00.png "Um espaço de cor")](wide-color-images/color00.png#lightbox)

### <a name="color-channels"></a>Canais de cores

Os componentes de cor também podem ser chamados como canais de cores. Algumas representações familiares seria RGB espaços, cinza espaços, espaços CMYK ou espaços independente de dispositivo. 

[![](wide-color-images/color02.png "Os componentes de cor também podem ser chamados como canais de cores")](wide-color-images/color02.png#lightbox)

### <a name="color-primaries"></a>Cores primárias

Cores primárias fornecem o sistema de coordenadas que é usado para comparar e cores de computação. Cores primárias geralmente se encaixam mais intensa versão de determinada cor que pode ser gerada no canal de cor.

[![](wide-color-images/color01.png "Cores primárias fornecem o sistema de coordenadas que é usado para comparar e cores de computação")](wide-color-images/color01.png#lightbox)

No caso do espaço de cor RGB representada acima, as cores primárias são onde o `1.0` coordenadas são ancoradas (como `[1.0, 0.0, 0.0]` para vermelho).

### <a name="color-gamut"></a>Gama de cores

Gama de cores se refere a todas as cores que podem ser definidas como uma combinação dos canais de cores individuais dentro de um espaço de cores oferecem.

[![](wide-color-images/color03.png "Exemplo de gama de cores")](wide-color-images/color03.png#lightbox)

## <a name="what-is-wide-color"></a>O que é a cor grande

Antes que abrangem o tópico de cores de largura, deveria uma discussão sobre o padrão para cor, o espaço de cor de RGB (sRGB) padrão do setor atual. É o espaço de cor mais amplamente usado em computação hoje e é o espaço de cor padrão para iOS e macOS.

O espaço de cor sRGB tem as seguintes propriedades:

- Ele se baseia no padrão BT.709 ITU-R.
- Ele tem uma gama aproximada de 2.2.
- Representa as condições de iluminação típica (D65).

Como sRGB é amplamente que usado na indústria, um desenvolvedor pode fazer algumas suposições que a cor especificada será representada fielmente em qualquer dispositivo que ele é exibido. No entanto, essa pode não ser sempre o caso. Além disso, há várias cores que não se ajustam o espaço de cor sRGB e, portanto, não podem ser representadas nele.

Por exemplo, muitos Têxteis são criados com threads usando muitos tintas e corantes cair fora sRGB. Também muitos produtos que uma pessoa se encontra em suas vidas diárias são criados com cores vívidas brilhantes que ficarem fora do espaço de cor sRGB. Alguns dos exemplos mais atraentes de cores que não podem ser representados em sRGB são coisas natureza como sunsets, folhas de outono, flores exóticas e águas tropicais.

Os usuários que têm captura de imagens digitais no formato BRUTO podem ter imagens em seus dispositivos que contêm todos os dados de cores, mesmo que ele não pode ser representado corretamente no espaço de cor sRGB e, portanto, não pode ser exibido corretamente na tela.

### <a name="the-display-p3-color-space"></a>O espaço de cor do vídeo P3

Em 2015, Apple lançou novos produtos (iMac e iPad Pro 9.7") que fornecem o espaço de cor P3 nova exibição para lidar com os problemas criados pelo espaço de cor sRGB.

[![](wide-color-images/color04.png "O espaço de cor nova exibição P3")](wide-color-images/color04.png#lightbox)

O espaço de cor P3 exibição tem as seguintes propriedades:

- Oferece suporte a um espaço de cor ampla para plataformas de hardware modernos.
- Com base no padrão SMPTE DCI-P3. DCI P3 foi projetado para projetores digitais, mas foi modificado pela Apple para dar suporte a monitores.
- Ele tem uma gama aproximada de 2.2.
- Representa as condições de iluminação típica (D65).

De acordo com a Apple, os usuários estão se movendo seus fluxos de trabalho para suas plataformas móveis. Solucionar os problemas de apresentação de cor apresentados pelo sRGB nos dispositivos móveis professional (iPad profissionais) necessário incluindo mais do que apenas uma exibição de cores de largura. Uma das soluções era a calibragem de fábrica, de atualização para que cada dispositivo individual foi calibrado na fábrica, garantindo que dispositivo, exibição de cor é consistentes e precisas.

Outra solução, é a inclusão completa, todo o sistema de gerenciamento de cores que Apple incorporou em 10 de iOS e macOS Serra. 

### <a name="the-extended-range-srgb-color-space"></a>O espaço de cores do intervalo estendido sRGB

O gerenciamento de cores de todo o sistema iOS 10 novos tem a conta para todos os aplicativos existentes do iOS que são criados e ajustados para sRGB. Ele foi projetado para garantir que ele não afetar o desempenho de aplicativo ou representação qualquer cor desses aplicativos existentes.

Para lidar com essa situação, a Apple incluiu intervalo estendido sRGB espaço de cor no iOS 10 (e macOS Serra).

O espaço de cores do intervalo estendido sRGB tem as seguintes propriedades:

- Tem o mesmo sRGB primárias.
- Ele tem uma gama aproximada de 2.2.
- Representa as condições de iluminação típica (D65).
- Ele dá suporte a valores negativos e valores maior que um (1).

Por permitindo valores menores que zero e maior que um, o sRGB intervalo estendido espaço de cor permite que aplicativos existentes com as cores presentes sRGB sem distorção ou ocorrências de desempenho, mas ele permite que o espaço de cor representar qualquer cor dentro do espectro. Tudo isso é feito enquanto mantém os mesmos pontos de ancoragem como o espaço de cor sRGB.

### <a name="extended-range-srgb-in-action"></a>Estendido sRGB intervalo em ação

Para ver como os valores fora do zero e um trabalho em sRGB o intervalo estendido espaço de cor, veja o exemplo seguinte da mais saturada vermelho disponíveis no espaço de cor P3 de exibição:

[![](wide-color-images/color05.png "Como os valores fora do zero e um funcionam em sRGB o intervalo estendido espaço de cor")](wide-color-images/color05.png#lightbox)

Na exibição P3, essa cor seria representada como `[1.0, 0.0, 0.0]` e no intervalo estendido sRGB seria `[1.358, -0.074, -0.012]`. Porque valores sRGB estão cheios contidos dentro de exibição P3 e os valores de exibição P3 dispor "fora" dos intervalos sRGB.

Para o hardware físico que permite que os valores em pixel ir do extremo positivo para valores negativos extremos, ele pode exibir todas as cores disponíveis no espectro visível e esses valores podem ser representados em sRGB o intervalo estendido espaço de cor.

### <a name="device-pixel-formats"></a>Formatos de Pixel do dispositivo 

O sRGB espaço de cor foi amplamente padronizar usando um formato de Pixel de 8 bits, como 8 bits por canal de cor é geralmente suficiente para descrever as cores em sRGB. Isso não é perfeito, mas bom o bastante, e ele fornece um bom equilíbrio entre o uso de memória e processador para exibir imagens.

Como exibir P3 podem representar coordenadas de cores fora do espaço de cores sRGB, ela requer 16 bits por canal de cores para representar corretamente a cores com o espaço de cores do intervalo estendido sRGB.

## <a name="system-wide-wide-color-support"></a>Suporte a cores de largura de todo o sistema

Para dar suporte a cores de largura e ampla gama de dentro de 10 de iOS e macOS Serra totalmente, Apple estendeu as seguintes estruturas para tirar proveito das sRGB intervalo estendido espaço de cor e exibir P3:

- UIKit (somente para iOS)
- SceneKit
- Gráficos de núcleo
- ImageIO
- Imagem principal
- WebKit
- SpriteKit
- Animação de núcleo
- AppKit (para macOS)

Além disso, a exibição de Retina suporte foi aprimorado para intervalo estendido sRGB espaço de cor e exibir P3 exibem.

Cores de largura é suportado e podem ser usada nos seguintes tipos de conteúdo do aplicativo:

- Recursos de imagem estática incluídos no pacote de aplicativo.
- Documento e rede com base em recursos de imagem.
- Mídia avançada como fotos ao vivo ou imagens no formato RAW.
- Imagens de textura de sombreador de elementos gráficos 3D.

## <a name="solving-the-color-problem"></a>Para solucionar o problema de cor

O conteúdo exibido no aplicativo pode vir de uma ampla variedade de fontes de cor avançada. Além disso, esse conteúdo pode ser exibido em uma ampla gama de dispositivos, cada qual com seu próprio intervalo de recursos de exibição de cor.

Um aplicativo iOS 10 preenche a diferença entre esses dois problemas usando o novo internas, todo o sistema cor gerenciar o sistema. Esse sistema garante que uma imagem tem a mesma aparência em qualquer dispositivo iOS, não importa qual espaço de cor, a imagem foi codificada em.

Gerenciamento de cores começa com cada imagem que tem um espaço de cor associado (ou perfil de cor). Essas informações são usadas no _processo de correspondência de cor_ onde as cores na imagem de origem são comparadas com cores no dispositivo de saída. Como cada pixel da imagem deve ser correspondido de cor, pode ser demorado e colocar uma carga na CPU do dispositivo.

Devido à natureza do _processo de correspondência de cor_, essa conversão pode ser potencialmente "perda" se o dispositivo de saída tem uma gama de menor que a imagem de origem.

Felizmente, os cálculos que envolvem ao _processo de correspondência de cores_ pode facilmente ser acelerada por hardware (tanto da CPU ou a GPU) e Apple garante que ele funciona automaticamente com a criação de suporte em sistemas de base como Quartz 2D, ColorSync e principais de animação. Para conteúdo marcado corretamente, a codificação não é necessário para aproveitar esses recursos.

Gerenciamento de cores é suportado em cada plataforma da seguinte maneira:

- **macOS** -macOS foi cores gerenciados desde o começo.
- **iOS** -iOS tem suporte para gerenciamento de cor automática desde o iOS 9.3 (e posterior).

### <a name="designing-for-wide-gamut"></a>Criando para ampla gama de cores

Apple tem a sugestão de seguir para criar e usar ampla cor, o conteúdo da imagem ampla gama de aplicativos iOS e macOS:

- Somente use gama ampla conteúdo onde fazer sentido no aplicativo, eles não devem automaticamente ser usados em qualquer lugar.
- Use somente o conteúdo de gama ampla onde cores vívidas melhorar a experiência do usuário.
- No não é necessário alterar a todo o conteúdo para P3 aplicativos existentes.

Cadeia de ferramentas da Apple torna o suporte para o conteúdo de imagem de gama ampla um gradual participar, dando suporte a cores de largura em um aplicativo não é uma situação de tudo ou nada.

### <a name="upgrading-existing-content-to-wide-color"></a>Atualizar o conteúdo existente para cores de largura

Apple tem as seguintes sugestões para atualizar o conteúdo de imagem existente para cores de largura:

- Não basta "atribua" um perfil P3 para o conteúdo do aplicativo de edição de imagens. Isso simplesmente mapeará a cor existente conteúda para o novo espaço de cor com resultados inesperados, como ampliar as cores para caber no espaço do novo, assim, alterar a imagem.
- O conteúdo da imagem precisará ser "convertidos" para o perfil de exibição P3 usando um aplicativo de edição de imagens.

### <a name="file-formats-and-color-profiles"></a>Formatos de arquivo e os perfis de cor

Apple tem as seguintes sugestões para os formatos de arquivo e perfis de cor usados no conteúdo de imagem de cores de largura do aplicativo:

- Use o perfil de cor de "Exibição P3" para espaços de trabalho de RGB.
- Use de 16 bits por modo de canal de cor.
- Use um iMac atrasada 2015 (ou posterior) para visualizar o conteúdo da imagem.
- Exporte ativos de imagem como arquivos PNG de 16 bits com um perfil de "Exibição P3" ICC incorporado.

> [!IMPORTANT]
> Usando o **Salvar para Web** ou **exportar ativos** recursos encontrados no software de edição de imagens mais populares _não_ para imagens de cores de largura, desde que esses recursos não foram atualizado para suportar as especificações de formato de arquivo necessário ainda.

### <a name="supporting-wide-color-with-asset-catalogs"></a>Dando suporte a ampla cor com catálogos de ativo

No iOS 10 e macOS serra, Apple expandiu os catálogos de ativo usada para incluir e categorizar imagem estática de conteúdo no pacote do aplicativo para oferecer suporte a cores de largura.

Usando os catálogos de ativo oferecem os seguintes benefícios para um aplicativo:

- Eles fornecem a melhor opção de implantação para os ativos de imagem estática.
- Eles oferecem suporte a correção de cor automática.
- Eles oferecem suporte a otimização de formato de pixel automática.
- Eles oferecem suporte a segmentação do aplicativo e diminuir o aplicativo que garante que apenas o conteúdo do get relevante entregue ao dispositivo do usuário final.

Apple tornou os seguintes aprimoramentos em catálogos do Asset para suporte a ampla cores:

- Eles oferecem suporte a conteúdo de origem de 16 bits (por canal de cor).
- Eles oferecem suporte a conteúdo de catalogação por gama de exibição. Conteúdo pode ser marcado para o sRGB ou gamas P3 de exibição.

O desenvolvedor tem três opções para dar suporte a conteúdo de cores de largura em seus aplicativos:

1. **Não fazer nada** -desde que o conteúdo de cor ampla só deve ser usado em situações em que o aplicativo precisa para exibir cores vívidas (onde eles melhorar a experiência do usuário), o conteúdo fora esse requisito deve ser deixado como-é. Ele continuará a ser renderizado corretamente em todas as situações de hardware.
2. **Atualizar o conteúdo existente para exibição P3** -isso requer que o desenvolvedor substitua o conteúdo da imagem existente no seu catálogo de ativos com um novo arquivo no formato P3 atualizado e marcá-lo como tal no Editor ativo. No momento da compilação, uma imagem derivados sRGB será gerada a partir desses ativos.
3. **Forneça a otimização de ativo de conteúdo** -nessa situação, o desenvolvedor fornecerá um sRGB de 8 bits e uma visão de exibição P3 de 16 bits de cada imagem no catálogo do Asset (corretamente marcados no editor ativo).

### <a name="asset-catalog-deployment"></a>Implantação de catálogo de ativos

O seguinte ocorrerá quando o desenvolvedor envia um aplicativo à loja de aplicativos com catálogos de ativo que incluem o conteúdo da imagem de cores de largura:

- Quando o aplicativo é implantado para o usuário final, a divisão de aplicativo irá garantir que apenas a variante de conteúdo apropriada é entregue ao dispositivo do usuário.
- No dispositivo que não dão suporte a cores de largura, não há nenhum custo de carga para incluir cores de largura de conteúdo, pois ele nunca é enviado para o dispositivo.
- `NSImage` em macOS Serra (e posterior) selecionará automaticamente a melhor representação conteúda para exibição do hardware.
- O conteúdo exibido será atualizado automaticamente quando ou se o hardware de dispositivos exibir características de alteração.

### <a name="asset-catalog-storage"></a>Armazenamento de catálogo de ativos

Armazenamento de catálogo Asset tem as propriedades e as implicações para um aplicativo a seguir:

- No momento da compilação, Apple tenta otimizar o armazenamento de conteúdo de imagem por meio de conversões de imagem de alta qualidade.
- 16 bits são usados por um canal de cor para o conteúdo de cores de largura.
- Compactação de conteúdo de imagem dependente é usada para reduzir o tamanho de conteúdo do produto. Novo compactações "perdas" foram adicionadas para otimizar ainda mais o tamanho do conteúdo.

## <a name="colors-in-user-interfaces"></a>Cores nas Interfaces do usuário

Ao trabalhar com as cores em uma Interface de usuário, a maioria dos pixels na tela está em uma cor sólida. Além disso, a maioria desses pixels não é proveniente de ativos de imagens, mas é desenhada diretamente pelo aplicativo (ou pelo sistema operacional em nome do aplicativo).

Cor gama ampla pode apresentar vários desafios ao trabalhar no nível de interface do usuário:

- **Comunicação cores** - quando se trata de cores entre designers e desenvolvedores normalmente há um _assumido_ sRGB envolvido do espaço de cor. Para uma cor pode ser comunicada como `rgb(128, 45, 56)` ou `#FF0456`. No design de uma ampla gama, essas representações não fornecerem informações suficientes para representar com exatidão a cor especificada, o espaço de cor de trabalho também deve ser incluído. Apple sugere usando `P3(128, 45, 56)` e `P3#FF0456` em vez disso. 
- **Cores de separação** - a maioria da imagem popular de edição e o design de software tem as mesmas limitações que o espaço de cor sRGB ao usar seus seletores de cores interna. O designer deve garantir que estejam no espaço de cor de "Exibição P3" no seletor de cores ao trabalhar com projetos de cores de largura.
- **Codificação de cores** - ambos `NSColor` (macOS) e `UIColor` (iOS & tvOS) têm novos métodos de conveniência para gerar cores P3 diretamente e ambos foram estendidas para oferecer suporte a cores de intervalo estendido espaço de cores sRGB também.
- **Armazenamento de cores** -deve ter cuidado ao armazenamento gama ampla de cores no documento do aplicativo. Onde 8 bits por canal de cor funcionou bem para o espaço de cor sRGB, 16 bits por canal de cor devem ser usado para cores amplas. O desenvolvedor também precisa detectar instâncias do espaço de cor assumida (desde tudo tradicionalmente sRGB somente).

## <a name="colors-on-the-web"></a>Cores na Web

Tome cuidado ao trabalhar com cores de largura em páginas da web e em dispositivos que oferecem suporte à exibição de cores de largura. Se todo o conteúdo de imagem que foi incluído no site foi marcada adequadamente, iOS e macOS automaticamente correspondência de cores e exibi-los corretamente.

As consultas de mídia também estão disponíveis para ajudar a resolver ativos entre P3 e sRGB dispositivos compatíveis com:

```xml
<picture>
    <source srcset="monkey-p3.jpg" media="(color-gamut: p3)">
    <source srcset="monkey-rpg.jpg">
</picture>
```

Apple também tem uma proposta WebKit permitirá CSS sejam especificadas nos outros espaços de cor além do espaço sRGB assumida.

## <a name="rendering-off-screen-images-in-app"></a>Renderizando imagens fora da tela no aplicativo

Com base no tipo de aplicativo que está sendo criado, ele pode permitir que o usuário incluir o conteúdo da imagem que coletaram da internet ou criar conteúdo de imagem diretamente dentro do aplicativo (como um vetor de aplicativo de desenho por exemplo).

Em ambos os casos, o aplicativo pode processar as imagens necessárias fora da tela ampla cor usando recursos aprimorados adicionados para iOS e macOS.

### <a name="drawing-wide-color-in-ios"></a>Cores de largura de desenho no iOS

Antes de discutir como desenhar corretamente uma imagem de cores de largura no iOS 10, observe o código de desenho de iOS comum seguintes:

```csharp
public UIImage DrawWideColorImage ()
{
    var size = new CGSize (250, 250);
    UIGraphics.BeginImageContext (size);

    ...
    
    UIGraphics.EndImageContext ();
    return image = UIGraphics.GetImageFromCurrentImageContext ();
}
```

Há problemas com o código padrão que precisam ser abordadas _antes de_ pode ser usado para desenhar uma imagem de cores de largura. O `UIGraphics.BeginImageContext (size)` método usado para iniciar o desenho de imagem iOS tem as seguintes limitações:

- Ele não pode criar contextos de imagem com mais de 8 bits por canal de cor.
- Ele não pode representar cores em sRGB o intervalo estendido espaço de cor.
- Ele não tem a capacidade de fornecer uma interface para criar contextos em um espaço de cor sRGB não devido as rotinas de baixo nível C que está sendo chamadas em segundo plano.

Para lidar com essas limitações e desenhar uma imagem de cores de largura no iOS 10, use o código a seguir:

```csharp
public UIImage DrawWideColorImage ()
{
    var size = new CGSize (250, 250);
    var render = new UIGraphicsImageRenderer (size);

    var image = render.CreateImage ((UIGraphicsImageRendererContext context) => {
        var bounds = context.Format.Bounds;
        CGRect slice;
        CGRect remainder;
        bounds.Divide (bounds.Width / 2, CGRectEdge.MinXEdge, out slice, out remainder);

        var redP3 = UIColor.FromDisplayP3 (1.0F, 0.0F, 0.0F, 1.0F);
        redP3.SetColor ();
        context.FillRect (slice);

        var redRGB = UIColor.Red;
        redRGB.SetColor ();
        context.FillRect (remainder);
    });

    // Return results
    return image;
}
```

O novo `UIGraphicsImageRenderer` classe cria um novo contexto de imagem que é capaz de manipular o espaço de cores do intervalo estendido sRGB e tem os seguintes recursos:

- Ele é totalmente cor gerenciado por padrão.
- Ele dá suporte a sRGB intervalo estendido espaço de cor, por padrão.
- Ele inteligente decide se ele deve renderizar na sRGB ou intervalo estendido sRGB espaço de cor com base nos recursos do que o aplicativo está em execução no dispositivo iOS.
- Ele totalmente e automaticamente gerencia o contexto de imagem (`CGContext`) o tempo de vida para o desenvolvedor não precisa se preocupar sobre chamada começam e terminam comandos de contexto.
- Ele é compatível com o `UIGraphics.GetCurrentContext()` método.

O `CreateImage` método o `UIGraphicsImageRenderer` classe é chamada para criar uma imagem de cores de largura e passada com o contexto de imagem para desenhar em um manipulador de conclusão. Todo o desenho é feita dentro deste manipulador de conclusão da seguinte maneira:

- O `UIColor.FromDisplayP3` método cria uma nova cor vermelha totalmente saturada em toda a gama ampla espaço de cor P3 exibição e é usado para desenhar a primeira metade do retângulo. 
- A segunda metade do retângulo é desenhado no sRGB normal totalmente saturado cor vermelha para comparação.

### <a name="drawing-wide-color-in-macos"></a>Cores de largura de desenho macOS

O `NSImage` classe foi expandida em macOS serra para suportar o desenho de imagens coloridas ampla. Por exemplo:

```csharp
var size = CGSize(250,250);
var wideColorImage = new NSImage(size, false, (drawRect) =>{
    var rects = drawRect.Divide(drawRect.Size.Width/2,CGRect.MinXEdge);
    
    var color = new NSColor(1.0, 0.0, 0.0, 1.0).Set();
    var path = new NSBezierPath(rects.Slice).Fill();
    
    color = new NSColor(1.0, 0.0, 0.0, 1.0).Set();
    path = new NSBezierPath(rects.Remainder).Fill();
    
    // Return modified
    return true;
});
```

## <a name="rendering-on-screen-images-in-app"></a>Na tela de renderização imagens no aplicativo

Para renderizar imagens coloridas ampla na tela, o processo funciona de modo semelhante a uma imagem de cores fora da tela de largura para macOS e iOS apresentada acima de desenho.

### <a name="rendering-on-screen-in-ios"></a>Na tela de renderização no iOS

Quando o aplicativo precisa processar uma imagem grande cor na tela no iOS, substituir o `Draw` método o `UIView` em questão como de costume. Por exemplo:

```csharp
using System;
using UIKit;
using CoreGraphics;

namespace MonkeyTalk
{
    public class MonkeyView : UIView
    {
        public MonkeyView ()
        {
        }

        public override void Draw (CGRect rect)
        {
            base.Draw (rect);

            // Draw the image to screen
            ...
        }
    }
}
``` 

Como o iOS 10 com o `UIGraphicsImageRenderer` classe mostrado acima, de maneira inteligente decide se ele deve renderizar na sRGB ou intervalo estendido sRGB espaço de cor com base nos recursos do dispositivo iOS que o aplicativo está em execução quando o `Draw` método é chamado. Além disso, o `UIImageView` foi cores gerenciados desde o iOS 9.3.

Se o aplicativo precisa saber como a renderização é feita em um `UIView` ou `UIViewController`, pode verificar o novo `DisplayGamut` propriedade o `UITraitCollection` classe. Esse valor será uma `UIDisplayGamut` enum das seguintes opções:

- P3
- SRGB
- Não especificado

Se quiser que o aplicativo para controlar quais espaço de cor é usado para desenhar uma imagem, pode usar um novo `ContentsFormat` propriedade o `CALayer` para especificar o espaço de cor desejada. Esse valor pode ser um `CAContentsFormat` enum das seguintes opções:

- Gray8Uint
- Rgba16Float
- Rgba8Uint

### <a name="rendering-on-screen-in-macos"></a>Na tela de renderização no macOS

Quando o aplicativo precisa processar uma imagem grande cor na tela em macOS, substituir o `DrawRect` método o `NSView` em questão como de costume. Por exemplo:

```csharp
using System;
using AppKit;
using CoreGraphics;
using CoreAnimation;

namespace MonkeyTalkMac
{
    public class MonkeyView : NSView
    {
        public MonkeyView ()
        {
        }

        public override void DrawRect (CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

            // Draw graphics into the view
            ...
        }
    }
}
```

Novamente, ele inteligente decide se ele deve renderizar na sRGB ou intervalo estendido sRGB espaço de cor com base nos recursos de hardware Mac que o aplicativo está em execução quando o `DrawRect` método é chamado.

Se quiser que o aplicativo para controlar quais espaço de cor é usado para desenhar uma imagem, pode usar um novo `DepthLimit` propriedade o `NSWindow` classe para especificar o espaço de cor desejada. Esse valor pode ser um `NSWindowDepth` enum das seguintes opções:

- OneHundredTwentyEightBitRgb
- SixtyfourBitRgb
- TwentyfourBitRgb

## <a name="summary"></a>Resumo

Este artigo abordou cor ampla e as maneiras que podem ser implementado e usado dentro de um aplicativo xamarin ou Xamarin.Mac.



## <a name="related-links"></a>Links relacionados

- [Exemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
