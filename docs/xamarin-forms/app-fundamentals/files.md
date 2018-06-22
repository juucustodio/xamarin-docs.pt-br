---
title: Manipulação de arquivos no xamarin. Forms
description: Manipulação com xamarin. Forms pode ser obtida usando o código em uma biblioteca .NET padrão, ou usando recursos inseridos.
ms.prod: xamarin
ms.assetid: 9987C3F6-5F04-403B-BBB4-ECB024EA6CC8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: 0be441a7be9777698212e690aca95fdd75e5050f
ms.sourcegitcommit: eac092f84b603958c761df305f015ff84e0fad44
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36310147"
---
# <a name="file-handling-in-xamarinforms"></a>Manipulação de arquivos no xamarin. Forms

_Manipulação com xamarin. Forms pode ser obtida usando o código em uma biblioteca .NET padrão, ou usando recursos inseridos._

## <a name="overview"></a>Visão geral

Código do Xamarin.Forms são executados em várias plataformas e cada uma delas tem seu próprio sistema de arquivos. Anteriormente, isso significa que, de leitura e gravação de arquivos foram mais facilmente executado usando o APIs de arquivo nativo em cada plataforma. Como alternativa, os recursos incorporados são uma solução mais simples para distribuir os arquivos de dados com um aplicativo. No entanto, com o .NET 2.0 padrão é possível compartilhar o código de acesso de arquivo em bibliotecas .NET padrão.

Para obter informações sobre como manipular arquivos de imagem, consulte o [trabalhando com imagens](~/xamarin-forms/user-interface/images.md) página.

<a name="Loading_and_Saving_Files" />

## <a name="saving-and-loading-files"></a>Salvando e carregando arquivos

O `System.IO` classes podem ser usadas para acessar o sistema de arquivos em cada plataforma. O `File` classe permite que você criar, excluir e ler arquivos e o `Directory` classe permite que você criar, excluir ou enumerar o conteúdo de pastas. Você também pode usar o `Stream` subclasses, que podem fornecer um nível maior de controle sobre as operações de arquivo (como pesquisa de compactação ou a posição dentro de um arquivo).

Um arquivo de texto pode ser escrito usando o `File.WriteAllText` método:

```csharp
File.WriteAllText(fileName, text);
```

Um arquivo de texto pode ser lidos usando o `File.ReadAllText` método:

```csharp
string text = File.ReadAllText(fileName);
```

Além disso, o `File.Exists` método determina se o arquivo especificado existe:

```csharp
bool doesExist = File.Exists(fileName);
```

O caminho do arquivo em cada plataforma pode ser determinado em uma biblioteca .NET padrão usando um valor de [ `Environment.SpecialFolder` ](xref:System.Environment.SpecialFolder) enumeração como o primeiro argumento para o `Environment.GetFolderPath` método. Isso pode ser combinado com um nome de arquivo com o `Path.Combine` método:

```csharp
string fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "temp.txt");
```

Essas operações são demonstradas no aplicativo de exemplo, o que inclui uma página que salva e carrega o texto:

