---
title: Visualizações da Web em Xamarin.iOS
description: Este documento descreve as várias maneiras pelas quais um aplicativo Xamarin.iOS pode exibir conteúdo da Web. Ele discute a segurança do WKWebView, SFSafariViewController, Safari e segurança de transporte de aplicativos.
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: cc6c87452bf5312d66e33b7c49d3dc216eceb7d6
ms.sourcegitcommit: b93754b220fca3d6e3d131341e3cfbe233d10f84
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80628294"
---
# <a name="web-views-in-xamarinios"></a>Visualizações da Web em Xamarin.iOS

Ao longo da vida do iOS, a Apple lançou uma série de maneiras para os desenvolvedores de aplicativos incorporarem a funcionalidade de visualização web em seus aplicativos. A maioria dos usuários utiliza o navegador Safari incorporado em seu dispositivo iOS e, portanto, espera que a funcionalidade de visualização da Web de outros aplicativos seja consistente com essa experiência. Eles esperam que os mesmos gestos funcionem, o desempenho seja igual e a funcionalidade igual.

O iOS 11 introduziu `WKWebView` `SFSafariViewController`novas mudanças em ambos e . Para obter mais informações sobre isso, consulte as alterações da Web no guia [do iOS 11.](~/ios/platform/introduction-to-ios11/web.md)

## <a name="wkwebview"></a>WKWebView

`WKWebView`foi introduzido no iOS 8 permitindo que os desenvolvedores de aplicativos implementassem uma interface de navegação na Web semelhante à do Safari móvel. Isso se deve, em parte, `WKWebView` ao fato de que usa o motor Nitro Javascript, o mesmo motor usado pelo Safari móvel. `WKWebView`deve ser sempre usado no UIWebView, sempre que possível, devido ao aumento do desempenho, construído em gestos fáceis de usar e a facilidade de interação entre a página da Web e seu aplicativo.

`WKWebView`pode ser adicionado ao seu aplicativo de uma forma quase idêntica ao UIWebView, no entanto, como desenvolvedor você tem muito mais controle sobre a UI/UX e funcionalidade. A criação e exibição do objeto de exibição da Web exibirá a página solicitada, no entanto, você pode controlar como a exibição é apresentada, como o usuário pode navegar e como o usuário sai da exibição.  

O código abaixo pode ser `WKWebView` usado para lançar um em seu aplicativo Xamarin.iOS:

```csharp
WKWebView webView = new WKWebView(View.Frame, new WKWebViewConfiguration());
View.AddSubview(webView);

var url = new NSUrl("https://docs.microsoft.com");
var request = new NSUrlRequest(url);
webView.LoadRequest(request);
```

É importante notar `WKWebView` que está `WebKit` no namespace, então você terá que adicionar isso usando diretiva ao topo da sua classe.

`WKWebView`também pode ser usado em aplicativos Xamarin.Mac, e você deve usá-lo se estiver criando um aplicativo Mac/iOS multiplataforma.

A receita [Handle JavaScript Alerts](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/handle_javascript_alerts) também fornece informações sobre como usar o WKWebView com Javascript.

## <a name="sfsafariviewcontroller"></a>SFSafariViewController

 `SFSafariViewController`é a última maneira de fornecer conteúdo web do seu aplicativo e está disponível no iOS 9 e posterior. Ao `UIWebView` `WKWebView`contrário `SFSafariViewController` ou , é um Controlador de Exibição e, portanto, não pode ser usado com outros pontos de vista. Você deve `SFSafariViewController` apresentar-se como um novo Controlador de Exibição, da mesma forma que apresentaria qualquer Controlador de Exibição.

 `SFSafariViewController`é essencialmente um "mini safári" que pode ser incorporado em seu aplicativo. Como o WKWebView, ele usa o mesmo Motor Nitro Javascript, mas também fornece uma gama de recursos adicionais do Safari, como AutoFill, Reader e a capacidade de compartilhar cookies e dados com o Safari móvel. A interação entre `SFSafariViewController` o usuário e o não está acessível ao seu aplicativo. Seu aplicativo não terá acesso a nenhum dos recursos padrão do Safari.

Ele também, por padrão, implementa um botão **Done,** permitindo que o usuário retorne facilmente ao seu aplicativo, e botões de navegação para frente e para trás, permitindo que o usuário navegue por uma pilha de páginas da Web. Além disso, ele também fornece ao usuário uma barra de endereços dando-lhe a tranquilidade de que eles estão na página web esperada. A barra de endereços não permite que o usuário altere a url.

Essas implementações não podem `SFSafariViewController` ser alteradas, por isso é ideal usar como navegador padrão se o seu aplicativo quiser apresentar uma página web sem qualquer personalização.

