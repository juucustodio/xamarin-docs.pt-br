---
Título: ' ' Xamarin.Essentials : Descrição da discagem telefônica ' ': ' a classe PhoneDialer no Xamarin.Essentials permite que um aplicativo Abra um número de telefone na discagem ' MS. AssetID: autor: MS. Custom: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---

# <a name="xamarinessentials-phone-dialer"></a>Xamarin.Essentials: Discagem telefônica

A classe **PhoneDialer** permite que um aplicativo abra um número de telefone pelo discador.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-phone-dialer"></a>Como usar a Discagem telefônica

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de Discagem telefônica chama o método `Open` com um número de telefone para abrir o discador. Quando `Open` é solicitada, a API automaticamente tenta formatar o número com base no código de país, caso tenha sido especificado.

```csharp
public class PhoneDialerTest
{
    public void PlacePhoneCall(string number)
    {
        try
        {
            PhoneDialer.Open(number);
        }
        catch (ArgumentNullException anEx)
        {
            // Number was null or white space
        }
        catch (FeatureNotSupportedException ex)
        {
            // Phone Dialer is not supported on this device.
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

## <a name="api"></a>API

- [Código-fonte de Discagem telefônica](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/PhoneDialer)
- [Documentação da API de Discagem Telefônica](xref:Xamarin.Essentials.PhoneDialer)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Phone-Dialer-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
