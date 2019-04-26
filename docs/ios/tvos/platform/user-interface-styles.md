---
title: Estilos de interface do usuário no Xamarin do tvOS
description: Este artigo aborda a luz e temas escuro de interface do usuário que a Apple adicionado ao tvOS 10 e como implementá-los em um aplicativo xamarin. tvos.
ms.prod: xamarin
ms.assetid: 8BC37683-AD9E-45CD-BE40-96965618AD1D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 2536ca5d3bff3f5b7962bc4fcf58b31a130fd03c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61270802"
---
# <a name="tvos-user-interface-styles-in-xamarin"></a>Estilos de interface do usuário no Xamarin do tvOS

_Este artigo aborda a luz e temas escuro de interface do usuário que a Apple adicionado ao tvOS 10 e como implementá-los em um aplicativo xamarin. tvos._

tvOS 10 agora dá suporte a tema um escuro e a Interface do usuário de luz que todos o build no UIKit controles serão automaticamente adaptar, com base nas preferências do usuário. Além disso, o desenvolvedor pode ajustar manualmente os elementos de interface do usuário com base no tema que o usuário selecionou e pode substituir um determinado tema.

<a name="About-the-New-User-Interface-Styles" />

## <a name="about-the-new-user-interface-styles"></a>Sobre os novos estilos de Interface do usuário

Como mencionado acima, tvOS 10 agora dá suporte a tema um escuro e a Interface do usuário de luz que todos o build no UIKit controles serão automaticamente adaptar, com base nas preferências do usuário.

O usuário pode alternar este tema indo até **as configurações** > **geral** > **aparência** e alternar entre **luz**  e **escuro**:

