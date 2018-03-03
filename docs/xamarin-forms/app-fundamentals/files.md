---
title: Arquivos
description: "Manipulação com xamarin. Forms pode ser feita usando recursos inseridos ou gravação em relação o APIs de sistema de arquivos nativo."
ms.topic: article
ms.prod: xamarin
ms.assetid: 9987C3F6-5F04-403B-BBB4-ECB024EA6CC8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/22/2017
ms.openlocfilehash: 605374c0f2bfe656e564e48d14ffe18ce5b7dfe5
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="files"></a>Arquivos

_Manipulação com xamarin. Forms pode ser feita usando recursos inseridos ou gravação em relação o APIs de sistema de arquivos nativo._

## <a name="overview"></a>Visão geral

Código do Xamarin.Forms são executados em várias plataformas e cada uma delas tem seu próprio sistema de arquivos. Isso significa que lendo e gravando arquivos mais facilmente feito usando o APIs de arquivo nativo em cada plataforma. Como alternativa, os recursos incorporados são uma solução mais simples para distribuir os arquivos de dados com um aplicativo.

Este documento aborda o seguinte arquivo comuns tratamento cenários:

-  [ **Arquivos inseridos como recursos** ](#Loading_Files_Embedded_as_Resources) -arquivos podem ser enviados como parte de um aplicativo e carregados usando a API de reflexão.
-  [ **Salvando e carregando arquivos** ](#Loading_and_Saving_Files) -usuário gravável armazenamento pode ser implementado de forma nativa e, em seguida, acessadas usando o `DependencyService` .


Uma chamada de componente de terceiros **PCLStorage** também pode ser usado para ler e gravar arquivos de usuário--armazenamento acessíveis do código PCL.

Para obter informações sobre como manipular arquivos de imagem, consulte o [trabalhando com imagens](~/xamarin-forms/user-interface/images.md) página.

<a name="Loading_Files_Embedded_as_Resources" />

## <a name="loading-files-embedded-as-resources"></a>Carregando arquivos inseridos como recursos

Para inserir um arquivo em um **PCL** assembly, criar ou adicionar um arquivo e certifique-se de que **ação de compilação: EmbeddedResource**.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[ ![Configurando incorporado a ação de compilação de recursos](files-images/vs-embeddedresource-sml.png "configuração EmbeddedResource BuildAction")](files-images/vs-embeddedresource.png "configuração EmbeddedResource BuildAction")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[ ![Arquivo de texto inserido em PCL, configurando a ação de compilação de recurso inserido](files-images/xs-embeddedresource-sml.png "configuração EmbeddedResource BuildAction")](files-images/xs-embeddedresource.png "configuração EmbeddedResource BuildAction")

-----

`GetManifestResourceStream` é usado para acessar o arquivo inserido usando seu **ID de recurso**. Por padrão, a ID de recurso é o nome do arquivo prefixado com o namespace padrão para o projeto que está inserido - nesse caso o assembly é **WorkingWithFiles** e o nome do arquivo é **PCLTextResource.txt**, Portanto, a ID de recurso é `WorkingWithFiles.PCLTextResource.txt`.

```csharp
var assembly = typeof(LoadResourceText).GetTypeInfo().Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.PCLTextResource.txt");
string text = "";
using (var reader = new System.IO.StreamReader (stream)) {
    text = reader.ReadToEnd ();
}
```

O `text` variável pode ser usada para exibir o texto ou caso contrário, usá-lo no código. Esta captura de tela do [aplicativo de exemplo](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/) mostra o texto renderizado em um `Label` controle.

 [ ![Arquivo de texto inserido em PCL](files-images/pcltext-sml.png "arquivo de texto inserido no PCL exibido no aplicativo")](files-images/pcltext.png "arquivo de texto inserido no PCL exibido no aplicativo")

Carregando e desserialização de XML são bem simple. O código a seguir mostra um arquivo XML está sendo carregado e desserializado a partir de um recurso, associado a um `ListView` para exibição. O arquivo XML contém uma matriz de `Monkey` objetos (a classe é definida no código de exemplo).

```csharp
var assembly = typeof(LoadResourceText).GetTypeInfo().Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.PCLXmlResource.xml");
List<Monkey> monkeys;
using (var reader = new System.IO.StreamReader (stream)) {
    var serializer = new XmlSerializer(typeof(List<Monkey>));
    monkeys = (List<Monkey>)serializer.Deserialize(reader);
}
var listView = new ListView ();
listView.ItemsSource = monkeys;
```

 [ ![Arquivo XML inseridos em PCL, exibido em ListView](files-images/pclxml-sml.png "arquivo XML inserido no PCL exibida em ListView")](files-images/pclxml.png "arquivo XML inserido no PCL exibida em ListView")

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
#if WINDOWS_PHONE
var resourcePrefix = "WorkingWithFiles.WinPhone.";
#endif

Debug.WriteLine("Using this resource prefix: " + resourcePrefix);
// note that the prefix includes the trailing period '.' that is required
var assembly = typeof(SharedPage).GetTypeInfo().Assembly;
Stream stream = assembly.GetManifestResourceStream
    (resourcePrefix + "SharedTextResource.txt");
```

<a name="Organizing_Resources" />

### <a name="organizing-resources"></a>Organização de recursos

Os exemplos anteriores supõem que o arquivo é incorporado na raiz do projeto PCL, caso em que a ID de recurso é o formato **NomeArquivo**, como `WorkingWithFiles.PCLTextResource.txt` e `WorkingWithFiles.iOS.SharedTextResource.txt`.

É possível organizar os recursos inseridos em pastas. Quando um recurso inserido é colocado em uma pasta, o nome da pasta se torna parte da ID de recurso (separado por pontos), para que o formato de ID de recurso se torne **Namespace.Folder.Filename.Extension**. Colocar os arquivos usados no aplicativo de exemplo em uma pasta **minhapasta** criam as IDs de recurso correspondente `WorkingWithFiles.MyFolder.PCLTextResource.txt` e `WorkingWithFiles.iOS.MyFolder.SharedTextResource.txt`.

<a name="Debugging_Embedded_Resources" />

### <a name="debugging-embedded-resources"></a>Depurando os recursos inseridos

Como algumas vezes é difícil de entender por que um determinado recurso não está sendo carregado, o seguinte código de depuração pode ser adicionado temporariamente a um aplicativo para confirmar que os recursos estão configurados corretamente. Ele mostrará conhecidos todos os recursos incorporados no assembly especificado para o **erros** teclado para ajudar a depurar problemas de carregamento de recursos.

```csharp
using System.Reflection;
// ...
// use for debugging, not in released app code!
var assembly = typeof(SharedPage).GetTypeInfo().Assembly;
foreach (var res in assembly.GetManifestResourceNames()) {
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

<a name="Loading_and_Saving_Files" />

## <a name="saving-and-loading-files"></a>Salvando e carregando arquivos

Como o xamarin. Forms é executado em várias plataformas, cada qual com seu próprio sistema de arquivos, não há nenhuma abordagem única para carregar e salvar arquivos criados pelo usuário. Para demonstrar como salvar e carregar arquivos de texto, que o aplicativo de exemplo inclui uma tela que salva e carrega alguma entrada do usuário - tela concluído é mostrado abaixo:

 [ ![Salvando e carregando texto](files-images/saveandload-sml.png "salvar e carregar arquivos no aplicativo")](files-images/saveandload.png "salvar e carregar arquivos no aplicativo")

Cada plataforma tem uma estrutura de diretório ligeiramente diferentes e recursos do sistema de arquivos diferente – por exemplo xamarin e xamarin dão suporte à maioria `System.IO` , mas a funcionalidade do Windows Phone só oferece suporte a `IsolatedStorage` e [ `Windows.Storage` ](http://msdn.microsoft.com/library/windowsphone/develop/jj681698(v=vs.105).aspx) APIs.

Para contornar esse problema, o aplicativo de exemplo define uma Interface na PCL xamarin. Forms para carregar e salvar arquivos. Ele fornece uma API simples para carregar e salvar arquivos de texto que será armazenada no dispositivo.

```csharp
public interface ISaveAndLoad {
    void SaveText (string filename, string text);
    string LoadText (string filename);
}
```

O PCL código, em seguida, usa o [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) para obter uma referência a uma implementação nativa da interface. Isso permite que o código portátil delegar o carregamento e salvamento de arquivos a serem gravados em cada um dos projetos específicos de plataforma de classe. No exemplo, o **salvar** e **carga** botões são gravados conforme mostrado:

```csharp
var saveButton = new Button {Text = "Save"};
saveButton.Clicked += (sender, e) => {
    DependencyService.Get<ISaveAndLoad>().SaveText("temp.txt", input.Text);
};
var loadButton = new Button {Text = "Load"};
loadButton.Clicked += (sender, e) => {
    output.Text = DependencyService.Get<ISaveAndLoad>().LoadText("temp.txt");
};
```

Uma implementação e precisa ser adicionado a cada um dos projetos específicos de plataforma antes de arquivos, na verdade, podem ser salvos e carregados.

### <a name="ios-and-android"></a>iOS e Android

A implementação da interface para projetos xamarin e xamarin pode ser idêntica. O código é mostrado abaixo, incluindo o `[assembly: Dependency (typeof (SaveAndLoad))]` atributo que é necessário para a `DependencyService` para trabalhar.

```csharp
[assembly: Dependency (typeof (SaveAndLoad))]
namespace WorkingWithFiles {
    public class SaveAndLoad : ISaveAndLoad {
        public void SaveText (string filename, string text) {
            var documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
            var filePath = Path.Combine (documentsPath, filename);
            System.IO.File.WriteAllText (filePath, text);
        }
        public string LoadText (string filename) {
            var documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
            var filePath = Path.Combine (documentsPath, filename);
            return System.IO.File.ReadAllText (filePath);
        }
    }
}
```

### <a name="universal-windows-platform-uwp-windows-81-and-windows-phone-81"></a>Plataforma universal do Windows (UWP), Windows 8.1 e Windows Phone 8.1

Essas plataformas têm um sistema de arquivos diferente API – [ `Windows.Storage` ](/windows/uwp/files/quickstart-reading-and-writing-files/) – que é usado para salvar e carregar arquivos.
O `ISaveAndLoad` interface pode ser implementada, conforme mostrado abaixo:

```csharp
using System;
using System.Threading.Tasks;
using Windows.Storage;
using WinApp;
using WorkingWithFiles;
using Xamarin.Forms;

[assembly: Dependency(typeof(SaveAndLoad_WinApp))]

namespace WindowsApp
{
    // https://msdn.microsoft.com/library/windows/apps/xaml/hh758325.aspx
    public class SaveAndLoad_WinApp : ISaveAndLoad
    {
        public async Task SaveTextAsync(string filename, string text)
        {
            StorageFolder localFolder = ApplicationData.Current.LocalFolder;
            StorageFile sampleFile = await localFolder.CreateFileAsync(filename, CreationCollisionOption.ReplaceExisting);
            await FileIO.WriteTextAsync(sampleFile, text);
        }
        public async Task<string> LoadTextAsync(string filename)
        {
            StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
            StorageFile sampleFile = await storageFolder.GetFileAsync(filename);
            string text = await Windows.Storage.FileIO.ReadTextAsync(sampleFile);
            return text;
        }
    }
}
```


<a name="Saving_and_Loading_in_Shared_Projects" />

### <a name="saving-and-loading-in-shared-projects"></a>Salvando e carregando em projetos compartilhados

Como as diretivas de compilador suporte a projetos compartilhados é possível incluir todo o código de plataforma específica em um arquivo de classe único dentro do projeto compartilhado (sem usar o `DependencyService`).
Um único `SaveAndLoad` classe poderia ser escrita que contém as duas implementações acima, compilado seletivamente os projetos de referência usando diretivas de compilador como `#if WINDOWS_PHONE`, `#if __IOS__`, e `#if __ANDROID__`.

## <a name="additional-information"></a>Informações adicionais

Com base em PCL xamarin. Forms projetos também podem tirar proveito do [PCLStorage NuGet](http://www.nuget.org/packages/pclstorage) ([código &amp; documentação](https://pclstorage.codeplex.com/)) para ajudar a implementar operações de arquivo de uma maneira de plataforma cruzada.


## <a name="summary"></a>Resumo

Este documento mostra algumas operações de arquivo simples para carregar os recursos incorporados e salvar e carregar texto no dispositivo. Os desenvolvedores podem implementar suas próprias APIs de arquivo nativo usando o `DependencyService`, tornando tão complexo quanto necessária para lidar com seus requisitos de manipulação do arquivo.


## <a name="related-links"></a>Links relacionados

- [FilesSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/)
- [Criar, gravar e ler um arquivo (UWP)](/windows/uwp/files/quickstart-reading-and-writing-files/)
- [Exemplos de xamarin. Forms](https://github.com/xamarin/xamarin-forms-samples)
- [Arquivos de pasta de trabalho](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/files/files.workbook)
