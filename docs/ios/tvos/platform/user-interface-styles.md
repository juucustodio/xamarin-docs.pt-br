---
title: "Novos estilos de interface do usuário"
description: "Este artigo aborda a luz e temas escuro de interface do usuário que Apple foi adicionado ao tvOS 10 e como implementá-los em um aplicativo Xamarin.tvOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 8BC37683-AD9E-45CD-BE40-96965618AD1D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: b970ff3570079c0e9fba10718ddcd75fbe857f2c
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="new-user-interface-styles"></a>Novos estilos de interface do usuário

_Este artigo aborda a luz e temas escuro de interface do usuário que Apple foi adicionado ao tvOS 10 e como implementá-los em um aplicativo Xamarin.tvOS._

tvOS 10 agora dá suporte a adaptar o tema um escuro e a Interface de usuário de luz todos UIKit interna controla será automaticamente, com base nas preferências do usuário. Além disso, o desenvolvedor pode ajustar manualmente os elementos de interface do usuário com base no tema que o usuário selecionou e pode substituir um determinado tema.


<a name="About-the-New-User-Interface-Styles" />

## <a name="about-the-new-user-interface-styles"></a>Sobre os novos estilos de Interface do usuário

Como mencionado acima, tvOS 10 agora dá suporte a adaptar o tema um escuro e a Interface de usuário de luz todos UIKit interna controla será automaticamente, com base nas preferências do usuário.

O usuário pode alternar este tema, vá para **configurações** > **geral** > **aparência** e alternar entre **luz**  e **escuro**:

