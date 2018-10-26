---
title: Cores amplas no xamarin. IOS
description: Este documento discute as cores de largura e como ele pode ser usado em um aplicativo xamarin. IOS ou xamarin. Mac. Ele também fornece uma visão geral dos muitos conceitos importantes relacionadas à cor como espaços de cores, canais e primários.
ms.prod: xamarin
ms.assetid: 576E978A-F182-489A-83E4-D8CDC6890B24
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: f139bcceda12752e43a3a8330fa0a0e038e539f9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121302"
---
# <a name="wide-color-in-xamarinios"></a>Cores amplas no xamarin. IOS

_Este artigo aborda as cores de largura e como ele pode ser usado em um aplicativo xamarin. IOS ou xamarin. Mac._

iOS 10 e o macOS Sierra aprimora o suporte para formatos de pixel de intervalo estendido e espaços de cores de toda a gama de cores em todo o sistema, incluindo estruturas como elementos gráficos principais, imagem principal, sistema operacional e AVFoundation. Suporte para dispositivos com telas de cores adicional é facilitado por fornecendo esse comportamento em toda a pilha inteira de gráficos.

## <a name="about-wide-color"></a>Sobre cores amplas

Como mencionado acima, o iOS 10 e o macOS Sierra aprimora o suporte para formatos de pixel de intervalo estendido e espaços de cores de toda a gama de cores em todo o sistema, incluindo estruturas como elementos gráficos principais, imagem principal, sistema operacional e AVFoundation. Suporte para dispositivos com telas de cores adicional é facilitado por fornecendo esse comportamento em toda a pilha inteira de gráficos.

O 90 Apple criou ColorSync para lidar com a cor de processamento no Mac. Eles também ajudaram a encontrar o International Color Consortium ICC () para criar e promover um conjunto de padrões para tratamento de cor no hardware de computador. Trabalho da Apple com o ICC foi incluído no ColorSync e ele foi criado sobre o núcleo dos X (agora chamado de macOS).

Apple também está na vanguarda da tecnologia de exibição com hardware, como a Retina exibir, o novo P3 exibir e espaço de cores de P3 exibir (lançado no iMac em 2015) e o TrueTone exibe nos profissionais de iPad, iPhone 7 e iPhone 7 Plus.

Com cores no macOS Sierra e iOS 10, Apple está mudando a maneira que ao macOS e iOS lidar com cor para tirar total proveito dessas novas tecnologias de exibição.

## <a name="core-color-concepts"></a>Principais conceitos de cor

Os seguintes conceitos de cor principais precisam ser abordadas antes de realizar uma análise mais profunda cor no iOS e macOS:

### <a name="color-space"></a>Espaço de cores

Um espaço de cores é um ambiente em que as cores podem ser representadas e comparadas. Ele pode ser um espaço dimensional de uma a quatro que é definido pela intensidade de seus componentes de cor. 

