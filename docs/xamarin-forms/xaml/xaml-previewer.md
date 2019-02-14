---
title: Pré-visualizador XAML para xamarin. Forms
description: Este artigo explica como usar o Visualizador de XAML para ver seus layouts do xamarin. Forms renderizados conforme você digita. O pré-visualizador XAML está disponível no Visual Studio 2017, Visual Studio para Mac e Visual Studio 2019 (visualização).
zone_pivot_groups: platform-dev16
ms.prod: xamarin
ms.assetid: 84769ff1-72fd-4c44-8251-dd6d5bf8c7b2
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 02/04/2019
ms.openlocfilehash: 4258eca8e18229420ceb043a010c896137187653
ms.sourcegitcommit: c6ff24b524d025d7e87b7b9c25f04c740dd93497
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56240377"
---
# <a name="xaml-previewer-for-xamarinforms"></a>Pré-visualizador XAML para xamarin. Forms

_Consulte seus layouts do xamarin. Forms renderizados conforme você digita!_

## <a name="requirements"></a>Requisitos

Os projetos requerem o pacote mais recente do NuGet xamarin. Forms para o pré-visualizador de XAML trabalhar. Visualização de aplicativos Android requer [JDK 1.8 x64](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Há mais informações na [notas de versão](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#Xamarin_Forms_Previewer).

## <a name="getting-started"></a>Introdução

O pré-visualizador de XAML do xamarin. Forms mostra uma visualização específica da plataforma do seu arquivo XAML em tempo real, enquanto você editá-lo.

::: zone pivot="windows"

### <a name="visual-studio"></a>Visual Studio

O pré-visualizador XAML está disponível ao expandir o painel de exibição de divisão. Comportamento do modo de exibição de divisão padrão pode ser controlada com o **Ferramentas > Opções > Xamarin > pré-visualizador formulários** caixa de diálogo. Nesta caixa de diálogo, você pode selecionar o modo de exibição de documento padrão e a orientação de divisão.

[![Opções de pré-visualizador do xamarin. Forms no Visual Studio](xaml-previewer-images/xamlp-options-vs-sm.png "opções do Visualizador do xamarin. Forms no Visual Studio")](xaml-previewer-images/xamlp-options-vs-lg.png#lightbox)

Ao abrir uma página XAML, o editor será dividido com base nas configurações selecionadas na **Ferramentas > Opções > Xamarin > formulários pré-visualizador** caixa de diálogo. No entanto, a divisão pode ser alterada para cada arquivo na janela do editor.

#### <a name="xaml-preview-controls"></a>Controles de visualização do XAML

A parte superior da janela do editor tem botões para selecionar qual painel está em uso, com o botão superior alternando para o painel de design e o botão inferior alternando para o painel de fonte. O botão do meio alterna a ordem do painel.

[![Controla o pré-visualizador de xamarin. Forms para alternar entre o design, a origem e o modo de divisão no Visual Studio](xaml-previewer-images/xamlp-controls-splitview-vs-sm.png "controla o pré-visualizador de xamarin. Forms para alternar entre o design, a origem e o modo de divisão no Visual Studio")](xaml-previewer-images/xamlp-controls-splitview-vs-lg.png#lightbox)

A parte inferior da janela do editor tem botões para os painéis, de dividir verticalmente e horizontalmente para expandir ou recolher o painel sub atual.

[![Controles de orientação de painel pré-visualizador de xamarin. Forms no Visual Studio](xaml-previewer-images/xamlp-controls-orientation-vs-sm.png "controles de orientação de painel pré-visualizador de xamarin. Forms no Visual Studio")](xaml-previewer-images/xamlp-controls-orientation-vs-lg.png#lightbox)

::: zone-end
::: zone pivot="macos"

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

O **visualização** botão é exibido no editor quando você abre uma página XAML. O painel de visualização pode ser mostrado ou ocultado, pressionando as **visualização** botão no canto superior direito de qualquer janela de documento XAML:

[![Pré-visualizador do xamarin. Forms no Visual Studio para Mac](xaml-previewer-images/xamlp-list-sml.png "pré-visualizador de xamarin. Forms no Visual Studio para Mac")](xaml-previewer-images/xamlp-list.png#lightbox)

::: zone-end
::: zone pivot="win-dev16"

### <a name="visual-studio-2019-preview"></a>Visual Studio 2019 (visualização)

O pré-visualizador XAML está disponível ao expandir o painel de exibição de divisão. Comportamento do modo de exibição de divisão padrão pode ser controlada com o **Ferramentas > Opções > Xamarin > pré-visualizador formulários** caixa de diálogo. Nesta caixa de diálogo, você pode selecionar o modo de exibição de documento padrão e a orientação de divisão.

[![Opções de pré-visualizador do xamarin. Forms no Visual Studio](xaml-previewer-images/xamlp-options-vs-sm.png "opções do Visualizador do xamarin. Forms no Visual Studio")](xaml-previewer-images/xamlp-options-vs-lg.png#lightbox)

Ao abrir uma página XAML, o editor será dividido com base nas configurações selecionadas na **Ferramentas > Opções > Xamarin > formulários pré-visualizador** caixa de diálogo. No entanto, a divisão pode ser alterada para cada arquivo na janela do editor.

#### <a name="xaml-preview-controls"></a>Controles de visualização do XAML

A parte superior da janela do editor tem botões para selecionar qual painel está em uso, com o botão superior alternando para o painel de design e o botão inferior alternando para o painel de fonte. O botão do meio alterna a ordem do painel.

[![Controla o pré-visualizador de xamarin. Forms para alternar entre o design, a origem e o modo de divisão no Visual Studio](xaml-previewer-images/xamlp-controls-splitview-vs-sm.png "controla o pré-visualizador de xamarin. Forms para alternar entre o design, a origem e o modo de divisão no Visual Studio")](xaml-previewer-images/xamlp-controls-splitview-vs-lg.png#lightbox)

A parte inferior da janela do editor tem botões para os painéis, de dividir verticalmente e horizontalmente para expandir ou recolher o painel sub atual.

[![Controles de orientação de painel pré-visualizador de xamarin. Forms no Visual Studio](xaml-previewer-images/xamlp-controls-orientation-vs-sm.png "controles de orientação de painel pré-visualizador de xamarin. Forms no Visual Studio")](xaml-previewer-images/xamlp-controls-orientation-vs-lg.png#lightbox)

::: zone-end

## <a name="xaml-previewer-options"></a>Opções do Visualizador de XAML

As opções na parte superior do painel de visualização são:

* **Telefone** – versão prévia em uma tela do telefone em tamanho normal
* **Tablet** -visualização em uma tela de tablet em tamanho normal
* **Android** – mostrar a versão do Android da tela
* **iOS** – mostra a versão do iOS da tela (*Observação: Se você estiver usando o Visual Studio no Windows, você deve ser [emparelhado com um Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) para usar esse modo*)
* **Retrato (ícone)** – usa a orientação de retrato para a versão prévia
* **Paisagem (ícone)** – usa paisagem orientação para a versão prévia

::: zone pivot="win-dev16"

## <a name="device-drop-down-menu"></a>Menu suspenso do dispositivo

No Visual Studio 2019 (visualização), você pode selecionar um dispositivo para visualizar em uma lista suspensa de dispositivos Android ou iOS. Essa lista suspensa substitui as opções "Phone" e "Tablet". Durante a visualização de iOS, na lista suspensa mostrará os dispositivos iPhone e iPad. Ao visualizar o Android, na lista suspensa mostrará vários telefones e tablets Android. As duas listas incluem tamanhos de tela e resoluções de tela.

::: zone-end

## <a name="detect-design-mode"></a>Detectar modo de design

Estático [ `DesignMode.IsDesignModeEnabled` ](xref:Xamarin.Forms.DesignMode.IsDesignModeEnabled) propriedade pode ser examinada para determinar se o aplicativo está em execução no visualizador. Isso permite que você especifique o código que será executado somente quando o aplicativo está em execução no pré-visualizador:

```csharp
if (DesignMode.IsDesignModeEnabled)
{
  // Previewer only code  
}
```

::: zone pivot="win-dev16"

## <a name="enable-design-time-rendering-for-custom-controls"></a>Habilitar o processamento em tempo de design para controles personalizados

Controles personalizados precisam opt-in para a renderização para aparecer no visualizador, independentemente do controle reside em seu projeto, ou é importado de uma biblioteca de tempo de design. Isso pode ser feito adicionando o [ `[DesignTimeVisible(true)]` ](xref:System.ComponentModel.DesignTimeVisibleAttribute) para sua classe do controle:

```csharp
namespace MyProject
{
  [DesignTimeVisible(true)]
  public class MyControl : BaseControl
  {
    // Your control's code here
  }

}
```

Um exemplo disso pode ser visto na [classe de base de ImageCirclePlugin de James Montemagno](https://github.com/jamesmontemagno/ImageCirclePlugin/blob/master/src/ImageCircle/CircleImage.shared.cs).

::: zone-end

## <a name="add-design-time-data"></a>Adicionar dados de tempo de design

Alguns layouts podem ser difícil visualizar sem dados associados a controles da interface do usuário. Para tornar a visualização mais úteis, atribua alguns dados estáticos para os controles por codificar um contexto de associação (seja no code-behind ou usando XAML).

Consulte de James Montemagno [postagem de blog sobre como adicionar dados de tempo de design](http://motzcod.es/post/143702671962/xamarinforms-xaml-previewer-design-time-data) para ver como associar a um ViewModel estático em XAML.

## <a name="troubleshooting"></a>Solução de problemas

Verifique os problemas a seguir e o [fóruns do Xamarin](https://forums.xamarin.com/categories/xamarin-forms), se você encontrar problemas.

### <a name="xaml-previewer-isnt-showing-or-shows-an-error"></a>Pré-visualizador de XAML não estiver visível ou mostrará um erro

Verifique o seguinte:

* O agente de Designer deve ser configurada na primeira vez em que você visualizar um arquivo XAML - um indicador de progresso será exibida no visualizador, juntamente com mensagens de progresso, até que isso esteja pronto.
* Tente fechar e abrir novamente o arquivo XAML.
* Certifique-se de que seu `App` classe tem um construtor sem parâmetros.

::: zone pivot="win-dev16"

### <a name="custom-controls-arent-rendering"></a>Controles personalizados não são de renderização

Certifique-se de que seu projeto é compilado. Se o pré-visualizador não é possível renderizar o controle sem um erro ou criador do controle não opt-in para a renderização em tempo de design, o pré-visualizador mostra a classe base do controle.

::: zone-end
