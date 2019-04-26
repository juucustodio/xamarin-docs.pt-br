---
title: Pesquisa e Home aprimoramentos de Widget de tela no iOS 10
description: Este documento descreve as melhorias feitas pela Apple para os Widgets no iOS 10, incluindo atualizações para pesquisa e widgets da tela inicial.
ms.prod: xamarin
ms.assetid: D66FD9E1-9E23-4BB6-825C-ED19B8F72A81
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: f693b480fff141c177ed135ced60afd65abd77de
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082158"
---
# <a name="search-and-home-screen-widget-enhancements-in-ios-10"></a>Pesquisa e Home aprimoramentos de Widget de tela no iOS 10

_Este artigo aborda as melhorias feitas pela Apple com o sistema de Widget no iOS 10._

Apple introduziu vários aprimoramentos para o sistema de Widget para garantir que os widgets combinam bem com qualquer plano de fundo que existe no iOS nova tela de bloqueio 10. Além disso, widgets agora contêm uma [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) propriedade que permite ao desenvolvedor descrever a quantidade de conteúdo está disponível e permite que o usuário expandir e recolher o conteúdo.

Widgets (também conhecido como hoje extensões) são um tipo especial de iOS extensão que exiba uma pequena quantidade de informações úteis ou expor a funcionalidade específica do aplicativo de forma oportuna. Por exemplo, o aplicativo de notícias traz um widget que mostra as manchetes superior e o aplicativo de calendário fornece dois widgets diferentes: um para exibir os atuais eventos e uma para mostrar os eventos futuros.

Widgets são altamente personalizáveis e podem conter elementos de interface do usuário, como texto, imagens, botões, etc. Além disso, o desenvolvedor pode personalizar o layout de seus widgets.