O código abaixo pode ser `SFSafariViewController` usado para lançar um em seu aplicativo Xamarin.iOS:

```csharp
var sfViewController = new SFSafariViewController(url);

PresentViewController(sfViewController, true, null);
```

Isso produz a seguinte visualização web:

[![Uma exibição web de exemplo com o SFSafariViewController](webview-images/sfsafariviewcontroller.png)](webview-images/sfsafariviewcontroller.png#lightbox)

## <a name="safari"></a>Safari

Também é possível abrir o aplicativo safari móvel de dentro do seu aplicativo, usando o código abaixo:

```csharp
var url = new NSUrl("https://docs.microsoft.com");

UIApplication.SharedApplication.OpenUrl(url);
```

Isso produz a seguinte visualização web:

[![Uma página web apresentada no Safari](webview-images/safari.png)](webview-images/safari.png#lightbox)

Navegar usuários longe do seu aplicativo para o Safari geralmente deve ser sempre evitado. A maioria dos usuários não espera navegação fora do seu aplicativo, portanto, se você navegar para longe do seu aplicativo, os usuários podem nunca devolvê-lo, essencialmente matando o engajamento.

As melhorias do iOS 9 permitem que o usuário retorne facilmente ao seu aplicativo através de um botão traseiro que é fornecido no canto superior esquerdo da página do Safari.

## <a name="app-transport-security"></a>Segurança do transporte de aplicativo

O App Transport Security, ou *ATS,* foi introduzido pela Apple no iOS 9 para garantir que todas as comunicações de internet estejam em conformidade com as práticas recomendadas de conexão seguras.

Para obter mais informações sobre o ATS, incluindo como implementá-lo em seu aplicativo, consulte o guia [de segurança de transporte de aplicativos.](~/ios/app-fundamentals/ats.md)

## <a name="uiwebview-deprecation"></a>Depreciação uiWebView

`UIWebView`é a maneira herdada da Apple de fornecer conteúdo web em seu aplicativo. Foi lançado no iOS 2.0, e foi preterido a partir de 8.0.

> [!IMPORTANT]
> O `UIWebView` foi preterido. Novos aplicativos que utilizam este controle [não serão aceitos na App Store a partir de abril de 2020, e as atualizações de aplicativos usando este controle não serão aceitas até dezembro de 2020](https://developer.apple.com/news/?id=12232019b).
>
> [A documentação `UIWebView` da Apple](https://developer.apple.com/documentation/uikit/uiwebview) sugere que os aplicativos devem ser usados [`WKWebView`](#wkwebview) em seu lugar.
>
> Se você estiver procurando recursos `UIWebView` em relação ao aviso de depreciação (ITMS-90809) ao usar o Xamarin.Forms, consulte a documentação [do Xamarin.Forms WebView.](~/xamarin-forms/user-interface/webview.md#uiwebview-deprecation-and-app-store-rejection-itms-90809)

Os desenvolvedores que enviaram aplicativos para iOS nos últimos seis meses (mais ou `UIWebView` menos) podem ter recebido um aviso da App Store, sobre serem preteridos.

Depreciações de APIs são comuns. Xamarin.iOS usa atributos personalizados para sinalizar essas APIs (e sugerir substituições quando disponíveis) de volta para os desenvolvedores. O que é diferente desta vez, e muito menos comum, é que a depreciação **será imposta** pela App Store da Apple no momento da apresentação.

Infelizmente, remover o `UIWebView` tipo `Xamarin.iOS.dll` de é uma [mudança binária de quebra](https://docs.microsoft.com/dotnet/core/compatibility/categories#binary-compatibility). Essa mudança quebrará bibliotecas de terceiros existentes, incluindo algumas que podem não ser mais suportadas ou até mesmo re-compiladas (por exemplo, fonte fechada). Isso só criará problemas adicionais para os desenvolvedores. Portanto, ainda não estamos removendo o *tipo.*

A partir [do Xamarin.iOS 13.16,](https://docs.microsoft.com/xamarin/ios/release-notes/13/13.16) novas ferramentas `UIWebView`e detecção estão disponíveis para ajudá-lo a migrar de .

### <a name="detection"></a>Detecção

Se você enviou recentemente um aplicativo para iOS na Apple App Store, você pode se perguntar se essa situação se aplica aos seus aplicativos( s).

Para descobrir, você `--warn-on-type-ref=UIKit.UIWebView` pode adicionar aos **argumentos adicionais** de mtouch do seu projeto. Isso avisará **sobre qualquer** referência ao `UIWebView` preterido dentro de sua aplicação (e todas as suas dependências). Diferentes avisos são usados para relatar tipos **antes** e **depois** que o linker gerenciado foi executado.

Os avisos, como outros, podem `-warnaserror:`ser transformados em erros usando . Isso pode ser útil se você quiser `UIWebView` garantir que uma nova dependência não seja adicionada após suas verificações. Por exemplo:

* `-warnaserror:1502`relatará erros se quaisquer referências forem encontradas em conjuntos pré-vinculados.
* `-warnaserror:1503`relatará erros se quaisquer referências forem encontradas em assembléias pós-vinculadas.

Você também pode silenciar os avisos se os resultados de vinculação pré/post não forem úteis. Por exemplo:

* `-nowarn:1502`**não** relatará avisos se quaisquer referências forem encontradas em assembléias pré-vinculadas.
* `-nowarn:1503`**não** relatará avisos se quaisquer referências forem encontradas em assembléias pós-vinculadas.

### <a name="removal"></a>Remoção

Cada aplicação é única. A `UIWebView` remoção do aplicativo pode exigir diferentes etapas, dependendo de como e onde ele é usado. Os cenários mais comuns são os seguintes:

- Não há uso `UIWebView` de dentro de sua aplicação. Tudo está bem. Você **não** deve ter avisos ao enviar para a AppStore. Nada mais é exigido de você.
- Uso direto `UIWebView` de sua aplicação. Comece removendo o `UIWebView`uso de , por exemplo, `WKWebView` substituí-lo pelos `SFSafariViewController` tipos mais novos (iOS 8) ou (iOS 9). Uma vez concluído, o linker gerenciado não `UIWebView` deve ver nenhuma referência e o binário final do aplicativo não terá nenhum vestígio dele.
- Uso indireto. `UIWebView`pode estar presente em algumas bibliotecas de terceiros, gerenciadas ou nativas que são usadas pelo seu aplicativo. Comece atualizando suas dependências externas para suas versões mais recentes, pois essa situação já pode ser resolvida em uma versão mais recente. Caso assim, entre em contato com os mantenedores das bibliotecas e pergunte sobre seus planos de atualização.

Alternativamente, você pode tentar as seguintes abordagens:

1. Se você estiver usando **Xamarin.Forms,** leia este [post no blog](https://devblogs.microsoft.com/xamarin/uiwebview-deprecation-xamarin-forms/).
1. Habilite o linker gerenciado (em todo o projeto ou, pelo menos, na dependência de uso) `UIWebView`para que ele *possa* ser removido, se não for referenciado. Isso resolverá o problema, mas pode exigir trabalho adicional para tornar seu linker de código seguro.
1. Se você não conseguir alterar as configurações do linker gerenciado, consulte os casos especiais abaixo.

#### <a name="applications-cannot-use-the-linker-or-change-its-settings"></a>Os aplicativos não podem usar o linker (ou alterar suas configurações)

Se por algum motivo você **não** estiver usando o linker gerenciado (por exemplo, **Não vincule**) então o `UIWebView` símbolo permanecerá no aplicativo binário que você envia para a Apple e ele pode ser rejeitado.

Uma solução *vigorosa* é `--optimize=force-rejected-types-removal` adicionar aos **argumentos adicionais**de mtouch do seu projeto. Isso removerá vestígios da `UIWebView` aplicação. No entanto, qualquer código que se refira ao tipo **não** funcionará corretamente (espere exceções ou falhas). Essa abordagem só deve ser usada se você tiver certeza de que o código não pode ser alcançado em tempo de execução (mesmo que seja acessível através de análise estática).

#### <a name="support-for-ios-7x-or-earlier"></a>Suporte para iOS 7.x (ou anterior)

`UIWebView`faz parte do iOS desde o v2.0. As substituições mais `WKWebView` comuns são `SFSafariViewController` (iOS 8) e (iOS 9). Se o seu aplicativo ainda suportar versões mais antigas do iOS, você deve considerar as seguintes opções:

* Faça do iOS 8 sua versão de destino mínima (uma decisão de tempo de construção).
* Só `WKWebView` use se o aplicativo estiver sendo executado no iOS 8+ (uma decisão em tempo de execução).

#### <a name="applications-not-submitted-to-apple"></a>Aplicativos não enviados à Apple

Se o seu aplicativo não for submetido à Apple, você deve planejar se afastar da API depreciada, já que ela pode ser removida em futuras versões do iOS. No entanto, você pode fazer essa transição usando seu próprio cronograma.

## <a name="related-links"></a>Links relacionados

- [WebViews (amostra)](https://docs.microsoft.com/samples/xamarin/ios-samples/webview)
