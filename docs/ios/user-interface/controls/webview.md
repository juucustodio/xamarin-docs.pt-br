---
title: Exibições da Web no Xamarin. iOS
description: Este documento descreve as várias maneiras como um aplicativo Xamarin. iOS pode exibir conteúdo da Web. Ele aborda WKWebView, SFSafariViewController, Safari e segurança de transporte de aplicativo.
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: bf8f6a9014192d680b0032e034e34b594ecf193c
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430536"
---
# <a name="web-views-in-xamarinios"></a>Exibições da Web no Xamarin. iOS

Ao longo do tempo de vida do iOS, a Apple lançou várias maneiras para os desenvolvedores de aplicativos incorporarem a funcionalidade de exibição da Web em seus aplicativos. A maioria dos usuários utiliza o navegador da Web interno do Safari em seu dispositivo iOS e, portanto, espera que a funcionalidade de exibição da Web de outros aplicativos seja consistente com essa experiência. Eles esperam que os mesmos gestos funcionem, o desempenho deve estar em par e a funcionalidade do mesmo.

o iOS 11 introduziu novas alterações no `WKWebView` e no `SFSafariViewController` . Para obter mais informações sobre eles, consulte as [alterações na Web no guia do guia do IOS 11](~/ios/platform/introduction-to-ios11/web.md) .

## <a name="wkwebview"></a>WKWebView

`WKWebView` foi introduzido no iOS 8, permitindo que os desenvolvedores de aplicativos implementem uma interface de navegação na Web semelhante à do Mobile Safari. Isso é devido, em parte, ao fato que `WKWebView` usa o mecanismo de JavaScript nitro, o mesmo mecanismo usado pelo Mobile Safari. `WKWebView` sempre deve ser usado em UIWebView, quando possível, devido ao aumento do desempenho, aos gestos internos amigáveis do usuário e à facilidade de interação entre a página da Web e seu aplicativo.

`WKWebView` pode ser adicionado ao seu aplicativo de maneira quase idêntica ao UIWebView, no entanto, como desenvolvedor, você tem muito mais controle sobre a interface do usuário/UX e a funcionalidade. Criar e exibir o objeto de exibição da Web exibirá a página solicitada, no entanto, você pode controlar como a exibição é apresentada, como o usuário pode navegar e como o usuário sai da exibição.  

O código a seguir pode ser usado para iniciar um `WKWebView` em seu aplicativo Xamarin. Ios:

```csharp
WKWebView webView = new WKWebView(View.Frame, new WKWebViewConfiguration());
View.AddSubview(webView);

var url = new NSUrl("https://docs.microsoft.com");
var request = new NSUrlRequest(url);
webView.LoadRequest(request);
```

É importante observar que `WKWebView` está no `WebKit` namespace, portanto, você precisará adicioná-lo usando a diretiva na parte superior da sua classe.

`WKWebView` também pode ser usado em aplicativos Xamarin. Mac e você deve usá-lo se estiver criando um aplicativo Mac/iOS de plataforma cruzada.

A receita [lidar com alertas JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/handle_javascript_alerts) também fornece informações sobre como usar o WKWebView com JavaScript.

## <a name="sfsafariviewcontroller"></a>SFSafariViewController

 `SFSafariViewController` é a maneira mais recente de fornecer conteúdo da Web do seu aplicativo e está disponível no iOS 9 e posterior. Ao contrário de `UIWebView` ou `WKWebView` , `SFSafariViewController` é um controlador de exibição e, portanto, não pode ser usado com outras exibições. Você deve apresentar `SFSafariViewController` como um novo controlador de exibição, da mesma maneira que apresentaria qualquer controlador de exibição.

 `SFSafariViewController` é essencialmente um "mini safari" que pode ser inserido em seu aplicativo. Como o WKWebView, ele usa o mesmo mecanismo de JavaScript nitro, mas também fornece uma variedade de recursos adicionais do Safari, como preenchimento automático, leitor e a capacidade de compartilhar cookies e dados com o Mobile Safari. A interação entre o usuário e o `SFSafariViewController` não é acessível para seu aplicativo. Seu aplicativo não terá acesso a nenhum dos recursos padrão do Safari.

Ele também, por padrão, implementa um botão **Done** , permitindo que o usuário retorne facilmente ao seu aplicativo e botões de navegação para frente e para trás, permitindo que o usuário navegue por uma pilha de páginas da Web. Além disso, ele também fornece ao usuário uma barra de endereços, oferecendo a tranqüilidade de que eles estejam na página da Web esperada. A barra de endereços não permite que o usuário altere a URL.

Essas implementações não podem ser alteradas, portanto, `SFSafariViewController` é ideal para usar como o navegador padrão se seu aplicativo quiser apresentar uma página da Web sem nenhuma personalização.

