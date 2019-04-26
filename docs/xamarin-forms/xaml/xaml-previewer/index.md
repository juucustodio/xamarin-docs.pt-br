---
title: Pré-visualizador XAML para xamarin. Forms
description: Este artigo explica como usar o Visualizador de XAML para ver seus layouts do xamarin. Forms renderizados conforme você digita. O pré-visualizador XAML está disponível no Visual Studio de 2019 e 2019 de Visual Studio para Mac.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 84769ff1-72fd-4c44-8251-dd6d5bf8c7b2
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 02/04/2019
ms.openlocfilehash: db243a9c8dcb25f51bc7926a7aa239531e9c24f6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61210768"
---
# <a name="xaml-previewer-for-xamarinforms"></a>Pré-visualizador XAML para xamarin. Forms

_Consulte seus layouts do xamarin. Forms renderizados conforme você digita_

## <a name="overview"></a>Visão geral

O pré-visualizador XAML mostra qual será a aparência de sua página XAML de xamarin. Forms no iOS e Android. Quando você faz alterações em seu XAML, você poderá vê-los imediatamente visualizado junto com seu código. O pré-visualizador XAML está disponível no Visual Studio e Visual Studio para Mac.

## <a name="getting-started"></a>Introdução

::: zone pivot="windows"

### <a name="visual-studio-2019"></a>Visual Studio 2019

Você pode abrir o pré-visualizador XAML clicando nas setas no painel de exibição de divisão. Se você quiser alterar o padrão dividir o comportamento do modo de exibição, use o **Ferramentas > Opções > Xamarin > formulários pré-visualizador** caixa de diálogo. Nesta caixa de diálogo, você pode selecionar o modo de exibição de documento padrão e a orientação de divisão.

[![Opções de pré-visualizador do xamarin. Forms no Visual Studio](xaml-previewer-images/xamlp-options-vs-sm.png "opções do Visualizador do xamarin. Forms no Visual Studio")](xaml-previewer-images/xamlp-options-vs-lg.png#lightbox)

Quando você abre um arquivo XAML, o editor será aberto em tamanho normal ou Avançar para o pré-visualizador, com base nas configurações selecionadas na **Ferramentas > Opções > Xamarin > formulários pré-visualizador** caixa de diálogo. No entanto, a divisão pode ser alterada para cada arquivo na janela do editor.

#### <a name="xaml-preview-controls"></a>Controles de visualização do XAML

Escolha se você quiser ver seu código, o pré-visualizador de XAML, ou ambas selecionando esses botões na divisão de exibir o painel. O botão do meio alterna o que são o pré-visualizador de lado e seu código em:

[![Controla o pré-visualizador de xamarin. Forms para alternar entre o design, a origem e o modo de divisão no Visual Studio](xaml-previewer-images/xamlp-controls-splitview-vs-sm.png "controla o pré-visualizador de xamarin. Forms para alternar entre o design, a origem e o modo de divisão no Visual Studio")](xaml-previewer-images/xamlp-controls-splitview-vs-lg.png#lightbox)

Você pode alterar se a tela é dividida verticalmente ou horizontalmente, ou recolher um painel completamente:

[![Controles de orientação de painel pré-visualizador de xamarin. Forms no Visual Studio](xaml-previewer-images/xamlp-controls-orientation-vs-sm.png "controles de orientação de painel pré-visualizador de xamarin. Forms no Visual Studio")](xaml-previewer-images/xamlp-controls-orientation-vs-lg.png#lightbox)

::: zone-end
::: zone pivot="macos"

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

O **visualização** botão é exibido no editor quando você abre uma página XAML. Mostrar ou ocultar o pré-visualizador pressionando a **visualização** botão no canto superior direito de qualquer janela de documento XAML:

[![Pré-visualizador do xamarin. Forms no Visual Studio para Mac](xaml-previewer-images/xamlp-list-sml.png "pré-visualizador de xamarin. Forms no Visual Studio para Mac")](xaml-previewer-images/xamlp-list.png#lightbox)

::: zone-end

## <a name="xaml-previewer-options"></a>Opções do Visualizador de XAML

As opções na parte superior do painel de visualização são:

* **Android** – mostrar a versão do Android da tela
* **iOS** – mostra a versão do iOS da tela (*Observação: Se você estiver usando o Visual Studio no Windows, você deve ser [emparelhado com um Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) para usar esse modo*)
* **Dispositivo** -lista suspensa de dispositivos Android ou iOS, incluindo o tamanho da tela e resolução
* **Retrato (ícone)** – usa a orientação de retrato para a versão prévia
* **Paisagem (ícone)** – usa paisagem orientação para a versão prévia

## <a name="detect-design-mode"></a>Detectar modo de design

Estático [ `DesignMode.IsDesignModeEnabled` ](xref:Xamarin.Forms.DesignMode.IsDesignModeEnabled) propriedade informa se o aplicativo é executado no visualizador. Usá-lo, você pode especificar o código que será executado somente quando o aplicativo é ou não está em execução no pré-visualizador:

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

Essa propriedade é útil se você inicializar uma biblioteca em seu construtor de página que não é possível executar em tempo de design.

## <a name="troubleshooting"></a>Solução de problemas

Verifique os problemas a seguir e o [fóruns do Xamarin](https://forums.xamarin.com/categories/xamarin-forms), se o pré-visualizador não está funcionando.

### <a name="xaml-previewer-isnt-showing-or-shows-an-error"></a>Pré-visualizador de XAML não estiver visível ou mostrará um erro

* Pode levar algum tempo para que o pré-visualizador iniciar - consulte "Inicializando renderizar" até que ele esteja pronto.
* Tente fechar e reabrir o arquivo XAML.
* Certifique-se de que seu `App` classe tem um construtor sem parâmetros.
* Verifique a versão do xamarin. Forms - ele deve ser pelo menos 3.6 do xamarin. Forms. Você pode atualizar para a versão mais recente do xamarin. Forms por meio do NuGet.
* Verifique sua instalação do JDK - visualizando Android requer pelo menos [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* Tente encapsulamento qualquer inicializado classes na página de C# código por trás `if (!DesignMode.IsDesignModeEnabled)`.

### <a name="custom-controls-arent-rendering"></a>Controles personalizados não são de renderização

Tente compilar seu projeto. O pré-visualizador mostra a classe base do controle se ele falhar ao renderizar o controle, ou se tiver optado-out de criador do controle do processamento em tempo de design. Para obter mais informações, consulte [controles de personalizados renderizar no pré-visualizador XAML](render-custom-controls.md).
