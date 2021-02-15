---
ms.topic: include
author: jamont
ms.author: jamont
ms.date: 01/16/2020
ms.openlocfilehash: ecce7ce05a680e668eefe6b482e7be7314470c85
ms.sourcegitcommit: 2a7bbe9cbee3727ba20ee755c1713bcfdb4d8ecb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98950944"
---
Ao solicitar um compartilhamento ou abrir um iniciador no iPadOS, você tem a capacidade de apresentá-lo em um controle pop-up. Isso especifica onde o pop-up aparecerá e apontará uma seta diretamente para. Esse local geralmente é o controle que iniciou a ação. Você pode especificar o local usando a `PresentationSourceBounds` Propriedade:

```csharp
await Share.RequestAsync(new ShareFileRequest
{
    Title = Title,
    File = new ShareFile(file),
    PresentationSourceBounds = DeviceInfo.Platform== DevicePlatform.iOS && DeviceInfo.Idiom == DeviceIdiom.Tablet
                            ? new System.Drawing.Rectangle(0, 20, 0, 0)
                            : System.Drawing.Rectangle.Empty
});
```

```csharp
await Launcher.OpenAsync(new OpenFileRequest
{
    File = new ReadOnlyFile(file),
    PresentationSourceBounds = DeviceInfo.Platform== DevicePlatform.iOS && DeviceInfo.Idiom == DeviceIdiom.Tablet
                            ? new System.Drawing.Rectangle(0, 20, 0, 0)
                            : System.Drawing.Rectangle.Empty
});
```

Tudo o que é descrito aqui funciona igualmente para `Share` e `Launcher` .

Se você estiver usando o Xamarin. Forms, poderá passar um `View` e calcular os limites:

```csharp
public static class ViewHelpers
{
    public static Rectangle GetAbsoluteBounds(this Xamarin.Forms.View element)
    {
        Element looper = element;

        var absoluteX = element.X + element.Margin.Top;
        var absoluteY = element.Y + element.Margin.Left;

        // Add logic to handle titles, headers, or other non-view bars

        while (looper.Parent != null)
        {
            looper = looper.Parent;
            if (looper is Xamarin.Forms.View v)
            {
                absoluteX += v.X + v.Margin.Top;
                absoluteY += v.Y + v.Margin.Left;
            }
        }

        return new Rectangle(absoluteX, absoluteY, element.Width, element.Height);
    }

    public static System.Drawing.Rectangle ToSystemRectangle(this Rectangle rect) =>
        new System.Drawing.Rectangle((int)rect.X, (int)rect.Y, (int)rect.Width, (int)rect.Height);
}
```

Isso pode ser usado ao chamar `RequstAsync` :

```csharp
public Command<Xamarin.Forms.View> ShareCommand { get; } = new Command<Xamarin.Forms.View>(Share);
async void Share(Xamarin.Forms.View element)
{
    try
    {
        Analytics.TrackEvent("ShareWithFriends");
        var bounds = element.GetAbsoluteBounds();

        await Share.RequestAsync(new ShareTextRequest
        {
            PresentationSourceBounds = bounds.ToSystemRectangle(),
            Title = "Title",
            Text = "Text"
        });
    }
    catch (Exception)
    {
        // Handle exception that share failed
    }
}
```

Você pode passar o elemento de chamada quando o `Command` for disparado:

```xml
<Button Text="Share"
        Command="{Binding ShareWithFriendsCommand}"
        CommandParameter="{Binding Source={RelativeSource Self}}"/>
```
