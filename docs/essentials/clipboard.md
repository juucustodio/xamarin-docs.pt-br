---
Título: ' ' Xamarin.Essentials : área de transferência ' ' Descrição: ' este documento descreve a classe de área de transferência no Xamarin.Essentials , que permite copiar e colar texto na área de transferência do sistema entre aplicativos. '
MS. AssetID: autor: MS. Author: MS. Data: MS. Custom: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---

# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials: Área de transferência

A classe **Clipboard** permite que você copie e cole o texto para a área de transferência do sistema entre aplicativos.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-clipboard"></a>Como usar Clipboard

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

Para verificar se a **Área de Transferência** tem algum texto pronto para ser colado:

```csharp
var hasText = Clipboard.HasText;
```

Para definir o texto na **Área de Transferência**:

```csharp
await Clipboard.SetTextAsync("Hello World");
```

Para ler o texto da **Área de Transferência**:

```csharp
var text = await Clipboard.GetTextAsync();
```

Sempre que qualquer um dos conteúdos da área de transferência tiver alterado, um evento será disparado:

```csharp
public class ClipboardTest
{
    public ClipboardTest()
    {
        // Register for clipboard changes, be sure to unsubscribe when needed
        Clipboard.ClipboardContentChanged += OnClipboardContentChanged;
    }

    void OnClipboardContentChanged(object sender, EventArgs    e)
    {
        Console.WriteLine($"Last clipboard change at {DateTime.UtcNow:T}";);
    }
}
```

> [!TIP]
> O acesso à Área de Transferência deve ser feito no thread de interface do usuário principal. Confira a API do [MainThread](~/essentials/main-thread.md) para ver como invocar métodos no thread de interface do usuário principal.

## <a name="api"></a>API

- [Código-fonte de Área de Transferência](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [Documentação de API de Área de Transferência](xref:Xamarin.Essentials.Clipboard)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Clipboard-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