[![](user-interface-styles-images/theme01.png "O aplicativo de configurações")](user-interface-styles-images/theme01.png#lightbox)

Quando o **escuro** tema for selecionado, todos os elementos de Interface do usuário serão alternado para o texto claro em um plano de fundo escuro:

[![](user-interface-styles-images/theme02.png "O tema escuro")](user-interface-styles-images/theme02.png#lightbox)

O usuário tem a opção de alternar o tema a qualquer momento e pode fazer isso com base na atividade atual, em que a Apple TV está localizado ou a hora do dia.

O tema de interface do usuário de luz é o tema padrão e quaisquer aplicativos existentes do tvOS ainda usar o tema claro, independentemente das preferências do usuário, a menos que eles são modificados para tvOS 10 podem aproveitar o tema escuro. Um aplicativo tvOS 10 também tem a capacidade de substituir o tema atual e sempre usar o tema claro ou escuro para alguns ou todos de sua interface do usuário.

<a name="Adopting-the-Light-and-Dark-Themes" />

## <a name="adopting-the-light-and-dark-themes"></a>Adotando os temas claras e escuros

Para dar suporte a esse recurso, a Apple adicionou uma nova API para o `UITraitCollection` classe e um aplicativo tvOS deverão participar para dar suporte a aparência escura (por meio de uma configuração no seu `Info.plist` arquivo).

Para aceitar o suporte de tema claro e escuro, faça o seguinte:

1. No **Gerenciador de Soluções**, clique duas vezes no arquivo `Info.plist` para abri-lo para edição.
2. Selecione o **origem** exibição (da parte inferior do editor).
3. Adicione uma nova chave e chamá-lo `UIUserInterfaceStyle`: 

    [![](user-interface-styles-images/theme03.png "A chave UIUserInterfaceStyle")](user-interface-styles-images/theme03.png#lightbox)
4. Deixe o tipo definido como `String` e insira um valor de `Automatic`: 

    [![](user-interface-styles-images/theme04.png "Insira automático")](user-interface-styles-images/theme04.png#lightbox)
5. Salve as alterações no arquivo.

Há três valores possíveis para o `UIUserInterfaceStyle` chave:

- **Luz** -força a interface de usuário do aplicativo tvOS sempre usar o tema claro.
- **Escuro** -força a interface de usuário do aplicativo tvOS sempre usar o tema escuro.
- **Automático** -alterna entre o tema claro e escuro com base nas preferências do usuário nas configurações. Essa é a configuração preferencial.

<a name="UIKit-Theme-Support" />

### <a name="uikit-theme-support"></a>Suporte a temas de UIKit

Se um aplicativo tvOS é usando o padrão e internos `UIView` com base em controles, eles serão automaticamente responder ao tema da interface do usuário sem qualquer intervenção do desenvolvedor.

Além disso, `UILabel` e `UITextView` vai alterar automaticamente sua cor com base no tema da interface do usuário selecione:

- O texto será preto no tema no claro.
- O texto será branco no tema escuro.

Se o desenvolvedor mudar a cor do texto manualmente (seja no código ou Storyboard), será responsáveis por manipular as alterações de cores com base no tema da interface do usuário.

<a name="New-Blur-Effects" />

### <a name="new-blur-effects"></a>Novos efeitos de desfoque

Para dar suporte a temas claro e escuro em um aplicativo tvOS 10, a Apple adicionou dois efeitos de desfoque novo. Esses novos efeitos ajustará automaticamente o desfoque com base no tema da interface do usuário que o usuário tiver selecionado da seguinte maneira:

- `UIBlurEffectStyleRegular` -Usa uma luz de desfoque no tema no claro e um escuro desfoque no tema escuro.
- `UIBlurEffectStyleProminent` -Usa um desfoque extremamente claro no tema no claro e um desfoque extra escuro no tema escuro.

<a name="Working-with-Trait-Collections" />

## <a name="working-with-trait-collections"></a>Trabalhar com coleções de característica

O novo `UserInterfaceStyle` propriedade do `UITraitCollection` classe pode ser usado para obter o tema da interface do usuário selecionado no momento e será um `UIUserInterfaceStyle` enum de um dos seguintes valores:

- **Luz** -tema a luz de interface do usuário está selecionado.
- **Escuro** -tema a interface do usuário escuro for selecionado.
- **Não especificado** -a exibição não foi exibida na tela ainda, portanto, o tema da interface do usuário atual é desconhecido.

Além disso, coleções de característica têm os seguintes recursos no tvOS 10:
 
- O proxy de aparência pode ser personalizado com base nas `UserInterfaceStyle` de um determinado `UITraitCollection` para alterar coisas como imagens ou item com base no tema de cores.
- Um aplicativo tvOS pode lidar com a coleção de característica é alterado, substituindo o `TraitCollectionDidChange` método de um `UIView` ou `UIViewController` classe.

> [!IMPORTANT]
> O xamarin. tvos visualização prévia para tvOS 10 não oferece suporte completo `UIUserInterfaceStyle` para `UITraitCollection` ainda. Suporte completo será adicionado em uma versão futura.




<a name="Customizing-Appearance-Based-on-Theme" />

### <a name="customizing-appearance-based-on-theme"></a>Personalizando a aparência com base no tema

Para elementos de Interface do usuário que dão suporte o proxy de aparência, sua aparência pode ser ajustada com base no tema da interface do usuário de sua coleção de característica. Portanto, para um determinado elemento de interface do usuário, o desenvolvedor pode especificar uma cor para o tema claro e outra cor para o tema escuro.

```csharp
button.SetTitleColor (UIColor.Red, UIControlState.Normal);

// TODO - Pseudocode because this isn't currently supported in the preview bindings.
var light = new UITraitCollection(UIUserInterfaceStyle.Light);
var dark = new UITraitCollection(UIUserInterfaceStyle.Dark);

button.ForTraitCollection(light).SetTitleColor (UIColor.Red, UIControlState.Normal);
button.ForTraitCollection(dark).SetTitleColor (UIColor.White, UIControlState.Normal);
```

> [!IMPORTANT]
> Infelizmente, a visualização de tvos para tvOS 10 não oferece suporte completo `UIUserInterfaceStyle` para `UITraitCollection`, portanto, esse tipo de personalização ainda não está disponível. Suporte completo será adicionado em uma versão futura.

<a name="Responding-to-Theme-Changes-Directly" />

### <a name="responding-to-theme-changes-directly"></a>Responder a alterações de tema diretamente

O desenvolvedor requer maior controle sobre a aparência de um elemento de interface do usuário com base no tema da interface do usuário selecionado, eles podem substituir os `TraitCollectionDidChange` método de um `UIView` ou `UIViewController` classe.

Por exemplo:

```csharp
public override void TraitCollectionDidChange (UITraitCollection previousTraitCollection)
{
    base.TraitCollectionDidChange (previousTraitCollection);
    
    // Take action based on the Light or Dark theme
    ...
}
```

<a name="Responding-to-Theme-Changes-Directly" />

### <a name="overriding-a-trait-collection"></a>Substituindo uma coleção de característica

Com base no design de um aplicativo tvOS, pode haver ocasiões em que o desenvolvedor precisa substituir a coleção de característica de um determinado elemento de Interface do usuário e fazer com que ele use sempre um tema específico da interface do usuário.

Isso pode ser feito usando o `SetOverrideTraitCollection` método no `UIViewController` classe. Por exemplo:

```csharp
// Create new trait and configure it
var trait = new UITraitCollection ();
...

// Apply new trait collection
SetOverrideTraitCollection (trait, this);
```

Para obter mais informações, consulte o [Traits](~/ios/user-interface/storyboards/unified-storyboards.md) e [substituindo características](~/ios/user-interface/storyboards/unified-storyboards.md) seções do nosso [Introduction to Storyboards unificados](~/ios/user-interface/storyboards/unified-storyboards.md) documentação.

<a name="Trait-Collections-and-Storyboards" />

### <a name="trait-collections-and-storyboards"></a>Coleções de característica e storyboards

No tvOS 10, o Storyboard de um aplicativo pode ser definido para responder a característica de coleções e muitos elementos de interface do usuário podem ser informados tema claro e escuro. O atual do xamarin. tvos visualização prévia para tvOS 10 ainda não suporta esse recurso no Designer de Interface, portanto, o Storyboard precisará ser editado no Interface Builder do Xcode, como uma solução alternativa.

Para habilitar o suporte de coleção de característica, faça o seguinte:

1. Clique com botão direito no arquivo de Storyboard na **Gerenciador de soluções** e selecione **abrir com** > **Xcode Interface Builder**: 

    [![](user-interface-styles-images/theme05.png "Abrir com o Xcode Interface Builder")](user-interface-styles-images/theme05.png#lightbox) 
2. Para habilitar o suporte de coleção de característica, alterne para o **Inspetor de arquivo** e verifique o **variações de características de uso** propriedade no **documento do construtor de Interface** seção: 

    [![](user-interface-styles-images/theme06.png "Habilitar o suporte de coleção de característica")](user-interface-styles-images/theme06.png#lightbox)
3. Confirme a alteração para usar variações de características: 

    [![](user-interface-styles-images/theme07.png "O alerta de variações de características de uso")](user-interface-styles-images/theme07.png#lightbox)
4. Salve as alterações no arquivo de Storyboard.

Apple adicionou as seguintes capacidades ao editar Storyboards do tvOS no Interface Builder:

* O desenvolvedor pode especificar diferentes variações de elementos de Interface do usuário com base no tema de interface do usuário na **Inspetor de atributo**:
    
    * Várias propriedades agora tem um **+** ao lado deles que pode ser clicado para adicionar uma versão específica do tema de interface do usuário: 

        [![](user-interface-styles-images/theme08.png "Adicionar uma versão específica do tema de interface do usuário")](user-interface-styles-images/theme08.png#lightbox) 
    
    * O desenvolvedor pode especificar uma nova propriedade ou clique no **x** botão para removê-lo: 

        [![](user-interface-styles-images/theme09.png "Especifique uma nova propriedade ou clique no botão x para removê-lo")](user-interface-styles-images/theme09.png#lightbox)
* O desenvolvedor pode visualizar um design de interface do usuário no tema claro ou escuro de dentro do Interface Builder:
    
    * A parte inferior da superfície de Design permite que o desenvolvedor alternar o tema da interface do usuário atual: 

        [![](user-interface-styles-images/theme10.png "A parte inferior da superfície de Design")](user-interface-styles-images/theme10.png#lightbox)
        
    * O novo tema será exibido na Interface Builder e os ajustes específicos da característica coleção serão exibidos: 

        [![](user-interface-styles-images/theme11.png "O tema exibido na Interface Builder")](user-interface-styles-images/theme11.png#lightbox)

Além disso, o simulador tvOS agora tem um atalho de teclado para permitir que o desenvolvedor alterne rapidamente entre os temas claro e escuro ao depurar um aplicativo tvOS. Use o **comando-Shift-D** sequência para alternar entre claro e escuro do teclado.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou a luz e temas escuro de interface do usuário que a Apple adicionado ao tvOS 10 e como implementá-los em um aplicativo xamarin. tvos.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [O que há de novo no tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