[![](wide-color-images/color00.png "Um espaço de cores")](wide-color-images/color00.png#lightbox)

### <a name="color-channels"></a>Canais de cores

Os componentes de cor também podem ser conhecidos como canais de cor. Algumas representações familiares seria RGB espaços, espaços de cinza, CMYK espaços ou espaços independentes de dispositivo. 

[![](wide-color-images/color02.png "Os componentes de cor também podem ser chamados de canais de cores")](wide-color-images/color02.png#lightbox)

### <a name="color-primaries"></a>Cores primárias

Cores primárias fornecem o sistema de coordenadas que é usado para comparar e cores de computação. Cores primárias geralmente se encaixam a versão mais intensa da determinada cor que pode ser gerada no canal de cor.

[![](wide-color-images/color01.png "Cores primárias de fornecem o sistema de coordenadas que é usado para comparar e cores de computação")](wide-color-images/color01.png#lightbox)

No caso do espaço de cor RGB representada acima, as cores primárias são onde o `1.0` coordenadas são ancoradas (como `[1.0, 0.0, 0.0]` para vermelho).

### <a name="color-gamut"></a>Gama de cores

Gama de cores se refere a todas as cores que podem ser definidas como uma combinação dos canais de cores individuais dentro de um determinado espaço de cores.

[![](wide-color-images/color03.png "Exemplo de gama de cores")](wide-color-images/color03.png#lightbox)

## <a name="what-is-wide-color"></a>O que é a cor ampla

Antes de abordar o tópico de cores amplas, deve ser tinha uma discussão sobre o atual padrão do setor para cor, o espaço de cor RGB padrão (sRGB). É o espaço de cores mais amplamente usados na computação hoje e é o espaço de cores padrão para iOS e macOS.

O espaço de cores sRGB tem as seguintes propriedades:

- Ele se baseia no padrão BT.709 ITU-R.
- Ele tem uma gama aproximada de 2.2.
- Representa as condições de iluminação típico (D65).

Como sRGB é amplamente que usado no setor, um desenvolvedor pode fazer algumas suposições que a cor especificada será representada fielmente em qualquer dispositivo que ele é exibido no. No entanto, isso pode não sempre ser o caso. Além disso, há várias cores que não cabem no espaço de cor sRGB e, portanto, não podem ser representadas nele.

Por exemplo, muitos Têxteis são projetados com threads que usam muitos tintas e corantes cair fora sRGB. Também muitos produtos que uma pessoa se encontra em sua rotina diária são criados com cores vivas brilhantes que estão fora do espaço de cores sRGB. Alguns dos exemplos mais interessantes de cores que não podem ser representados em sRGB são coisas por natureza, como pores do sol, folhas de outono, exóticas flores e águas tropicais.

Os usuários que têm captura de imagens digitais no formato BRUTO podem ter imagens em seus dispositivos que contêm todos esses dados de cor, mesmo que ele não pode ser representado adequadamente o espaço de cor sRGB e, portanto, não pode ser exibido corretamente na tela.

### <a name="the-display-p3-color-space"></a>O espaço de cor exibição P3

Em 2015, a Apple lançou novos produtos (iMac e iPad Pro 9,7 pol.) que fornecem o novo espaço de cor do P3 de exibição para lidar com os problemas criados pelo espaço de cores sRGB.

[![](wide-color-images/color04.png "O novo espaço de cor do P3 de exibição")](wide-color-images/color04.png#lightbox)

O espaço de cor P3 exibição tem as seguintes propriedades:

- Dá suporte a um espaço de cores para plataformas de hardware modernos.
- Com base no padrão DCI SMPTE-P3. P3 DCI foi projetado para projetores digitais, mas foi modificado pela Apple para dar suporte a monitores.
- Ele tem uma gama aproximada de 2.2.
- Representa as condições de iluminação típico (D65).

De acordo com a Apple, os usuários estão migrando seus fluxos de trabalho para suas plataformas móveis. Resolvendo os problemas de apresentação de cor apresentados pelo sRGB nos dispositivos móveis profissionais (iPad profissionais), necessária, incluindo mais do que apenas uma exibição em cores ampla. Uma das soluções era atualizar a calibragem de fábrica, portanto, cada dispositivo individual foi calibrado a fábrica para assegurar que dispositivo, exibição em cores seja precisos e consistentes.

Outra solução, é a inclusão completa, todo o sistema de gerenciamento de cores que a Apple incorporou no iOS 10 e o macOS Sierra. 

### <a name="the-extended-range-srgb-color-space"></a>O espaço de cores sRGB de intervalo estendido

O novo gerenciamento de cor em todo o sistema iOS 10 tem para levar em conta todos os aplicativos existentes do iOS que são criados e ajustados para sRGB. Ele foi projetado para assegurar que ele não afete o desempenho de representação ou o aplicativo de qualquer cor desses aplicativos existentes.

Para lidar com essa situação, a Apple incluiu o espaço de cores de sRGB de intervalo estendido no iOS 10 (e o macOS Sierra).

O espaço de cores sRGB de intervalo estendido tem as seguintes propriedades:

- Tem todos o mesmo sRGB primários.
- Ele tem uma gama aproximada de 2.2.
- Representa as condições de iluminação típico (D65).
- Ele dá suporte a valores negativos e valores maior do que um (1).

Por permitindo valores menores que zero e maior do que um, o sRGB de intervalo estendido espaço de cor não só permite que aplicativos existentes presentes cores sRGB sem ocorrências de desempenho ou distorção, mas permite que o espaço de cores representar qualquer cor dentro do visível espectro. Tudo isso é feito enquanto mantém os mesmos pontos de ancoragem que o espaço de cores sRGB.

### <a name="extended-range-srgb-in-action"></a>SRGB de intervalo estendido em ação

Para ver como os valores fora do zero e um trabalho em que o espaço de cores sRGB de intervalo estendido, veja o exemplo a seguir da mais saturada vermelha disponíveis no espaço de cores P3 exibição:

[![](wide-color-images/color05.png "Como os valores fora do zero e um funcionam no espaço de cores sRGB de intervalo estendido")](wide-color-images/color05.png#lightbox)

No monitor P3, essa cor seria representada como `[1.0, 0.0, 0.0]` e em sRGB de intervalo estendido seria `[1.358, -0.074, -0.012]`. Porque valores sRGB estão cheios contidos dentro de monitor P3 e os valores de monitor P3 layout "fora" os intervalos de sRGB.

Para hardware físico que permite que os valores de pixel ir do extremo positivo para valores negativos extremos, ele pode exibir qualquer cor disponível no espectro visível e esses valores podem ser representados no espaço de cores sRGB de intervalo estendido.

### <a name="device-pixel-formats"></a>Formatos de Pixel de dispositivo 

O espaço de cores tem sido amplamente de sRGB padronizem usando um formato de Pixel de 8 bits, uma vez que é de 8 bits por canal de cor principalmente o suficiente para descrever cores sRGB. Isso não é perfeito, mas bom o suficiente, e ele oferece um bom equilíbrio entre o uso de memória e processador para exibir imagens.

Como o monitor P3 podem representar coordenadas de cores fora do espaço de cor sRGB, ele requer 16 bits por canal de cor para representar corretamente a cores com o espaço de cores sRGB de intervalo estendido.

## <a name="system-wide-wide-color-support"></a>Suporte a cores de largura de todo o sistema

Para dar suporte a cores de largura e a ampla gama de dentro do iOS 10 e macOS Sierra totalmente, Apple estendeu as seguintes estruturas para tirar proveito do que o espaço de cores sRGB de intervalo estendido e monitor P3:

- UIKit (para iOS)
- SceneKit
- Elementos gráficos principais
- ImageIO
- Imagem principal
- WebKit
- SpriteKit
- Animação principal
- AppKit (para macOS)

Além disso, o suporte foi aprimorado para o espaço de cores sRGB de intervalo estendido tela retina e monitor P3 exibem.

Cores amplas tem suporte e podem ser usada nos seguintes tipos de conteúdo do aplicativo:

- Recursos de imagem estática incluídos no pacote de aplicativo.
- Documento e rede com base em recursos de imagem.
- Mídia avançada, como fotos ao vivo ou imagens no formato BRUTO.
- Imagens de textura de sombreador de gráficos 3D.

## <a name="solving-the-color-problem"></a>Para solucionar o problema de cor

O conteúdo exibido em um aplicativo pode vir de uma ampla variedade de fontes de cor sofisticados. Além disso, esse conteúdo pode ser exibido em uma ampla gama de dispositivos, cada um com seu próprio intervalo de recursos de exibição de cor.

Um aplicativo iOS 10 preenche a diferença entre esses dois problemas usando o novo internas, todo o sistema cor gerenciar o sistema. Esse sistema garante que uma imagem tem a mesma aparência em qualquer dispositivo iOS, não importa qual espaço de cores a imagem foi codificada no.

Gerenciamento de cores começa com cada imagem tendo um espaço de cor associado (ou o perfil de cor). Essas informações são usadas no _processo de correspondência de cor_ onde as cores da imagem de origem são correspondidas às cores no dispositivo de saída. Como cada pixel da imagem precisa ser correspondidos de cor, pode ser demorado e distorcer após a CPU do dispositivo.

Devido à natureza de _processo de correspondência de cor_, essa conversão pode ser potencialmente "com perdas" se o dispositivo de saída tem uma gama de menor que a imagem de origem.

Felizmente, os cálculos que vão para o _processo de correspondência de cor_ pode facilmente ser acelerada por hardware (seja na CPU ou GPU) e Apple garante que ele funciona automaticamente com a criação de suporte em sistemas de base como habilitado para Quartz 2D, ColorSync e animação principal. Para conteúdo marcado corretamente, nenhuma codificação é necessária para tirar proveito desses recursos.

Gerenciamento de cores tem sido suportado em cada plataforma da seguinte maneira:

- **macOS** -macOS tiver sido gerenciado desde a concepção de cor.
- **iOS** -iOS tem suporte para gerenciamento de cores automático desde o iOS 9.3 (e posterior).

### <a name="designing-for-wide-gamut"></a>Projetando para ampla gama de cores

Apple tem a seguinte sugestão para projetar e usando ampla de cores, conteúdo da imagem ampla gama de cores em aplicativos iOS e macOS:

- Usar somente o conteúdo de gama ampla onde na marca de detecção no aplicativo, eles não devem automaticamente ser usados em qualquer lugar.
- Use somente o conteúdo de ampla gama onde cores vivas melhorará a experiência do usuário.
- Em não é necessário alterar todo o conteúdo para P3 para aplicativos existentes.

Cadeia de ferramentas da Apple torna o suporte para conteúdo da imagem ampla variedade um gradual opt-in, portanto, não é uma situação de tudo ou nada que dão suporte a cores em um aplicativo.

### <a name="upgrading-existing-content-to-wide-color"></a>Atualizando o conteúdo existente para cores amplas

Apple tem as sugestões a seguir para atualizar o conteúdo de imagem existente para cores amplas:

- Não apenas "atribua" um perfil P3 para o conteúdo da imagem editando o aplicativo. Ao fazer isso simplesmente mapeará a cor existente conteúda ao novo espaço de cor com resultados inesperados, como ampliar as cores para caber no espaço de novo, assim, alterar a imagem.
- O conteúdo da imagem precisará ser "convertidos" para o perfil de monitor P3 usando um aplicativo de edição de imagens.

### <a name="file-formats-and-color-profiles"></a>Formatos de arquivo e perfis de cor

Apple tem as seguintes sugestões para os formatos de arquivo e os perfis de cor usados no conteúdo de imagem de cores do aplicativo:

- Use o perfil de cor do "Monitor P3" para espaços de trabalho de RGB.
- Use de 16 bits por modo de canal de cor.
- Use um iMac Late 2015 (ou posterior) para visualizar com precisão o conteúdo da imagem.
- Exporte os ativos de imagem como arquivos PNG de 16 bits com um perfil de "Monitor P3" ICC inserido.

> [!IMPORTANT]
> Usando o **Salvar para Web** ou **ativos exportar** recursos encontrados no software de edição de imagens mais populares _não_ trabalho ampla para imagens de cores, pois esses recursos não foram atualizado para suportar as especificações de formato de arquivo necessário ainda.

### <a name="supporting-wide-color-with-asset-catalogs"></a>Suporte a cores com catálogos de ativos

No iOS 10 e no macOS Sierra, Apple expandiu os catálogos de ativo usado para incluir e categorizar o conteúdo de imagem estática no pacote do aplicativo para dar suporte a cores de largura.

Usando catálogos de ativos oferecem os seguintes benefícios para um aplicativo:

- Eles fornecem a melhor opção de implantação para ativos de imagem estática.
- Eles oferecem suporte a correção de cor automática.
- Eles dão suporte a otimização de formato de pixel automáticas.
- Eles oferecem suporte a divisão do aplicativo e diminuir o aplicativo que garante que somente o conteúdo que é relevante get entregue ao dispositivo do usuário final.

Apple tornou os seguintes aprimoramentos para catálogos de ativos para suporte a cores de largura:

- Eles oferecem suporte a conteúdo de origem de 16 bits (por canal de cor).
- Eles oferecem suporte a conteúdo de catalogação por gama de exibição. Conteúdo pode ser marcado para o monitor P3 gamas ou sRGB.

O desenvolvedor tem três opções para dar suporte a conteúdo de cores em seus aplicativos:

1. **Não faça nada** -uma vez que o conteúdo de cores amplas só deve ser usado em situações em que o aplicativo precisa para exibir cores vivas (onde eles serão aprimorar a experiência do usuário), o conteúdo fora esse requisito deve ser deixado como-está. Ele continuará a ser renderizado corretamente em todas as situações de hardware.
2. **Atualizar o conteúdo existente para o monitor P3** -isso exige que o desenvolvedor substitua o conteúdo de imagem existente em seu catálogo de ativos com um arquivo novo e atualizado no formato P3 e marcá-lo como tal no Editor ativo. No momento da compilação, uma imagem de derivativos sRGB será gerada a partir desses ativos.
3. **Fornecer conteúdo de ativos com otimização de** -nessa situação, o desenvolvedor fornecerá um sRGB de 8 bits e uma visão de monitor P3 de 16 bits de cada imagem no catálogo de ativos (corretamente marcados no editor ativo).

### <a name="asset-catalog-deployment"></a>Implantação do catálogo de ativos

O seguinte ocorrerá quando o desenvolvedor envia um aplicativo para a Store do aplicativo com catálogos de ativos que incluem o conteúdo da imagem de cores:

- Quando o aplicativo é implantado para o usuário final, aplicativo de divisão garantirá que apenas a variante de conteúdo apropriada é entregue ao dispositivo do usuário.
- No dispositivo que não dão suporte a cores amplas, não há nenhum custo de carga para incluir o conteúdo de cores, pois ele nunca é enviado ao dispositivo.
- `NSImage` no macOS Sierra (e posterior) selecionará automaticamente a melhor representação de conteúdo para exibição do hardware.
- O conteúdo exibido será atualizado automaticamente quando ou se o hardware de dispositivos exibir características de alteração.

### <a name="asset-catalog-storage"></a>Armazenamento de catálogo de ativos

Armazenamento de catálogo ativo tem as propriedades e as implicações para um aplicativo a seguir:

- No momento da compilação, o Apple tenta otimizar o armazenamento do conteúdo de imagem por meio de conversões de imagem de alta qualidade.
- 16 bits são usados por um canal de cor para o conteúdo de cores.
- Compactação de conteúdo de imagem dependente é usada para reduzir os tamanhos de conteúdo do produto. Compressões "com perdas" novos foram adicionados para otimizar ainda mais os tamanhos de conteúdo.

## <a name="colors-in-user-interfaces"></a>Cores nas Interfaces do usuário

Ao trabalhar com as cores em uma Interface do usuário, a maioria dos pixels na tela está em uma cor sólida. Além disso, a maioria desses pixels não vêm de ativos de imagens, mas é desenhadas diretamente pelo aplicativo (ou pelo sistema operacional em nome do aplicativo).

Cor gama ampla pode apresentar vários desafios ao trabalhar no nível da interface do usuário:

- **Comunicando-se cores** - quando falamos sobre cor entre designers e desenvolvedores, geralmente há um _assumido_ sRGB envolvido do espaço de cores. Portanto, uma cor pode ser comunicada como `rgb(128, 45, 56)` ou `#FF0456`. No design de uma ampla gama de cores, essas representações não fornecem informações suficientes para representar com exatidão a cor especificada, o espaço de cores do trabalho também deve ser incluído. Apple sugere o uso de `P3(128, 45, 56)` e `P3#FF0456` em vez disso. 
- **Cores de separação** – a maioria da edição de imagens populares e o design de software apresentam as mesmas limitações que o espaço de cores sRGB ao usar seus selecionadores de cores interna. O designer deve garantir que estejam no espaço de cores "Monitor P3" no seletor de cores ao trabalhar com designs de cores.
- **Codificação de cores** – tanto `NSColor` (macOS) e `UIColor` (iOS e tvOS) tem novos métodos de conveniência para a geração de cores P3 diretamente e ambos foram estendidas para dar suporte a cores de intervalo estendido espaço de cores sRGB também.
- **Armazenar cores** -deve ter cuidado ao armazenar a ampla gama de cores no documento do aplicativo. Onde os 8 bits por canal de cor funcionou bem para o espaço de cor sRGB, 16 bits por canal de cor devem ser usado para cores amplas. O desenvolvedor também precisa vigiar para instâncias do espaço de cores assumida (já que tudo o que costumava é sRGB somente).

## <a name="colors-on-the-web"></a>Cores na Web

Tome cuidado ao trabalhar com cores em páginas da web e em dispositivos que dão suporte à exibição em cores ampla. Se todo o conteúdo de imagem que foi incluído no site tem sido devidamente marcados, iOS e macOS automaticamente correspondência de cores e exibi-los corretamente.

Consultas de mídia também estão disponíveis para ajudar a resolver ativos entre P3 e sRGB dispositivos compatíveis com:

```xml
<picture>
    <source srcset="monkey-p3.jpg" media="(color-gamut: p3)">
    <source srcset="monkey-rpg.jpg">
</picture>
```

Apple também tem uma proposta de WebKit que permitirá que o CSS seja especificada em outros espaços de cores, além do espaço sRGB assumida.

## <a name="rendering-off-screen-images-in-app"></a>Renderizando imagens fora da tela no aplicativo

Com base no tipo de aplicativo que está sendo criado, ele pode permitir que o usuário incluir o conteúdo da imagem que coletaram da internet ou criar conteúdo de imagem diretamente dentro do aplicativo (como um vetor de aplicativo de desenho por exemplo).

Em ambos os casos, o aplicativo pode processar as imagens necessárias fora da tela ampla cor usando os recursos aprimorados adicionados para iOS e macOS.

### <a name="drawing-wide-color-in-ios"></a>Cores amplas de desenho no iOS

Antes de discutir como corretamente desenhar uma imagem de cores de largura no iOS 10, vejamos o código de desenho de iOS comuns seguintes:

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

Há problemas com o código padrão que precisam ser abordadas _antes de_ pode ser usado para desenhar uma imagem de cores. O `UIGraphics.BeginImageContext (size)` método usado para iniciar o desenho da imagem de iOS tem as seguintes limitações:

- Ele não é possível criar os contextos de imagem com mais de 8 bits por canal de cor.
- Ele não pode representar cores em que o espaço de cores sRGB de intervalo estendido.
- Ele não tem a capacidade de fornecer uma interface para criar os contextos em um espaço de cor não sRGB devido a rotinas C baixo nível que está sendo chamadas em segundo plano.

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

O novo `UIGraphicsImageRenderer` classe cria um novo contexto de imagem que é capaz de lidar com o espaço de cores sRGB de intervalo estendido e tem os seguintes recursos:

- Ele é totalmente gerenciado pelo padrão de cor.
- Ele oferece suporte para o espaço de cores sRGB de intervalo estendido por padrão.
- -Lo de maneira inteligente decide se ele deve renderizar na sRGB ou sRGB de intervalo estendido o espaço de cores com base nos recursos do dispositivo iOS que o aplicativo está sendo executado.
- Ele totalmente e automaticamente gerencia o contexto de imagem (`CGContext`) tempo de vida para que o desenvolvedor não precisa se preocupar sobre como chamar begin e end comandos de contexto.
- Ele é compatível com o `UIGraphics.GetCurrentContext()` método.

O `CreateImage` método da `UIGraphicsImageRenderer` classe é chamado para criar uma imagem de cores e passado com o contexto de imagem para desenhar em um manipulador de conclusão. Tudo o desenho é feito dentro este manipulador de conclusão da seguinte maneira:

- O `UIColor.FromDisplayP3` método cria uma nova cor vermelha totalmente saturada no espaço de cor P3 exibição ampla variedade e ele é usado para desenhar na primeira metade do retângulo. 
- A segunda metade do retângulo é desenhado no sRGB normal totalmente saturado cor vermelha para comparação.

### <a name="drawing-wide-color-in-macos"></a>Cores amplas de desenho no macOS

O `NSImage` classe foi expandido no macOS Sierra para dar suporte ao desenho de imagens coloridas ampla. Por exemplo:

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

## <a name="rendering-on-screen-images-in-app"></a>Na tela de renderização de imagens no aplicativo

Para renderizar imagens com cores ampla na tela, o processo funciona de forma semelhante ao desenho de uma imagem de fora da tela de cores amplas para macOS e iOS apresentada acima.

### <a name="rendering-on-screen-in-ios"></a>Na tela de renderização no iOS

Quando o aplicativo precisa renderizar uma imagem de cores na tela no iOS, substituir os `Draw` método da `UIView` em questão como de costume. Por exemplo:

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

Como o iOS 10 com o `UIGraphicsImageRenderer` classe mostrada acima, de maneira inteligente decide se ele deve renderizar na sRGB ou sRGB de intervalo estendido o espaço de cores com base nos recursos do dispositivo iOS que o aplicativo está sendo executado quando o `Draw` método é chamado. Além disso, o `UIImageView` tem sido cor gerenciado desde o iOS 9.3.

Se o aplicativo precisa saber como o processamento está sendo feito em um `UIView` ou `UIViewController`, ele pode verificar o novo `DisplayGamut` propriedade o `UITraitCollection` classe. Esse valor será uma `UIDisplayGamut` enum das seguintes opções:

- P3
- SRGB
- Não especificado

Se o aplicativo deseja controle qual espaço de cores é usado para desenhar uma imagem, pode usar um novo `ContentsFormat` propriedade do `CALayer` para especificar o espaço de cor desejado. Esse valor pode ser um `CAContentsFormat` enum das seguintes opções:

- Gray8Uint
- Rgba16Float
- Rgba8Uint

### <a name="rendering-on-screen-in-macos"></a>Na tela de renderização no macOS

Quando o aplicativo precisa renderizar uma imagem de cores na tela no macOS, substituir os `DrawRect` método da `NSView` em questão como de costume. Por exemplo:

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

Novamente, ela de maneira inteligente decide se ele deve renderizar na sRGB ou sRGB de intervalo estendido o espaço de cores com base nos recursos de hardware Mac que o aplicativo está sendo executado quando o `DrawRect` método é chamado.

Se o aplicativo deseja controle qual espaço de cores é usado para desenhar uma imagem, pode usar um novo `DepthLimit` propriedade do `NSWindow` classe para especificar o espaço de cor desejado. Esse valor pode ser um `NSWindowDepth` enum das seguintes opções:

- OneHundredTwentyEightBitRgb
- SixtyfourBitRgb
- TwentyfourBitRgb

## <a name="summary"></a>Resumo

Este artigo cobriu cores e as maneiras que podem ser implementado e usado dentro de um aplicativo xamarin. IOS ou xamarin. Mac.



## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
