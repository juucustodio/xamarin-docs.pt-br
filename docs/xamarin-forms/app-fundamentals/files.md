---
title: Manipulação de arquivos no Xamarin.Forms
description: O tratamento de arquivos com o Xamarin.Forms pode ser alcançado usando código em uma biblioteca .NET Standard ou usando recursos inseridos.
ms.prod: xamarin
ms.assetid: 9987C3F6-5F04-403B-BBB4-ECB024EA6CC8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: 93218289cefe26c523bf14721202f7ec4743f022
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53057607"
---
# <a name="file-handling-in-xamarinforms"></a>Manipulação de arquivos no Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/)

_O tratamento de arquivos com o Xamarin.Forms pode ser alcançado usando código em uma biblioteca .NET Standard ou usando recursos inseridos._

## <a name="overview"></a>Visão geral

Código do Xamarin.Forms são executados em várias plataformas e cada uma delas tem seu próprio sistema de arquivos. Anteriormente, isso significava que ler e gravar arquivos era realizado mais facilmente usando as APIs de arquivo nativo em cada plataforma. Como alternativa, os recursos inseridos são uma solução mais simples para distribuir os arquivos de dados com um aplicativo. No entanto, com o .NET Standard 2.0, é possível compartilhar o código de acesso de arquivo nas bibliotecas .NET Standard.

Para saber mais sobre o tratamento de arquivos de imagem, confira a página [Trabalhando com imagens](~/xamarin-forms/user-interface/images.md).

<a name="Loading_and_Saving_Files" />

## <a name="saving-and-loading-files"></a>Salvamento e carregamento de arquivos

As classes `System.IO` podem ser usadas para acessar o sistema de arquivos em cada plataforma. A classe `File` permite criar, excluir e ler arquivos e a classe `Directory` permite criar, excluir ou enumerar o conteúdo de diretórios. Também é possível usar as subclasses `Stream`, que podem fornecer um maior grau de controle sobre operações de arquivo (como compactação ou pesquisa de posição em um arquivo).

Um arquivo de texto pode ser escrito usando o método `File.WriteAllText`:

```csharp
File.WriteAllText(fileName, text);
```

Um arquivo de texto pode ser lido usando o método `File.ReadAllText`:

```csharp
string text = File.ReadAllText(fileName);
```

Além disso, o método `File.Exists` determina se o arquivo especificado existe:

```csharp
bool doesExist = File.Exists(fileName);
```

O caminho do arquivo em cada plataforma pode ser determinado com base em uma biblioteca .NET Standard usando um valor da enumeração [`Environment.SpecialFolder`](xref:System.Environment.SpecialFolder) como o primeiro argumento para o método `Environment.GetFolderPath`. Isso pode ser combinado com um nome de arquivo com o método `Path.Combine`:

```csharp
string fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "temp.txt");
```

Essas operações são demonstradas no aplicativo de exemplo, que inclui uma página que salva e carrega texto:

