---
title: Localização de cadeias de caracteres e imagens
description: Aplicativos do Xamarin.Forms podem ser localizados usando arquivos de recursos do .NET.
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/06/2016
ms.openlocfilehash: a00b8052bfa2cd3a762e1b78409ffaa85cdae70c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646759"
---
# <a name="localization"></a>Localização

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingresxlocalization)

_Aplicativos do Xamarin.Forms podem ser localizados usando arquivos de recursos do .NET._

## <a name="overview"></a>Visão geral

O mecanismo interno para localização de usos de aplicativos .NET [arquivos RESX](https://msdn.microsoft.com/library/ekyft91f(v=vs.90).aspx) e as classes nos namespaces `System.Resources` e `System.Globalization`. Arquivos RESX que contêm cadeias de caracteres traduzidas são inseridos no assembly do Xamarin.Forms, junto com uma classe gerada pelo compilador que fornece acesso fortemente tipado para as traduções. O texto traduzido pode ser então recuperado no código.

### <a name="sample-code"></a>Código de exemplo

Há dois exemplos associados a este documento:

* [UsingResxLocalization](https://github.com/xamarin/xamarin-forms-samples/tree/master/UsingResxLocalization) é uma demonstração muito simples dos conceitos explicados. Todos os trechos de código mostrados abaixo são provenientes deste exemplo.
* [TodoLocalized](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized) é um aplicativo de trabalho básico que usa essas técnicas de localização.

#### <a name="shared-projects-are-not-recommended"></a>Não é recomendado o uso de Projetos compartilhados

O exemplo TodoLocalized inclui uma [Demonstração de Projeto compartilhado](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized/SharedProject/). No entanto, devido a limitações do sistema de build, os arquivos de recurso não geram um arquivo **.designer.cs**, o que impossibilita o acesso a cadeias de caracteres traduzidas fortemente tipadas no código.

O restante deste documento aborda projetos que usam o modelo de biblioteca .NET Standard do Xamarin.Forms.

## <a name="globalizing-xamarinforms-code"></a>Globalizando o código do Xamarin.Forms

**Globalizar** um aplicativo é o processo de deixá-lo "pronto para o mundo". Isso significa escrever um código que seja capaz de exibir idiomas diferentes.

Uma dos principais aspectos de globalizar um aplicativo é compilar a interface do usuário de forma que não haja nenhum texto *embutido em código*. Em vez disso, tudo o que é exibido para o usuário deve ser recuperado de um conjunto de cadeias de caracteres que foram traduzidas para o idioma escolhido.

Neste documento, examinaremos como usar arquivos RESX para armazenar essas cadeias de caracteres e recuperá-las para exibição, dependendo da preferência do usuário.

Os exemplos têm como alvo os idiomas inglês, francês, espanhol, alemão, chinês, japonês, russo e português brasileiro. É possível traduzir os aplicativos para tantos idiomas quantos forem necessários.

> [!NOTE]
> Na Plataforma Universal do Windows, é necessário usado arquivos RESW para localização de notificações por push, em vez de arquivos RESX. Para obter mais informações, confira [Localização da UWP](/windows/uwp/design/globalizing/globalizing-portal/).

### <a name="adding-resources"></a>Adicionando recursos

A primeira etapa da globalização de um aplicativo de biblioteca .NET Standard do Xamarin.Forms é adicionar os arquivos de recursos RESX que serão usados para armazenar todo o texto usado no aplicativo. Precisamos adicionar um arquivo RESX que contém o texto padrão e, em seguida, adicionar arquivos RESX adicionais para cada idioma a que desejamos dar suporte.

#### <a name="base-language-resource"></a>Recurso de idioma base

O arquivo de recursos base (RESX) contém as cadeias de caracteres de idioma padrão (os exemplos pressupõem o que inglês seja o idioma padrão). Adicione o arquivo ao projeto de código comum do Xamarin.Forms clicando com o botão direito do mouse no projeto e escolhendo **Adicionar > Novo Arquivo...** .

Escolha um nome significativo, como **AppResources**, e pressione **OK**.

[![Adicionar arquivo de recurso](text-images/resx-new-file-sml.png "Caixa de diálogo Novo Arquivo")](text-images/resx-new-file.png#lightbox "Caixa de diálogo Novo Arquivo")

Dois arquivos serão adicionados ao projeto:

* Arquivo **AppResources.resx** em que as cadeias de caracteres traduzíveis são armazenadas no formato XML.
* Arquivo **AppResources.designer.cs**, que declara uma classe parcial que contém referências a todos os elementos criados no arquivo XML RESX.

A árvore de solução mostrará que os arquivos são relacionados. O arquivo RESX *deve* ser editado para adicionar novas cadeias de caracteres traduzíveis; o arquivo **.designer.cs** *não* deve ser editado.

![](text-images/appresources-tree.png "Arquivo AppResources.resx")

##### <a name="string-visibility"></a>Visibilidade da cadeia de caracteres

Por padrão, quando são geradas referências fortemente tipadas a cadeias de caracteres, elas são o `internal` para o assembly. Isso ocorre porque a ferramenta de build padrão para arquivos RESX gera o arquivo **.designer.cs** com propriedades `internal`.

Selecione o arquivo **AppResources.resx** e mostre o painel **Propriedades** para ver onde a ferramenta de build está configurada. A captura de tela abaixo mostra a **Ferramenta Personalizada: ResXFileCodeGenerator**.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](text-images/vs-resx-internal-sml.png "Janela Propriedades de AppResources.resx")](text-images/vs-resx-internal.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](text-images/xs-resx-internal-sml.png "Painel de Propriedades de AppResources.resx")](text-images/xs-resx-internal.png#lightbox)

-----

Para tornar as propriedades `public` da cadeia de caracteres fortemente tipadas, você deve alterar manualmente a configuração da **Ferramenta Personalizada: PublicResXFileCodeGenerator**, como mostrado na captura de tela abaixo:


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](text-images/vs-resx-public-sml.png "Janela Propriedades de AppResources.resx")](text-images/vs-resx-public.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](text-images/xs-resx-internal-sml.png "Painel de Propriedades de AppResources.resx")](text-images/xs-resx-internal.png#lightbox)


[![](text-images/xs-resx-public-sml.png "Painel de Propriedades de AppResources.resx")](text-images/xs-resx-public.png#lightbox)

-----

Essa alteração é opcional e só é necessária se você deseja fazer referência a cadeias de caracteres localizadas em assemblies diferentes (por exemplo, se você colocar os arquivos RESX em um assembly diferente de seu código). O exemplo deste tópico deixa as cadeias de caracteres `internal` porque elas são definidas no mesmo assembly de biblioteca .NET Standard do Xamarin.Forms em que são usadas.

Você só precisa definir a ferramenta personalizada no arquivo RESX base, conforme mostrado acima. Não é necessário definir *nenhuma* ferramenta de build nos arquivos RESX específicos a um idioma discutidos nas seções a seguir.

##### <a name="editing-the-resx-file"></a>Editando o arquivo RESX

Infelizmente, não há um editor de RESX interno no Visual Studio para Mac. Adicionar novas cadeias de caracteres traduzíveis requer a adição de um novo elemento `data` XML para cada cadeia de caracteres. Cada elemento `data` pode conter os seguintes elementos:

* O atributo `name` (obrigatório) é a chave para essa cadeia de caracteres traduzível. Ele deve ser um nome de propriedade C# válido, de modo que não são permitidos espaços nem caracteres especiais.
* Elemento `value` (obrigatório), que é a cadeia de caracteres que de fato é exibida no aplicativo.
* O elemento `comment` (opcional) pode conter instruções para o tradutor explicando como a cadeia de caracteres é usada.
* O atributo `xml:space` (opcional), para controlar como o espaçamento na cadeia de caracteres é preservado.

Alguns exemplos de elementos `data` são mostrados aqui:

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

Conforme o aplicativo é escrito, cada parte do texto exibido para o usuário deve ser adicionada ao arquivo de recursos RESX base em um novo elemento `data`. É recomendável que você inclua tantos `comment`s quanto possível para garantir uma tradução de alta qualidade.

> [!NOTE]
> O Visual Studio (incluindo a edição Community gratuita) contém um editor do RESX básico. Se você tem acesso a um computador com Windows, essa pode ser uma maneira conveniente de adicionar e editar cadeias de caracteres em arquivos RESX.

#### <a name="language-specific-resources"></a>Recursos específicos a um idioma

Normalmente, a tradução de fato das cadeias de caracteres de texto padrão não acontecerá até que grandes partes do aplicativo tenham sido escritas (neste caso, o arquivo RESX padrão conterá várias cadeias de caracteres). Ainda é uma boa ideia adicionar os recursos específicos a um idioma no início do ciclo de desenvolvimento, sendo possível também preencher com texto traduzido de maneira automática para ajudar a testar o código de localização.

Um arquivo RESX adicional é adicionado para cada idioma a que desejamos dar suporte.
Arquivos de recurso específico a um idioma devem seguir uma convenção de nomenclatura específica: usar o mesmo nome de arquivo que o arquivo de recurso (por exemplo, **AppResources**) seguido por um ponto (.) e, em seguida, o código de idioma. Veja alguns exemplos simples:

* **AppResources.fr.resx** – traduções para o idioma francês.
* **AppResources.es.resx** – traduções para o idioma espanhol.
* **AppResources.de.resx** – traduções para o idioma alemão.
* **AppResources.ja.resx** – traduções para o idioma japonês.
* **AppResources.zh-Hans.resx** – traduções para o idioma chinês (simplificado).
* **AppResources.zh-Hant.resx** – traduções para o idioma chinês (tradicional).
* **AppResources.pt.resx** – traduções para o idioma português.
* **AppResources.pt-BR.resx** – traduções para o idioma português brasileiro.

O padrão geral é usar códigos de idioma com duas letras, mas há alguns exemplos (como chinês) em que um formato diferente é usado, bem como outros exemplos (por exemplo, português brasileiro) em que um identificador de localidade de quatro caracteres é necessário.

Estes arquivos de recursos específicos a um idioma *não* exigem uma classe parcial **.designer.cs** para que possam ser adicionados como arquivos XML regulares, com **Compilar Ação: EmbeddedResource** definido. Esta captura de tela mostra uma solução que contém os arquivos de recursos específicos a um idioma:

![](text-images/appresources-langs.png "Arquivos de recursos específicos a um idioma")

Conforme o aplicativo é desenvolvido e o texto é adicionado ao arquivo RESX base, você deve enviá-lo para os tradutores, que traduzirão cada elemento `data` e retornarão um arquivo de recurso específico a um idioma (usando a convenção de nomenclatura mostrada) para ser incluído no aplicativo. Alguns exemplos 'traduzidos de maneira automática' são mostrados abaixo:

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

**AppResources.pt-BR.resx (português brasileiro)**

```xml
<data name="AddButton" xml:space="preserve">
    <value>adicionar novo item</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

Somente o elemento `value` precisa ser atualizado pelo tradutor – o `comment` não se destina a ser traduzido. Lembre-se: ao editar arquivos XML, faça o escape de caracteres reservados como `<`, `>`, `&` com `&lt;`, `&gt;` e `&amp;` se eles aparecerem no `value` ou `comment`.

<a name="incode" />

### <a name="using-resources-in-code"></a>Usando recursos no código

Cadeias de caracteres em arquivos de recursos RESX estarão disponíveis para uso no código da interface do usuário usando a classe `AppResources`. O `name` atribuído a cada cadeia de caracteres no arquivo RESX se torna uma propriedade na classe, o que pode ser referenciado no código do Xamarin.Forms da seguinte forma:

```csharp
var myLabel = new Label ();
var myEntry = new Entry ();
var myButton = new Button ();
// populate UI with translated text values from resources
myLabel.Text = AppResources.NotesLabel;
myEntry.Placeholder = AppResources.NotesPlaceholder;
myButton.Text = AppResources.AddButton;
```

A interface do usuário no iOS, no Android e na UWP (Plataforma Universal do Windows) é renderizada da forma esperada, mas agora é possível traduzir o aplicativo para vários idiomas, porque o texto está sendo carregado de um recurso em vez de ser embutido em código. Veja uma captura de tela que mostra a interface do usuário em cada plataforma antes da tradução:

![](text-images/simple-example-english.png "Interfaces do usuário multiplataforma antes da tradução")

### <a name="troubleshooting"></a>Solução de problemas

#### <a name="testing-a-specific-language"></a>Testando um idioma específico

Pode ser complicado passar o simulador ou um dispositivo para idiomas diferentes, especialmente durante o desenvolvimento, quando você deseja testar rapidamente diferentes culturas.

É possível forçar o carregamento de um idioma específico definindo o `Culture`, conforme mostrado neste snippet de código:

```csharp
// force a specific culture, useful for quick testing
AppResources.Culture =  new CultureInfo("fr-FR");
```

Essa abordagem – configurar a cultura diretamente na classe `AppResources` – também pode ser usada para implementar um seletor de idioma dentro de seu aplicativo (em vez de usar a localidade do dispositivo).

#### <a name="loading-embedded-resources"></a>Carregando recursos inseridos

O snippet de código a seguir é útil ao tentar depurar problemas com os recursos inseridos (como arquivos RESX). Adicione este código ao seu aplicativo (no início do ciclo de vida do aplicativo) e ele listará todos os recursos inseridos no assembly, mostrando o identificador de recurso completo:

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

No arquivo **AppResources.Designer.cs**(próximo da *linha 33*), o *nome completo do gerenciador de recursos* é especificado (por exemplo, `"UsingResxLocalization.Resx.AppResources"`), semelhante ao código a seguir:

```csharp
System.Resources.ResourceManager temp =
        new System.Resources.ResourceManager(
                "UsingResxLocalization.Resx.AppResources",
                typeof(AppResources).GetTypeInfo().Assembly);
```

Verifique na **Saída do Aplicativo** os resultados do código de depuração mostrado acima para confirmar se os recursos corretos estão listados (por exemplo, `"UsingResxLocalization.Resx.AppResources"`).

Se não estiverem, a classe `AppResources` não poderá carregar seus recursos.
Verifique o seguinte para resolver problemas em que os recursos não podem ser encontrados:

* O namespace padrão do projeto corresponde ao namespace raiz no arquivo **AppResources.Designer.cs**.
* Se o arquivo **AppResources.resx** estiver localizado em um subdiretório, o nome do subdiretório deverá fazer parte do namespace *e* do identificador do recurso.
* O arquivo **AppResources.resx** tem **Compilar Ação: EmbeddedResource**.
* A configuração **Opções do Projeto > Código-fonte > Políticas de Nomenclatura do .NET > Usar nomes de recursos com estilo do Visual Studio** está marcada. Você pode desmarcar essa opção se preferir, mas os namespaces usados ao fazer referência a seus recursos RESX precisarão ser atualizados em todo o aplicativo.

#### <a name="doesnt-work-in-debug-mode-android-only"></a>Não funciona no modo de depuração (somente Android)

Se as cadeias de caracteres traduzidas estiverem funcionando em seus builds de VERSÃO do Android, mas não durante a depuração, clique com o botão direito do mouse em **Projeto do Android** e selecione **Opções > Build > Build do Android** e certifique-se de que **Implantação de Assembly Rápida** NÃO esteja marcado. Essa opção causa problemas ao carregar recursos e não deverá ser usada se você estiver testando aplicativos localizados.

### <a name="displaying-the-correct-language"></a>Exibindo o idioma correto

Até agora, examinamos como escrever código para que as traduções possam ser fornecidas, mas não como realmente fazê-las aparecer. O código do Xamarin.Forms pode tirar proveito de recursos do .NET para carregar as traduções no idioma correto, mas é necessário consultar o sistema operacional de cada plataforma para determinar qual idioma foi selecionado pelo usuário.

Como são necessários alguns códigos específicos da plataforma para obter a preferência de idioma do usuário, use um [serviço de dependência](~/xamarin-forms/app-fundamentals/dependency-service/index.md) para expor essas informações no aplicativo do Xamarin.Forms e implementá-las para cada plataforma.

Primeiro, defina uma interface para expor a cultura preferencial do usuário, semelhante ao código a seguir:

```csharp
public interface ILocalize
{
    CultureInfo GetCurrentCultureInfo ();
    void SetLocale (CultureInfo ci);
}
```

Depois, use o [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) na classe `App` do Xamarin.Forms para chamar a interface e definir nossa cultura de recursos RESX com o valor correto. Observe que não precisamos definir manualmente esse valor para a Plataforma Universal do Windows, pois a estrutura de recursos reconhece automaticamente o idioma selecionado nessas plataformas.

```csharp
if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
{
    var ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
    Resx.AppResources.Culture = ci; // set the RESX for resource localization
    DependencyService.Get<ILocalize>().SetLocale(ci); // set the Thread for locale-aware methods
}
```

O recurso `Culture` precisa ser definido quando o aplicativo é carregado pela primeira vez para que as cadeias de caracteres no idioma correto sejam usadas. Você pode, opcionalmente, atualizar esse valor de acordo com eventos específicos da plataforma que podem ser acionados no iOS ou no Android se o usuário atualizar suas preferências de idioma enquanto o aplicativo está em execução.

As implementações para a interface `ILocalize` são mostradas na seção [Código específico da plataforma](#Platform-specific_Code) abaixo. Essas implementações tiram proveito dessa classe auxiliar `PlatformCulture`:

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

O código para detectar o idioma a ser exibido deve ser específico da plataforma, pois o iOS, o Android e a UWP expõem essas informações de maneiras ligeiramente diferentes. O código do serviço de dependência `ILocalize` é fornecido abaixo para cada plataforma, junto com requisitos adicionais específicos da plataforma para garantir que o texto localizado seja renderizado corretamente.

O código específico da plataforma também deve lidar com casos em que o sistema operacional permite que o usuário configure um identificador de localidade que não tem suporte da classe `CultureInfo` do .NET. Nesses casos, é necessário escrever um código personalizado para detectar localidades sem suporte e substituir o melhor localidade compatível com o .NET.

#### <a name="ios-application-project"></a>Projeto de Aplicativo iOS

Os usuários do iOS selecionam seu idioma preferencial separadamente da cultura de formatação de data e hora. Para carregar os recursos corretos para localizar um aplicativo Xamarin.Forms, precisamos apenas consultar a matriz `NSLocale.PreferredLanguages` para o primeiro elemento.

A seguinte implementação do serviço de dependência `ILocalize` deve ser colocada no projeto do aplicativo iOS. Como o iOS usa sublinhados em vez de traços (que são a representação padrão do .NET), o código substitui o sublinhado antes de instanciar a classe `CultureInfo`:

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
            // .NET cultures don't support underscores
            string netLanguage = iOSLanguage.Replace("_", "-");

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
> Os blocos `try/catch` no método `GetCurrentCultureInfo` imitam o comportamento de fallback usado normalmente com especificadores de localidade – se a correspondência exata não for encontrada, procure uma correspondência aproximada com base apenas no idioma (primeiro bloco de caracteres na localidade).
>
> No caso do Xamarin.Forms, algumas localidades são válidas no iOS, mas não correspondem a um `CultureInfo` válido no .NET e o código acima tenta lidar com isso.
>
> Por exemplo, a tela **Configurações > Idioma Geral &amp; Região** do iOS permite que você defina o **Idioma** de seu telefone como **Inglês** e a **Região** como **Espanha**, o que resulta na cadeia de caracteres de localidade `"en-ES"`. Quando a criação de `CultureInfo` falha, o código volta a usar apenas as primeiras duas letras para selecionar o idioma de exibição.
>
> Os desenvolvedores devem modificar os métodos `iOSToDotnetLanguage` e `ToDotnetFallbackLanguage` para lidar com casos específicos necessários em seus idiomas com suporte.

Há alguns elementos da interface do usuário definidos pelo sistema que são traduzidos automaticamente pelo iOS, como o botão **Concluído** no controle `Picker`. Para forçar o iOS a traduzir esses elementos, precisamos indicar a quais idiomas damos suporte no arquivo **Info.plist**. Você pode adicionar esses valores usando **Info.plist > Origem**, conforme mostrado aqui:

![Chaves de localização em Info.plist](text-images/info-plist.png "Chaves de localização em Info.plist")

Como alternativa, abra o arquivo **Info.plist** em um editor de XML e edite os valores diretamente:

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

Após você ter implementado o serviço de dependência e atualizado **Info.plist**, o aplicativo do iOS poderá exibir o texto localizado.

> [!NOTE]
> Observe que a Apple trata o idioma português um pouco diferente da forma esperada.
> Nos [documentos a Apple](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW2): _"use pt como a ID do idioma português usado no Brasil e pt-PT como a ID do idioma português usado em Portugal"_ .
> Isso significa que, quando o idioma português é escolhido em uma localidade não padrão, o idioma de fallback é o português brasileiro no iOS, a menos que seja escrito um código para alterar esse comportamento (como o `ToDotnetFallbackLanguage` acima).

Para obter mais informações sobre a localização no iOS, confira [Localização no iOS](~/ios/app-fundamentals/localization/index.md).

#### <a name="android-application-project"></a>Projeto de Aplicativo Android

O Android expõe a localidade selecionada por meio de `Java.Util.Locale.Default` e também usa o sublinhado como caractere separador, em vez do traço (que é substituído pelo código a seguir). Adicione essa implementação do serviço de dependência ao projeto de aplicativo do Android:

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
> Os blocos `try/catch` no método `GetCurrentCultureInfo` imitam o comportamento de fallback usado normalmente com especificadores de localidade – se a correspondência exata não for encontrada, procure uma correspondência aproximada com base apenas no idioma (primeiro bloco de caracteres na localidade).
>
> No caso do Xamarin.Forms, algumas localidades são válidas no Android, mas não correspondem a um `CultureInfo` válido no .NET e o código acima tenta lidar com isso.
>
> Os desenvolvedores devem modificar os métodos `iOSToDotnetLanguage` e `ToDotnetFallbackLanguage` para lidar com casos específicos necessários em seus idiomas com suporte.

Após esse código ter sido adicionado ao projeto de aplicativo do Android, ele será capaz de exibir automaticamente as cadeias de caracteres traduzidas.

> [!WARNING]
> Se as cadeias de caracteres traduzidas estiverem funcionando em seus builds de VERSÃO do Android, mas não durante a depuração, clique com o botão direito do mouse em **Projeto do Android** e selecione **Opções > Build > Build do Android** e certifique-se de que **Implantação de Assembly Rápida** NÃO esteja marcado. Essa opção causa problemas ao carregar recursos e não deverá ser usada se você estiver testando aplicativos localizados.

Para obter mais informações sobre a localização no Android, confira [Localização no Android](~/android/app-fundamentals/localization.md).

#### <a name="universal-windows-platform"></a>Plataforma Universal do Windows

Projetos da UWP (Plataforma Universal do Windows) não requerem o serviço de dependência. Em vez disso, a plataforma define automaticamente a cultura do recurso corretamente.

##### <a name="assemblyinfocs"></a>AssemblyInfo.cs

Expanda o nó Propriedades no projeto da biblioteca .NET Standard e clique duas vezes no arquivo **AssemblyInfo.cs**. Adicione a seguinte linha ao arquivo para definir o idioma do assembly de recursos neutro como inglês:

```csharp
[assembly: NeutralResourcesLanguage("en")]
```

Isso informa ao gerenciador de recursos a cultura padrão do aplicativo, garantindo assim que as cadeias de caracteres definidas no arquivo RESX de idioma neutro (**AppResources.resx**) sejam exibidas quando o aplicativo estiver em execução em uma das localidades de idioma inglês.

### <a name="example"></a>Exemplo

Depois de atualizar projetos específicos da plataforma conforme mostrado acima e recompilar o aplicativo com arquivos RESX traduzidos, traduções atualizadas serão disponibilizadas em cada aplicativo. Está é uma captura de tela do código de exemplo traduzido para chinês simplificado:

![](text-images/simple-example-hans.png "Interfaces do usuário multiplataforma traduzidas para chinês simplificado")

Para obter mais informações sobre a localização na UWP, confira [Localização na UWP](/windows/uwp/design/globalizing/globalizing-portal/).

## <a name="localizing-xaml"></a>Localizando XAML

Ao compilar uma interface do usuário do Xamarin.Forms em XAML, a marcação teria uma aparência semelhante a esta, com cadeias de caracteres inseridas diretamente no XML:

```xaml
<Label Text="Notes:" />
<Entry Placeholder="eg. buy milk" />
<Button Text="Add to list" />
```

Idealmente, poderíamos traduzir os controles da interface do usuário diretamente no XAML, o que pode ser feito criando uma *extensão de marcação*. O código de uma extensão de marcação que expõe os recursos RESX para XAML é mostrado abaixo. Essa classe deve ser adicionada ao código comum do Xamarin.Forms (juntamente com as páginas XAML):

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

Os marcadores a seguir explicam os elementos importantes do código acima:

* A classe é denominada `TranslateExtension`, mas, por convenção, podemos nos referir a ela como **Translate** em nossa marcação.
* A classe implementa `IMarkupExtension`, que é necessário para que o Xamarin.Forms funcione.
* `"UsingResxLocalization.Resx.AppResources"` é o identificador de recurso de nossos recursos RESX. Ele é composto por nosso namespace padrão, pela pasta em que se encontram os arquivos de recurso e pelo nome do arquivo RESX padrão.
* A classe `ResourceManager` é criada usando `IntrospectionExtensions.GetTypeInfo(typeof(TranslateExtension)).Assembly)` para determinar o assembly atual do qual carregar os recursos e é armazenada em cache no campo `ResMgr` estático. Ela é criada como um tipo `Lazy`, de forma que sua criação é adiada até que ela seja usada pela primeira vez no método `ProvideValue`.
* `ci` usa o serviço de dependência para obter o idioma escolhido pelo usuário do sistema operacional nativo.
* `GetString` é o método que recupera a cadeia de caracteres traduzida real dos arquivos de recursos. Na Plataforma Universal do Windows, `ci` será nulo porque a interface `ILocalize` não está implementada nessas plataformas. Isso é equivalente a chamar o método `GetString` apenas com o primeiro parâmetro. Em vez disso, a estrutura de recursos automaticamente reconhecerá a localidade e recuperará a cadeia de caracteres traduzida do arquivo RESX apropriado.
* O tratamento de erro foi incluído para ajudar a depurar recursos ausentes lançando uma exceção (somente no modo `DEBUG`).

O snippet de XAML a seguir mostra como usar a extensão de marcação. Há duas etapas para fazê-la funcionar:

1. Declarar o namespace `xmlns:i18n` personalizado no nó raiz. `namespace` e `assembly` devem corresponder exatamente às configurações do projeto – neste exemplo, são idênticos, mas podem ser diferentes em seu projeto.
2. Use a sintaxe `{Binding}` em atributos que normalmente conteriam texto para chamar a extensão de marcação `Translate`. A chave de recurso é o único parâmetro necessário.

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

A sintaxe mais detalhada a seguir também é válida para a extensão de marcação:

```xaml
<Button Text="{i18n:TranslateExtension Text=AddButton}" />
```

## <a name="localizing-platform-specific-elements"></a>Localizando elementos específicos da plataforma

Embora possamos manipular a tradução da interface do usuário no código do Xamarin.Forms, há alguns elementos que precisam ser feitos em cada projeto específico da plataforma. Esta seção abordará como localizar:

* Nome do aplicativo
* Imagens

O projeto de exemplo inclui uma imagem localizada chamada **flag.png**, que é referenciada em C# da seguinte maneira:

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

A imagem do sinalizador também é referenciada no XAML desta forma:

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

Todas as plataformas resolverão automaticamente referências de imagem como essa para versões localizadas das imagens, desde que as estruturas de projeto explicadas abaixo sejam implementadas.

### <a name="ios-application-project"></a>Projeto de Aplicativo iOS

O iOS usa um padrão de nomenclatura chamado Localization Projects ou diretórios **.lproj** para conter os recursos de imagem e cadeia de caracteres. Esses diretórios podem conter versões localizadas de imagens usadas no aplicativo e também o arquivo **InfoPlist.strings**, que pode ser usado para localizar o nome do aplicativo. Para obter mais informações sobre a localização no iOS, confira [Localização no iOS](~/ios/app-fundamentals/localization/index.md).

#### <a name="images"></a>Imagens

Esta captura de tela mostra o aplicativo de exemplo do iOS com os diretórios **.lproj** específicos a um idioma. O diretório do idioma espanhol, chamado **es.lproj**, contém as versões localizadas da imagem padrão, bem como **flag.png**:

![](text-images/ios-resources.png "Diretórios do projeto de localização do iOS")

Cada diretório de idioma contém uma cópia de **flag.png**, localizada para esse idioma. Se nenhuma imagem for fornecida, o sistema operacional, por padrão, usará a imagem no diretório de idioma padrão. Para ter suporte completo do Retina, você precisa fornecer cópias de **@2x** e **@3x** de cada imagem.

#### <a name="app-name"></a>Nome do Aplicativo

O conteúdo de **InfoPlist.strings** é apenas um par de chave-valor para configurar o nome do aplicativo:

```csharp
"CFBundleDisplayName" = "ResxEspañol";
```

Quando o aplicativo é executado, o nome do aplicativo e a imagem são localizados:

![](text-images/ios-imageicon.png "Localização de imagem e texto no aplicativo de exemplo de iOS")

### <a name="android-application-project"></a>Projeto de Aplicativo Android

O Android segue um esquema diferente para armazenar imagens localizadas usando diferentes diretórios **desenháveis** e de **cadeias de caracteres** com um sufixo de código de idioma. Quando um código de localidade de quatro letras é necessário (por exemplo, zh-TW ou pt-BR), observe que o Android exige um **r** adicional após o traço/antes do código de localidade (por exemplo, zh-rTW ou pt-rBR). Para obter mais informações sobre a localização no Android, confira [Localização no Android](~/android/app-fundamentals/localization.md).

#### <a name="images"></a>Imagens

Esta captura de tela mostra o exemplo do Android com um alguns recursos desenháveis e cadeias de caracteres localizados:

![](text-images/android-resources.png "Diretórios de recursos desenháveis e cadeias de caracteres localizados no Android")

Observe que o Android não usa os códigos zh-Hans e zh-Hant para chinês simplificado e chinês tradicional. Em vez disso, ele dá suporte apenas aos códigos específicos do país zh-CN e zh-TW.

Para dar suporte a imagens com resolução diferente para telas de alta densidade, crie pastas adicionais com os sufixos `-*dpi`, como **drawables-es-mdpi**, **drawables-es-xdpi**, **drawables-es-xxdpi** etc. Confira [Fornecendo recursos alternativos do Android](https://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources) para obter mais informações.

#### <a name="app-name"></a>Nome do Aplicativo

O conteúdo de **strings.xml** é apenas um par de chave-valor para configurar o nome do aplicativo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">ResxEspañol</string>
</resources>
```

Atualize **MainActivity.cs** no projeto de aplicativo do Android para que `Label` referencie as cadeias de caracteres XML.

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

Agora, o aplicativo localiza o nome do aplicativo e a imagem. Veja uma captura de tela do resultado (em espanhol):

![](text-images/android-imageicon.png "Localização de imagem e texto no aplicativo de exemplo de Android")

### <a name="universal-windows-platform-application-projects"></a>Projetos de aplicativo da Plataforma Universal do Windows

A Plataforma Universal do Windows tem uma infraestrutura de recurso que simplifica a localização de imagens e o nome do aplicativo. Para obter mais informações sobre a localização na UWP, confira [Localização na UWP](/windows/uwp/design/globalizing/globalizing-portal/).

#### <a name="images"></a>Imagens

É possível localizar imagens colocando-as em uma pasta específica do recurso, como demonstrado na seguinte captura de tela:

![](text-images/uwp-image-folder-structure.png "Estrutura de pastas de localização de imagens da UWP")

Em tempo de execução, a infraestrutura de recursos do Windows selecionará a imagem apropriada com base na localidade do usuário.

## <a name="summary"></a>Resumo

Aplicativos do Xamarin.Forms podem ser localizados usando arquivos RESX e classes de globalização do .NET. Exceto por uma pequena quantidade de código específico da plataforma para detectar qual é o idioma preferido pelo usuário, a maior parte do esforço de localização está centralizado no código comum.

Normalmente, as imagens são manipuladas de forma específica à plataforma para tirar proveito do suporte de resolução múltipla fornecido no iOS e no Android.

## <a name="related-links"></a>Links relacionados

- [Exemplo de localização de RESX](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingresxlocalization)
- [Aplicativo de exemplo TodoLocalized](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todolocalized)
- [Localização multiplataforma](~/cross-platform/app-fundamentals/localization.md)
- [Localização no iOS](~/ios/app-fundamentals/localization/index.md)
- [Localização no Android](~/android/app-fundamentals/localization.md)
- [Localização na UWP](/windows/uwp/design/globalizing/globalizing-portal/)
- [Usando a classe CultureInfo (MSDN)](https://msdn.microsoft.com/library/87k6sx8t%28v=vs.90%29.aspx)
- [Localizando e usando recursos para uma cultura específica (MSDN)](https://msdn.microsoft.com/library/s9ckwb4b%28v=vs.90%29.aspx)