O código a seguir pode ser usado para iniciar um `SFSafariViewController` em seu aplicativo Xamarin. Ios:

```csharp
var sfViewController = new SFSafariViewController(url);

PresentViewController(sfViewController, true, null);
```

Isso produz a seguinte exibição da Web:

[![Um exemplo de exibição da Web com SFSafariViewController](webview-images/sfsafariviewcontroller.png)](webview-images/sfsafariviewcontroller.png#lightbox)

## <a name="safari"></a>Safari

Também é possível abrir o aplicativo Mobile Safari de dentro de seu aplicativo, usando o código abaixo:

```csharp
var url = new NSUrl("https://docs.microsoft.com");

UIApplication.SharedApplication.OpenUrl(url);
```

Isso produz a seguinte exibição da Web:

[![Uma página da Web apresentada no Safari](webview-images/safari.png)](webview-images/safari.png#lightbox)

A navegação de usuários fora do seu aplicativo para o Safari geralmente deve ser evitada sempre. A maioria dos usuários não esperará a navegação fora do seu aplicativo, portanto, se você sair do seu aplicativo, os usuários nunca poderão retorná-lo, essencialmente eliminando o envolvimento.

os aprimoramentos do iOS 9 permitem que o usuário retorne facilmente ao seu aplicativo por meio de um botão voltar que é fornecido no canto superior esquerdo da página do Safari.

## <a name="app-transport-security"></a>Segurança do transporte de aplicativo

A segurança de transporte de aplicativo ou a *ATS* foi introduzida pela Apple no Ios 9 para garantir que todas as comunicações da Internet estejam em conformidade com as práticas recomendadas de conexão seguras.

Para obter mais informações sobre o ATS, incluindo como implementá-lo em seu aplicativo, consulte o guia de [segurança do transporte de aplicativo](~/ios/app-fundamentals/ats.md) .

## <a name="uiwebview-deprecation"></a>Reprovação de UIWebView

`UIWebView` é a maneira herdada da Apple de fornecer conteúdo da Web em seu aplicativo. Ele foi lançado no iOS 2,0 e foi preterido a partir de 8,0.

> [!IMPORTANT]
> O `UIWebView` foi preterido. Os novos aplicativos que usam esse controle [não serão aceitos na loja de aplicativos a partir de abril de 2020 e as atualizações de aplicativos que usam esse controle não serão aceitas até 2020 de dezembro](https://developer.apple.com/news/?id=12232019b).
>
> A [ `UIWebView` documentação da Apple](https://developer.apple.com/documentation/uikit/uiwebview) sugere que aplicativos devem usar o [`WKWebView`](#wkwebview) em vez disso.
>
> Se você estiver procurando recursos em relação ao aviso de `UIWebView` reprovação (ITMS-90809) ao usar o xamarin. Forms, consulte a documentação do [WebView Xamarin. Forms](~/xamarin-forms/user-interface/webview.md#uiwebview-deprecation-and-app-store-rejection-itms-90809) .

Os desenvolvedores que enviaram aplicativos iOS nos últimos seis meses (ou mais) podem ter recebido um aviso da loja de aplicativos, sobre como `UIWebView` serem preteridos.

As preterições das APIs são comuns. O Xamarin. iOS usa atributos personalizados para sinalizar essas APIs (e sugerir substituições quando disponíveis) de volta para os desenvolvedores. O que é diferente desta vez e muito menos comum é que a substituição **será imposta** pela loja de aplicativos da Apple no momento do envio.

Infelizmente, a remoção do `UIWebView` tipo de `Xamarin.iOS.dll` é uma [alteração de quebra binária](/dotnet/core/compatibility/categories#binary-compatibility). Essa alteração interromperá as bibliotecas de terceiros existentes, incluindo algumas que podem não ter suporte ou até mesmo mais compiláveis (por exemplo, código-fonte fechado). Isso criará apenas problemas adicionais para os desenvolvedores. Portanto, *ainda*não estamos removendo o tipo.

A partir do [Xamarin. iOS 13,16](/xamarin/ios/release-notes/13/13.16) , novas ferramentas e detecção estão disponíveis para ajudá-lo a migrar do `UIWebView` .

### <a name="detection"></a>Detecção

Se você have'nt recentemente um aplicativo iOS para a Apple App Store, você pode se perguntar se essa situação se aplica ao (s) aplicativo (es).

Para descobrir, você pode adicionar `--warn-on-type-ref=UIKit.UIWebView` aos **argumentos de mTouch adicionais** do seu projeto. Isso avisará sobre **qualquer** referência para o preterido `UIWebView` dentro de seu aplicativo (e todas as suas dependências). Diferentes avisos são usados para relatar tipos **antes** e **após** a execução do vinculador gerenciado.

Os avisos, como outros, podem ser convertidos em erros usando `-warnaserror:` . Isso pode ser útil se você quiser garantir que uma nova dependência `UIWebView` não seja adicionada após suas verificações. Por exemplo:

* `-warnaserror:1502` relatará erros se quaisquer referências forem encontradas em assemblies previamente vinculados.
* `-warnaserror:1503` relatará erros se quaisquer referências forem encontradas em assemblies pós-vinculados.

Você também pode silenciar os avisos se os resultados de vinculação prévia/pós não forem úteis. Por exemplo:

* `-nowarn:1502`**não** relatará avisos se quaisquer referências forem encontradas em assemblies previamente vinculados.
* `-nowarn:1503`**não** relatará avisos se quaisquer referências forem encontradas em assemblies pós-vinculados.

### <a name="removal"></a>Remoção

Cada aplicativo é exclusivo. `UIWebView`A remoção do seu aplicativo pode exigir etapas diferentes, dependendo de como e onde ela é usada. Os cenários mais comuns são os seguintes:

- Não há nenhum uso de `UIWebView` dentro de seu aplicativo. Tudo está bem. Você **não** deve ter avisos ao enviar para o AppStore. Nada mais é necessário de você.
- Uso direto do `UIWebView` seu aplicativo. Comece removendo o uso do `UIWebView` , por exemplo, substitua-o pelos tipos mais recentes `WKWebView` (Ios 8) ou `SFSafariViewController` (Ios 9). Depois que isso for concluído, o vinculador gerenciado não deverá ver nenhuma referência a `UIWebView` e o binário do aplicativo final não terá nenhum rastreamento.
- Uso indireto. `UIWebView` pode estar presente em algumas bibliotecas de terceiros, gerenciadas ou nativas, que são usadas pelo seu aplicativo. Comece atualizando suas dependências externas para suas versões mais recentes, pois essa situação já pode estar resolvida em uma versão mais recente. Caso contrário, entre em contato com os mantenedores das bibliotecas e pergunte sobre seus planos de atualização.

Como alternativa, você pode tentar as seguintes abordagens:

1. Se você estiver usando o **Xamarin. Forms**, leia esta [postagem no blog](https://devblogs.microsoft.com/xamarin/uiwebview-deprecation-xamarin-forms/).
1. Habilite o vinculador gerenciado (no projeto inteiro ou, pelo menos, na dependência usando `UIWebView` ) para que ele *possa* ser removido, se não for referenciado. Isso resolverá o problema, mas poderá exigir trabalho adicional para tornar o seu código seguro para o vinculador.
1. Se não for possível alterar as configurações do vinculador gerenciado, consulte os casos especiais abaixo.

#### <a name="applications-cannot-use-the-linker-or-change-its-settings"></a>Os aplicativos não podem usar o vinculador (ou alterar suas configurações)

Se, por alguma razão, você **não** estiver usando o vinculador gerenciado (por exemplo, **não vincular**), o `UIWebView` símbolo permanecerá no aplicativo binário que você o enviará para a Apple e poderá ser rejeitado.

Uma solução de *força* é adicionar `--optimize=force-rejected-types-removal` aos **argumentos de mTouch adicionais**do seu projeto. Isso removerá os rastreamentos do `UIWebView` aplicativo. No entanto, qualquer código que se refere ao tipo **não** funcionará corretamente (espera-se exceções ou falhas). Essa abordagem só deve ser usada se você tiver certeza de que o código não está acessível em tempo de execução (mesmo se ele estivesse acessível por meio de análise estática).

#### <a name="support-for-ios-7x-or-earlier"></a>Suporte para iOS 7. x (ou anterior)

`UIWebView` faz parte do iOS desde o v 2.0. As substituições mais comuns são `WKWebView` (Ios 8) e `SFSafariViewController` (Ios 9). Se seu aplicativo ainda oferecer suporte a versões mais antigas do iOS, considere as seguintes opções:

* Torne o iOS 8 sua versão de destino mínima (uma decisão de tempo de compilação).
* Use somente `WKWebView` se o aplicativo estiver em execução no Ios 8 + (uma decisão de tempo de execução).

#### <a name="applications-not-submitted-to-apple"></a>Aplicativos não enviados à Apple

Se seu aplicativo não for enviado para a Apple, você deverá planejar para sair da API preterida, já que ela pode ser removida em futuras versões do iOS. No entanto, você pode fazer essa transição usando seu próprio timetable.

## <a name="related-links"></a>Links relacionados

- [Webviews (exemplo)](/samples/xamarin/ios-samples/webview)