[![Salvamento e carregamento de texto](files-images/saveandload-sml.png "Salvamento e carregamento de texto no aplicativo")](files-images/saveandload.png#lightbox "Salvamento e carregamento de texto no aplicativo")

<a name="Loading_Files_Embedded_as_Resources" />

## <a name="loading-files-embedded-as-resources"></a>Carregamento de arquivos inseridos como recursos

Para inserir um arquivo em um assembly do **.NET Standard**, crie ou adicione um arquivo e certifique-se de que seja **Ação de build: EmbeddedResource**.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Configuração da ação de build de recurso inserido](files-images/vs-embeddedresource-sml.png "Configuração da ação de build de recurso inserido")](files-images/vs-embeddedresource.png#lightbox "Configuração da ação de build de recurso inserido")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Arquivo de texto inserido no PCL, configuração da ação de build de recurso inserido](files-images/xs-embeddedresource-sml.png "Configuração da ação de build de recurso inserido")](files-images/xs-embeddedresource.png#lightbox "Configuração da ação de build de recurso inserido")

-----

`GetManifestResourceStream` é usado para acessar o arquivo inserido usando sua **ID de recurso**. Por padrão, a ID de recurso é o nome do arquivo com o prefixo do namespace padrão para o projeto no qual ele está inserido – nesse caso, o assembly é **WorkingWithFiles** e o nome do arquivo é **PCLTextResource.txt**; portanto, a ID de recurso é `WorkingWithFiles.PCLTextResource.txt`.

```csharp
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(LoadResourceText)).Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.PCLTextResource.txt");
string text = "";
using (var reader = new System.IO.StreamReader (stream)) {
    text = reader.ReadToEnd ();
}
```

A variável `text` pode ser usada para exibir o texto ou, caso contrário, usá-lo no código. Esta captura de tela do [aplicativo de exemplo](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/) mostra o texto renderizado em um controle `Label`.

 [![Arquivo de texto inserido em PCL](files-images/pcltext-sml.png "Arquivo de texto inserido no PCL exibido no aplicativo")](files-images/pcltext.png#lightbox "Arquivo de texto inserido no PCL exibido no aplicativo")

Carregar e desserializar um XML é igualmente simples. O código a seguir mostra um arquivo XML sendo carregado e desserializado de um recurso, associado a um `ListView` para exibição. O arquivo XML contém uma matriz de objetos `Monkey` (a classe é definida no código de exemplo).

```csharp
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(LoadResourceText)).Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.PCLXmlResource.xml");
List<Monkey> monkeys;
using (var reader = new System.IO.StreamReader (stream)) {
    var serializer = new XmlSerializer(typeof(List<Monkey>));
    monkeys = (List<Monkey>)serializer.Deserialize(reader);
}
var listView = new ListView ();
listView.ItemsSource = monkeys;
```

 [![Arquivo XML inserido no PCL, exibido em ListView](files-images/pclxml-sml.png "Arquivo XML inserido no PCL, exibido em ListView")](files-images/pclxml.png#lightbox "Arquivo XML inserido no PCL, exibido em ListView")

<a name="Embedding_in_Shared_Projects" />

## <a name="embedding-in-shared-projects"></a>Inserção em projetos compartilhados

Projetos compartilhados também podem conter arquivos como recursos inseridos; no entanto, como o conteúdo de um Projeto compartilhado é compilado em projetos de referência, o prefixo usado para IDs de recurso de arquivo inserido pode ser alterado. Isso significa que a ID do recurso para cada arquivo inserido pode ser diferente para cada plataforma.

Há duas soluções para esse problema com Projetos compartilhados:

-  **Sincronizar os projetos** – edite as propriedades do projeto para cada plataforma para usar o **mesmo** nome de assembly e o namespace padrão. Esse valor pode ser embutido como o prefixo para IDs de recurso inserido no Projeto compartilhado.
-  **Diretivas de compilador #if** – use diretivas de compilador para definir o prefixo correto de ID do recurso e use esse valor para construir dinamicamente a ID do recurso correta.


Veja abaixo o código ilustrando a segunda opção. As diretivas de compilador são usadas para selecionar o prefixo de recurso embutido (normalmente o mesmo que o namespace padrão para o projeto de referência). A variável `resourcePrefix` é usada para criar uma ID de recurso válida, concatenando-a com o nome do arquivo de recurso inserido.

```csharp
#if __IOS__
var resourcePrefix = "WorkingWithFiles.iOS.";
#endif
#if __ANDROID__
var resourcePrefix = "WorkingWithFiles.Droid.";
#endif

Debug.WriteLine("Using this resource prefix: " + resourcePrefix);
// note that the prefix includes the trailing period '.' that is required
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(SharedPage)).Assembly;
Stream stream = assembly.GetManifestResourceStream
    (resourcePrefix + "SharedTextResource.txt");
```

<a name="Organizing_Resources" />

### <a name="organizing-resources"></a>Organização de recursos

Os exemplos acima supõem que o arquivo é inserido na raiz do projeto da biblioteca .NET Standard, em cujo caso a ID de recurso é do formato **Namespace.Filename.Extension**, como `WorkingWithFiles.PCLTextResource.txt` e `WorkingWithFiles.iOS.SharedTextResource.txt`.

É possível organizar os recursos inseridos em pastas. Quando um recurso inserido é colocado em uma pasta, o nome da pasta se torna parte da ID de recurso (separado por pontos) para que o formato de ID de recurso se torne **Namespace.Folder.Filename.Extension**. Colocar os arquivos usados no aplicativo de exemplo em uma pasta **MyFolder** tornaria as IDs de recurso correspondentes `WorkingWithFiles.MyFolder.PCLTextResource.txt` e `WorkingWithFiles.iOS.MyFolder.SharedTextResource.txt`.

<a name="Debugging_Embedded_Resources" />

### <a name="debugging-embedded-resources"></a>Depuração de recursos inseridos

Como às vezes é difícil entender por que determinado recurso não está sendo carregado, o código de depuração a seguir pode ser adicionado temporariamente a um aplicativo para ajudar a confirmar que os recursos foram configurados corretamente. Ele transmitirá todos os recursos conhecidos inseridos no assembly fornecido para o painel **Erros** para ajudar a depurar problemas de carregamento de recursos.

```csharp
using System.Reflection;
// ...
// use for debugging, not in released app code!
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(SharedPage)).Assembly;
foreach (var res in assembly.GetManifestResourceNames()) {
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

## <a name="summary"></a>Resumo

Este artigo mostrou algumas operações de arquivo simples para salvar e carregar texto no dispositivo e para carregar recursos inseridos. Com o .NET Standard 2.0, é possível compartilhar o código de acesso de arquivo nas bibliotecas .NET Standard.

## <a name="related-links"></a>Links relacionados

- [FilesSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/)
- [Amostras do Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
- [Trabalhando com o sistema de arquivos no Xamarin.iOS](~/ios/app-fundamentals/file-system.md)

