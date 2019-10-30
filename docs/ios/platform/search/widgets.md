---
title: Aprimoramentos no widget de pesquisa e na tela inicial no iOS 10
description: Este documento descreve os aprimoramentos que a Apple fez nos widgets no iOS 10, incluindo atualizações nos widgets de pesquisa e tela inicial.
ms.prod: xamarin
ms.assetid: D66FD9E1-9E23-4BB6-825C-ED19B8F72A81
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: ca6ccce934b32fa0d7e48cd8f295d9acefe6e121
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031506"
---
# <a name="search-and-home-screen-widget-enhancements-in-ios-10"></a>Aprimoramentos no widget de pesquisa e na tela inicial no iOS 10

_Este artigo aborda os aprimoramentos que a Apple fez no sistema de widget no iOS 10._

A Apple introduziu vários aprimoramentos no sistema de widget para garantir que os widgets fiquem ótimos em qualquer plano de fundo que exista na nova tela de bloqueio do iOS 10. Além disso, os widgets agora contêm uma propriedade [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) que permite que o desenvolvedor descreva a quantidade de conteúdo disponível e permite que o usuário expanda e recolha o conteúdo.

Os widgets (também conhecidos como extensões atuais) são um tipo especial de extensão do iOS que exibe uma pequena quantidade de informações úteis ou expõe a funcionalidade específica do aplicativo em tempo hábil. Por exemplo, o aplicativo News tem um widget que mostra as principais manchetes e o aplicativo Calendar fornece dois widgets diferentes: um para exibir os eventos de hoje e outro para mostrar os próximos eventos.

Os widgets são altamente personalizáveis e podem conter elementos de interface do usuário, como texto, imagens, botões, etc. Além disso, o desenvolvedor pode personalizar ainda mais o layout de seus widgets.

