---
title: Título deste artigo
description: Uma boa e sólida descrição deste artigo. O conteúdo mantido aqui é o que o sistema do Microsoft Docs retorna com os resultados da pesquisa, portanto, deve ser uma descrição completa da finalidade e do conteúdo deste arquivo de documento
keywords: palavra-chave, lista, para, interno, pesquisa
author: conceptdev
ms.author: crdun
ms.date: 02/26/2018
ms.topic: conceptual
ms.assetid: 11111111-2222-3333-4444-555555555555
ms.prod: xamarin
ms.openlocfilehash: eac7d316b65b57231d2fc25ef6b859293650d3f8
ms.sourcegitcommit: 6be6374664cd96a7d924c2e0c37aeec4adf8be13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2018
ms.locfileid: "53709569"
---
# <a name="metadata-and-markdown-template"></a>Metadados e modelo Markdown

Esse modelo contém exemplos da sintaxe Markdown, bem como diretrizes de definição dos metadados. Para aproveitá-la ao máximo, é necessário exibir o **Markdown bruto** e a **exibição renderizada** (por exemplo, o Markdown bruto mostra o bloco de metadados, ao contrário da exibição renderizada).

Ao criar um arquivo Markdown, é necessário copiar esse modelo para um novo arquivo, preencher os metadados conforme especificado abaixo, definir o cabeçalho H1 acima do título do artigo e excluir o conteúdo.

## <a name="metadata"></a>Metadados

O bloco de metadados completo, com muitas anotações, é mostrado abaixo:

```
---
title: This Article's Title
description: A good, solid description of this article. The content kept here is what the Microsoft Docs system returns with search results, so this had better be a complete description of the purpose and content for this document file
keywords: a, comma, separated, list, of keywords, to, use, for, this, doc, however, this, might, not, be, used, for, anything, much, anymore, but, still, good, to, have, if, you, want
author: conceptdev (your Github user name)
ms.author: crdun (your Microsoft Alias)
ms.date: 02/26/2018 (creation date, in US format: mm/dd/yyyy)
ms.topic: conceptual (one only of the following: conceptual, quickstart, tutorial, overview)
ms.assetid: b39854a6-c523-4a66-bef6-9b5da03bafff (a unique number representing the asset - just use a GUID, you can generate one at https://www.guidgenerator.com/)
ms.prod: xamarin (no reason to change this)
ms.custom: Analytics data, a field that gets imported into SkyEye so you can use it in custom reports
---
```

Algumas observações importantes:

