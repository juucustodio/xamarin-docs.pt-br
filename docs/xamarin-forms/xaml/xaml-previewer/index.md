---
title: Visualizar XAML para Xamarin. Forms
description: Este artigo explica como usar o visualizador XAML para ver os layouts do Xamarin. Forms renderizados conforme você digita. O XAML Visualizer está disponível no Visual Studio 2019 e no Visual Studio 2019 para Mac.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 84769ff1-72fd-4c44-8251-dd6d5bf8c7b2
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 02/04/2019
ms.openlocfilehash: b287d523101bb8ca7faca8ea95ee898ccf9c0bb1
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305314"
---
# <a name="xaml-previewer-for-xamarinforms"></a>Visualizar XAML para Xamarin. Forms

_Consulte os layouts do Xamarin. Forms renderizados conforme você digita_

## <a name="overview"></a>Visão geral

O visualizador XAML mostra como sua página XAML do Xamarin. Forms será exibida no iOS e no Android. Quando você fizer alterações no XAML, verá-as visualizadas imediatamente junto com seu código. O modo de visualização de XAML está disponível no Visual Studio e Visual Studio para Mac.

## <a name="getting-started"></a>Introdução

::: zone pivot="windows"

### <a name="visual-studio-2019"></a>Visual Studio 2019

Você pode abrir o visualizador XAML clicando nas setas no painel exibição de divisão. Se você quiser alterar o comportamento padrão de exibição de divisão, use as **ferramentas > opções > caixa de diálogo visualização do Xamarin > Forms** . Nesta caixa de diálogo, você pode selecionar a exibição de documento padrão e a orientação de divisão.

[![Opções de visualização do Xamarin. Forms no Visual Studio](xaml-previewer-images/xamlp-options-vs-sm.png "Opções de visualização do Xamarin. Forms no Visual Studio")](xaml-previewer-images/xamlp-options-vs-lg.png#lightbox)

Quando você abrir um arquivo XAML, o editor será aberto de tamanho completo ou ao lado do previsor, com base nas configurações selecionadas na caixa de diálogo **ferramentas > opções > Xamarin > Forms de visualização** . No entanto, a divisão pode ser alterada para cada arquivo na janela do editor.

#### <a name="xaml-preview-controls"></a>Controles de visualização XAML

Escolha se você deseja ver seu código, o visualizador XAML ou ambos selecionando esses botões no painel exibição de divisão. O botão do meio troca o que o lado do visualizador e seu código estão:

[![Controles de visualização do Xamarin. Forms para alternar entre o design, a origem e a exibição dividida no Visual Studio](xaml-previewer-images/xamlp-controls-splitview-vs-sm.png "Controles de visualização do Xamarin. Forms para alternar entre o design, a origem e a exibição dividida no Visual Studio")](xaml-previewer-images/xamlp-controls-splitview-vs-lg.png#lightbox)

Você pode alterar se a tela é dividida vertical ou horizontalmente ou recolher um painel completamente:

[![Controles de orientação do painel de visualização do Xamarin. Forms no Visual Studio](xaml-previewer-images/xamlp-controls-orientation-vs-sm.png "Controles de orientação do painel de visualização do Xamarin. Forms no Visual Studio")](xaml-previewer-images/xamlp-controls-orientation-vs-lg.png#lightbox)

::: zone-end
::: zone pivot="macos"

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

O botão **Visualizar** é exibido no editor quando você abre uma página XAML. Mostre ou oculte o visualizador pressionando os botões **Visualizar** ou **dividir** na parte inferior esquerda de qualquer janela de documento XAML:

[![o pré-visor Xamarin. Forms habilitado com o botão Visualizar ou dividir](xaml-previewer-images/xamlp-list-sml.png)](xaml-previewer-images/xamlp-list.png#lightbox)

> [!NOTE]
> Em versões mais antigas do Visual Studio para Mac, o botão **Visualizar** estava localizado no canto superior direito da janela.

::: zone-end

## <a name="xaml-previewer-options"></a>Opções de visualização do XAML

As opções na parte superior do painel de visualização são:

* **Android** – mostrar a versão do Android da tela
* **Ios** – mostrar a versão do IOS da tela (*Observação: se você estiver usando o Visual Studio no Windows, deverá ser [emparelhado com um Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) para usar esse modo*)
* **Dispositivo** -lista suspensa de dispositivos Android ou Ios, incluindo resolução e tamanho da tela
* **Retrato (ícone)** – usa a orientação retrato para a visualização
* **Paisagem (ícone)** – usa a orientação paisagem para a versão prévia

## <a name="detect-design-mode"></a>Detectar modo de design

A propriedade estática [`DesignMode.IsDesignModeEnabled`](xref:Xamarin.Forms.DesignMode.IsDesignModeEnabled) informa se o aplicativo está em execução no visualizador. Usando-o, você pode especificar o código que será executado somente quando o aplicativo estiver ou não estiver em execução no visualizador:

```csharp
if (DesignMode.IsDesignModeEnabled)
{
  // Previewer only code  
}

if (!DesignMode.IsDesignModeEnabled)
{
  // Don't run in the Previewer  
}
```

Essa propriedade será útil se você inicializar uma biblioteca em seu construtor de página que não seja executada em tempo de design.

## <a name="troubleshooting"></a>Solução de problemas

Verifique os problemas abaixo e os [fóruns do Xamarin](https://forums.xamarin.com/categories/xamarin-forms), se o previsor não estiver funcionando.

### <a name="xaml-previewer-isnt-showing-or-shows-an-error"></a>O Visualizar XAML não está sendo exibido ou mostra um erro

* Pode levar algum tempo para que o visualizador seja iniciado – você verá "Inicializando o processamento" até que ele esteja pronto.
* Tente fechar e reabrir o arquivo XAML.
* Verifique se sua classe de `App` tem um construtor sem parâmetros.
* Verifique sua versão do Xamarin. Forms-deve ser pelo menos Xamarin. Forms 3,6. Você pode atualizar para a versão mais recente do Xamarin. Forms por meio do NuGet.
* Verifique a instalação do JDK-a visualização do Android requer pelo menos o [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* Tente encapsular todas as classes inicializadas no C# code-behind da página no `if (!DesignMode.IsDesignModeEnabled)`.

### <a name="custom-controls-arent-rendering"></a>Os controles personalizados não são renderizados

Tente compilar seu projeto. O previsor mostra a classe base do controle se ele falhar ao renderizar o controle ou se o criador do controle aceitou-out do processamento do tempo de design. Para obter mais informações, consulte [renderizar controles personalizados no previsor XAML](render-custom-controls.md).