[![](widgets-images/widgets01.png "Widgets de exemplo")](widgets-images/widgets01.png#lightbox)

Há dois locais principais que um usuário pode exibir e interagir com os Widgets do aplicativo:

- **A tela de pesquisa** -os usuários podem adicionar os Widgets que eles considerarem mais úteis para a tela de pesquisa. A tela de pesquisa é acessada passando o dedo para a direita em telas iniciais e de bloqueio.
- **A tela inicial** -da tela inicial, o usuário pode usar toque 3D para abrir a lista de ações rápidas por meio da aplicação de pressão para o ícone do aplicativo. Widgets do aplicativo serão exibidos acima da lista de ação rápida. Consulte nosso [Introdução ao 3D Touch](~/ios/platform/3d-touch.md) documentação para obter mais informações.

## <a name="widgets-developer-suggestions"></a>Sugestões de desenvolvedor de widgets

O ideal é que o desenvolvedor deve sempre tentar e criar Widgets que o usuário deseja adicionar a suas telas de pesquisa. Para esse fim, a Apple tem as seguintes sugestões:

- **Criar um ótimo, experiência Glanceable** -usuário deseja Widgets que fornecem informações glanceable breves de atualizações de status ou permitir que eles realizem tarefas simples rapidamente. Assim, fornecendo a quantidade certa de informações e uma ferramenta essencial de interatividade. Sempre que possível, permitir que o usuário executar uma tarefa específica com um único toque. Além disso, como os Widgets não dão suporte a movimento panorâmico ou rolar, isso terá ser levadas em consideração no design do Widget.
- **Mostrar conteúdo rapidamente** -Widgets são projetados para serem glanceable, portanto, o usuário nunca deve ter que aguardar o conteúdo a ser carregado depois que um Widget é exibido. Widgets devem armazenar em cache seu conteúdo localmente de forma que eles podem mostrar conteúdo recente durante o carregamento de conteúdo atualizado em segundo plano.
- **Forneça apropriado de preenchimento e margens** -Widgets nunca deve parecer lotados, portanto, evite estendendo o conteúdo para as bordas do modo de exibição de um Widget. Sempre deve haver uma margem de largura de pixel vários entre as bordas e o conteúdo. A Apple também sugere usando o ícone do aplicativo, exibido na parte superior do Widget, como uma guia de alinhamento. Se o Widget apresenta um layout de grade, certifique-se de que há adequada de preenchimento entre os itens na grade e tente limitar o número de itens para o máximo de quatro.
- **Usar Layouts adaptáveis** -largura de um Widget irão variar com base no dispositivo ela está sendo executada e a orientação do dispositivo. Altura de um Widget também pode variar com base em se ele está sendo exibido em um estado de expandido (sem suporte por todos os Widgets) ou recolhido (o padrão). Um Widget recolhido tem uma altura das linhas da tabela padrão do iOS aproximadamente dois e meia. O desenvolvedor pode solicitar o tamanho para um Widget expandido, mas o ideal é que deve ser menor do que a altura da tela. No estado recolhido, o Widget deve mostrar informações apenas essenciais e autônomas. Quando expandido, o Widget deve mostrar informações complementares que aprimora o conteúdo principal, mostrado no estado recolhido. Só será mostrados na lista de ação rápida de widgets no estado recolhido.
- **Não personalizar a tela de fundo do Widget** -Widgets são exibidos em um plano de fundo de luz, indefinido fornecido pelo sistema. Isso é feito para promover a consistência entre os Widgets e melhorar a legibilidade de seus conteúdos. Evite usar uma imagem como um plano de fundo do Widget, porque ele pode estar em conflito com os papéis de parede bloqueio e a tela inicial do usuário.
- **Usar a fonte do sistema em preto ou cinza escuro** - quando exibindo texto em um Widget, a fonte do sistema de funciona melhor. A fonte deve estar em uma cor cinza preta ou escura para se destacar o plano de fundo de luz, indefinida de Widget.
- **Fornecer um aplicativo de acesso quando apropriado** -Widget deve sempre operam de forma separada do seu aplicativo, no entanto, se uma funcionalidade mais aprofundada for necessária, o Widget deve ser capaz de iniciar o aplicativo para ver ou editar uma parte específica de informações. Nunca incluir um botão "abrir o aplicativo", simplesmente permite que o usuário tocar em seu próprio conteúdo e terceiros nunca abrir uma 3ª aplicativo.
- **Selecione um claro, conciso nome do Widget** -o ícone do aplicativo e o nome do Widget são sempre exibidos sobre o conteúdo do Widget. A Apple sugere usando o nome do aplicativo para seu Widget primário e um nome claro e concisa voltado para quaisquer outras que ele fornece. Ao fornecer um título de Widget personalizado, eles devem ser prefixados com o nome do aplicativo (por exemplo, mapas próximos, mapas de restaurantes, etc.).
- **Informar quando a autenticação adiciona valor** - se informações ou funcionalidade adicional está disponível apenas quando o usuário é autenticado e conectado, apresentar isso para o usuário. Por exemplo, uma aplicativo de compartilhamento de corrida pode dizer "Sign in a jornada do livro".
- **Selecione um Widget de lista de ação rápida** -se o aplicativo fornece mais de um Widget, o desenvolvedor deve escolher aquele para apresentar quando o usuário abre a lista de ação rápida por meio da aplicação de pressão para o ícone do aplicativo usando o toque 3D.

Para obter mais detalhes sobre como trabalhar com os widgets, consulte nosso [Introdução às extensões](~/ios/platform/extensions.md), [Introdução ao 3D Touch](~/ios/platform/3d-touch.md) documentação e do Apple [guia de programação de extensão de aplicativo](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html).

## <a name="working-with-vibrancy"></a>Trabalhando com valorize

Valorize garante que o texto de um Widget permaneça legível quando apresentadas na luz do Widget, desfocada em segundo plano (fornecido pelo sistema). Antes do iOS 10, o desenvolvedor usa uma [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect) para vitalidade do Widget. Por exemplo:

```csharp
// DEPRECATED: Get Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreateForNotificationCenter ();
```

Isso foi preterido no iOS 10 e deve ser substituído por qualquer um uma [WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect) ou um [WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect). Por exemplo:

```csharp
// Get Primary Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreatePrimaryVibrancyEffectForNotificationCenter ();

// Get Secondary Widget Vibrancy Effect
var vibrancy2 = UIVibrancyEffect.CreateSecondaryVibrancyEffectForNotificationCenter ();
```

## <a name="working-with-collapsed-and-expanded-widgets"></a>Trabalhando com os Widgets expandidos e recolhidos

Novo para o iOS 10, widgets agora contêm uma [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) propriedade que permite ao desenvolvedor descrever a quantidade de conteúdo está disponível e permite que o usuário expandir e recolher o conteúdo.

Quando um Widget é exibido inicialmente, é em um estado recolhido. Um Widget recolhido tem uma altura das linhas da tabela padrão do iOS aproximadamente dois e meia. O desenvolvedor pode solicitar o tamanho para um Widget expandido, mas o ideal é que deve ser menor do que a altura da tela. 

No estado recolhido, o Widget deve mostrar informações apenas essenciais e autônomas. Quando expandido, o Widget deve mostrar informações complementares que aprimora o conteúdo principal, mostrado no estado recolhido. Por exemplo, o aplicativo meteorológico mostra as condições meteorológicas atuais quando recolhido e adiciona a cada hora quando expandido de previsão.

Só será mostrados na lista de ação rápida de widgets no estado recolhido. Se o aplicativo fornece mais de um Widget, o desenvolvedor deve escolher aquele para apresentar quando o usuário abre a lista de ação rápida por meio da aplicação de pressão para o ícone do aplicativo usando o toque 3D.

O exemplo a seguir é de uma simples hoje extensão (Widget) que manipula os estados recolhido e expandido:

```csharp
using System;
using NotificationCenter;
using Foundation;
using UIKit;
using CoreGraphics;

namespace MonkeyAbout
{
    public partial class TodayViewController : UIViewController, INCWidgetProviding
    {
        protected TodayViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Tell widget it can be expanded
            ExtensionContext.SetWidgetLargestAvailableDisplayMode (NCWidgetDisplayMode.Expanded);

            // Get the maximum size
            var maxSize = ExtensionContext.GetWidgetMaximumSize (NCWidgetDisplayMode.Expanded);
        }

        [Export ("widgetPerformUpdateWithCompletionHandler:")]
        public void WidgetPerformUpdate (Action<NCUpdateResult> completionHandler)
        {
            // Take action based on the display mode
            switch (ExtensionContext.GetWidgetActiveDisplayMode()) {
            case NCWidgetDisplayMode.Compact:
                Content.Text = "Let's Monkey About!";
                break;
            case NCWidgetDisplayMode.Expanded:
                Content.Text = "Gorilla!!!!";
                break;
            }

            // Report results
            // If an error is encoutered, use NCUpdateResultFailed
            // If there's no update required, use NCUpdateResultNoData
            // If there's an update, use NCUpdateResultNewData
            completionHandler (NCUpdateResult.NewData);
        }

        [Export ("widgetActiveDisplayModeDidChange:withMaximumSize:")]
        public void WidgetActiveDisplayModeDidChange (NCWidgetDisplayMode activeDisplayMode, CGSize maxSize)
        {
            // Take action based on the display mode
            switch (activeDisplayMode) {
            case NCWidgetDisplayMode.Compact:
                PreferredContentSize = maxSize;
                Content.Text = "Let's Monkey About!";
                break;
            case NCWidgetDisplayMode.Expanded:
                PreferredContentSize = new CGSize (0, 200);
                Content.Text = "Gorilla!!!!";
                break;
            }
        }

    }
}
```

Examine o código específico do modo de exibição do Widget em detalhes. Para informar o sistema que este Widget suporta o estado expandido, ele usa:

```csharp
// Tell widget it can be expanded
ExtensionContext.SetWidgetLargestAvailableDisplayMode (NCWidgetDisplayMode.Expanded);
```

Para obter o modo de exibição atual do Widget, ele usa:

```csharp
ExtensionContext.GetWidgetActiveDisplayMode()
```

Para obter o tamanho máximo para o estado recolhido ou expandido, ele usa:

```csharp
// Get the maximum size
var maxSize = ExtensionContext.GetWidgetMaximumSize (NCWidgetDisplayMode.Expanded);
```

E para lidar com o estado (modo de exibição) alterando, ele usa:

```csharp
[Export ("widgetActiveDisplayModeDidChange:withMaximumSize:")]
public void WidgetActiveDisplayModeDidChange (NCWidgetDisplayMode activeDisplayMode, CGSize maxSize)
{
    // Take action based on the display mode
    switch (activeDisplayMode) {
    case NCWidgetDisplayMode.Compact:
        PreferredContentSize = maxSize;
        Content.Text = "Let's Monkey About!";
        break;
    case NCWidgetDisplayMode.Expanded:
        PreferredContentSize = new CGSize (0, 200);
        Content.Text = "Gorilla!!!!";
        break;
    }
}
```

Além de definir o tamanho solicitado para cada estado (recolhido ou expandido), ele também atualiza o conteúdo que está sendo exibido de acordo com o novo tamanho.

## <a name="summary"></a>Resumo

Este artigo tem abordadas as melhorias feitas pela Apple com o sistema de Widget no iOS 10 e mostrado como implementá-los no xamarin. IOS.



## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Introdução às extensões](~/ios/platform/extensions.md)
- [Introdução ao 3D Touch](~/ios/platform/3d-touch.md)
- [Guia de programação de extensão do aplicativo](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html)