- **Deve** haver um espaço entre os dois-pontos (:) e o valor de um elemento de metadados.
- Se um elemento de metadados opcional não tiver um valor, exclua-o (não deixe em branco ou use "na").
- Dois-pontos em um valor (por exemplo, um título) quebram o analisador de metadados. Nesse caso, coloque o título entre aspas duplas (por exemplo, `title: "Writing .NET Core console apps: An advanced step-by-step guide"`).
- **title**: Esse título aparecerá nos resultados do mecanismo de pesquisa. O título não precisa ser idêntico ao título do cabeçalho H1 e deve conter 60 caracteres ou menos.
- **author**, **manager**, **ms.reviewer**: O campo author deve conter o **nome de usuário do GitHub** do autor, não seu alias.  Por outro lado, os campos “manager” e “ms.reviewer” devem conter aliases da Microsoft. ms.reviewer especifica o nome do PM/desenvolvedor associado ao artigo ou ao recurso.
- **ms.assetid**: Esse é o GUID do artigo usado para fins de acompanhamento interno, como BI (Business Intelligence). Ao criar um arquivo markdown, obtenha um GUID em [https://www.guidgenerator.com](https://www.guidgenerator.com).

## <a name="basic-markdown-gfm-and-special-characters"></a>Marcação básica, GFM e caracteres especiais

Há suporte para todo o GFM (GitHub Flavored Markdown) e básico. Para obter mais informações sobre eles, consulte:

- [Sintaxe Markdown de linha de base](https://daringfireball.net/projects/markdown/syntax)
- [DocFX Flavored Markdown](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html)
- [Documentação de GFM](https://guides.github.com/features/mastering-markdown)

O Markdown usa caracteres especiais, como \*, \` e \# para formatação. Se desejar incluir um desses caracteres no conteúdo, realize uma destas ações:

- Coloque uma barra invertida antes do caractere especial para inserir “escape” nele (por exemplo, `\*` para um \*)
- Use o [código de entidade HTML](http://www.ascii.cl/htmlcodes.htm) do caractere (por exemplo, `&#42;` para um &#42;).

## <a name="file-name"></a>Nome do arquivo

Os nomes de arquivo usam as seguintes regras:
* Conter apenas letras minúsculas, números e hifens.
* Sem espaços nem caracteres de pontuação. Use os hifens para separar palavras e números no nome do arquivo.
* Use verbos de ação específicos, como desenvolver, comprar, compilar, solucionar problemas. Sem palavras no gerúndio.
* Sem palavras pequenas – não inclua artigos nem preposições.
* Deve estar em Markdown e usar a extensão de arquivo .md.
* Mantenha os nomes de arquivo razoavelmente curtos. Eles fazem parte da URL dos artigos.



## <a name="headings"></a>Títulos

Use a capitalização de estilo da frase. Sempre coloque em maiúsculas:
- A primeira palavra de um cabeçalho.
- A palavra após dois pontos em um título ou cabeçalho (por exemplo, "Como classificar uma matriz").

Os cabeçalhos devem ser feitos usando o atx-style ou seja, use de 1 a 6 caracteres de hash (#) no início da linha para indicar um cabeçalho correspondente aos níveis de cabeçalho HTML de H1 a H6. Exemplos de cabeçalhos de primeiro e segundo nível são usados acima.

**Deve** haver apenas um cabeçalho de primeiro nível (H1) no tópico, que será exibido como o título na página.

Se o cabeçalho terminar com um `#` caractere, você precisará adicionar um caractere `#` extra ao final para que o título seja renderizado corretamente. Por exemplo, `# Async Programming in F# #`.

Os cabeçalhos de segundo nível gerarão o Sumário na página exibida na seção “Neste artigo” sob o título na página.

### <a name="third-level-heading"></a>Cabeçalho de terceiro nível
#### <a name="fourth-level-heading"></a>Cabeçalho de quarto nível
##### <a name="fifth-level-heading"></a>Cabeçalho de quinto nível
###### <a name="sixth-level-heading"></a>Cabeçalho de sexto nível

## <a name="text-styling"></a>Estilo do texto

*Itálico* Use para arquivos, pastas, caminhos (para itens longos, divididos em sua própria linha) – novos termos – URLs (a menos que sejam renderizadas como links, que é o padrão).

**Negrito** Use para elementos de interface do usuário.

## <a name="links"></a>Links

### <a name="internal-links"></a>Links internos

Para vincular a um cabeçalho no mesmo arquivo Markdown (também conhecido como links do tipo âncora), você precisará descobrir a ID do cabeçalho à qual está tentando vincular. Para confirmar a ID, exiba a origem do artigo renderizado, localize a ID do cabeçalho (por exemplo, `id="blockquote"`) e vincule-a usando # + ID (por exemplo, `#blockquote`).
A ID é gerada automaticamente com base no texto do cabeçalho. Assim, por exemplo, considerando uma seção exclusiva chamada `## Step 2`, a ID terá a aparência deste `id="step-2"`.

- Exemplo: `[Chapter 1](#chapter-1)`

Para vincular a um arquivo Markdown no mesmo repositório, use [links relativos](https://www.w3.org/TR/WD-html40-970917/htmlweb.html#h-5.1.2), incluindo o “.md” ao final do nome de arquivo.

- Exemplo: `[Readme file](../readme.md)`
- Exemplo: `[Welcome to .NET](../docs/welcome.md)`

Para vincular a um cabeçalho em um arquivo Markdown no mesmo repositório, use a vinculação relativa + vinculação de hashtag.

- Exemplo: `[.NET Community](../docs/welcome.md#community)`

### <a name="external-links"></a>Links externos

Para vincular a um arquivo externo, use a URL completa como o link.

- Exemplo: `[GitHub](http://www.github.com)`

Se uma URL for exibida em um arquivo Markdown, ela será transformada em um link clicável.

- Exemplo: `http://www.github.com`

### <a name="links-to-apis"></a>Links para APIs

O sistema de build tem algumas extensões que permitem vincular a APIs do .NET sem a necessidade de usar links externos.
Ao vincular a uma API, é possível usar seu UID (identificador exclusivo) que é gerado automaticamente com base no código-fonte.

É possível usar uma das seguintes sintaxes:
1. Link de markdown: `[link_text](xref:UID)`
2. Link automático: `<xref:UID>`
3. Forma abreviada: `@UID`

- Exemplo: `@System.String`
- Exemplo: `[String class](xref:System.String)`

Para obter mais informações sobre como usar essa notação, consulte [Using cross reference](https://dotnet.github.io/docfx/tutorial/links_and_cross_references.html#using-cross-reference) (Usando a referência cruzada).

> No momento, não há uma maneira fácil de encontrar os UIDs. A melhor maneira de encontrar o UID de uma API é pesquisá-lo neste repositório: [docascode/coreapi](https://github.com/docascode/coreapi). Estamos trabalhando para ter um sistema melhor no futuro.

Quando o UID contém os caracteres especiais \` ou \#, o valor do UID precisa ser codificado em HTML como %60 e %23, respectivamente, como nos seguintes exemplos:
- Exemplo: @System.Threading.Tasks.Task\`1 se torna `@System.Threading.Tasks.Task%601`
- Exemplo: @System.Exception.\#ctor torna-se `@System.Exception.%23ctor`

## <a name="lists"></a>Listas

### <a name="ordered-lists"></a>Listas ordenadas

1. Este
1. É
1. Uma
1. Ordenada
1. Lista


#### <a name="ordered-list-with-an-embedded-list"></a>Lista ordenada com uma lista inserida

1. Esta
1. é
1. uma
1. lista
    1. Senhorita Costa
    1. Professor Mendes
1. ordered
1. lista


### <a name="unordered-lists"></a>Listas não ordenadas

- Este
- is
- a
- com marcadores
- lista


##### <a name="unordered-list-with-an-embedded-list"></a>Lista não ordenada com uma lista inserida

- Este
- com marcadores
- lista
    - Sra. Pereira
    - Sr. Martins
- contém
- outras
    1. Coronel Teixeira
    1. Sra. Barbosa
- listas


## <a name="horizontal-rule"></a>Régua horizontal

---

## <a name="tables"></a>Tabelas

| Tabelas        | São           | Legais  |
| ------------- |:-------------:| -----:|
| A coluna 3 está      | alinhada à direita | US$ 1.600 |
| A coluna 2 está      | centralizada      |   US$ 12 |
| A coluna 1 está por padrão | alinhada à esquerda     |    US$ 1 |

É possível usar uma [ferramenta de gerador de tabelas Markdown](http://www.tablesgenerator.com/markdown_tables) para ajudar a criá-las com mais facilidade.


### <a name="inline-code-blocks-with-language-identifier"></a>Blocos de código embutidos com identificador de linguagem

Use três backticks (\`\`\`) + uma ID de idioma para aplicar a codificação de cores específico a um idioma para um bloco de código. Esta é a lista inteira de [IDs de linguagem de GFM](https://github.com/jmm/gfm-lang-ids/wiki/GitHub-Flavored-Markdown-(GFM)-language-IDs).

##### <a name="c9839"></a>C&#9839;

```c#
using System;
namespace HelloWorld
{
    class Hello
    {
        static void Main()
        {
            Console.WriteLine("Hello World!");

            // Keep the console window open in debug mode.
            Console.WriteLine("Press any key to exit.");
            Console.ReadKey();
        }
    }
}
```

#### <a name="xml"></a>xml

```xml
<dict>
    <key>CFBundleURLSchemes</key>
    <array>
        <string>evolveCountdown</string>
    </array>
```

#### <a name="xaml"></a>XAML

```xaml
<Label Text="Hello, XAML!"
        FontSize="Large"
        FontAttributes="Bold"
        TextColor="Blue" />
```

#### <a name="powershell"></a>PowerShell

```powershell
Clear-Host
$Directory = "C:\Windows\"
$Files = Get-Childitem $Directory -recurse -Include *.log `
-ErrorAction SilentlyContinue
```

### <a name="generic-code-block"></a>Bloco de código genérico

Use três backticks (&#96;&#96;&#96;) para a codificação de bloco de código genérico.

> A abordagem recomendada é usar blocos de código com identificadores de linguagem, conforme explicado na seção anterior, para garantir o realce de sintaxe apropriado no site da documentação. Use blocos de código genérico somente quando necessário.

```
function fancyAlert(arg) {
    if(arg) {
        $.docs({div:'#foo'})
    }
}
```

### <a name="inline-code"></a>Código embutido

Use backticks (&#96;) para `inline code`. Use código embutido para comandos da linha de comando, nomes de coluna e tabela de banco de dados e palavras-chave de linguagem.

## <a name="blockquotes"></a>Blockquotes

> A seca agora se estende para dez milhões de anos e o reino dos terríveis lagartos desde então foi extinto. Aqui no Equador, no continente em que um dia seria conhecido como África, a luta pela existência atingiu um novo auge de ferocidade e o vencedor ainda não foi avistado. Nesta terra árida e desidratada, apenas as espécies pequenas, ágeis ou ferozes poderiam prosperar ou mesmo ter a esperança de sobreviver.

## <a name="images"></a>Imagens

### <a name="static-image-or-animated-gif"></a>Imagem estática ou GIF animado

```
![this is the alt text](images/dotnet.png)
```

![Design responsivo](images/responsivedesign.gif)

### <a name="linked-image"></a>Imagem vinculada

```
[![alt text for linked image](images/dotnet.png)](https://dot.net)
```

## <a name="videos"></a>Vídeos

### <a name="channel-9"></a>Channel 9

Use esta sintaxe de blockquote especial:

```
> [!Video https://channel9.msdn.com/Shows/On-NET/Shipping-NET-Core-RC2--Tools-Preview-1/player]
```

A URL do vídeo do Channel 9 deve começar com `https` e terminar com `/player`, caso contrário, será inserida a página inteira em vez de apenas o vídeo.

### <a name="youtube"></a>YouTube

Use esta sintaxe de blockquote especial:

```
> [!Video https://youtube.com/embed/wXgnh2Q7Uv8]
```

O vídeo do YouTube deve conter `/embed/` (isto é, siga o formato acima), caso contrário, ele tentará renderizar a página inteira e poderá não funcionar.

## <a name="docsmicrosoft-extensions"></a>Extensões docs.microsoft

O docs.microsoft fornece algumas extensões adicionais para o GitHub Flavored Markdown.

### <a name="alerts"></a>Alertas

É importante usar os estilos de alerta a seguir para que eles sejam renderizados com o estilo adequado no site da documentação. No entanto, o mecanismo de renderização no GitHub não os diferencia.

#### <a name="note"></a>Observação

```
> [!NOTE]
> This is a NOTE
```

#### <a name="warning"></a>Aviso

```
> [!WARNING]
> This is a WARNING
```

#### <a name="tip"></a>Dica

```
> [!TIP]
> This is a TIP
```

#### <a name="important"></a>Importante

```
> [!IMPORTANT]
> This is IMPORTANT
```

Eles serão renderizados assim:

![Estilos de alerta](images/alerts.png)

### <a name="buttons"></a>Botões

```
> [!div class="button"]
[button links](../docs/core/index.md)
```

É possível ver um exemplo de botões em ação no [Intune Docs](https://docs.microsoft.com/intune/get-started/choose-how-to-enroll-devices).

### <a name="selectors"></a>Seletores

```
> [!div class="op_single_selector"]
- [macOS](../docs/core/tutorials/using-on-macos.md)
- [Windows](../docs/core/tutorials/using-on-windows.md)
```

É possível ver um exemplo de seletores em ação no [Intune Docs](https://docs.microsoft.com/intune/deploy-use/what-to-tell-your-end-users-about-using-microsoft-intune#how-your-end-users-get-their-apps).

### <a name="step-by-steps"></a>Passo a passo

```
> [!div class="step-by-step"]
[Pre](../docs/csharp/expression-trees-interpreting.md)
[Next](../docs/csharp/expression-trees-translating.md)
```

É possível ver um exemplo de passo a passo em ação no [Advanced Threat Analytics Docs](https://docs.microsoft.com/advanced-threat-analytics/deploy-use/install-ata-step2).
