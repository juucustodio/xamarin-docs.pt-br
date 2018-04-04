---
title: Como posso instalar manualmente as bibliotecas de suporte do Android necessárias pelos pacotes Xamarin.Android.Support?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A9CB8CA8-8A6D-405E-B84C-A16CE452C0F7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: e760a87cbd1e0220ed5cf3a350d3539ffe29650e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>Como posso instalar manualmente as bibliotecas de suporte do Android necessárias pelos pacotes Xamarin.Android.Support?

## <a name="example-steps-for-xamarinandroidsupportv4"></a>Etapas de exemplo de Xamarin.Android.Support.v4 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Baixe o pacote do Xamarin.Android.Support NuGet desejado (por exemplo, instalá-lo com o NuGet package manager).

Use `ildasm` para verificar qual versão do **android_m2repository.zip** o pacote NuGet precisa:

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr SourceUrl
```
Saída de exemplo:

```cmd
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
```

Baixar **android\_m2repository.zip** do Google usando a URL retornada de **ildasm**. Como alternativa, você pode verificar qual versão do _repositório de suporte Android_ instalado atualmente no Gerenciador de SDK do Android:

!["Gerenciador de SDK do android mostrando o repositório de suporte Android versão 32 instalado"](install-android-support-library-images/sdk-extras.png)

Se a versão corresponde ao que é necessário para o pacote NuGet, você não precisa baixar nada de novo. Você pode em vez disso, compactar novamente existente **m2repository** que está localizado no diretório **extras\\android** no _caminho SDK_ (como mostra a parte superior do Android Janela do Gerenciador de SDK).

Calcular o hash MD5 do URL retornado de **ildasm**. Formato de cadeia de caracteres resultante para usar todas as letras maiusculas e sem espaços. Por exemplo, ajustar o `$url` variável como necessário e, em seguida, execute as seguintes 2 linhas (com base em [o código c# original do xamarin](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)) do PowerShell:

```powershell
$url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"
(([System.Security.Cryptography.MD5]::Create()).ComputeHash([System.Text.Encoding]::UTF8.GetBytes($url)) | %{ $_.ToString("X02") }) -join ""
```
Saída de exemplo:

```powershell
F16A3455987DBAE5783F058F19F7FCDF
```

Cópia **android\_m2repository.zip** para o **% LOCALAPPDATA %\\Xamarin\\fechar\\**  pasta. Renomeie o arquivo para usar o hash MD5 do hash MD5 anterior Calculando a etapa. Por exemplo:

**%LOCALAPPDATA%\\Xamarin\\zips\\F16A3455987DBAE5783F058F19F7FCDF.zip**

(Opcional) Descompacte o arquivo em **% LOCALAPPDATA %\\Xamarin\\Xamarin.Android.Support.v4\\23.4.0.0\\conteúdo\\**  (Criando um **conteúdo\\m2repository** subdiretório). Se você ignorar essa etapa, em seguida, a primeira compilação que usa a biblioteca levará um pouco mais porque ele será necessário concluir esta etapa.
O número de versão para o subdiretório (**23.4.0.0** neste exemplo) não é exatamente o mesmo que a versão do pacote NuGet. Você pode usar `ildasm` para localizar o número da versão correta:

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr /C:"string 'Version'"
```
Saída de exemplo:

```cmd
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
```

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Baixe o pacote do Xamarin.Android.Support NuGet desejado (por exemplo, instalá-lo com o NuGet package manager).

Clique duas vezes o _Xamarin.Android.Support.v4_ assembly sob o _referências_ seção do projeto Android no Visual Studio para Mac abrir o assembly no navegador do Assembly. Certifique-se de que o _idioma_ suspenso é definido como _c#_ e selecione o nível superior _Xamarin.Android.Support.v4_ assembly da árvore de navegação do navegador de Assembly. Localize o `SourceUrl` propriedade em uma a `IncludeAndroidResourcesFrom` ou `JavaLibraryReference` atributos:

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

Baixar **android\_m2repository.zip** do Google usando o `SourceUrl` retornado de **ildasm**. Como alternativa, você pode verificar qual versão do _repositório de suporte Android_ instalado atualmente no Gerenciador de SDK do Android:

!["Gerenciador de SDK do android mostrando o repositório de suporte Android versão 32 instalado"](install-android-support-library-images/sdk-extras.png)

Se a versão corresponde ao que é necessário para o pacote NuGet, você não precisa baixar nada de novo. Você pode em vez disso, compactar novamente existente **m2repository** que está localizado no diretório **extras/android** no _caminho SDK_ (como mostra a parte superior da janela do Gerenciador de SDK do Android) .

Calcular o hash MD5 do URL retornado de **ildasm**. Formato de cadeia de caracteres resultante para usar todas as letras maiusculas e sem espaços. Por exemplo, ajustar a cadeia de caracteres da URL conforme necessário e, em seguida, execute o seguinte comando um **Terminal.app** prompt de comando:

```bash
echo -n "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip" | md5 | tr '[:lower:]' '[:upper:]'
```

Outra opção é usar o `csharp` interpretador para executar [o código c# mesmo que usa xamarin próprio](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208).
Para fazer isso, ajustar o `url` variável conforme necessário e, em seguida, execute o seguinte comando um **Terminal.app** prompt de comando:

```bash
csharp -e 'var url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"; string.Concat((System.Security.Cryptography.MD5.Create().ComputeHash(System.Text.Encoding.UTF8.GetBytes(url))).Select(b => b.ToString("X02")))'
```
Saída de exemplo:

```bash
F16A3455987DBAE5783F058F19F7FCDF
```

Cópia **android\_m2repository.zip** para o **$HOME/.local/share/Xamarin/zips/** pasta. Renomeie o arquivo para usar o hash MD5 do hash MD5 anterior Calculando a etapa. Por exemplo:

**$HOME/.local/share/Xamarin/zips/F16A3455987DBAE5783F058F19F7FCDF.zip**

(Opcional) Descompacte o arquivo em: 

**$HOME/.local/share/Xamarin/Xamarin.Android.Support.v4/23.4.0.0/content/**

(Criando um **conteúdo/m2repository** subdiretório). Se você ignorar essa etapa, em seguida, a primeira compilação que usa a biblioteca levará um pouco mais porque ele será necessário concluir esta etapa.

O número de versão para o subdiretório (**23.4.0.0** neste exemplo) não é exatamente o mesmo que a versão do pacote NuGet. Como no **ildasm** etapa anterior, você pode usar o navegador de Assembly no Visual Studio para Mac para localizar o número da versão correta. Procure o `Version` propriedade em uma a `IncludeAndroidResourcesFrom` ou `JavaLibraryReference` atributos:

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

-----


## <a name="additional-references"></a>Referências adicionais

- [Bug 43245](https://bugzilla.xamarin.com/show_bug.cgi?id=43245) – impreciso "Falha no Download. Baixar {0} e colocá-lo para o diretório de \\{1 \\}." e "instale o pacote: '{0}' disponíveis no instalador do SDK" mensagens de erro relacionadas a pacotes de Xamarin.Android.Support

### <a name="next-steps"></a>Próximas etapas

Este documento aborda o comportamento atual a partir de agosto de 2016. A técnica descrita neste documento não é parte do conjunto de teste estável para Xamarin, ele pode quebrar no futuro.

Para obter mais assistência, entre em contato conosco ou se esse problema permanece mesmo após utilizando as informações acima, consulte [quais opções de suporte estão disponíveis para Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obter informações sobre opções de contato, sugestões, bem como a registrar um bug de novo, se necessário.