[![Salvando e carregando texto](files-images/saveandload-sml.png "salvar e carregar arquivos no aplicativo")](files-images/saveandload.png#lightbox "salvar e carregar arquivos no aplicativo")

<a name="Loading_Files_Embedded_as_Resources" />

## <a name="loading-files-embedded-as-resources"></a>Carregando arquivos inseridos como recursos

Para inserir um arquivo em um **.NET padrão** assembly, criar ou adicionar um arquivo e certifique-se de que **ação de compilação: EmbeddedResource**.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Configurando incorporado a ação de compilação de recursos](files-images/vs-embeddedresource-sml.png "configuração EmbeddedResource BuildAction")](files-images/vs-embeddedresource.png#lightbox "configuração EmbeddedResource BuildAction")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Arquivo de texto inserido em PCL, configurando a ação de compilação de recurso inserido](files-images/xs-embeddedresource-sml.png "configuração EmbeddedResource BuildAction")](files-images/xs-embeddedresource.png#lightbox "configuração EmbeddedResource BuildAction")

-----

`GetManifestResourceStream` é usado para acessar o arquivo inserido usando seu **ID de recurso**. Por padrão, a ID de recurso é o nome do arquivo prefixado com o namespace padrão para o projeto que está inserido - nesse caso o assembly é **WorkingWithFiles** e o nome do arquivo é **PCLTextResource.txt**, Portanto, a ID de recurso é `WorkingWithFiles.PCLTextResource.txt`.

```csharp
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(LoadResourceText)).Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.PCLTextResource.txt");
string text = "";
using (var reader = new System.IO.StreamReader (stream)) {
    text = reader.ReadToEnd ();
}
```

O `text` variável pode ser usada para exibir o texto ou caso contrário, usá-lo no código. Esta captura de tela do [aplicativo de exemplo](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/) mostra o texto renderizado em um `Label` controle.

 [![Arquivo de texto inserido em PCL](files-images/pcltext-sml.png "arquivo de texto inserido no PCL exibido no aplicativo")](files-images/pcltext.png#lightbox "arquivo de texto inserido no PCL exibido no aplicativo")

Carregando e desserialização de XML são bem simple. O código a seguir mostra um arquivo XML está sendo carregado e desserializado a partir de um recurso, associado a um `ListView` para exibição. O arquivo XML contém uma matriz de `Monkey` objetos (a classe é definida no código de exemplo).

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

 [![Arquivo XML inseridos em PCL, exibido em ListView](files-images/pclxml-sml.png "arquivo XML inserido no PCL exibida em ListView")](files-images/pclxml.png#lightbox "arquivo XML inserido no PCL exibida em ListView")

<a name="Embedding_in_Shared_Projects" />

### <a name="embedding-in-shared-projects"></a>Inserindo em projetos compartilhados

Projetos compartilhados também podem conter arquivos como recursos incorporados, no entanto, como o conteúdo de um projeto compartilhado é compilado em projetos de referência, o prefixo usado para inserido podem alterar IDs de recurso de arquivo. Isso significa que a ID de recurso para cada arquivo inserido pode ser diferente para cada plataforma.

Há duas soluções para esse problema com projetos compartilhados:

-  **Sincronizar os projetos** -editar as propriedades de projeto para cada plataforma usar o **mesmo** espaço para nome padrão e de nome de assembly. Esse valor pode ser "fixo" como o prefixo de recurso inserido IDs no projeto compartilhado.
-  **diretivas de compilador #if** -usar diretivas de compilador para definir o prefixo de ID de recurso correto e usar esse valor para construir dinamicamente a ID do recurso correto.


Código que ilustra a segunda opção é mostrado abaixo. Diretivas de compilador são usadas para selecionar o prefixo de recurso codificado (que normalmente é o mesmo que o namespace padrão para o projeto de referência). O `resourcePrefix` variável é então usada para criar uma ID de recurso válido concatenando-lo com o nome do arquivo de recurso inserido.

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

Os exemplos anteriores supõem que o arquivo é incorporado na raiz do projeto de biblioteca de .NET padrão, caso em que a ID de recurso é o formato **NomeArquivo**, como `WorkingWithFiles.PCLTextResource.txt` e `WorkingWithFiles.iOS.SharedTextResource.txt`.

É possível organizar os recursos inseridos em pastas. Quando um recurso inserido é colocado em uma pasta, o nome da pasta se torna parte da ID de recurso (separado por pontos), para que o formato de ID de recurso se torne **Namespace.Folder.Filename.Extension**. Colocar os arquivos usados no aplicativo de exemplo em uma pasta **minhapasta** criam as IDs de recurso correspondente `WorkingWithFiles.MyFolder.PCLTextResource.txt` e `WorkingWithFiles.iOS.MyFolder.SharedTextResource.txt`.

<a name="Debugging_Embedded_Resources" />

### <a name="debugging-embedded-resources"></a>Depurando os recursos inseridos

Como algumas vezes é difícil de entender por que um determinado recurso não está sendo carregado, o seguinte código de depuração pode ser adicionado temporariamente a um aplicativo para confirmar que os recursos estão configurados corretamente. Ele mostrará conhecidos todos os recursos incorporados no assembly especificado para o **erros** teclado para ajudar a depurar problemas de carregamento de recursos.

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

Este artigo mostra algumas operações de arquivo simples para salvar e carregar o texto no dispositivo e para carregar os recursos inseridos. Com o .NET 2.0 padrão, é possível compartilhar o código de acesso de arquivo em bibliotecas .NET padrão.

## <a name="related-links"></a>Links relacionados

- [FilesSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/)
- [Amostras do Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
- [Trabalhando com o sistema de arquivos do xamarin](~/ios/app-fundamentals/file-system.md)
- [Arquivos de pasta de trabalho](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/files/files.workbook)
