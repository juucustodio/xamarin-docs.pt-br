---
title: Pré-visualizador XAML para xamarin. Forms
description: Este artigo explica como usar o Visualizador de XAML para ver seus layouts do xamarin. Forms renderizados conforme você digita. O pré-visualizador XAML está disponível no Visual Studio 2017 e Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: 84769ff1-72fd-4c44-8251-dd6d5bf8c7b2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: d29186681f79a2f7591978411b5d15fd9f90f807
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563466"
---
# <a name="xaml-previewer-for-xamarinforms"></a>Pré-visualizador XAML para xamarin. Forms

_Consulte seus layouts do xamarin. Forms renderizados conforme você digita!_

## <a name="requirements"></a>Requisitos

Os projetos requerem o pacote mais recente do NuGet xamarin. Forms para o pré-visualizador de XAML trabalhar. Visualização de aplicativos Android requer [JDK 1.8 x64](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Há mais informações na [notas de versão](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#Xamarin_Forms_Previewer).

## <a name="getting-started"></a>Guia de Introdução

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

O pré-visualizador XAML é ativado por padrão e pode ser controlado com o **Ferramentas > Opções > Xamarin > pré-visualizador formulários** caixa de diálogo. Nesta caixa de diálogo, você pode selecionar o modo de exibição de documento padrão e a orientação de divisão.

[![Visualização do controle ListView no Visual Studio](xaml-previewer-images/xamlp-options-vs.png "formulários pré-visualizador opções no Visual Studio")](xaml-previewer-images/xamlp-options-vs.png#lightbox "formulários pré-visualizador opções no Visual Studio")

Ao abrir uma página XAML editor será dividido com base nas configurações selecionadas na **Ferramentas > Opções > Xamarin > formulários pré-visualizador** caixa de diálogo. No entanto, essas preferências podem ser alteradas na janela do editor.

## <a name="xaml-preview-controls"></a>Controles de visualização do XAML

A parte superior da janela do editor tem botões para selecionar qual painel está em uso, com o botão superior alternando para o painel de design e o botão inferior alternando para o painel de fonte. O botão do meio alterna a ordem do painel.

[![Visualização do controle ListView no Visual Studio](xaml-previewer-images/xamlp-controls-vs.png "controles de formulários pré-visualizador painel no Visual Studio")](xaml-previewer-images/xamlp-controls-vs.png#lightbox "controles de formulários pré-visualizador painel no Visual Studio")

A parte inferior da janela do editor tem botões para os painéis, de dividir verticalmente e horizontalmente para expandir ou recolher o painel sub atual.

[![Visualização do controle ListView no Visual Studio](xaml-previewer-images/xamlp-controls2-vs.png "controles de formulários pré-visualizador painel no Visual Studio")](xaml-previewer-images/xamlp-controls2-vs.png#lightbox "controles de formulários pré-visualizador painel no Visual Studio")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

O **visualização** botão é exibido no editor quando você abre uma página XAML. O painel de visualização pode ser mostrado ou ocultado, pressionando as **visualização** botão no canto superior direito de qualquer janela de documento XAML:

[![Visualização do controle ListView no Visual Studio para Mac](xaml-previewer-images/xamlp-list-sml.png "pré-visualizador Forms no Visual Studio para Mac")](xaml-previewer-images/xamlp-list.png#lightbox "pré-visualizador Forms no Visual Studio para Mac")

-----

## <a name="xaml-preview-options"></a>Opções de visualização do XAML

As opções na parte superior do painel de visualização são:

* **Telefone** – renderizar em uma tela de tamanho de telefone
* **Tablet** – renderizar em uma tela de tamanho do tablet (Observe que há controles de zoom no canto inferior direito do painel)
* **Android** – mostrar a versão do Android da tela
* **iOS** – mostrar a versão do iOS da tela
* Portait (ícone) – usa a orientação de retrato para a versão prévia
* Paisagem (ícone) – usa paisagem orientação para a versão prévia

## <a name="adding-design-time-data"></a>Adicionando dados de tempo de Design

Alguns layouts podem ser difícil visualizar sem dados associados a controles da interface do usuário. Para tornar a visualização mais úteis, atribua alguns dados estáticos para os controles por codificar um contexto de associação (seja no code-behind ou usando XAML).

Consulte de James Montemagno [postagem de blog sobre como adicionar dados de tempo de design](http://motzcod.es/post/143702671962/xamarinforms-xaml-previewer-design-time-data) para ver como associar a um ViewModel estático em XAML.

## <a name="detecting-design-mode"></a>Detectando o modo de Design

Estático [ `DesignMode.IsDesignModeEnabled` ](xref:Xamarin.Forms.DesignMode.IsDesignModeEnabled) propriedade pode ser examinada para determinar se o aplicativo está em execução no visualizador. Isso permite que você especifique o código que será executado somente quando o aplicativo está em execução no pré-visualizador:

```csharp
if (DesignMode.IsDesignModeEnabled)
{
  // Previewer only code  
}
```

## <a name="troubleshooting"></a>Solução de problemas

Verifique os problemas a seguir e o [fóruns do Xamarin](https://forums.xamarin.com/categories/xamarin-forms), se você encontrar problemas.

### <a name="xaml-preview-isnt-showing"></a>Visualização do XAML não estiver visível

Verifique o seguinte:

* Projeto deve ser compilado (compilado) antes de tentar visualizar arquivos XAML.
* O agente de Designer deve ser configurada na primeira vez em que você visualizar um arquivo XAML - um indicador de progresso será exibida no visualizador, juntamente com mensagens de progresso, até que isso esteja pronto.
* Tente fechar e abrir novamente o arquivo XAML.
* Certifique-se de que seu `App` classe tem um construtor sem parâmetros.

### <a name="invalid-xaml-the-android-project-needs-to-built-before-preview-can-be-created"></a>XAML inválido: O projeto do Android precisa criado antes da visualização pode ser criada

O pré-visualizador XAML requer que o projeto ser compilado antes da renderização de uma página.
Se o erro abaixo será exibida na parte superior do painel de visualização, compile novamente o aplicativo e tente novamente.

![Mensagem de erro: projeto deve ser compilado primeiro](xaml-previewer-images/error-not-built-sml.png "mensagem de erro: recompile o projeto")
