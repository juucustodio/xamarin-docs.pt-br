---
title: Cadeia de caracteres e a localização da imagem
description: Aplicativos xamarin. Forms podem ser localizados usando arquivos de recursos do .NET.
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/06/2016
ms.openlocfilehash: 09fe3587e4e435383822e50bd12616747b807f82
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108451"
---
# <a name="localization"></a>Localização

_Aplicativos xamarin. Forms podem ser localizados usando arquivos de recursos do .NET._

## <a name="overview"></a>Visão geral

O mecanismo interno para localização de usos de aplicativos .NET [arquivos RESX](http://msdn.microsoft.com/library/ekyft91f(v=vs.90).aspx) e as classes a `System.Resources` e `System.Globalization` namespaces. Arquivos RESX que contém cadeias de caracteres traduzidas são inseridos no assembly do xamarin. Forms, junto com uma classe gerada pelo compilador que fornece acesso fortemente tipado para as traduções. O texto traduzido, em seguida, pode ser recuperado no código.

### <a name="sample-code"></a>Código de exemplo

Há dois exemplos associados a este documento:

* [UsingResxLocalization](https://github.com/xamarin/xamarin-forms-samples/tree/master/UsingResxLocalization) é uma demonstração muito simple dos conceitos explicados. Os trechos de código mostrados abaixo são tudo a partir de neste exemplo.
* [TodoLocalized](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized) é um aplicativo de trabalho básico que usa essas técnicas de localização.

#### <a name="shared-projects-are-not-recommended"></a>Projetos compartilhados não são recomendados.

O exemplo TodoLocalized inclui um [demonstração de projeto compartilhado](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized/SharedProject/) no entanto, devido às limitações do sistema de compilação, os arquivos de recurso não obter uma **. designer.cs** arquivo gerado que interrompe a capacidade de acessar cadeias de caracteres traduzidas fortemente tipado no código.

O restante deste documento está relacionada a projetos usando o modelo de biblioteca xamarin. Forms .NET Standard.

## <a name="globalizing-xamarinforms-code"></a>Globalizando o código do xamarin. Forms

**Globalizando** um aplicativo é o processo de fazê-lo "world ready". Isso significa que escrever um código que é capaz de exibir idiomas diferentes.

Uma das partes principais da globalização de um aplicativo está criando a interface do usuário para que haja nenhuma *embutido em código* texto. Em vez disso, nada é exibido para o usuário deve ser recuperado de um conjunto de cadeias de caracteres que foram traduzidas para o idioma escolhido.

Neste documento, examinaremos como usar arquivos RESX para armazenar essas cadeias de caracteres e recuperá-los para exibição, dependendo da preferência do usuário.

Os exemplos de idiomas inglês, francês, espanhol, alemão, chinês, japonês, russo e português (Brasil) de destino. Aplicativos podem ser convertidos em mínimo ou tantos idiomas quantos forem necessários.

> [!NOTE]
> Na plataforma Universal do Windows, os arquivos RESW devem ser usados para localização de notificação por push, em vez de arquivos RESX. Para obter mais informações, consulte [localização UWP](/windows/uwp/design/globalizing/globalizing-portal/).

### <a name="adding-resources"></a>Adicionando recursos

A primeira etapa na globalização de um aplicativo de biblioteca xamarin. Forms .NET Standard é adicionar os arquivos de recursos RESX que serão usados para armazenar todo o texto usado no aplicativo. Precisamos adicionar um arquivo RESX que contém o texto padrão e, em seguida, adicionar arquivos RESX adicionais para cada idioma que desejamos dar suporte.

#### <a name="base-language-resource"></a>Recurso de idioma base

O arquivo de recursos (RESX) de base contém as cadeias de caracteres de idioma padrão (os exemplos pressupõem o que inglês é o idioma padrão). Adicione o arquivo para o projeto de código comuns do xamarin. Forms clicando duas vezes no projeto e escolhendo **Adicionar > novo arquivo...** .

Escolha um nome significativo, como **AppResources** e pressione **Okey**.

[![Adicionar arquivo de recurso](text-images/resx-new-file-sml.png "caixa de diálogo Novo arquivo")](text-images/resx-new-file.png#lightbox "caixa de diálogo Novo arquivo")

Dois arquivos serão adicionados ao projeto:

* **AppResources** arquivo onde traduzíveis cadeias de caracteres são armazenadas em um formato XML.
* **AppResources.designer.cs** arquivo que declara uma classe parcial para conter referências a todos os elementos criados no arquivo RESX XML.

A árvore de solução mostrará os arquivos como relacionada. Arquivo RESX *devem* ser editada para adicionar novas cadeias de caracteres traduzíveis; a **. designer.cs** arquivo deve *não* ser editado.

![](text-images/appresources-tree.png "Arquivo AppResources. resx")

##### <a name="string-visibility"></a>Visibilidade de cadeia de caracteres

Por padrão quando são geradas referências fortemente tipados para cadeias de caracteres, eles serão `internal` ao assembly. Isso ocorre porque a ferramenta de compilação padrão para arquivos RESX gera o **. designer.cs** do arquivo com `internal` propriedades.

Selecione o **AppResources** do arquivo e mostrar o **propriedades** painel para ver onde a ferramenta de compilação é configurar. A captura de tela abaixo mostra a **ferramenta personalizada: ResXFileCodeGenerator**.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](text-images/vs-resx-internal-sml.png "Janela Propriedades de AppResources. resx")](text-images/vs-resx-internal.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](text-images/xs-resx-internal-sml.png "Painel de propriedades para AppResources. resx")](text-images/xs-resx-internal.png#lightbox)

-----

Para tornar as propriedades de cadeia de caracteres fortemente tipadas `public`, você deve alterar manualmente a configuração **ferramenta personalizada: PublicResXFileCodeGenerator**, conforme mostrado na captura de tela abaixo:


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](text-images/vs-resx-public-sml.png "Janela Propriedades de AppResources. resx")](text-images/vs-resx-public.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](text-images/xs-resx-internal-sml.png "Painel de propriedades para AppResources. resx")](text-images/xs-resx-internal.png#lightbox)


[![](text-images/xs-resx-public-sml.png "Painel de propriedades para AppResources. resx")](text-images/xs-resx-public.png#lightbox)

-----

Essa alteração é opcional e só é necessário se você deseja fazer referência a cadeias de caracteres localizadas em assemblies diferentes (por exemplo, se você colocar os arquivos RESX em um assembly diferente para seu código). O exemplo deste tópico deixa as cadeias de caracteres `internal` porque eles são definidos no mesmo assembly biblioteca xamarin. Forms .NET Standard onde eles são usados.

Você só precisa definir a ferramenta personalizada no arquivo RESX base, conforme mostrado acima. não é necessário definir *qualquer* ferramenta de compilação nos arquivos específicos de linguagem RESX discutidos nas seções a seguir.

##### <a name="editing-the-resx-file"></a>Editando o arquivo RESX

Infelizmente não há nenhum editor do RESX interno no Visual Studio para Mac. Adicionar novas cadeias de caracteres traduzíveis requer a adição de um novo XML `data` elemento para cada cadeia de caracteres. Cada `data` elemento pode conter o seguinte:

* `name` atributo (obrigatório) é a chave para essa cadeia de caracteres traduzível. Ele deve ser válido C# nome de propriedade – então, sem espaços ou caracteres especiais são permitidos.
* `value` elemento (obrigatório), que é a cadeia de caracteres real que é exibida no aplicativo.
* `comment` (opcional) do elemento pode conter instruções para o conversor que explica como essa cadeia de caracteres é usada.
* `xml:space` atributo (opcional) para controlar como o espaçamento na cadeia de caracteres é preservado.

Alguns exemplos `data` elementos são mostrados aqui:

```xml
<data name="NotesLabel" xml:space="preserve">
    <value>Notes:</value>
    <comment>label for input field</comment>
</data>
<data name="NotesPlaceholder" xml:space="preserve">
    <value>eg. buy milk</value>
    <comment>example input for notes field</comment>
</data>
<data name="AddButton" xml:space="preserve">
    <value>Add new item</value>
</data>
```

Como o aplicativo é escrito, cada parte do texto exibido para o usuário deve ser adicionado ao arquivo de recursos RESX base em uma nova `data` elemento. É recomendável que você inclua `comment`s tanto quanto possível para garantir uma tradução de alta qualidade.

> [!NOTE]
> O Visual Studio (incluindo a Community edition gratuita) contém um editor do RESX básico. Se você tiver acesso a um computador Windows, que pode ser uma maneira conveniente de adicionar e editar cadeias de caracteres em arquivos RESX.

#### <a name="language-specific-resources"></a>Recursos específicos de linguagem

Normalmente, a conversão de cadeias de caracteres de texto padrão não acontecerá até que foram gravados grandes partes do aplicativo (neste caso, o arquivo RESX padrão conterá várias sequências de caracteres). Ainda é uma boa ideia para adicionar os recursos específicos da linguagem no início do ciclo de desenvolvimento, opcionalmente, populando com o texto traduzido por máquina para ajudar a testar o código de localização.

Um arquivo RESX adicional é adicionado para cada idioma que desejamos dar suporte.
Arquivos de recurso específico de idioma devem seguir uma convenção de nomenclatura específica: usar o mesmo nome de arquivo, como os recursos básicos de arquivo (por exemplo. **AppResources**) seguido por um ponto (.) e, em seguida, o código de idioma. Exemplos simples:

* **AppResources.fr.resx** -traduções de idioma em francês.
* **AppResources.es.resx** -traduções de idioma espanhol.
* **AppResources.de.resx** -traduções de idioma alemão.
* **AppResources.ja.resx** -traduções de idioma japonês.
* **AppResources.zh Hans.resx** - chinês (simplificado) traduções de idioma.
* **AppResources.zh Hant.resx** - chinês (tradicional) traduções de idioma.
* **AppResources.pt.resx** -traduções de idioma português.
* **AppResources.pt BR.resx** -traduções de idioma português brasileiro.

O padrão geral é usar códigos de idioma de duas letras, mas há alguns exemplos (como chinês) em que um formato diferente é usado e outros exemplos (por exemplo, português (Brasil)) em que um identificador de localidade de quatro caracteres é necessário.

Esses arquivos de recursos específicos de linguagem *não tiver* exigem uma **. designer.cs** parcial de classe para que eles podem ser adicionados como arquivos XML regulares, com o **Build Action: EmbeddedResource**definido. Esta captura de tela mostra uma solução que contém os arquivos de recursos específicos de idioma:

![](text-images/appresources-langs.png "Arquivos de recursos específicos de idioma")

Como um aplicativo é desenvolvido e o arquivo RESX base tem o texto adicionado, você deve enviá-las para tradutores que converterá cada `data` elemento e retornar um arquivo de recurso específicos de linguagem (usando a convenção de nomenclatura mostrada) a serem incluídos no aplicativo. Alguns exemplos de 'traduzido por máquina' são mostrados abaixo:

**AppResources.es.resx (espanhol)**

```xml
<data name="AddButton" xml:space="preserve">
    <value>Escribir un artículo</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

**AppResources.ja.resx (japonês)**

```xml
<data name="AddButton" xml:space="preserve">
    <value>新しい項目を追加</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

**AppResources.pt-BR.resx (português Brasil)**

```xml
<data name="AddButton" xml:space="preserve">
    <value>adicionar novo item</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

Somente o `value` elemento precisa ser atualizado pelo conversor - o `comment` não se destina a ser traduzido. Lembre-se: ao editar arquivos XML para escapar caracteres reservados como `<`, `>`, `&` com `&lt;`, `&gt;`, e `&amp;` se aparecerem no `value` ou `comment`.

<a name="incode" />

### <a name="using-resources-in-code"></a>Uso de recursos em código

Cadeias de caracteres em arquivos de recursos RESX estarão disponíveis para usar em seu código de interface de usuário usando o `AppResources` classe. O `name` atribuído a cada cadeia de caracteres ao RESX arquivo se torna uma propriedade na classe que pode ser referenciado no código do xamarin. Forms, conforme mostrado abaixo:

```csharp
var myLabel = new Label ();
var myEntry = new Entry ();
var myButton = new Button ();
// populate UI with translated text values from resources
myLabel.Text = AppResources.NotesLabel;
myEntry.Placeholder = AppResources.NotesPlaceholder;
myButton.Text = AppResources.AddButton;
```

A interface do usuário no iOS, Android e renderiza a plataforma Universal do Windows (UWP) que você esperaria, exceto que agora é possível converter o aplicativo em vários idiomas, porque o texto está sendo carregado de um recurso em vez de ser embutido em código. Aqui está uma captura de tela mostrando a interface do usuário em cada plataforma antes da conversão:

![](text-images/simple-example-english.png "Interfaces do usuário antes da conversão de plataforma cruzada")

### <a name="troubleshooting"></a>Solução de problemas

#### <a name="testing-a-specific-language"></a>Teste a um idioma específico

Ele pode ser complicado alternar o simulador ou um dispositivo para diferentes idiomas, especialmente durante o desenvolvimento quando você deseja testar rapidamente a diferentes culturas.

Você pode forçar um idioma específico a ser carregado, definindo o `Culture` conforme mostrado neste trecho de código:

```csharp
// force a specific culture, useful for quick testing
AppResources.Culture =  new CultureInfo("fr-FR");
```

Essa abordagem – configurar a cultura diretamente no `AppResources` classe – também pode ser usada para implementar um seletor de idioma dentro de seu aplicativo (em vez de usar a localidade do dispositivo).

#### <a name="loading-embedded-resources"></a>Recursos inseridos de carregamento

O trecho de código a seguir é útil ao tentar depurar problemas com os recursos incorporados (como arquivos RESX). Adicione este código ao seu aplicativo (no início do ciclo de vida do aplicativo) e ele listará todos os recursos incorporados no assembly, que mostra o identificador de recurso completo:

```csharp
using System.Reflection;
// ...
// NOTE: use for debugging, not in released app code!
var assembly = typeof(EmbeddedImages).GetTypeInfo().Assembly; // "EmbeddedImages" should be a class in your app
foreach (var res in assembly.GetManifestResourceNames())
{
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

No **AppResources.Designer.cs** arquivo (em torno *linha 33*), completo *nome do Gerenciador de recursos* for especificado (por exemplo. `"UsingResxLocalization.Resx.AppResources"`) semelhante ao código a seguir:

```csharp
System.Resources.ResourceManager temp =
        new System.Resources.ResourceManager(
                "UsingResxLocalization.Resx.AppResources",
                typeof(AppResources).GetTypeInfo().Assembly);
```

Verifique as **saída do aplicativo** para os resultados de depuração de código mostrado acima, para confirmar os recursos corretos são listados (ie. `"UsingResxLocalization.Resx.AppResources"`).

Caso contrário, o `AppResources` classe não será possível carregar seus recursos.
Verifique o seguinte para resolver problemas em que os recursos não podem ser encontrados:

* O namespace padrão para o projeto corresponde ao namespace raiz na **AppResources.Designer.cs** arquivo.
* Se o **AppResources** arquivo está localizado em um subdiretório, o nome do subdiretório deve ser parte do namespace *e* faz parte do identificador de recurso.
* O **AppResources** arquivo tem **Build Action: EmbeddedResource**.
* O **opções de projeto > código-fonte > políticas de nomenclatura .NET > nomes de recursos de estilo do Use Visual Studio** está marcada. Você pode untick isso se você preferir, no entanto os namespaces usados ao fazer referência a seus recursos RESX precisará ser atualizado em todo o aplicativo.

#### <a name="doesnt-work-in-debug-mode-android-only"></a>Não funciona no modo de depuração (somente Android)

Se as cadeias de caracteres traduzidas estiver trabalhando em suas compilações de versão Android, mas não durante a depuração, clique com botão direito no **projeto do Android** e selecione **opções > Build > Build do Android** e certifique-se de que o **Implantação de assembly rápida** não está marcada. Essa opção faz com que os problemas com o carregamento de recursos e não deve ser usada se você estiver testando aplicativos localizados.

### <a name="displaying-the-correct-language"></a>Exibindo o idioma correto

Até agora, examinamos como escrever código para que as traduções podem ser fornecidas, mas não para realmente fazê-lo aparecer. Código do xamarin. Forms pode tirar proveito do. Recursos do NET para carregar as traduções de idioma correto, mas é necessário consultar o sistema operacional em cada plataforma para determinar qual idioma selecionado pelo usuário.

Como alguns códigos específicos da plataforma é necessária para obter a preferência de idioma do usuário, use uma [serviço de dependência](~/xamarin-forms/app-fundamentals/dependency-service/index.md) para expor essas informações no aplicativo xamarin. Forms e implementá-la para cada plataforma.

Primeiro, defina uma interface para expor a cultura preferencial do usuário, semelhante ao código a seguir:

```csharp
public interface ILocalize
{
    CultureInfo GetCurrentCultureInfo ();
    void SetLocale (CultureInfo ci);
}
```

Em segundo lugar, use o [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) em que o xamarin. Forms `App` classe para chamar a interface e definir nossa cultura de recursos RESX como o valor correto. Observe que não precisamos definir manualmente esse valor para a plataforma Universal do Windows, desde a estrutura de recursos automaticamente reconhece o idioma selecionado nessas plataformas.

```csharp
if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
{
    var ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
    Resx.AppResources.Culture = ci; // set the RESX for resource localization
    DependencyService.Get<ILocalize>().SetLocale(ci); // set the Thread for locale-aware methods
}
```

O recurso `Culture` precisa ser definida quando o aplicativo é carregado pela primeira vez para que as cadeias de caracteres de idioma correto sejam usadas. Você pode, opcionalmente, atualize esse valor de acordo com os eventos específicos da plataforma que podem ser acionados no iOS ou Android, se o usuário atualizar suas preferências de idioma, enquanto o aplicativo está em execução.

As implementações para o `ILocalize` interface são mostrados na [código específico da plataforma](#Platform-specific_Code) seção abaixo. Essas implementações tiram proveito dessa `PlatformCulture` classe auxiliar:

```csharp
public class PlatformCulture
{
    public PlatformCulture (string platformCultureString)
    {
        if (String.IsNullOrEmpty(platformCultureString))
        {
            throw new ArgumentException("Expected culture identifier", "platformCultureString"); // in C# 6 use nameof(platformCultureString)
        }
        PlatformString = platformCultureString.Replace("_", "-"); // .NET expects dash, not underscore
        var dashIndex = PlatformString.IndexOf("-", StringComparison.Ordinal);
        if (dashIndex > 0)
        {
            var parts = PlatformString.Split('-');
            LanguageCode = parts[0];
            LocaleCode = parts[1];
        }
        else
        {
            LanguageCode = PlatformString;
            LocaleCode = "";
        }
    }
    public string PlatformString { get; private set; }
    public string LanguageCode { get; private set; }
    public string LocaleCode { get; private set; }
    public override string ToString()
    {
        return PlatformString;
    }
}
```

<a name="Platform-specific_Code" />

### <a name="platform-specific-code"></a>Código específico da plataforma

O código para detectar qual idioma de exibição deve ser específico da plataforma, pois o iOS, Android e UWP expõem essas informações de maneiras ligeiramente diferentes. O código para o `ILocalize` serviço de dependência é fornecido abaixo para cada plataforma, junto com os requisitos específicos de plataforma adicionais para garantir que o texto localizado é renderizado corretamente.

O código específico da plataforma também deve lidar com casos em que o sistema operacional permite que o usuário configure um identificador de localidade que não tem suporte. Do NET `CultureInfo` classe. Nesses casos, código personalizado deve ser escrito para detectar localidades sem suporte e substitua o melhor. Localidade compatível com .NET.

#### <a name="ios-application-project"></a>Projeto de Aplicativo iOS

os usuários do iOS selecionarem seu idioma preferencial separadamente da data e hora, formatação de cultura. Para carregar os recursos corretos para localizar um aplicativo xamarin. Forms, só precisamos consultar o `NSLocale.PreferredLanguages` matriz para o primeiro elemento.

A seguinte implementação do `ILocalize` serviço de dependência deve ser colocado no projeto de aplicativo do iOS. Porque o iOS usa sublinhados em vez de traços (que é a representação padrão do .NET) o código substitui o caractere de sublinhado antes da instanciação de `CultureInfo` classe:

```csharp
[assembly:Dependency(typeof(UsingResxLocalization.iOS.Localize))]

namespace UsingResxLocalization.iOS
{
public class Localize : UsingResxLocalization.ILocalize
    {
        public void SetLocale (CultureInfo ci)
        {
            Thread.CurrentThread.CurrentCulture = ci;
            Thread.CurrentThread.CurrentUICulture = ci;
        }
        public CultureInfo GetCurrentCultureInfo ()
        {
            var netLanguage = "en";
            if (NSLocale.PreferredLanguages.Length > 0)
            {
                var pref = NSLocale.PreferredLanguages [0];
                netLanguage = iOSToDotnetLanguage(pref);
            }
            // this gets called a lot - try/catch can be expensive so consider caching or something
            System.Globalization.CultureInfo ci = null;
            try
            {
                ci = new System.Globalization.CultureInfo(netLanguage);
            }
            catch (CultureNotFoundException e1)
            {
                // iOS locale not valid .NET culture (eg. "en-ES" : English in Spain)
                // fallback to first characters, in this case "en"
                try
                {
                    var fallback = ToDotnetFallbackLanguage(new PlatformCulture(netLanguage));
                    ci = new System.Globalization.CultureInfo(fallback);
                }
                catch (CultureNotFoundException e2)
                {
                    // iOS language not valid .NET culture, falling back to English
                    ci = new System.Globalization.CultureInfo("en");
                }
            }
            return ci;
        }
        string iOSToDotnetLanguage(string iOSLanguage)
        {
            var netLanguage = iOSLanguage;
            //certain languages need to be converted to CultureInfo equivalent
            switch (iOSLanguage)
            {
                case "ms-MY":   // "Malaysian (Malaysia)" not supported .NET culture
                case "ms-SG":    // "Malaysian (Singapore)" not supported .NET culture
                    netLanguage = "ms"; // closest supported
                    break;
                case "gsw-CH":  // "Schwiizertüütsch (Swiss German)" not supported .NET culture
                    netLanguage = "de-CH"; // closest supported
                    break;
                // add more application-specific cases here (if required)
                // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
        string ToDotnetFallbackLanguage (PlatformCulture platCulture)
        {
            var netLanguage = platCulture.LanguageCode; // use the first part of the identifier (two chars, usually);
            switch (platCulture.LanguageCode)
            {
                case "pt":
                    netLanguage = "pt-PT"; // fallback to Portuguese (Portugal)
                    break;
                case "gsw":
                    netLanguage = "de-CH"; // equivalent to German (Switzerland) for this app
                    break;
                // add more application-specific cases here (if required)
                // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
    }
}
```

> [!NOTE]
> O `try/catch` blocos no `GetCurrentCultureInfo` método imitam o comportamento de fallback usado normalmente com especificadores de localidade – se a correspondência exata não for encontrada, procure para uma correspondência aproximada com base apenas no idioma (primeiro bloco de caracteres na localidade).
>
> No caso do xamarin. Forms, algumas localidades são válidas no iOS, mas não correspondem à válido `CultureInfo` em .NET e o código acima tenta lidar com isso.
>
> Por exemplo, o iOS **Configurações > geral Language &amp; região** tela permite que você defina seu telefone **idioma** para **inglês** , mas o  **Região** à **Espanha**, que resulta em uma cadeia de caracteres de localidade de `"en-ES"`. Quando o `CultureInfo` criação falhar, o código voltará a usar apenas as primeiras duas letras para selecionar o idioma de exibição.
>
> Os desenvolvedores devem modificar a `iOSToDotnetLanguage` e `ToDotnetFallbackLanguage` métodos para lidar com casos específicos necessários para suas linguagens com suporte.


Há alguns elementos de interface do usuário definida pelo sistema são automaticamente convertidos pelo iOS, como o **feito** botão o `Picker` controle. Para forçar o iOS para converter esses elementos precisamos indicar quais idiomas damos suporte nas **Info. plist** arquivo. Você pode adicionar esses valores por meio **Info. plist > origem** conforme mostrado aqui:

![Chaves de localização no Info. plist](text-images/info-plist.png "chaves de localização no Info. plist")

Como alternativa, abra o **Info. plist** em um editor de XML do arquivo e edite os valores diretamente:

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>de</string>
    <string>es</string>
    <string>fr</string>
    <string>ja</string>
    <string>pt</string> <!-- Brazil -->
    <string>pt-PT</string> <!-- Portugal -->
    <string>ru</string>
    <string>zh-Hans</string>
    <string>zh-Hant</string>
</array>
<key>CFBundleDevelopmentRegion</key>
<string>en</string>
```

Depois de ter implementado o serviço de dependência e atualizado **Info. plist**, o aplicativo do iOS poderão exibir o texto localizado.

> [!NOTE]
> Observe que se trata de Apple português ligeiramente diferente que você esperaria.
> Partir [seus docs](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW2): _"usar pt como a ID de idioma para português porque ele é usado no Brasil e pt-PT como a ID de idioma para português como ele é usado em Portugal"_.
> Isso significa que, quando português idioma é escolhido em uma localidade não padrão, o idioma de fallback será português (Brasil) no iOS, a menos que o código é escrito para alterar esse comportamento (como o `ToDotnetFallbackLanguage` acima).

Para obter mais informações sobre a localização do iOS, consulte [iOS localização](~/ios/app-fundamentals/localization/index.md).

#### <a name="android-application-project"></a>Projeto de Aplicativo Android

Android expõe a localidade selecionada no momento por meio de `Java.Util.Locale.Default`e também usa um separador de caractere de sublinhado, em vez de um traço (que é substituído pelo código a seguir). Adicione essa implementação do serviço de dependência para o projeto de aplicativo do Android:

```csharp
[assembly:Dependency(typeof(UsingResxLocalization.Android.Localize))]

namespace UsingResxLocalization.Android
{
    public class Localize : UsingResxLocalization.ILocalize
    {
        public void SetLocale(CultureInfo ci)
        {
            Thread.CurrentThread.CurrentCulture = ci;
            Thread.CurrentThread.CurrentUICulture = ci;
        }
        public CultureInfo GetCurrentCultureInfo()
        {
            var netLanguage = "en";
            var androidLocale = Java.Util.Locale.Default;
            netLanguage = AndroidToDotnetLanguage(androidLocale.ToString().Replace("_", "-"));
            // this gets called a lot - try/catch can be expensive so consider caching or something
            System.Globalization.CultureInfo ci = null;
            try
            {
                ci = new System.Globalization.CultureInfo(netLanguage);
            }
            catch (CultureNotFoundException e1)
            {
                // iOS locale not valid .NET culture (eg. "en-ES" : English in Spain)
                // fallback to first characters, in this case "en"
                try
                {
                    var fallback = ToDotnetFallbackLanguage(new PlatformCulture(netLanguage));
                    ci = new System.Globalization.CultureInfo(fallback);
                }
                catch (CultureNotFoundException e2)
                {
                    // iOS language not valid .NET culture, falling back to English
                    ci = new System.Globalization.CultureInfo("en");
                }
            }
            return ci;
        }
        string AndroidToDotnetLanguage(string androidLanguage)
        {
            var netLanguage = androidLanguage;
            //certain languages need to be converted to CultureInfo equivalent
            switch (androidLanguage)
            {
                case "ms-BN":   // "Malaysian (Brunei)" not supported .NET culture
                case "ms-MY":   // "Malaysian (Malaysia)" not supported .NET culture
                case "ms-SG":   // "Malaysian (Singapore)" not supported .NET culture
                    netLanguage = "ms"; // closest supported
                    break;
                case "in-ID":  // "Indonesian (Indonesia)" has different code in  .NET
                    netLanguage = "id-ID"; // correct code for .NET
                    break;
                case "gsw-CH":  // "Schwiizertüütsch (Swiss German)" not supported .NET culture
                    netLanguage = "de-CH"; // closest supported
                    break;
                    // add more application-specific cases here (if required)
                    // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
        string ToDotnetFallbackLanguage(PlatformCulture platCulture)
        {
            var netLanguage = platCulture.LanguageCode; // use the first part of the identifier (two chars, usually);
            switch (platCulture.LanguageCode)
            {
                case "gsw":
                    netLanguage = "de-CH"; // equivalent to German (Switzerland) for this app
                    break;
                    // add more application-specific cases here (if required)
                    // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
    }
}
```

> [!NOTE]
> O `try/catch` blocos no `GetCurrentCultureInfo` método imitam o comportamento de fallback usado normalmente com especificadores de localidade – se a correspondência exata não for encontrada, procure para uma correspondência aproximada com base apenas no idioma (primeiro bloco de caracteres na localidade).
>
> No caso do xamarin. Forms, algumas localidades são válidas no Android, mas não correspondem à válido `CultureInfo` em .NET e o código acima tenta lidar com isso.
>
> Os desenvolvedores devem modificar a `iOSToDotnetLanguage` e `ToDotnetFallbackLanguage` métodos para lidar com casos específicos necessários para suas linguagens com suporte.

Depois que esse código foi adicionado ao projeto de aplicativo Android, ele será capaz de exibir automaticamente cadeias de caracteres traduzidas.

> [!NOTE]
>️ **Aviso:** se as cadeias de caracteres traduzidas estiver trabalhando em suas compilações de versão Android, mas não durante a depuração, clique com botão direito no **projeto Android** e selecione **opções > Build > Android Crie** e certifique-se de que o **implantação de assembly rápida** não está marcada. Essa opção faz com que os problemas com o carregamento de recursos e não deve ser usada se você estiver testando aplicativos localizados.

Para obter mais informações sobre a localização do Android, consulte [localização Android](~/android/app-fundamentals/localization.md).

#### <a name="universal-windows-platform"></a>Plataforma Universal do Windows

Projetos do Universal Windows Platform (UWP) não requerem o serviço de dependência. Em vez disso, essa plataforma define automaticamente cultura do recurso corretamente.

##### <a name="assemblyinfocs"></a>AssemblyInfo.cs

Expanda o nó de propriedades no projeto da biblioteca .NET Standard e clique duas vezes no **AssemblyInfo.cs** arquivo. Adicione a seguinte linha ao arquivo para definir a linguagem de assembly de recursos neutros para inglês:

```csharp
[assembly: NeutralResourcesLanguage("en")]
```

Isso informa o Gerenciador de recursos da cultura padrão do aplicativo, garantindo assim que as cadeias de caracteres definidas no arquivo RESX idioma neutro (**AppResources**) será exibida quando o aplicativo está em execução em um as localidades do inglês.

### <a name="example"></a>Exemplo

Depois de atualizar os projetos específicos da plataforma conforme mostrado acima e recompilar o aplicativo com arquivos traduzidos de RESX, traduções atualizadas serão disponibilizado em cada aplicativo. Aqui está uma captura de tela do código de exemplo convertido em chinês simplificado:

![](text-images/simple-example-hans.png "Interfaces de usuário de plataforma cruzada convertidos em chinês simplificado")

Para obter mais informações sobre localização de UWP, consulte [localização UWP](/windows/uwp/design/globalizing/globalizing-portal/).

## <a name="localizing-xaml"></a>Localizando o XAML

Quando criando uma interface de usuário do xamarin. Forms no XAML a marcação teria uma aparência semelhante a este, com cadeias de caracteres inseridos diretamente no XML:

```xaml
<Label Text="Notes:" />
<Entry Placeholder="eg. buy milk" />
<Button Text="Add to list" />
```

O ideal é que podemos foi possível converter controles de interface do usuário diretamente no XAML, que pode ser feito com a criação de um *extensão de marcação*. O código para uma extensão de marcação que expõe os recursos RESX para XAML é mostrado abaixo. Essa classe deve ser adicionada para o código comum do xamarin. Forms (juntamente com as páginas XAML):

```csharp
using System;
using System.Globalization;
using System.Reflection;
using System.Resources;
using Xamarin.Forms;
using Xamarin.Forms.Xaml;

namespace UsingResxLocalization
{
    // You exclude the 'Extension' suffix when using in XAML
    [ContentProperty("Text")]
    public class TranslateExtension : IMarkupExtension
    {
        readonly CultureInfo ci = null;
        const string ResourceId = "UsingResxLocalization.Resx.AppResources";

        static readonly Lazy<ResourceManager> ResMgr = new Lazy<ResourceManager>(
            () => new ResourceManager(ResourceId, IntrospectionExtensions.GetTypeInfo(typeof(TranslateExtension)).Assembly));

        public string Text { get; set; }

        public TranslateExtension()
        {
            if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
            {
                ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
            }
        }

        public object ProvideValue(IServiceProvider serviceProvider)
        {
            if (Text == null)
                return string.Empty;

            var translation = ResMgr.Value.GetString(Text, ci);
            if (translation == null)
            {
#if DEBUG
                throw new ArgumentException(
                    string.Format("Key '{0}' was not found in resources '{1}' for culture '{2}'.", Text, ResourceId, ci.Name),
                    "Text");
#else
                translation = Text; // HACK: returns the key, which GETS DISPLAYED TO THE USER
#endif
            }
            return translation;
        }
    }
}
```

Os marcadores a seguir explicam os elementos importantes no código acima:

* A classe é nomeada `TranslateExtension`, mas, por convenção, podemos nos referir é como **traduzir** na nossa marcação.
* A classe implementa `IMarkupExtension`, que é necessária para xamarin. Forms para que ele funcione.
* `"UsingResxLocalization.Resx.AppResources"` é o identificador de recurso para nossos recursos RESX. Ele é composto de nosso espaço para nome padrão, a pasta onde se encontram os arquivos de recurso e o nome do arquivo RESX de padrão.
* O `ResourceManager` classe é criado usando `IntrospectionExtensions.GetTypeInfo(typeof(TranslateExtension)).Assembly)` para determinar o assembly atual para carregar recursos e em cache na estático `ResMgr` campo. Ela é criada como uma `Lazy` tipo de forma que sua criação é adiada até que ele é usado primeiro o `ProvideValue` método.
* `ci` usa o serviço de dependência para obter o idioma do usuário escolhido do sistema operacional nativo.
* `GetString` é o método que recupera a cadeia de caracteres traduzida real dos arquivos de recursos. Na plataforma Universal do Windows, `ci` será nulo porque o `ILocalize` interface não está implementada nessas plataformas. Isso é equivalente a chamar o `GetString` método com apenas o primeiro parâmetro. Em vez disso, a estrutura de recursos automaticamente reconheça a localidade e irá recuperar a cadeia de caracteres traduzida do arquivo RESX apropriado.
* Tratamento de erros foi incluído para ajudar a depurar recursos ausentes, lançando uma exceção (em `DEBUG` somente no modo).

O trecho XAML a seguir mostra como usar a extensão de marcação. Há duas etapas para fazê-lo funcionar:

1. Declarar personalizado `xmlns:i18n` namespace no nó raiz. O `namespace` e `assembly` devem corresponder às configurações de projeto exatamente - neste exemplo eles são idênticos, mas pode ser diferentes em seu projeto.
2. Use `{Binding}` sintaxe de atributos que normalmente seria contêm o texto para chamar o `Translate` extensão de marcação. A chave de recurso é o único parâmetro necessário.

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        x:Class="UsingResxLocalization.FirstPageXaml"
        xmlns:i18n="clr-namespace:UsingResxLocalization;assembly=UsingResxLocalization">
    <StackLayout Padding="0, 20, 0, 0">
        <Label Text="{i18n:Translate NotesLabel}" />
        <Entry Placeholder="{i18n:Translate NotesPlaceholder}" />
        <Button Text="{i18n:Translate AddButton}" />
    </StackLayout>
</ContentPage>
```

A seguinte sintaxe mais detalhada também é válido para a extensão de marcação:

```xaml
<Button Text="{i18n:TranslateExtension Text=AddButton}" />
```

## <a name="localizing-platform-specific-elements"></a>Localização de elementos específicos da plataforma

Embora podemos manipular a tradução da interface do usuário no código do xamarin. Forms, há alguns elementos que devem ser feitos em cada projeto específico da plataforma. Esta seção abordará como localizar:

* Nome do aplicativo
* Imagens

O projeto de exemplo inclui uma imagem localizada chamada **flag.png**, que é referenciado no C# da seguinte maneira:

```csharp
var flag = new Image();
switch (Device.RuntimePlatform)
{
    case Device.iOS:
    case Device.Android:
        flag.Source = ImageSource.FromFile("flag.png");
        break;
    case Device.UWP:
        flag.Source = ImageSource.FromFile("Assets/Images/flag.png");
        break;
}
```

A imagem do sinalizador também é referenciada no XAML assim:

```xaml
<Image>
  <Image.Source>
    <OnPlatform x:TypeArguments="ImageSource">
        <On Platform="iOS, Android" Value="flag.png" />
        <On Platform="UWP" Value="Assets/Images/flag.png" />
    </OnPlatform>
  </Image.Source>
</Image>
```

Todas as plataformas resolver automaticamente as referências de imagem como esses para versões localizadas das imagens, desde que as estruturas de projeto explicadas abaixo são implementadas.

### <a name="ios-application-project"></a>Projeto de Aplicativo iOS

o iOS usa um padrão de nomenclatura chamado localização projetos ou **.lproj** diretórios para conter os recursos de imagem e a cadeia de caracteres. Esses diretórios podem conter as versões localizadas de imagens usadas no aplicativo e também a **InfoPlist.strings** arquivo que pode ser usado para localizar o nome do aplicativo. Para obter mais informações sobre a localização do iOS, consulte [iOS localização](~/ios/app-fundamentals/localization/index.md).

#### <a name="images"></a>Imagens

Esta captura de tela mostra o aplicativo de exemplo do iOS com o idioma específico **.lproj** diretórios. O diretório de espanhol chamado **es.lproj**, contém as versões localizadas da imagem padrão, bem como **flag.png**:

![](text-images/ios-resources.png "Diretórios de projeto de localização do iOS")

Cada diretório de idioma contém uma cópia da **flag.png**, localizada para esse idioma. Se nenhuma imagem for fornecida, o sistema operacional padrão será a imagem na pasta de idioma padrão. Para obter suporte completo de Retina, você deve fornecer **@2x** e **@3x** cópias de cada imagem.

#### <a name="app-name"></a>Nome do Aplicativo

O conteúdo a **InfoPlist.strings** é apenas uma único chave-valor para configurar o nome do aplicativo:

```csharp
"CFBundleDisplayName" = "ResxEspañol";
```

Quando o aplicativo é executado, o nome do aplicativo e a imagem são ambos localizados:

![](text-images/ios-imageicon.png "Localização de imagem e texto do aplicativo de exemplo de iOS")

### <a name="android-application-project"></a>Projeto de Aplicativo Android

O Android segue um esquema diferente para armazenar imagens localizadas usando diferentes **desenháveis** e **cadeias de caracteres** diretórios com um sufixo de código de idioma. Quando um código de localidade de quatro letras é necessário (por exemplo, zh-TW ou pt-BR), observe que o Android requer um adicional **r** seguindo dash/anterior de localidade (por exemplo, código zh-rTW ou rBR pt). Para obter mais informações sobre a localização do Android, consulte [localização Android](~/android/app-fundamentals/localization.md).

#### <a name="images"></a>Imagens

Esta captura de tela mostra o Android localizadas de exemplo com um alguns desenháveis e cadeias de caracteres:

![](text-images/android-resources.png "Android localizadas Desenháveis e diretórios de cadeia de caracteres")

Observe que o Android usa zh-Hans e zh-Hant códigos para simplificado e chinês tradicional; em vez disso, ele só dá suporte a códigos de país específicos zh-CN e zh-TW.

Para dar suporte a imagens de uma resolução diferente para telas de alta densidade, criar pastas adicionais com `-*dpi` sufixos, tais como **desenháveis-es-mdpi**, **desenháveis-es-xdpi**, **desenháveis-es-xxdpi**, etc. Ver [fornecendo recursos do Android da alternativa](http://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources) para obter mais informações.

#### <a name="app-name"></a>Nome do Aplicativo

O conteúdo a **strings. XML** é apenas uma único chave-valor para configurar o nome do aplicativo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">ResxEspañol</string>
</resources>
```

Atualizar o **MainActivity.cs** no projeto de aplicativo do Android para que o `Label` referencia as cadeias de caracteres XML.

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

Agora, o aplicativo localiza o nome do aplicativo e a imagem. Aqui está uma captura de tela do resultado (em espanhol):

![](text-images/android-imageicon.png "Localização de imagem e texto do aplicativo de exemplo do Android")

### <a name="universal-windows-platform-application-projects"></a>Projetos de aplicativos de plataforma universal do Windows

A plataforma Universal do Windows possui uma infra-estrutura de recurso que simplifica a localização de imagens e o nome do aplicativo. Para obter mais informações sobre localização de UWP, consulte [localização UWP](/windows/uwp/design/globalizing/globalizing-portal/).

#### <a name="images"></a>Imagens

Imagens podem ser localizadas, colocando-os em uma pasta de recursos específicos, como demonstrado na seguinte captura de tela:

![](text-images/uwp-image-folder-structure.png "Estrutura de pastas de localização de imagem UWP")

Em tempo de execução, a infraestrutura de recursos do Windows selecionará a imagem apropriada com base na localidade do usuário.

## <a name="summary"></a>Resumo

Aplicativos xamarin. Forms podem ser localizados usando arquivos RESX e classes de globalização do .NET. Além de uma pequena quantidade de código específico da plataforma para detectar qual idioma preferido pelo usuário, a maioria do esforço de localização é centralizada no código comum.

Imagens geralmente são manipuladas de forma específica da plataforma para aproveitar o suporte de resolução múltipla fornecido no iOS e Android.

## <a name="related-links"></a>Links relacionados

- [Exemplo de localização do RESX](https://developer.xamarin.com/samples/xamarin-forms/UsingResxLocalization/)
- [Aplicativo de exemplo TodoLocalized](https://developer.xamarin.com/samples/xamarin-forms/TodoLocalized/)
- [Localização de plataforma cruzada](~/cross-platform/app-fundamentals/localization.md)
- [Localização do iOS](~/ios/app-fundamentals/localization/index.md)
- [Localização do Android](~/android/app-fundamentals/localization.md)
- [Localização da UWP](/windows/uwp/design/globalizing/globalizing-portal/)
- [Usando a classe CultureInfo (MSDN)](http://msdn.microsoft.com/library/87k6sx8t%28v=vs.90%29.aspx)
- [Localizando e usando recursos para uma cultura específica (MSDN)](http://msdn.microsoft.com/library/s9ckwb4b%28v=vs.90%29.aspx)
