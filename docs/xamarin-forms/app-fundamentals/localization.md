---
title: Localização
description: Aplicativos xamarin. Forms podem ser localizados usando arquivos de recursos do .NET.
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/06/2016
ms.openlocfilehash: f179fcfc26dd73bf1655c786078dce1f6a02b3a9
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2018
---
# <a name="localization"></a>Localização

_Aplicativos xamarin. Forms podem ser localizados usando arquivos de recursos do .NET._

## <a name="overview"></a>Visão geral

O mecanismo interno para localização de usos de aplicativos .NET [arquivos RESX](http://msdn.microsoft.com/library/ekyft91f(v=vs.90).aspx) e as classes de `System.Resources` e `System.Globalization` namespaces. Arquivos RESX que contém cadeias de caracteres traduzidas são inseridos no assembly xamarin. Forms, juntamente com uma classe gerada pelo compilador que fornece acesso fortemente tipado para as traduções. O texto de tradução pode ser recuperado no código.

### <a name="sample-code"></a>Código de exemplo

Há duas amostras associadas a este documento:

* [UsingResxLocalization](https://github.com/xamarin/xamarin-forms-samples/tree/master/UsingResxLocalization) uma demonstração muito simples dos conceitos explicados. Os trechos de código mostrados abaixo são todos deste exemplo.
* [TodoLocalized](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized) é um aplicativo de trabalho básico que usa essas técnicas de localização.

#### <a name="shared-projects-are-not-recommended"></a>Projetos compartilhados não são recomendados.

O exemplo TodoLocalized inclui um [demonstração de projeto compartilhado](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized/SharedProject/) no entanto, devido a limitações de sistema de compilação os arquivos de recurso não obtém um **. designer.cs** arquivo gerado que interrompe a capacidade de acessar convertidas de cadeias de caracteres [fortemente tipado no código](~/xamarin-forms/app-fundamentals/localization.md).

O restante deste documento se relaciona aos projetos usando o modelo PCL xamarin. Forms.

## <a name="globalizing-xamarinforms-code"></a>Globalizando código xamarin. Forms

**Globalizando** um aplicativo é o processo de fazê-lo "prontos". Isso significa que escrever um código que é capaz de exibir idiomas diferentes.

Uma das partes principais de globalização de um aplicativo está criando a interface do usuário para que não há nenhum *embutido* texto. Em vez disso, nada é exibido para o usuário deve ser recuperado de um conjunto de cadeias de caracteres que foram traduzidos para o idioma escolhido.

Neste documento, examinaremos como usar arquivos RESX para armazenar as cadeias de caracteres e recuperá-los para exibição a preferência do usuário.

Os exemplos de destino idiomas inglês, francês, espanhol, alemão, chinês, japonês, russo e português (Brasil). Aplicativos podem ser convertidos em apenas ou como vários idiomas conforme necessário.

### <a name="adding-resources"></a>Adição de recursos

Globalização de um aplicativo xamarin. Forms PCL a primeira etapa é adicionar os arquivos de recurso RESX que serão usados para armazenar todo o texto usado no aplicativo. Precisamos adicionar um arquivo RESX que contém o texto padrão e, em seguida, adicione arquivos RESX adicionais para cada idioma que deseja dar suporte.

#### <a name="base-language-resource"></a>Recurso de idioma de base

O arquivo de base de recursos (RESX) contém as cadeias de caracteres de idioma padrão (os exemplos assumem o que inglês é o idioma padrão). Adicione o arquivo para o projeto de código comum do xamarin. Forms clicando duas vezes no projeto e escolha **Adicionar > novo arquivo...** .

Escolha um nome significativo, como **AppResources** e pressione **Okey**.

[![Adicionar arquivo de recurso](localization-images/resx-new-file-sml.png "caixa de diálogo do novo arquivo")](localization-images/resx-new-file.png#lightbox "caixa de diálogo do novo arquivo")

Dois arquivos serão adicionados ao projeto:

* **AppResources. resx** arquivo onde traduzíveis cadeias de caracteres são armazenadas em um formato XML.
* **AppResources.designer.cs** arquivo que declara uma classe parcial para conter referências a todos os elementos criados no arquivo RESX XML.

A árvore de solução mostrará os arquivos relacionados. O arquivo RESX *devem* ser editadas para adicionar novas cadeias de caracteres traduzíveis; o **. designer.cs** arquivo deve *não* ser editado.

![](localization-images/appresources-tree.png "Arquivo AppResources. resx")

##### <a name="string-visibility"></a>Visibilidade de cadeia de caracteres

Por padrão quando referências fortemente tipada em cadeias de caracteres são geradas, eles serão `internal` ao assembly. Isso ocorre porque a ferramenta de compilação padrão para arquivos RESX gera o **. designer.cs** arquivo com `internal` propriedades.

Selecione o **AppResources. resx** de arquivo e mostrar o **propriedades** painel para ver onde a ferramenta de compilação é configurar. A captura de tela abaixo mostra o **ferramenta personalizada: ResXFileCodeGenerator**.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](localization-images/vs-resx-internal-sml.png "Janela de propriedades para AppResources. resx")](localization-images/vs-resx-internal.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](localization-images/xs-resx-internal-sml.png "Painel de propriedades de AppResources. resx")](localization-images/xs-resx-internal.png#lightbox)

-----

Para que as propriedades de cadeia de caracteres fortemente tipada `public`, você deverá alterar manualmente a configuração **ferramenta personalizada: PublicResXFileCodeGenerator**, conforme mostrado na captura de tela abaixo:


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](localization-images/vs-resx-public-sml.png "Janela de propriedades para AppResources. resx")](localization-images/vs-resx-public.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](localization-images/xs-resx-internal-sml.png "Painel de propriedades de AppResources. resx")](localization-images/xs-resx-internal.png#lightbox)


[![](localization-images/xs-resx-public-sml.png "Painel de propriedades de AppResources. resx")](localization-images/xs-resx-public.png#lightbox)

-----

Essa alteração é opcional e só é necessário se você deseja fazer referência a cadeias de caracteres localizadas em assemblies diferentes (por exemplo, se você colocar os arquivos RESX em um assembly diferente ao seu código). O exemplo deste tópico deixa as cadeias de caracteres `internal` porque elas são definidas no mesmo assembly xamarin. Forms PCL onde eles são usados.

Você só precisa definir a ferramenta personalizada no arquivo RESX base, conforme mostrado acima. Você não precisa definir *qualquer* ferramenta de compilação nos arquivos específicos de linguagem RESX discutidos nas seções a seguir.

##### <a name="editing-the-resx-file"></a>Editar o arquivo RESX

Infelizmente não há nenhum editor RESX interno no Visual Studio para Mac. Adicionar novas cadeias de caracteres traduzíveis requer a adição de um novo XML `data` elemento para cada cadeia de caracteres. Cada `data` elemento pode conter o seguinte:

* `name` atributo (obrigatório) é a chave para essa cadeia de caracteres da traduzível. Ele deve ser um C# nome de propriedade válido - então sem espaços ou caracteres especiais são permitidos.
* `value` elemento (obrigatório), que é a cadeia de caracteres real que é exibida no aplicativo.
* `comment` elemento (opcional) pode conter instruções para o conversor que explica como essa cadeia de caracteres é usada.
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
> O Visual Studio (incluindo o Community edition gratuita) contém um editor básico de RESX. Se você tiver acesso a um computador Windows, que pode ser uma maneira conveniente para adicionar e editar cadeias de caracteres em arquivos RESX.

#### <a name="language-specific-resources"></a>Recursos específicos de linguagem

Normalmente, a conversão de cadeias de caracteres de texto padrão não acontecerá até que foram gravados grandes partes do aplicativo (neste caso o arquivo RESX padrão conterá muitas cadeias de caracteres). Ainda é uma boa ideia para adicionar os recursos específicos de linguagem no início do ciclo de desenvolvimento, opcionalmente, preenchendo com texto traduzido para ajudar a testar o código de localização.

Um arquivo RESX adicional é adicionado para cada idioma que deseja dar suporte.
Arquivos de recursos específicos de idioma devem seguir uma convenção de nomenclatura específica: usar o mesmo nome de arquivo, como os recursos básicos de arquivos (por exemplo. **AppResources**) seguido por um ponto (.) e, em seguida, o código de idioma. Exemplos simples:

* **AppResources.fr.resx** -traduções de francês.
* **AppResources.es.resx** -traduções de idioma espanhol.
* **AppResources.de.resx** -traduções de idioma alemão.
* **AppResources.ja.resx** -traduções de idioma japonês.
* **AppResources.zh Hans.resx** - chinês (simplificado) traduções de idioma.
* **AppResources.zh Hant.resx** - chinês (tradicional) traduções de idioma.
* **AppResources.pt.resx** -português traduções.
* **AppResources.pt BR.resx** -traduções português brasileiro.

O padrão geral é usar códigos de idioma de duas letras, mas há alguns exemplos (como chinês) em um formato diferente é usado e outros exemplos (por exemplo, português (Brasil)) em que é necessário um identificador de localidade de quatro caracteres.

Esses arquivos de recursos específicos de linguagem *não* exigem um **. designer.cs** de classe parcial para que eles podem ser adicionados como arquivos XML regulares, com o **ação de compilação: EmbeddedResource**definido. Esta captura de tela mostra uma solução que contém arquivos de recursos específicos de idioma:

![](localization-images/appresources-langs.png "Arquivos de recursos específicos de idioma")

Como um aplicativo é desenvolvido e o arquivo RESX base tiver texto adicionado, você deverá enviá-las para tradutores que converterá cada `data` elemento e retornar um arquivo de recurso específico de idioma (usando a convenção de nomenclatura mostrada) a serem incluídos no aplicativo. Alguns exemplos de 'traduzido' são mostrados abaixo:

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

Somente o `value` elemento precisa ser atualizado pelo conversor - o `comment` não se destina a ser convertido. Lembre-se: ao editar arquivos XML para escapar caracteres reservados como `<`, `>`, `&` com `&lt;`, `&gt;`, e `&amp;` se aparecerem no `value` ou `comment`.

<a name="incode" />

### <a name="using-resources-in-code"></a>Uso de recursos em código

Cadeias de caracteres em arquivos de recurso RESX estarão disponíveis para usar em seu código de interface de usuário usando o `AppResources` classe. O `name` atribuído a cada cadeia de caracteres no RESX arquivo se torna uma propriedade na classe que pode ser referenciada no código do xamarin. Forms, conforme mostrado abaixo:

```csharp
var myLabel = new Label ();
var myEntry = new Entry ();
var myButton = new Button ();
// populate UI with translated text values from resources
myLabel.Text = AppResources.NotesLabel;
myEntry.Placeholder = AppResources.NotesPlaceholder;
myButton.Text = AppResources.AddButton;
```

A interface do usuário no iOS, Android e a Windows UWP (plataforma Universal) é renderizada como você esperaria, exceto agora é possível converter o aplicativo em vários idiomas, porque o texto está sendo carregado de um recurso em vez de ser codificada. Aqui está uma captura de tela mostrando a interface do usuário em cada plataforma antes da conversão:

![](localization-images/simple-example-english.png "Interfaces do usuário antes da conversão de plataforma cruzada")

### <a name="troubleshooting"></a>Solução de problemas

#### <a name="testing-a-specific-language"></a>Testando um idioma específico

Ele pode ser complicado alternar o simulador ou um dispositivo para idiomas diferentes, particulary durante o desenvolvimento quando quiser testar rapidamente a diferentes culturas.

Você pode forçar um idioma específico a ser carregado, definindo o `Culture` conforme mostrado no trecho de código a este:

```csharp
// force a specific culture, useful for quick testing
AppResources.Culture =  new CultureInfo("fr-FR");
```

Essa abordagem – definir a cultura diretamente a `AppResources` classe – também podem ser usadas para implementar um seletor de idioma dentro de seu aplicativo (em vez de usar a localidade do dispositivo).

#### <a name="loading-embedded-resources"></a>Recursos incorporados de carregamento

O trecho de código a seguir é útil ao tentar depurar problemas com os recursos incorporados (como arquivos RESX). Adicione este código ao seu aplicativo (no início do ciclo de vida do aplicativo) e uma lista de todos os recursos incorporados no assembly, mostrando o identificador de recurso completo:

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

No **AppResources.Designer.cs** arquivo (em torno de *linha 33*), completo *nome do Gerenciador de recursos* for especificado (por exemplo. `"UsingResxLocalization.Resx.AppResources"`) como o código a seguir:

```csharp
System.Resources.ResourceManager temp =
        new System.Resources.ResourceManager(
                "UsingResxLocalization.Resx.AppResources",
                typeof(AppResources).GetTypeInfo().Assembly);
```

Verifique o **saída do aplicativo** para os resultados do código de depuração mostrado acima, para confirmar os recursos corretos são listados (ie. `"UsingResxLocalization.Resx.AppResources"`).

Caso contrário, o `AppResources` classe será possível carregar seus recursos.
Verifique o seguinte para resolver problemas em que os recursos não podem ser encontrados:

* O namespace padrão para o projeto coincide com o namespace raiz no **AppResources.Designer.cs** arquivo.
* Se o **AppResources. resx** arquivo está localizado em um subdiretório, o nome do subdiretório deve ser parte do namespace *e* parte do identificador de recurso.
* O **AppResources. resx** arquivo tem **ação de compilação: EmbeddedResource**.
* O **opções de projeto > código-fonte > políticas de nomeação de .NET > nomes de recursos de estilo do uso de Visual Studio** está marcada. Você pode untick isso se você preferir, porém os namespaces usados ao fazer referência a seus recursos RESX precisará atualizado em todo o aplicativo.

#### <a name="doesnt-work-in-debug-mode-android-only"></a>Não funciona no modo de depuração (somente Android)

Se as cadeias de caracteres traduzidas estiver trabalhando em compilações versão Android, mas não durante a depuração, clique duas vezes no **projeto Android** e selecione **opções > compilar > compilar Android** e certifique-se de que o **a rápida implantação de assembly** não está marcada. Essa opção faz com que os problemas com o carregamento de recursos e não deve ser usada se você estiver testando aplicativos localizados.

### <a name="displaying-the-correct-language"></a>Exibindo no idioma correto

Até agora, examinamos como escrever código para que as traduções podem ser fornecidas, mas não como realmente fazer com que apareçam. Código do xamarin. Forms pode aproveitar. Recursos do NET para carregar as traduções de idioma correto, mas precisamos consultará o sistema operacional em cada plataforma para determinar qual idioma selecionado pelo usuário.

Como um código de plataforma específica é necessária para obter a preferência de idioma do usuário, use um [serviço de dependência](~/xamarin-forms/app-fundamentals/dependency-service/index.md) expor essas informações no aplicativo xamarin. Forms e implementá-la para cada plataforma.

Primeiro, defina uma interface para expor a cultura preferencial do usuário, semelhante ao código a seguir:

```csharp
public interface ILocalize
{
    CultureInfo GetCurrentCultureInfo ();
    void SetLocale (CultureInfo ci);
}
```

Em segundo lugar, use o [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) no xamarin. Forms `App` classe para chamar a interface e definir nossa cultura de recursos RESX para o valor correto. Observe que não precisamos definir manualmente esse valor para a plataforma Universal do Windows, desde que a estrutura de recursos automaticamente reconhece o idioma selecionado nessas plataformas.

```csharp
if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
{
    var ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
    Resx.AppResources.Culture = ci; // set the RESX for resource localization
    DependencyService.Get<ILocalize>().SetLocale(ci); // set the Thread for locale-aware methods
}
```

O recurso `Culture` precisa ser definida quando o aplicativo é carregado pela primeira vez para que sejam usadas as cadeias de caracteres do idioma correto. Opcionalmente, você pode atualizar esse valor de acordo com os eventos específicos de plataforma que pode ser gerado no iOS ou Android, se o usuário atualiza suas preferências de idioma, enquanto o aplicativo está em execução.

As implementações do `ILocalize` mostrados na interface de [código específico da plataforma](#Platform-specific_Code) seção abaixo. Essas implementações se aproveitar disso `PlatformCulture` classe auxiliar:

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

Para detectar o idioma para exibir o código deve ser específico da plataforma porque iOS, Android e UWP expõem essas informações de maneiras ligeiramente diferentes. O código para o `ILocalize` serviço de dependência é fornecido abaixo para cada plataforma, juntamente com requisitos específicos de plataforma adicionais para garantir que o texto localizado é renderizado corretamente.

O código específico da plataforma também deve lidar com casos em que o sistema operacional permite que o usuário configure um identificador de localidade que não tem suporte. Do NET `CultureInfo` classe. Nesses casos o código personalizado deve ser escrito para detectar localidades sem suporte e substitua o melhor desempenho. Localidade compatível com .NET.

#### <a name="ios-application-project"></a>Projeto de Aplicativo iOS

usuários do iOS selecionem seu idioma preferencial separadamente da data e hora formatação cultura. Para carregar os recursos corretos para localizar um aplicativo xamarin. Forms é necessário consultar o `NSLocale.PreferredLanguages` matriz para o primeiro elemento.

A seguinte implementação do `ILocalize` serviço de dependência deve ser colocado no projeto de aplicativo iOS. Como iOS usa sublinhados em vez de traços (que é a representação padrão do .NET) o código substitui o sublinhado antes da instanciação de `CultureInfo` classe:

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
                case "ms-SG":   // "Malaysian (Singapore)" not supported .NET culture
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
> O `try/catch` blocos no `GetCurrentCultureInfo` método imitam o comportamento de fallback normalmente usado com especificadores de localidade – se a correspondência exata não for encontrada, procure para uma correspondência aproximada com base apenas em da linguagem (primeiro bloco de caracteres na localidade).
>
> No caso do xamarin. Forms, algumas localidades são válidas no iOS, mas não correspondem às válido `CultureInfo` no .NET e o código acima tentativas para lidar com isso.
>
> Por exemplo, o iOS **Configurações > geral idioma &amp; região** tela permite que você defina o seu telefone **idioma** para **inglês** , mas o  **Região** para **Espanha**, o que resulta em uma cadeia de caracteres de localidade de `"en-ES"`. Quando o `CultureInfo` criação falhar, o código voltará a usar apenas as duas primeiras letras para selecionar o idioma de exibição.
>
> Os desenvolvedores devem modificar a `iOSToDotnetLanguage` e `ToDotnetFallbackLanguage` métodos para lidar com casos específicos necessários para seus idiomas com suporte.


Há alguns elementos de interface do usuário definidas pelo sistema que são convertidos automaticamente pelo iOS, como o **feito** botão o `Picker` controle. Para forçar o iOS para converter esses elementos, precisamos indicar quais idiomas apoiamos no **Info. plist** arquivo. Você pode adicionar esses valores por meio de **Info. plist > origem** conforme mostrado aqui:

![Chaves de localização no Info. plist](localization-images/info-plist.png "chaves de localização no Info. plist")

Como alternativa, abra o **Info. plist** do arquivo em um editor de XML e edite os valores diretamente:

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

Depois que você tiver implementado o serviço de dependência e atualizada **Info. plist**, o aplicativo iOS poderá exibir o texto localizado.

> [!NOTE]
> Observe que trata de Apple português ligeiramente diferente do que seria esperado.
> De [seus documentos](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW2): _"usar pt como a ID de idioma para português como ele é usado no Brasil e pt-PT como a ID de idioma para português como ele é usado no Portugal"_.
> Isso significa que, quando português é escolhido em um local não padrão, o idioma de fallback será português (Brasil) no iOS, a menos que o código é escrito para alterar esse comportamento (como o `ToDotnetFallbackLanguage` acima).

#### <a name="android-application-project"></a>Projeto de Aplicativo Android

Android expõe o local selecionado no momento por meio de `Java.Util.Locale.Default`e também usa um separador de sublinhado em vez de um traço (que é substituído pelo código a seguir). Adicione essa implementação de serviço de dependência para o projeto de aplicativo do Android:

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
> O `try/catch` blocos no `GetCurrentCultureInfo` método imitam o comportamento de fallback normalmente usado com especificadores de localidade – se a correspondência exata não for encontrada, procure para uma correspondência aproximada com base apenas em da linguagem (primeiro bloco de caracteres na localidade).
>
> No caso do xamarin. Forms, algumas localidades são válidas no Android, mas não correspondem às válido `CultureInfo` no .NET e o código acima tentativas para lidar com isso.
>
> Os desenvolvedores devem modificar a `iOSToDotnetLanguage` e `ToDotnetFallbackLanguage` métodos para lidar com casos específicos necessários para seus idiomas com suporte.


Depois que esse código foi adicionado ao projeto de aplicativo do Android, ele poderá ser exibido automaticamente as cadeias de caracteres traduzidas.

> [!NOTE]
>️ **Aviso:** se as cadeias de caracteres traduzidas estiver trabalhando em compilações versão Android, mas não durante a depuração, clique duas vezes no **projeto Android** e selecione **opções > compilar > Android Criar** e certifique-se de que o **a rápida implantação de assembly** não está marcada. Essa opção faz com que os problemas com o carregamento de recursos e não deve ser usada se você estiver testando aplicativos localizados.

#### <a name="universal-windows-platform"></a>Plataforma Universal do Windows

Projetos universal do Windows UWP (plataforma) não requerem o serviço de dependência. Em vez disso, essa plataforma define automaticamente cultura do recurso corretamente.

##### <a name="assemblyinfocs"></a>AssemblyInfo.cs

Expanda o nó de propriedades do projeto de biblioteca de classe portátil (PCL) e clique duas vezes no **AssemblyInfo.cs** arquivo. Adicione a seguinte linha ao arquivo para definir a linguagem de assembly de recursos neutros para o inglês:

```csharp
[assembly: NeutralResourcesLanguage("en")]
```

Isso informa o Gerenciador de recursos de cultura do padrão do aplicativo, portanto, garantindo que as cadeias de caracteres definidas no arquivo RESX de neutra de idioma (**AppResources. resx**) será exibido quando o aplicativo está em execução em um de localidades do inglês.

### <a name="example"></a>Exemplo

Depois de atualizar os projetos específicos de plataforma como mostrado acima e recompilar o aplicativo com arquivos RESX traduzidos, traduções atualizadas estão disponíveis em cada aplicativo. Aqui está uma captura de tela de convertido em chinês simplificado, o exemplo de código:

![](localization-images/simple-example-hans.png "Interfaces de plataforma cruzada convertidas em chinês simplificado")

## <a name="localizing-xaml"></a>Localizando o XAML

Quando criar a marcação de uma interface de usuário do xamarin. Forms em XAML seria semelhante a isso, com cadeias de caracteres inseridos diretamente no XML:

```xaml
<Label Text="Notes:" />
<Entry Placeholder="eg. buy milk" />
<Button Text="Add to list" />
```

Idealmente, podemos foi possível converter controles de interface do usuário diretamente no XAML, que pode ser feito criando um *extensão de marcação*. O código para uma extensão de marcação que expõe os recursos RESX para XAML é mostrado abaixo. Essa classe deve ser adicionada ao código comum xamarin. Forms (juntamente com as páginas XAML):

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

* A classe é nomeada `TranslateExtension`, mas, por convenção, podemos nos referir é como **traduzir** em nossa marcação.
* A classe implementa `IMarkupExtension`, que é exigido pelo xamarin. Forms para que ele funcione.
* `"UsingResxLocalization.Resx.AppResources"` é o identificador de recurso para nossos recursos RESX. Ela é composta de nosso espaço para nome padrão, a pasta onde se encontram os arquivos de recurso e o nome do arquivo RESX de padrão.
* O `ResourceManager` classe é criada usando `IntrospectionExtensions.GetTypeInfo(typeof(TranslateExtension)).Assembly)` para determinar o assembly atual ao carregar recursos e em cache em estático `ResMgr` campo. Ele é criado como um `Lazy` tipo de forma que sua criação é adiada até que ele é usado pela primeira vez no `ProvideValue` método.
* `ci` usa o serviço de dependência para obter a linguagem escolhida do usuário do sistema operacional nativo.
* `GetString` é o método que recupera a cadeia de caracteres traduzida real dos arquivos de recursos. Na plataforma Universal do Windows, `ci` será nulo porque o `ILocalize` interface não está implementada nessas plataformas. Isso é equivalente a chamar o `GetString` método com somente o primeiro parâmetro. Em vez disso, a estrutura de recursos automaticamente reconhecerá a localidade e recupera a cadeia de caracteres traduzida do arquivo RESX apropriado.
* Tratamento de erros foi incluído para ajudar a depurar recursos ausentes, lançando uma exceção (em `DEBUG` somente modo).

O trecho XAML a seguir mostra como usar a extensão de marcação. Há duas etapas para funcionar:

1. Declarar personalizado `xmlns:i18n` namespace no nó raiz. O `namespace` e `assembly` devem corresponder às configurações de projeto exatamente - neste exemplo, eles são idênticos mas podem ser diferentes em seu projeto.
2. Use `{Binding}` a sintaxe de atributos que normalmente seria contêm o texto para chamar o `Translate` extensão de marcação. A chave de recurso é o único parâmetro necessário.

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

A seguinte sintaxe mais detalhado também é válido para a extensão de marcação:

```xaml
<Button Text="{i18n:TranslateExtension Text=AddButton}" />
```

## <a name="localizing-platform-specific-elements"></a>Localizando elementos específicos de plataforma

Embora podemos manipular a tradução da interface do usuário no código do xamarin. Forms, há alguns elementos que devem ser executados em cada projeto específico da plataforma. Esta seção aborda como localizar:

* Nome do aplicativo
* Imagens

O projeto de exemplo inclui uma imagem localizada chamada **flag.png**, que é referenciado em c# como a seguir:

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

A imagem do sinalizador também é referenciada em XAML, assim:

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

Todas as plataformas serão resolvida automaticamente as referências de imagem como para versões localizadas de imagens, como as estruturas de projeto explicadas abaixo são implementadas.

### <a name="ios-application-project"></a>Projeto de Aplicativo iOS

iOS usa um padrão de nomeação de chamadas de projetos de localização ou **.lproj** diretórios contêm recursos de imagem e a cadeia de caracteres. Esses diretórios podem conter versões localizadas de imagens usadas no aplicativo e também o **InfoPlist.strings** arquivo que pode ser usado para localizar o nome do aplicativo.

#### <a name="images"></a>Imagens

Esta captura de tela mostra o aplicativo de exemplo do iOS com o idioma específico **.lproj** diretórios. O diretório espanhol chamado **es.lproj**, contém as versões localizadas da imagem padrão, bem como **flag.png**:

![](localization-images/ios-resources.png "Diretórios de projetos de localização do iOS")

Cada pasta de idioma contém uma cópia de **flag.png**localizado para esse idioma. Se nenhuma imagem for fornecida, o sistema operacional padrão será a imagem na pasta de idioma padrão. Para suporte de Retina completo, você deve fornecer **@2x** e **@3x** cópias de cada imagem.

#### <a name="app-name"></a>Nome do Aplicativo

O conteúdo do **InfoPlist.strings** é apenas um único chave-valor para configurar o nome do aplicativo:

```csharp
"CFBundleDisplayName" = "ResxEspañol";
```

Quando o aplicativo é executado, o nome do aplicativo e a imagem são ambos localizados:

![](localization-images/ios-imageicon.png "iOS texto do aplicativo de exemplo e a localização da imagem")

### <a name="android-application-project"></a>Projeto de Aplicativo Android

Android segue um esquema diferente para armazenar imagens localizadas usando diferentes **drawable** e **cadeias de caracteres** diretórios com um sufixo de código de idioma. Quando um código de localidade de quatro letras é necessário (por exemplo, zh-TW ou pt-BR), observe que o Android requer um adicional **r** seguintes dash/anterior a localidade do código (por exemplo. zh-rTW ou pt rBR).

#### <a name="images"></a>Imagens

Esta captura de tela mostra o Android localizadas de exemplo com um algumas drawables e cadeias de caracteres:

![](localization-images/android-resources.png "Android localizadas Drawables e diretórios de cadeia de caracteres")

Observe que o Android não usa zh-Hans e zh-Hant códigos simplificado e chinês tradicional; em vez disso, ele suporta apenas códigos específicos de país zh-CN e zh-TW.

Para dar suporte a imagens de resolução diferente para telas de alta densidade, criar pastas adicionais com `-*dpi` sufixos, tais como **drawables de es de mdpi**, **drawables de es de xdpi**, **drawables de es de xxdpi**, etc. Consulte [fornecendo recursos Android alternativa](http://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources) para obter mais informações.

#### <a name="app-name"></a>Nome do Aplicativo

O conteúdo do **strings.xml** é apenas um único chave-valor para configurar o nome do aplicativo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">ResxEspañol</string>
</resources>
```

Atualização de **MainActivity.cs** no projeto de aplicativo do Android para que o `Label` referencia as cadeias de caracteres XML.

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

Agora, o aplicativo localiza o nome do aplicativo e a imagem. Aqui está uma captura de tela do resultado (em espanhol):

![](localization-images/android-imageicon.png "Texto do aplicativo de exemplo do Android e a localização da imagem")

### <a name="universal-windows-platform-application-projects"></a>Projetos de aplicativo de plataforma universal do Windows

A plataforma Universal do Windows possui uma infraestrutura de recurso que simplifica a localização de imagens e o nome do aplicativo.

#### <a name="images"></a>Imagens

Imagens podem ser localizadas, colocando-os em uma pasta específica, conforme mostrado na seguinte captura de tela:

![](localization-images/uwp-image-folder-structure.png "Estrutura de pasta de localização de imagem UWP")

Em tempo de execução da infra-estrutura de recursos do Windows irá selecionar a imagem apropriada com base na localidade do usuário.

## <a name="summary"></a>Resumo

Aplicativos xamarin. Forms podem ser localizados usando arquivos RESX e classes de globalização do .NET. Além de uma pequena quantidade de código específico da plataforma para detectar o idioma preferido pelo usuário, a maior parte do esforço de localização é centralizado no código comum.

Imagens geralmente são tratadas de forma específica de plataforma para aproveitar o suporte de resolução múltipla fornecido no iOS e Android. 

## <a name="related-links"></a>Links relacionados

- [Exemplo de localização RESX](https://developer.xamarin.com/samples/xamarin-forms/UsingResxLocalization/)
- [Aplicativo de exemplo TodoLocalized](https://developer.xamarin.com/samples/xamarin-forms/TodoLocalized/)
- [Localização de plataforma cruzada](~/cross-platform/app-fundamentals/localization.md)
- [Localização do iOS](~/ios/app-fundamentals/localization/index.md)
- [Localização do Android](~/android/app-fundamentals/localization.md)
- [Usando a classe CultureInfo (MSDN)](http://msdn.microsoft.com/library/87k6sx8t%28v=vs.90%29.aspx)
- [Localizando e usando recursos para uma cultura específica (MSDN)](http://msdn.microsoft.com/library/s9ckwb4b%28v=vs.90%29.aspx)
