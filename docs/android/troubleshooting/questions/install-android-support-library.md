---
title: Como posso instalar manualmente as bibliotecas de suporte do Android exigidas pelos pacotes Support?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A9CB8CA8-8A6D-405E-B84C-A16CE452C0F7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 84ee33fe174c01656144e55bc3cbba7c773950fd
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120639"
---
# <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>Como posso instalar manualmente as bibliotecas de suporte do Android exigidas pelos pacotes Support?

## <a name="example-steps-for-xamarinandroidsupportv4"></a>Etapas de exemplo de Xamarin.Android.Support.v4 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Baixe o pacote do NuGet Support desejado (por exemplo, instalá-lo com o NuGet package manager).

Use `ildasm` para verificar qual versão do **android_m2repository.zip** os pacotes do NuGet precisam de:

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr SourceUrl
```
Saída de exemplo:

```cmd
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
```

Baixe **android\_m2repository.zip** do Google usando a URL retornada da **ildasm**. Como alternativa, você pode verificar qual versão dos _repositório de suporte do Android_ instalado atualmente no Gerenciador de SDK do Android:

!["Gerenciador de SDK do android mostrando o repositório de suporte do Android versão 32 instalado"](install-android-support-library-images/sdk-extras.png)

Se a versão corresponde em que você precisa para o pacote NuGet, em seguida, você não precisa baixar nada de novo. Você pode em vez disso, compactar novamente os existentes **m2repository** que está localizado no diretório **extras\\android** no _caminho do SDK_ (como mostra a parte superior do Android Janela do Gerenciador de SDK).

Calcular o hash MD5 da URL retornada de **ildasm**. Formate a cadeia de caracteres resultante para usar todas as letras maiusculas e sem espaços. Por exemplo, ajustar o `$url` variável conforme necessário e, em seguida, execute as seguintes linhas de 2 (com base no [original C# código do xamarin. Android](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)) no PowerShell:

```powershell
$url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"
(([System.Security.Cryptography.MD5]::Create()).ComputeHash([System.Text.Encoding]::UTF8.GetBytes($url)) | %{ $_.ToString("X02") }) -join ""
```
Saída de exemplo:

```powershell
F16A3455987DBAE5783F058F19F7FCDF
```

Cópia **android\_m2repository.zip** para o **% LOCALAPPDATA %\\Xamarin\\zips\\**  pasta. Renomeie o arquivo para usar o hash MD5 do hash MD5 anterior Calculando a etapa. Por exemplo:

**%LOCALAPPDATA%\\Xamarin\\zips\\F16A3455987DBAE5783F058F19F7FCDF.zip**

(Opcional) Descompacte o arquivo em **% LOCALAPPDATA %\\Xamarin\\Xamarin.Android.Support.v4\\23.4.0.0\\conteúdo\\**  (Criando um **conteúdo\\m2repository** subdiretório). Se você ignorar essa etapa, em seguida, a primeira compilação que usa a biblioteca será demorar um pouco mais porque ele será necessário concluir esta etapa.
O número de versão para o subdiretório (**23.4.0.0** neste exemplo) não é exatamente o mesmo que a versão do pacote NuGet. Você pode usar `ildasm` para localizar o número de versão correto:

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr /C:"string 'Version'"
```
Saída de exemplo:

```cmd
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
```

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Baixe o pacote do NuGet Support desejado (por exemplo, instalá-lo com o NuGet package manager).

Clique duas vezes o _Xamarin.Android.Support.v4_ assembly sob o _referências_ seção do projeto do Android no Visual Studio para Mac abrir o assembly no navegador de Assembly. Certifique-se de que o _linguagem_ suspensa é definida como _C#_ e selecione o nível superior _Xamarin.Android.Support.v4_ assembly da árvore de navegação do navegador de Assembly . Localize o `SourceUrl` propriedade em um dos `IncludeAndroidResourcesFrom` ou `JavaLibraryReference` atributos:

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

Baixe **android\_m2repository.zip** do Google usando o `SourceUrl` retornado do **ildasm**. Como alternativa, você pode verificar qual versão dos _repositório de suporte do Android_ instalado atualmente no Gerenciador de SDK do Android:

!["Gerenciador de SDK do android mostrando o repositório de suporte do Android versão 32 instalado"](install-android-support-library-images/sdk-extras.png)

Se a versão corresponde em que você precisa para o pacote NuGet, em seguida, você não precisa baixar nada de novo. Você pode em vez disso, compactar novamente os existentes **m2repository** que está localizado no diretório **extras/android** no _caminho do SDK_ (como mostra a parte superior da janela do Gerenciador de SDK do Android) .

Calcular o hash MD5 da URL retornada de **ildasm**. Formate a cadeia de caracteres resultante para usar todas as letras maiusculas e sem espaços. Por exemplo, ajustar a cadeia de caracteres de URL conforme necessário e, em seguida, execute o seguinte comando um **terminal** prompt de comando:

```bash
echo -n "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip" | md5 | tr '[:lower:]' '[:upper:]'
```

Outra opção é usar o `csharp` interpretador para ser executado [o mesmo C# código que usa xamarin. Android em si](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208).
Para fazer isso, ajustar o `url` variável conforme necessário e, em seguida, execute o seguinte comando um **terminal** prompt de comando:

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

(criar uma **conteúdo/m2repository** subdiretório). Se você ignorar essa etapa, em seguida, a primeira compilação que usa a biblioteca será demorar um pouco mais porque ele será necessário concluir esta etapa.

O número de versão para o subdiretório (**23.4.0.0** neste exemplo) não é exatamente o mesmo que a versão do pacote NuGet. Como mostra a **ildasm** etapa anteriormente, você pode usar o navegador de Assembly no Visual Studio para Mac para localizar o número da versão correta. Procure os `Version` propriedade em um dos `IncludeAndroidResourcesFrom` ou `JavaLibraryReference` atributos:

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

-----


## <a name="additional-references"></a>Referências adicionais

- [Bug 43245](https://bugzilla.xamarin.com/show_bug.cgi?id=43245) – impreciso "Falha no Download. Baixe {0} e coloque-o o {1} directory. " e "instale o pacote: '{0}' disponível no instalador do SDK" mensagens de erro relacionadas a pacotes Support

### <a name="next-steps"></a>Próximas etapas

Este documento aborda o comportamento atual a partir de agosto de 2016. A técnica descrita neste documento não é parte do conjunto de teste estável para Xamarin, ele pode ser desfeito no futuro.

Para obter mais assistência, entre em contato conosco ou se esse problema permanece mesmo após utilizando as informações acima, consulte [quais opções de suporte estão disponíveis para o Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obter informações sobre opções de contato, sugestões, e também como um novo bug de arquivo se necessário.

