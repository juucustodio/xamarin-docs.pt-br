---
title: Pesquisa e Home aprimoramentos de Widget de tela no iOS 10
description: Este documento descreve os aprimoramentos de que Apple fez o Widgets no iOS 10, incluindo atualizações para pesquisa e widgets da tela inicial.
ms.prod: xamarin
ms.assetid: D66FD9E1-9E23-4BB6-825C-ED19B8F72A81
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 8383f167f8c85459e996b83368281fa243317948
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788105"
---
# <a name="search-and-home-screen-widget-enhancements-in-ios-10"></a>Pesquisa e Home aprimoramentos de Widget de tela no iOS 10

_Este artigo aborda os aprimoramentos de que Apple tornou-se ao sistema de Widget no iOS 10._

Apple introduziu várias melhorias para o sistema de Widget para garantir que os widgets uma aparência excelentes em qualquer plano de fundo que existe no iOS a nova tela de bloqueio 10. Além disso, widgets agora contém um [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) propriedade que permite ao desenvolvedor descrever a quantidade de conteúdo está disponível e permite que o usuário expandir e recolher o conteúdo.

Widgets (também conhecido como hoje extensões) são um tipo especial de iOS extensão que exibem uma pequena quantidade de informações úteis ou expor a funcionalidade específica do aplicativo de maneira oportuna. Por exemplo, o aplicativo de notícias tem um widget que mostra as manchetes superiores e o aplicativo de calendário fornece dois widgets diferentes: um para exibir atuais eventos e outra para mostrar os eventos futuros.

Widgets são altamente personalizáveis e podem conter elementos de interface do usuário, como texto, imagens, botões, etc. Além disso, o desenvolvedor pode personalizar o layout de seus widgets.