[![](widgets-images/widgets01.png "Example widgets")](widgets-images/widgets01.png#lightbox)

Há dois locais principais que um usuário pode exibir e interagir com os widgets de um aplicativo:

- **A tela de pesquisa** -os usuários podem adicionar os widgets que eles acham mais úteis para a tela de pesquisa. A tela de pesquisa é acessada passando o dedo para a direita nas telas início e bloqueio.
- **A tela inicial** – na tela inicial, o usuário pode usar o 3D Touch para abrir a lista de ações rápidas aplicando pressão ao ícone do aplicativo. Os widgets de um aplicativo serão exibidos acima da lista de ações rápidas. Consulte nossa [introdução à documentação do 3D Touch](~/ios/platform/3d-touch.md) para obter mais informações.

## <a name="widgets-developer-suggestions"></a>Sugestões de desenvolvedor de widgets

O ideal é que o desenvolvedor sempre tente e crie widgets que o usuário desejará adicionar às suas telas de pesquisa. Para esse fim, a Apple tem as seguintes sugestões:

- **Crie uma experiência excelente e interessante** – os widgets do usuário querem que forneçam informações breves e resumidas de atualizações de status ou que permitam executar tarefas simples rapidamente. Isso facilita o fornecimento da quantidade certa de informações e da interatividade de um essencial. Sempre que possível, permita que o usuário execute uma determinada tarefa com um único toque. Além disso, como os widgets não dão suporte à panorâmica ou à rolagem, isso terá que ser levado em consideração no design do widget.
- **Mostrar rapidamente o conteúdo** -os widgets são projetados para serem resumidos, de modo que o usuário nunca deve esperar que o conteúdo seja carregado quando um widget for exibido. Os widgets devem armazenar em cache seu conteúdo localmente para que eles possam mostrar conteúdo recente enquanto o conteúdo atual está sendo carregado em segundo plano.
- **Forneça o preenchimento e as margens apropriadas** – os widgets nunca devem parecer lotados, portanto, evite estender o conteúdo para as bordas da exibição de um widget. Deve haver sempre uma margem de largura de vários pixels entre as bordas e o conteúdo. A Apple também sugere o uso do ícone do aplicativo, exibido na parte superior do widget, como um guia de alinhamento. Se o widget apresentar um layout de grade, verifique se há um preenchimento adequado entre os itens na grade e tente limitar o número de itens a quatro máx.
- **Usar layouts adaptáveis** -a largura de um widget varia de acordo com o dispositivo em que está sendo executado e a orientação do dispositivo. A altura de um widget também pode variar com base em se ele estiver sendo exibido em um estado recolhido (o padrão) ou expandido (não suportado por todos os widgets). Um widget recolhido tem uma altura de aproximadamente duas e uma metade de linhas da tabela do iOS padrão. O desenvolvedor pode solicitar o tamanho de um widget expandido, mas, idealmente, deve ser menor que a altura da tela. No estado recolhido, o widget deve mostrar somente informações essenciais e autônomas. Quando expandido, o widget deve mostrar informações complementares que aprimoram o conteúdo primário mostrado no estado recolhido. Os widgets mostrados na lista de ações rápidas só estarão no estado recolhido.
- **Não personalize os widgets em segundo plano do widget** são exibidos em um plano de fundo claro e borrado fornecido pelo sistema. Isso é feito para promover a consistência entre widgets e melhorar a legibilidade de seu conteúdo. Evite usar uma imagem como um plano de fundo de widget porque ele poderia conflitar com os papéis de parede de bloqueio e tela inicial do usuário.
- **Usar a fonte do sistema em preto ou cinza-escuro** -ao exibir o texto em um widget, a fonte do sistema funciona melhor. A fonte deve estar em uma cor cinza preta ou escura para destacar a luz de fundo do widget claro e borrado.
- **Fornecer acesso ao aplicativo quando apropriado** -o widget sempre deve operar separadamente de seu aplicativo, no entanto, se uma funcionalidade mais profunda for necessária, o widget deverá ser capaz de iniciar o aplicativo para ver ou editar uma informação específica. Nunca inclua um botão "Open app", basta permitir que o usuário toque no próprio conteúdo e nunca abra um aplicativo de terceiros.
- **Selecione um nome de widget claro e conciso** – o ícone do aplicativo e o nome do widget são sempre exibidos sobre o conteúdo do widget. A Apple sugere o uso do nome do aplicativo para seu widget principal e um nome conciso e simples para quaisquer outros que ele fornecer. Ao fornecer um título de widget personalizado, eles devem ser prefixados com o nome do aplicativo (como mapas próximos, mapas de restaurantes, etc.).
- **Informe quando a autenticação agrega valor** – se a funcionalidade adicional ou as informações estiverem disponíveis somente quando o usuário for autenticado e conectado, apresente isso ao usuário. Por exemplo, um aplicativo de compartilhamento de Rides pode dizer "entrar na jornada do livro".
- **Selecione um widget lista de ações rápidas** – se o aplicativo fornecer mais de um widget, o desenvolvedor deverá escolher aquele a ser apresentado quando o usuário abrir a lista de ações rápidas aplicando pressão ao ícone do aplicativo usando o 3D Touch.

Para obter mais detalhes sobre como trabalhar com widgets, consulte nossa [introdução às extensões](~/ios/platform/extensions.md), [introdução à documentação de toque 3D](~/ios/platform/3d-touch.md) e [Guia de programação de extensão de aplicativo](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html)da Apple.

## <a name="working-with-vibrancy"></a>Trabalhando com Vibrancy

O Vibrancy garante que o texto de um widget permaneça legível quando apresentado na luz do widget, segundo plano indefinido (fornecido pelo sistema). Antes do iOS 10, o desenvolvedor usaria um [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect) para o Vibrancy do widget. Por exemplo:

```csharp
// DEPRECATED: Get Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreateForNotificationCenter ();
```

Isso foi preterido no iOS 10 e deve ser substituído por um [WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect) ou um [WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect). Por exemplo:

```csharp
// Get Primary Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreatePrimaryVibrancyEffectForNotificationCenter ();

// Get Secondary Widget Vibrancy Effect
var vibrancy2 = UIVibrancyEffect.CreateSecondaryVibrancyEffectForNotificationCenter ();
```

## <a name="working-with-collapsed-and-expanded-widgets"></a>Trabalhando com widgets recolhidos e expandidos

Novo no iOS 10, os widgets agora contêm uma propriedade [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) que permite ao desenvolvedor descrever a quantidade de conteúdo disponível e permite que o usuário expanda e recolha o conteúdo.

Quando um widget é mostrado inicialmente, ele está em um estado recolhido. Um widget recolhido tem uma altura de aproximadamente duas e uma metade de linhas da tabela do iOS padrão. O desenvolvedor pode solicitar o tamanho de um widget expandido, mas, idealmente, deve ser menor que a altura da tela.

No estado recolhido, o widget deve mostrar somente informações essenciais e autônomas. Quando expandido, o widget deve mostrar informações complementares que aprimoram o conteúdo primário mostrado no estado recolhido. Por exemplo, o aplicativo meteorológico mostra as condições do clima atual quando recolhido e adiciona a previsão por hora quando expandida.

Os widgets mostrados na lista de ações rápidas só estarão no estado recolhido. Se o aplicativo fornecer mais de um widget, o desenvolvedor deverá escolher aquele a ser apresentado quando o usuário abrir a lista de ações rápidas aplicando pressão ao ícone do aplicativo usando o 3D Touch.

O exemplo a seguir é de uma extensão de hoje simples (widget) que manipula os Estados recolhidos e expandidos:

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

Dê uma olhada no código específico do modo de exibição do widget em detalhes. Para informar ao sistema que esse widget dá suporte ao estado expandido, ele usa:

```csharp
// Tell widget it can be expanded
ExtensionContext.SetWidgetLargestAvailableDisplayMode (NCWidgetDisplayMode.Expanded);
```

Para obter o modo de exibição atual do widget, ele usa:

```csharp
ExtensionContext.GetWidgetActiveDisplayMode()
```

Para obter o tamanho máximo do estado recolhido ou expandido, ele usa:

```csharp
// Get the maximum size
var maxSize = ExtensionContext.GetWidgetMaximumSize (NCWidgetDisplayMode.Expanded);
```

E para lidar com a alteração do estado (modo de exibição), ele usa:

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

Além de definir o tamanho solicitado para cada Estado (recolhido ou expandido), ele também atualiza o conteúdo que está sendo exibido para corresponder ao novo tamanho.

## <a name="summary"></a>Resumo

Este artigo abordou os aprimoramentos que a Apple fez no sistema de widget no iOS 10 e mostrou como implementá-los no Xamarin. iOS.

## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
- [Introdução às extensões](~/ios/platform/extensions.md)
- [Introdução ao toque 3D](~/ios/platform/3d-touch.md)
- [Guia de programação de extensão de aplicativo](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html)
