---
Título: " Xamarin.Forms ciclo de vida do aplicativo": "Este artigo explica como responder ao ciclo de vida do aplicativo, incluindo métodos de ciclo de vida, eventos de notificação de página e eventos de navegação modal".
MS. Prod: xamarin MS. AssetID: 69B416CF-B243-4790-AB29-F030B32465BE MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 05/31/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-app-lifecycle"></a>Xamarin.FormsCiclo de vida do aplicativo

A [`Application`](xref:Xamarin.Forms.Application) classe base fornece os seguintes recursos:

- [Métodos de ciclo](#lifecycle-methods) `OnStart` de vida, `OnSleep` e `OnResume` .
- [Eventos de navegação de página](#page-navigation-events) [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) , [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) .
- [Eventos de navegação modal](#modal-navigation-events) `ModalPushing` , `ModalPushed` , `ModalPopping` e `ModalPopped` .

## <a name="lifecycle-methods"></a>Métodos de ciclo de vida

A [`Application`](xref:Xamarin.Forms.Application) classe contém três métodos virtuais que podem ser substituídos para responder às alterações do ciclo de vida:

- `OnStart` – chamado quando o aplicativo é iniciado.
- `OnSleep` – chamado sempre que o aplicativo vai para segundo plano.
- `OnResume` – chamado quando o aplicativo é retomado, após ter sido enviado para o segundo plano.

> [!NOTE]
> Não há *nenhum* método de encerramento do aplicativo. Em circunstâncias normais (ou seja, não uma falha), o encerramento do aplicativo acontecerá do estado *OnSleep*, sem notificações adicionais em seu código.

Para observar quando esses métodos são chamados, implemente uma chamada `WriteLine` em cada um (conforme mostrado abaixo) e teste em cada plataforma.

```csharp
protected override void OnStart()
{
    Debug.WriteLine ("OnStart");
}
protected override void OnSleep()
{
    Debug.WriteLine ("OnSleep");
}
protected override void OnResume()
{
    Debug.WriteLine ("OnResume");
}
```

> [!IMPORTANT]
> No Android, o método `OnStart` será chamado em rotação, assim como quando o aplicativo for iniciado pela primeira vez, se a atividade principal não tiver `ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation` no atributo `[Activity()]`.

## <a name="page-navigation-events"></a>Eventos de navegação de página

Há dois eventos na [`Application`](xref:Xamarin.Forms.Application) classe que fornecem notificação de páginas que aparecem e desaparecem:

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing)-gerado quando uma página está prestes a aparecer na tela.
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing)-gerado quando uma página está prestes a desaparecer da tela.

Esses eventos podem ser usados em cenários em que você deseja controlar páginas à medida em que aparecem na tela.

> [!NOTE]
> Os [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) eventos e são gerados a partir da [`Page`](xref:Xamarin.Forms.Page) classe base imediatamente após os [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) eventos e [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing) , respectivamente.

## <a name="modal-navigation-events"></a>Eventos de navegação modal

Há quatro eventos na [`Application`](xref:Xamarin.Forms.Application) classe, cada um com seus próprios argumentos de evento, que permitem que você responda às páginas modais que estão sendo mostradas e ignoradas:

- `ModalPushing` – acionado quando uma página é enviada por push de forma modal.
- `ModalPushed` – acionado após uma página ter sido enviada por push de forma modal.
- `ModalPopping` – acionado quando uma página é removida mais recente da pilha de forma modal.
- `ModalPopped` – acionado após uma página ter sido removida mais recente da pilha de forma modal.

> [!NOTE]
> Os argumentos do evento `ModalPopping`, do tipo `ModalPoppingEventArgs`, contêm uma propriedade `Cancel`. Quando `Cancel` é definido como `true`, a remoção modal é cancelada.