[![](widgets-images/widgets01.png "Widgets de exemplo")](widgets-images/widgets01.png#lightbox)

Há dois principais locais que um usuário pode exibir e interagir com os Widgets do aplicativo:

- **A tela de pesquisa** -os usuários podem adicionar Widgets eles mais útil à sua tela de pesquisa. A tela de pesquisa é acessada passando direita nas telas de Home e o bloqueio.
- **A tela inicial** -da tela inicial, o usuário pode usar o toque 3D para abrir a lista de ações rápidas aplicando pressão para o ícone do aplicativo. Widgets do aplicativo serão exibidos acima da lista de ação rápida. Confira nosso [Introdução ao 3D Touch](~/ios/platform/3d-touch.md) documentação para obter mais informações.

## <a name="widgets-developer-suggestions"></a>Sugestões de desenvolvedor de widgets

Idealmente, o desenvolvedor deve sempre tentar e criar Widgets que o usuário deseja adicionar a suas telas de pesquisa. Para esse fim, a Apple tem as seguintes sugestões:

- **Criar um ótimo, experiência rápidas** -usuário quer Widgets que fornecem informações rápidas breves de atualizações de status ou permitir que eles executam tarefas simples rapidamente. Isso torna fornecendo a quantidade certa de informações e uma ferramenta essencial de interatividade. Sempre que possível, permitir que o usuário executar uma tarefa específica com um único toque. Além disso, desde que os Widgets não oferecem suporte a rolagem ou panorâmica, isso precisará ser levada em consideração no design do Widget.
- **Mostrar conteúdo rapidamente** -Widgets são projetados para serem rápidas, para que o usuário nunca deve ter que aguardar o conteúdo a ser carregado uma vez que um Widget é exibido. Widgets devem armazenar em cache o conteúdo localmente para exibir conteúdo recente durante o carregamento de conteúdo atualizado em segundo plano.
- **Fornecer preenchimento apropriado e as margens** -Widgets nunca deve parecer cheias, portanto, evite estendendo o conteúdo até as bordas da exibição do Widget. Deve haver sempre uma margem de largura de pixel várias entre as bordas e o conteúdo. Apple também sugere usando o ícone do aplicativo, exibida na parte superior do Widget, como um guia de alinhamento. Se o Widget apresenta um layout de grade, verifique se há adequada de preenchimento entre os itens na grade e tente limitar o número de itens para quatro max.
- **Usar Layouts adaptáveis** -largura de um Widget variam conforme o dispositivo estiver em execução e a orientação do dispositivo. Altura do Widget também pode variar com base em se ele está sendo exibido em um estado de expandido (todos os Widgets não tem suportado) ou recolhido (o padrão). Um Widget recolhido tem uma altura das linhas da tabela aproximadamente dois e meia iOS padrão. O desenvolvedor pode solicitar o tamanho para um Widget expandido mas idealmente deve ser menor do que a altura da tela. No estado recolhido, o Widget deve mostrar informações essenciais apenas autônomas. Quando expandido, o Widget devem mostrar informações complementares que aprimora o conteúdo principal mostrado no estado recolhido. Só será mostrados na lista de ação rápida de widgets no estado recolhido.
- **Não personalizar plano de fundo do Widget** -Widgets são exibidos em um plano de fundo de luz, indefinido fornecido pelo sistema. Isso é feito para promover a consistência entre os Widgets e melhorar a legibilidade de conteúdo. Evite usar uma imagem como plano de fundo do Widget, porque ele foi podem conflitar com papéis de parede bloqueio e a tela inicial do usuário.
- **Use a fonte do sistema em preto ou cinza escuro** - quando exibindo texto em um Widget, fonte do sistema funciona melhor. A fonte deve ser uma cor cinza preto ou para destacar o plano de fundo de Widget leve, indefinida.
- **Fornecer um aplicativo Access quando apropriado** -Widget sempre deve operar separadamente do seu aplicativo, no entanto, se mais funcionalidade for necessária, o Widget deve ser capaz de iniciar o aplicativo para ver ou editar uma informação específica. Nunca incluir um botão "abrir o aplicativo", simplesmente permite que o usuário tocar em seu próprio conteúdo e nunca abrir uma 3ª parte aplicativo.
- **Selecione uma clara, concisa nome do Widget** -ícone do aplicativo e o nome do Widget são sempre exibidos sobre o conteúdo do Widget. Apple sugere usando o nome do aplicativo para o Widget primário e um nome de claro e conciso para quaisquer outras que ela fornece. Ao fornecer um título de Widget personalizados, eles devem ser prefixados com o nome do aplicativo (como mapas próximos restaurantes mapas, etc.).
- **Informar quando autenticação adiciona valor** - se informações ou funcionalidade adicional está disponível apenas quando o usuário é autenticado e conectado, isso apresentar ao usuário. Por exemplo, uma jornada meu diga "Entrar a jornada do catálogo" do aplicativo de compartilhamento.
- **Selecione um Widget de lista de ação rápida** -se o aplicativo fornece mais de um Widget, o desenvolvedor deve escolher o para apresentar quando o usuário exibe a lista de ações rápidas aplicando pressão para o ícone do aplicativo usando 3D Touch.

Para obter mais detalhes sobre como trabalhar com widgets, consulte nosso [Introdução a extensões](~/ios/platform/extensions.md), [Introdução ao 3D Touch](~/ios/platform/3d-touch.md) documentação e da Apple [guia de programação de extensão de aplicativo](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html).

## <a name="working-with-vibrancy"></a>Trabalhando com valorize

Valorize garante que o texto do Widget permaneça legível quando exibidas luz do Widget, borrado em segundo plano (fornecido pelo sistema). Antes de iOS 10, o desenvolvedor usa uma [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect) para vitalidade do Widget. Por exemplo:

```csharp
// DEPRECATED: Get Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreateForNotificationCenter ();
```

Isso foi preterido no iOS 10 e deve ser substituído por um um [WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect) ou um [WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect). Por exemplo:

```csharp
// Get Primary Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreatePrimaryVibrancyEffectForNotificationCenter ();

// Get Secondary Widget Vibrancy Effect
var vibrancy2 = UIVibrancyEffect.CreateSecondaryVibrancyEffectForNotificationCenter ();
```

## <a name="working-with-collapsed-and-expanded-widgets"></a>Trabalhando com os Widgets expandidos e recolhidos

Novo para o iOS 10, widgets agora contêm um [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) propriedade que permite ao desenvolvedor descrever a quantidade de conteúdo está disponível e permite que o usuário expandir e recolher o conteúdo.

Quando um Widget é exibido inicialmente, é em um estado recolhido. Um Widget recolhido tem uma altura das linhas da tabela aproximadamente dois e meia iOS padrão. O desenvolvedor pode solicitar o tamanho para um Widget expandido mas idealmente deve ser menor do que a altura da tela. 

No estado recolhido, o Widget deve mostrar informações essenciais apenas autônomas. Quando expandido, o Widget devem mostrar informações complementares que aprimora o conteúdo principal mostrado no estado recolhido. Por exemplo, o aplicativo de tempo mostra as condições de tempo atual quando recolhido e adiciona a cada hora prever quando expandido.

Só será mostrados na lista de ação rápida de widgets no estado recolhido. Se o aplicativo fornece mais de um Widget, o desenvolvedor deve escolher o para apresentar quando o usuário exibe a lista de ações rápidas aplicando pressão para o ícone do aplicativo usando 3D Touch.

O exemplo a seguir é de uma simples hoje extensão (Widget) que trata os estados recolhido e expandido:

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

Observe o código específico do modo de exibição de Widget em detalhes. Para informar o sistema se este Widget oferece suporte para o estado de expandido, ele usa:

```csharp
// Tell widget it can be expanded
ExtensionContext.SetWidgetLargestAvailableDisplayMode (NCWidgetDisplayMode.Expanded);
```

Para obter o modo de exibição atual do Widget, ele usa:

```csharp
ExtensionContext.GetWidgetActiveDisplayMode()
```

Para obter o tamanho máximo para o estado de expandido ou recolhido, ele usa:

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

Além de definir o tamanho solicitado para cada estado (recolhido ou expandido), ele também atualiza o conteúdo que está sendo exibido para coincidir com o novo tamanho.

## <a name="summary"></a>Resumo

Este artigo tem cobertos os aprimoramentos de que Apple tornou-se ao sistema Widget em 10 de iOS e mostrado como implementá-los no xamarin.



## <a name="related-links"></a>Links relacionados

- [Exemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Introdução a extensões](~/ios/platform/extensions.md)
- [Introdução ao 3D Touch](~/ios/platform/3d-touch.md)
- [Guia de programação de extensão do aplicativo](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html)