[![](user-interface-styles-images/theme01.png "O aplicativo de configurações")](user-interface-styles-images/theme01.png#lightbox)

Quando o **escuro** tema for selecionado, todos os elementos de Interface do usuário alternará para texto claro em um plano de fundo escuro:

[![](user-interface-styles-images/theme02.png "O tema escuro")](user-interface-styles-images/theme02.png#lightbox)

O usuário tem a opção de alternar o tema a qualquer momento e pode fazer de acordo com a atividade atual, onde se encontra o Apple TV ou a hora do dia.

O tema de interface do usuário de luz é o tema padrão e quaisquer aplicativos tvOS existentes ainda usará o tema claro, independentemente das preferências do usuário, a menos que eles são modificados para tvOS 10 para tirar proveito do tema escuro. Um aplicativo tvOS 10 também tem a capacidade de substituir o tema atual e sempre usar o claro ou escuro tema algumas ou todas de sua interface do usuário.

<a name="Adopting-the-Light-and-Dark-Themes" />

## <a name="adopting-the-light-and-dark-themes"></a>Adotando os temas claro e escuros

Para dar suporte a esse recurso, a Apple adicionou uma nova API para o `UITraitCollection` classe e um aplicativo de tvOS devem participar para dar suporte a aparência escura (por meio de uma configuração em seu `Info.plist` arquivo).

Para aceitar o suporte de tema claro e escuro, faça o seguinte:

1. No **Gerenciador de Soluções**, clique duas vezes no arquivo `Info.plist` para abri-lo para edição.
2. Selecione o **fonte** exibição (da parte inferior do editor).
3. Adicione uma nova chave e chamá-lo `UIUserInterfaceStyle`: 

    [![](user-interface-styles-images/theme03.png "A chave UIUserInterfaceStyle")](user-interface-styles-images/theme03.png#lightbox)
4. Deixe o tipo definido `String` e insira um valor de `Automatic`: 

    [![](user-interface-styles-images/theme04.png "Insira automático")](user-interface-styles-images/theme04.png#lightbox)
5. Salve as alterações no arquivo.

Há três valores possíveis para a `UIUserInterfaceStyle` chave:

- **Luz** -força a interface do usuário do aplicativo tvOS para usar sempre o tema claro.
- **Escuro** -força a interface do usuário do aplicativo tvOS para usar sempre o tema escuro.
- **Automático** -alterna entre o tema claro e escuro com base nas preferências do usuário nas configurações. Essa é a configuração preferencial.

<a name="UIKit-Theme-Support" />

### <a name="uikit-theme-support"></a>Suporte a temas UIKit

Se um aplicativo de tvOS estiver usando o padrão e internos `UIView` com base em controles, eles responderá automaticamente para o tema de interface do usuário sem qualquer intervenção do desenvolvedor.

Além disso, `UILabel` e `UITextView` automaticamente alterará a cor com base no tema da interface do usuário selecione:

- O texto será preto o tema claro.
- O texto estará em branco no tema escuro.

Se o desenvolvedor mudar a cor do texto manualmente (tanto no código de Storyboard ou), será responsáveis por manipular as alterações de cores com base no tema da interface do usuário.

<a name="New-Blur-Effects" />

### <a name="new-blur-effects"></a>Novos efeitos de desfoque

Para dar suporte os temas claros e escuros em um aplicativo tvOS 10, a Apple adicionou dois novos desfoque efeitos. Esses efeitos novo ajustará automaticamente o desfoque com base no tema da interface do usuário que o usuário selecionado da seguinte maneira:

- `UIBlurEffectStyleRegular` -Usa a luz desfoque no tema claro e um escuro desfoque no tema escuro.
- `UIBlurEffectStyleProminent` -Usa um desfoque extremamente leve no tema claro e um desfoque extra escuro no tema escuro.

<a name="Working-with-Trait-Collections" />

## <a name="working-with-trait-collections"></a>Trabalhar com coleções de característica

O novo `UserInterfaceStyle` propriedade o `UITraitCollection` classe pode ser usada para obter o tema de interface do usuário selecionado no momento e será um `UIUserInterfaceStyle` enum de um dos seguintes valores:

- **Luz** -tema a luz de interface do usuário está selecionado.
- **Escuro** -a interface do usuário escuro tema for selecionado.
- **Não especificado** -o modo de exibição não foi exibido na tela ainda, portanto o tema de interface do usuário atual é desconhecido.

Além disso, as coleções de característica têm os seguintes recursos no tvOS 10:
 
- O proxy de aparência pode ser personalizado com base no `UserInterfaceStyle` de um determinado `UITraitCollection` para alterar as coisas como imagens ou item com base no tema de cores.
- Um aplicativo tvOS pode lidar com a característica de conjunto de alterações, substituindo o `TraitCollectionDidChange` método de um `UIView` ou `UIViewController` classe.

> [!IMPORTANT]
> **Observação:** totalmente não oferece suporte para a visualização inicial de Xamarin.tvOS para tvOS 10 `UIUserInterfaceStyle` para `UITraitCollection` ainda. Suporte completo será adicionado em uma versão futura.




<a name="Customizing-Appearance-Based-on-Theme" />

### <a name="customizing-appearance-based-on-theme"></a>Personalizando a aparência com base no tema

Para elementos de Interface do usuário que suportam o proxy de aparência, sua aparência pode ser ajustada com base no tema da interface do usuário de sua coleção de característica. Portanto, para um determinado elemento de interface do usuário, o desenvolvedor pode especificar uma cor para o tema claro e outra cor do tema escuro.

```csharp
button.SetTitleColor (UIColor.Red, UIControlState.Normal);

// TODO - Pseudocode because this isn't currently supported in the preview bindings.
var light = new UITraitCollection(UIUserInterfaceStyle.Light);
var dark = new UITraitCollection(UIUserInterfaceStyle.Dark);

button.ForTraitCollection(light).SetTitleColor (UIColor.Red, UIControlState.Normal);
button.ForTraitCollection(dark).SetTitleColor (UIColor.White, UIControlState.Normal);
```

> [!IMPORTANT]
> **Observação:** Infelizmente, a visualização de Xamarin.tvOS para tvOS 10 não suportam totalmente `UIUserInterfaceStyle` para `UITraitCollection`, portanto, esse tipo de personalização ainda não está disponível. Suporte completo será adicionado em uma versão futura.

<a name="Responding-to-Theme-Changes-Directly" />

### <a name="responding-to-theme-changes-directly"></a>Responder a alterações de tema diretamente

Em que o desenvolvedor requer mais controle sobre a aparência de um elemento de interface do usuário com base no tema da interface do usuário selecionado, eles podem substituir o `TraitCollectionDidChange` método de um `UIView` ou `UIViewController` classe.

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

Com base no design de um aplicativo tvOS, pode haver momentos em que o desenvolvedor precisa substituir a coleção de característica de um determinado elemento de Interface do usuário e a use sempre um tema de interface do usuário específico.

Isso pode ser feito usando o `SetOverrideTraitCollection` método sobre o `UIViewController` classe. Por exemplo:

```csharp
// Create new trait and configure it
var trait = new UITraitCollection ();
...

// Apply new trait collection
SetOverrideTraitCollection (trait, this);
```

Para obter mais informações, consulte o [características](~/ios/user-interface/storyboards/unified-storyboards.md) e [características substituindo](~/ios/user-interface/storyboards/unified-storyboards.md) seções do nosso [Introdução ao Unified Storyboards](~/ios/user-interface/storyboards/unified-storyboards.md) documentação.

<a name="Trait-Collections-and-Storyboards" />

### <a name="trait-collections-and-storyboards"></a>Storyboards e coleções de característica

Em 10 de tvOS, Storyboard do aplicativo pode ser definido para responder a característica coleções e muitos elementos de interface do usuário podem ser informados tema claro e escuro. Visualização inicial Xamarin.tvOS atual para tvOS 10 ainda não dá suporte esse recurso no Designer de Interface, então o Storyboard precisará ser editado no construtor de Interface do Xcode como uma solução alternativa.

Para habilitar o suporte de coleção de característica, faça o seguinte:

1. Clique com botão direito no arquivo de Storyboard no **Solution Explorer** e selecione **abrir com** > **Xcode Interface Builder**: 

    [![](user-interface-styles-images/theme05.png "Abrir com o Xcode Interface construtor")](user-interface-styles-images/theme05.png#lightbox) 
2. Para habilitar o suporte de coleção de característica, alterne para o **Inspetor de arquivo** e verifique o **Use característica variações** propriedade no **documento de construtor da Interface** seção: 

    [![](user-interface-styles-images/theme06.png "Habilitar suporte a coleção de característica")](user-interface-styles-images/theme06.png#lightbox)
3. Confirme a alteração para usar a característica variações: 

    [![](user-interface-styles-images/theme07.png "O alerta de característica variações de uso")](user-interface-styles-images/theme07.png#lightbox)
4. Salve as alterações no arquivo de Storyboard.

Apple adicionou os seguintes recursos ao editar tvOS Storyboards no construtor de Interface:

* O desenvolvedor pode especificar diferentes variações de elementos de Interface de usuário com base no tema da interface do usuário no **Inspetor de atributo**:
    
    * Várias propriedades agora tem um  **+**  ao lado deles que pode ser clicado para adicionar uma versão específica do tema de interface do usuário: 

        [![](user-interface-styles-images/theme08.png "Adicionar uma versão específica do tema de interface do usuário")](user-interface-styles-images/theme08.png#lightbox) 
    
    * O desenvolvedor pode especificar uma nova propriedade ou clique no **x** botão para removê-lo: 

        [![](user-interface-styles-images/theme09.png "Especifique uma nova propriedade ou clique no botão x para removê-lo")](user-interface-styles-images/theme09.png#lightbox)
* O desenvolvedor pode visualizar um design de interface do usuário no tema a claro ou escuro de dentro de construtor da Interface:
    
    * A parte inferior da superfície de Design permite que o desenvolvedor alternar o tema de interface do usuário atual: 

        [![](user-interface-styles-images/theme10.png "A parte inferior da superfície de Design")](user-interface-styles-images/theme10.png#lightbox)
        
    * O novo tema será exibido no construtor de Interface e os ajustes de coleção de característica específicos serão exibidos: 

        [![](user-interface-styles-images/theme11.png "O tema exibido no construtor de Interface")](user-interface-styles-images/theme11.png#lightbox)

Além disso, o simulador tvOS agora tem um atalho de teclado para permitir que o desenvolvedor alternar rapidamente entre os temas claros e escuros ao depurar um aplicativo tvOS. Use o **comando-Shift-D** sequência para alternar entre claro e escuro de teclado.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou claro e escuro temas de interface do usuário que Apple tenha adicionado ao tvOS 10 e como implementá-los em um aplicativo Xamarin.tvOS.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [O que há de novo no tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
