---
Título: " Xamarin.Forms localização" Descrição: "a estrutura de localização interna do .net pode ser usada para criar aplicativos multilíngües entre plataformas com o Xamarin.Forms . Textos e imagens podem ser localizados, e os aplicativos podem dar suporte a uma direção de fluxo da direita para a esquerda. "
MS. Prod: xamarin MS. AssetID: 97BF843B-BDAA-4CEA-8189-6DB54B291D7F MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 11/07/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-localization"></a>Xamarin.FormsLocalizar

_A estrutura de localização interna do .NET pode ser usada para criar aplicativos multilíngües entre plataformas com o Xamarin.Forms ._

## <a name="xamarinforms-string-and-image-localizationtextmd"></a>[Xamarin.FormsLocalização de cadeia de caracteres e imagem](text.md)

O mecanismo interno para localização de usos de aplicativos .NET [arquivos RESX](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps#resources-in-resx-files) e as classes nos namespaces `System.Resources` e `System.Globalization`. Os arquivos RESX que contêm cadeias de caracteres traduzidas são inseridos no Xamarin.Forms assembly, juntamente com uma classe gerada pelo compilador que fornece acesso fortemente tipado às traduções. O texto traduzido pode ser então recuperado no código.

## <a name="right-to-left-localization"></a>[Localização da direita para a esquerda](right-to-left.md)

A direção do fluxo é a direção na qual os elementos de interface do usuário na página são detectados pelos olhos. A localização da direita para a esquerda adiciona suporte à direção de fluxo da direita para a esquerda para Xamarin.Forms aplicativos.
