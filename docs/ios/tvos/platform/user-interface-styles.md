---
title: Estilos de interface do usuário tvOS no Xamarin
description: Este artigo aborda os temas de interface do usuário leves e escuros que a Apple adicionou ao tvOS 10 e como implementá-los em um aplicativo Xamarin. tvOS.
ms.prod: xamarin
ms.assetid: 8BC37683-AD9E-45CD-BE40-96965618AD1D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 793a82b7bb88f3e0c0a233c8c18c2d90f741b137
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434766"
---
# <a name="tvos-user-interface-styles-in-xamarin"></a>Estilos de interface do usuário tvOS no Xamarin

_Este artigo aborda os temas de interface do usuário leves e escuros que a Apple adicionou ao tvOS 10 e como implementá-los em um aplicativo Xamarin. tvOS._

o tvOS 10 agora dá suporte a um tema de interface do usuário escuro e leve que todos os controles de UIKit de compilação se adaptam automaticamente ao, com base nas preferências do usuário. Além disso, o desenvolvedor pode ajustar manualmente os elementos da interface do usuário com base no tema que ele selecionou e pode substituir um determinado tema.

<a name="About-the-New-User-Interface-Styles"></a>

## <a name="about-the-new-user-interface-styles"></a>Sobre os novos estilos de interface do usuário

Como mencionado acima, o tvOS 10 agora dá suporte a um tema de interface do usuário escuro e leve que todos os controles de UIKit de compilação se adaptam automaticamente ao, com base nas preferências do usuário.

O usuário pode mudar esse tema acessando **configurações**  >  **General**  >  **aparência** geral e alternando entre **claro** e **escuro**:

