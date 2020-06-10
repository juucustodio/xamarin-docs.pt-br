---
Título: "InputView ordem de leitura no Windows" Descrição: "as especificações da plataforma permitem que você consuma a funcionalidade disponível apenas em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma específica do Windows que permite que a ordem de leitura do texto bidirecional seja detectada dinamicamente. "
MS. Prod: xamarin MS. AssetID: E61BAEE0-C8B7-4F33-8DDC-FA1B9CA8E81D MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 10/24/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="inputview-reading-order-on-windows"></a>InputView ordem de leitura no Windows

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa Plataforma Universal do Windows específica da plataforma habilita a ordem de leitura (da esquerda para a direita ou da direita para a esquerda) de texto bidirecional [`Entry`](xref:Xamarin.Forms.Entry) nas [`Editor`](xref:Xamarin.Forms.Editor) instâncias do, e [`Label`](xref:Xamarin.Forms.Label) que serão detectadas dinamicamente. Ele é consumido em XAML definindo o [`InputView.DetectReadingOrderFromContent`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.DetectReadingOrderFromContentProperty) (para `Entry` e as `Editor` instâncias) ou [`Label.DetectReadingOrderFromContent`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Label.DetectReadingOrderFromContentProperty) a propriedade anexada como um `boolean` valor:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Editor ... windows:InputView.DetectReadingOrderFromContent="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

editor.On<Windows>().SetDetectReadingOrderFromContent(true);
```

O `Editor.On<Windows>` método especifica que essa plataforma específica será executada somente no plataforma universal do Windows. O [ `InputView.SetDetectReadingOrderFromContent` ] (xref: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. InputView. SetDetectReadingOrderFromContent ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . InputView}, System. Boolean)), no [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace, é usado para controlar se a ordem de leitura é detectada do conteúdo no [`InputView`](xref:Xamarin.Forms.InputView) . Além disso, o `InputView.SetDetectReadingOrderFromContent` método pode ser usado para alternar se a ordem de leitura é detectada do conteúdo chamando [ `InputView.GetDetectReadingOrderFromContent` ] (xref: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. InputView. GetDetectReadingOrderFromContent ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . InputView})), o método para retornar o valor atual:

```csharp
editor.On<Windows>().SetDetectReadingOrderFromContent(!editor.On<Windows>().GetDetectReadingOrderFromContent());
```

O resultado é que [`Entry`](xref:Xamarin.Forms.Entry) , [`Editor`](xref:Xamarin.Forms.Editor) e [`Label`](xref:Xamarin.Forms.Label) as instâncias podem ter a ordem de leitura de seu conteúdo detectado dinamicamente:

[![InputView detectando a ordem de leitura do conteúdo específico da plataforma](inputview-reading-order-images/editor-readingorder.png "InputView detectando a ordem de leitura do conteúdo específico da plataforma")](inputview-reading-order-images/editor-readingorder-large.png#lightbox "InputView detectando a ordem de leitura do conteúdo específico da plataforma")

> [!NOTE]
> Ao contrário da definição da [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade, a lógica para exibições que detectam a ordem de leitura do conteúdo do texto não afetará o alinhamento do texto dentro da exibição. Em vez disso, ele ajusta a ordem na qual os blocos de texto bidirecional são dispostos.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
