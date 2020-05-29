---
Título: Xamarin.Forms Descrição de "plataformas com suporte": "requisitos de sistema de plataforma e desenvolvimento para Xamarin.Forms ."
MS. Prod: xamarin MS. AssetID: eecaf6a5-567C-49b2-ac83-2a195596c5bf MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 01/22/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-supported-platforms"></a>Xamarin.Formsplataformas com suporte

Xamarin.Formsos aplicativos podem ser escritos para os seguintes sistemas operacionais:

- iOS 9 ou superior.
- Android 4.4 (API 19) ou superior ([mais detalhes](#android-platform-support)). No entanto, o Android 5.0 (API 21) é recomendado como a API mínima. Isso garante a compatibilidade total com todas as bibliotecas compatíveis com Android, embora ainda visando a maioria dos dispositivos Android.
- Plataforma Universal do Windows para o Windows 10.

Xamarin.Formsaplicativos para iOS, Android e Plataforma Universal do Windows (UWP) podem ser criados no Visual Studio. Porém, um Mac em rede é necessário para o desenvolvimento do iOS usando a versão mais recente do Xcode e a versão mínima do macOS especificada pela Apple. Para obter mais informações, veja [Requisitos do Windows](~/cross-platform/get-started/requirements.md#windows-requirements).

Xamarin.Formsos aplicativos para iOS e Android podem ser incorporados Visual Studio para Mac. Para obter mais informações, veja [Requisitos do macOS](~/cross-platform/get-started/requirements.md#macos-requirements).

> [!NOTE]
> O desenvolvimento de aplicativos usando o Xamarin.Forms requer familiaridade com o [.net Standard](~/cross-platform/app-fundamentals/net-standard.md).

## <a name="additional-platform-support"></a>Suporte adicional da plataforma

Xamarin.Formsdá suporte a plataformas adicionais além do iOS, Android e Windows:

- Samsung Tizen
- macOS 10,13 ou superior
- GTK#
- WPF

O status dessas plataformas está disponível no wiki de [ Xamarin.Forms suporte à plataforma GitHub](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support).

## <a name="android-platform-support"></a>Suporte à plataforma Android

Você precisa ter a plataforma das Ferramentas do SDK do Android e da API do Android mais recente instalada. É possível atualizar para as versões mais recentes usando o [Gerenciador de SDK do Android](~/android/get-started/installation/android-sdk.md).

Além disso, a versão de destino/compilação para projetos do Android **deve** ser definida como *Usar a plataforma mais recente instalada*. No entanto, a versão mínima pode ser definida como API 19, de modo que você pode continuar dando suporte a dispositivos que usam Android 4.4 e mais recentes. Esses valores são definidos nas **Opções de projeto**:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

**Opções de Projeto > Aplicativo > Propriedades do Aplicativo**

![Opções de build do Android no Visual Studio](requirements-images/options-android-vs-sml.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

**Build > Geral**

![Selecione a estrutura de destino mais recente](requirements-images/options-general-sml.png)

**Build > Aplicativo Android**

![Selecione as versões mínima e alvo do Android para seu aplicativo](requirements-images/options-android-sml.png)

-----

## <a name="deprecated-platforms"></a>Plataformas preteridas

Não há suporte para essas plataformas ao usar Xamarin.Forms 3,0 ou mais recente:

- *WinRT do Windows 8.1/Windows Phone 8.1*
- *Windows Phone 8 Silverlight*