[![O aplicativo configurações](user-interface-styles-images/theme01.png)](user-interface-styles-images/theme01.png#lightbox)

Quando o tema **escuro** for selecionado, todos os elementos da interface do usuário mudarão para texto claro em um plano de fundo escuro:

[![O tema escuro](user-interface-styles-images/theme02.png)](user-interface-styles-images/theme02.png#lightbox)

O usuário tem a opção de alternar o tema a qualquer momento e pode fazer isso com base na atividade atual, onde a Apple TV está localizada ou na hora do dia.

O tema da interface do usuário leve é o tema padrão, e todos os aplicativos tvOS existentes ainda usarão o tema claro, independentemente das preferências do usuário, a menos que sejam modificados para tvOS 10 para tirar proveito do tema escuro. Um aplicativo tvOS 10 também tem a capacidade de substituir o tema atual e sempre usar o tema claro ou escuro para algumas ou todas as suas interfaces do usuário.

<a name="Adopting-the-Light-and-Dark-Themes"></a>

## <a name="adopting-the-light-and-dark-themes"></a>Adotando os temas claros e escuros

Para dar suporte a esse recurso, a Apple adicionou uma nova API à `UITraitCollection` classe e um aplicativo tvOS deve optar por dar suporte à aparência escura (por meio de uma configuração em seu `Info.plist` arquivo).

Para aceitar o suporte a temas leves e escuros, faça o seguinte:

1. No **Gerenciador de Soluções**, clique duas vezes no arquivo `Info.plist` para abri-lo para edição.
2. Selecione o modo de exibição de **origem** (na parte inferior do editor).
3. Adicione uma nova chave e chame-a `UIUserInterfaceStyle` :

    [![A chave UIUserInterfaceStyle](user-interface-styles-images/theme03.png)](user-interface-styles-images/theme03.png#lightbox)
4. Deixe o tipo definido como `String` e insira um valor de `Automatic` :

    [![Inserir automático](user-interface-styles-images/theme04.png)](user-interface-styles-images/theme04.png#lightbox)
5. Salve as alterações no arquivo.

Há três valores possíveis para a `UIUserInterfaceStyle` chave:

- **Leve** -força a interface do usuário do aplicativo tvOS a sempre usar o tema claro.
- **Dark** -força a interface do usuário do aplicativo tvOS a sempre usar o tema escuro.
- **Automático** – alterna entre o tema claro e escuro com base nas preferências do usuário em configurações. Essa é a configuração preferida.

<a name="UIKit-Theme-Support"></a>

### <a name="uikit-theme-support"></a>Suporte ao tema do UIKit

Se um aplicativo tvOS estiver usando controles internos baseados em padrão `UIView` , eles responderão automaticamente ao tema da interface do usuário sem qualquer intervenção do desenvolvedor.

Além disso, `UILabel` e `UITextView` o alterará automaticamente sua cor com base no tema selecionar interface do usuário:

- O texto será preto no tema claro.
- O texto será branco no tema escuro.

Se o desenvolvedor mudar a cor do texto manualmente (no storyboard ou no código), ele será responsável por manipular as alterações de cores com base no tema da interface do usuário.

<a name="New-Blur-Effects"></a>

### <a name="new-blur-effects"></a>Novos efeitos de desfoque

Para dar suporte aos temas claros e escuros em um aplicativo tvOS 10, a Apple adicionou dois novos efeitos de desfoque. Esses novos efeitos ajustarão automaticamente o desfoque com base no tema da interface de usuário que o usuário selecionou da seguinte maneira:

- `UIBlurEffectStyleRegular` -Usa um desfoque claro no tema claro e um desfoque escuro no tema escuro.
- `UIBlurEffectStyleProminent` -Usa um desfoque extra-claro no tema claro e um desfoque extra escuro no tema escuro.

<a name="Working-with-Trait-Collections"></a>

## <a name="working-with-trait-collections"></a>Trabalhando com coleções de características

A nova `UserInterfaceStyle` propriedade da `UITraitCollection` classe pode ser usada para obter o tema da interface do usuário selecionado no momento e será uma `UIUserInterfaceStyle` enumeração de um dos seguintes valores:

- **Claro** -o tema da interface do usuário leve está selecionado.
- **Escuro** -o tema da interface do usuário escura está selecionado.
- Não **especificado** -a exibição ainda não foi exibida para a tela, portanto, o tema da interface do usuário atual é desconhecido.

Além disso, as coleções de características têm os seguintes recursos no tvOS 10:

- O proxy de aparência pode ser personalizado com base no `UserInterfaceStyle` de um determinado `UITraitCollection` para alterar itens como imagens ou cores de itens com base no tema.
- Um aplicativo tvOS pode lidar com alterações de coleção de características, substituindo o `TraitCollectionDidChange` método de uma `UIView` `UIViewController` classe ou.

> [!IMPORTANT]
> A visualização antecipada do Xamarin. tvOS para o tvOS 10 não oferece suporte completo `UIUserInterfaceStyle` para o `UITraitCollection` ainda. O suporte completo será adicionado em uma versão futura.

<a name="Customizing-Appearance-Based-on-Theme"></a>

### <a name="customizing-appearance-based-on-theme"></a>Personalizando a aparência com base no tema

Para elementos da interface do usuário que dão suporte ao proxy de aparência, sua aparência pode ser ajustada com base no tema da interface do usuário de sua coleção de características. Portanto, para um determinado elemento de interface do usuário, o desenvolvedor pode especificar uma cor para o tema claro e outra cor para o tema escuro.

```csharp
button.SetTitleColor (UIColor.Red, UIControlState.Normal);

// TODO - Pseudocode because this isn't currently supported in the preview bindings.
var light = new UITraitCollection(UIUserInterfaceStyle.Light);
var dark = new UITraitCollection(UIUserInterfaceStyle.Dark);

button.ForTraitCollection(light).SetTitleColor (UIColor.Red, UIControlState.Normal);
button.ForTraitCollection(dark).SetTitleColor (UIColor.White, UIControlState.Normal);
```

> [!IMPORTANT]
> Infelizmente, a versão prévia do Xamarin. tvOS para o tvOS 10 não dá suporte total `UIUserInterfaceStyle` para `UITraitCollection` o, portanto, esse tipo de personalização ainda não está disponível. O suporte completo será adicionado em uma versão futura.

<a name="Responding-to-Theme-Changes-Directly"></a>

### <a name="responding-to-theme-changes-directly"></a>Respondendo diretamente às alterações de tema

No desenvolvedor requer um controle mais profundo sobre a aparência de um elemento de interface do usuário com base no tema da interface do usuário selecionado, eles podem substituir o `TraitCollectionDidChange` método de uma `UIView` `UIViewController` classe ou.

Por exemplo:

```csharp
public override void TraitCollectionDidChange (UITraitCollection previousTraitCollection)
{
    base.TraitCollectionDidChange (previousTraitCollection);

    // Take action based on the Light or Dark theme
    ...
}
```

<a name="Responding-to-Theme-Changes-Directly"></a>

### <a name="overriding-a-trait-collection"></a>Substituindo uma coleção de características

Com base no design de um aplicativo tvOS, pode haver ocasiões em que o desenvolvedor precise substituir a coleção de características de um determinado elemento de interface do usuário e fazer com que ele sempre use um tema de interface de usuário específico.

Isso pode ser feito usando o `SetOverrideTraitCollection` método na `UIViewController` classe. Por exemplo:

```csharp
// Create new trait and configure it
var trait = new UITraitCollection ();
...

// Apply new trait collection
SetOverrideTraitCollection (trait, this);
```

Para obter mais informações, consulte as seções [características](~/ios/user-interface/storyboards/unified-storyboards.md) e [substituição de características](~/ios/user-interface/storyboards/unified-storyboards.md) da nossa introdução à documentação de [storyboards unificadas](~/ios/user-interface/storyboards/unified-storyboards.md) .

<a name="Trait-Collections-and-Storyboards"></a>

### <a name="trait-collections-and-storyboards"></a>Coleções de características e storyboards

No tvOS 10, o storyboard de um aplicativo pode ser definido para responder a coleções de características e muitos elementos da interface do usuário podem se tornar um reconhecimento claro e escuro do tema. A visualização antecipada atual do Xamarin. tvOS para o tvOS 10 não dá suporte a esse recurso no designer de interface ainda, portanto, o storyboard precisará ser editado no Interface Builder do Xcode como uma solução alternativa.

Para habilitar o suporte à coleta de características, faça o seguinte:

1. Clique com o botão direito do mouse no arquivo de storyboard na **Gerenciador de soluções** e selecione **abrir com**o  >  **Xcode Interface Builder**:

    [![Abrir com o Xcode Interface Builder](user-interface-styles-images/theme05.png)](user-interface-styles-images/theme05.png#lightbox)
2. Para habilitar o suporte à coleta de características, alterne para o **Inspetor de arquivo** e marque a propriedade **usar variações de características** na seção **interface Builder documento** :

    [![Habilitar suporte à coleta de características](user-interface-styles-images/theme06.png)](user-interface-styles-images/theme06.png#lightbox)
3. Confirme a alteração para usar variações de características:

    [![O alerta usar variações de características](user-interface-styles-images/theme07.png)](user-interface-styles-images/theme07.png#lightbox)
4. Salve as alterações no arquivo de storyboard.

A Apple adicionou as seguintes habilidades ao editar storyboards tvOS no Interface Builder:

- O desenvolvedor pode especificar diferentes variações de elementos da interface do usuário com base no tema da interface do utilizador no **Inspetor de atributos**:

  - Várias propriedades agora têm um **+** ao lado delas, que podem ser clicadas para adicionar uma versão específica de tema de interface do usuário:

    [![Adicionar uma versão específica de tema de interface do usuário](user-interface-styles-images/theme08.png)](user-interface-styles-images/theme08.png#lightbox)

  - O desenvolvedor pode especificar uma nova propriedade ou clicar no botão **x** para removê-la:

    [![Especifique uma nova propriedade ou clique no botão x para removê-la](user-interface-styles-images/theme09.png)](user-interface-styles-images/theme09.png#lightbox)
- O desenvolvedor pode visualizar um design de interface do usuário no tema claro ou escuro de dentro Interface Builder:

  - A parte inferior da Design Surface permite que o desenvolvedor Alterne o tema da interface do usuário atual:

    [![A parte inferior da Design Surface](user-interface-styles-images/theme10.png)](user-interface-styles-images/theme10.png#lightbox)

  - O novo tema será exibido no Interface Builder e quaisquer ajustes específicos da coleção de características serão exibidos:

    [![O tema exibido no Interface Builder](user-interface-styles-images/theme11.png)](user-interface-styles-images/theme11.png#lightbox)

Além disso, o simulador tvOS agora tem um atalho de teclado para permitir que o desenvolvedor Alterne rapidamente entre os temas leves e escuros ao depurar um aplicativo tvOS. Use a sequência de teclado **Command-Shift-D** para alternar entre Light e Dark.

<a name="Summary"></a>

## <a name="summary"></a>Resumo

Este artigo abordou os temas de interface do usuário leves e escuros que a Apple adicionou ao tvOS 10 e como implementá-los em um aplicativo Xamarin. tvOS.

## <a name="related-links"></a>Links Relacionados

- [Exemplos do tvOS](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [O que há de novo no tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)