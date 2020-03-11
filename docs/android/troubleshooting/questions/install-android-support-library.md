---
title: Como posso instalar manualmente as bibliotecas de suporte do Android exigidas pelos pacotes Xamarin.Android.Support?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A9CB8CA8-8A6D-405E-B84C-A16CE452C0F7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 99571e0b62592597bb1fffdc8d3ed8336fe050b2
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73026936"
---
# <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>Como posso instalar manualmente as bibliotecas de suporte do Android exigidas pelos pacotes Xamarin.Android.Support?

## <a name="example-steps-for-xamarinandroidsupportv4"></a>Etapas de exemplo para Xamarin. Android. support. v4 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Baixe o pacote NuGet do Xamarin. Android. support desejado (por exemplo, instalando-o com o Gerenciador de pacotes NuGet).

Use `ildasm` para verificar qual versão do **android_m2repository. zip** o pacote NuGet precisa:

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr SourceUrl
```

Saída de exemplo:

```cmd
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
```

Baixe o **android\_m2repository. zip** do Google usando a URL retornada de **ILDASM**. Como alternativa, você pode verificar qual versão do _repositório de suporte do Android_ está instalada no momento no gerenciador de SDK do Android:

!["SDK do Android Manager mostrando o repositório de suporte do Android versão 32 instalado"](install-android-support-library-images/sdk-extras.png)

Se a versão corresponder à que você precisa para o pacote NuGet, você não precisará baixar nada de novo. Em vez disso, você pode recompactar o diretório **m2repository** existente que está localizado em **extras\\Android** no _caminho do SDK_ (como mostrado na parte superior da janela do SDK do Android Manager).

Calcule o hash MD5 da URL retornada de **ILDASM**. Formate a cadeia de caracteres resultante para usar todas as letras maiúsculas e nenhum espaço. Por exemplo, ajuste a variável `$url` conforme necessário e, em seguida, execute as duas linhas a seguir (com base no [código original C# do Xamarin. Android](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)) no PowerShell:

```powershell
$url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"
(([System.Security.Cryptography.MD5]::Create()).ComputeHash([System.Text.Encoding]::UTF8.GetBytes($url)) | %{ $_.ToString("X02") }) -join ""
```

Saída de exemplo:

```powershell
F16A3455987DBAE5783F058F19F7FCDF
```

Copie o **android\_m2repository. zip** na pasta **% LocalAppData%\\Xamarin\\zips\\** . Renomeie o arquivo para usar o hash MD5 da etapa de cálculo de hash MD5 anterior. Por exemplo:

**% LOCALAPPDATA%\\Xamarin\\zips\\F16A3455987DBAE5783F058F19F7FCDF. zip**

Adicional Descompacte o arquivo em **% LocalAppData%\\xamarin\\xamarin. Android. support. v4\\23.4.0.0\\content\\** (criando um **conteúdo\\** subdiretório m2repository). Se você ignorar essa etapa, a primeira compilação que usa a biblioteca levará um pouco mais de tempo porque será necessário concluir esta etapa.
O número de versão do subdiretório (**23.4.0.0** neste exemplo) não é exatamente o mesmo que a versão do pacote NuGet. Você pode usar `ildasm` para localizar o número de versão correto:

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr /C:"string 'Version'"
```

Saída de exemplo:

```cmd
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Baixe o pacote NuGet do Xamarin. Android. support desejado (por exemplo, instalando-o com o Gerenciador de pacotes NuGet).

Clique duas vezes no assembly _Xamarin. Android. support. v4_ na seção _referências_ do projeto do Android no Visual Studio para Mac para abrir o assembly no navegador de assembly. Verifique se a lista suspensa _idioma_ está definida como _C#_ e selecione o assembly _Xamarin. Android. support. v4_ de nível superior na árvore de navegação do navegador de assembly. Localize a propriedade `SourceUrl` em um dos atributos `IncludeAndroidResourcesFrom` ou `JavaLibraryReference`:

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

Baixe o **android\_m2repository. zip** do Google usando o `SourceUrl` retornado de **ILDASM**. Como alternativa, você pode verificar qual versão do _repositório de suporte do Android_ está instalada no momento no gerenciador de SDK do Android:

!["SDK do Android Manager mostrando o repositório de suporte do Android versão 32 instalado"](install-android-support-library-images/sdk-extras.png)

Se a versão corresponder à que você precisa para o pacote NuGet, você não precisará baixar nada de novo. Em vez disso, você pode recompactar o diretório **m2repository** existente que está localizado em **extras/Android** no _caminho do SDK_ (como mostrado na parte superior da janela do Gerenciador de SDK do Android).

Calcule o hash MD5 da URL retornada de **ILDASM**. Formate a cadeia de caracteres resultante para usar todas as letras maiúsculas e nenhum espaço. Por exemplo, ajuste a cadeia de caracteres da URL conforme necessário e, em seguida, execute o seguinte comando em um prompt de comando do **terminal. app** :

```bash
echo -n "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip" | md5 | tr '[:lower:]' '[:upper:]'
```

Outra opção é usar o interpretador de `csharp` para executar [o C# mesmo código que o Xamarin. Android usa](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208).
Para fazer isso, ajuste a variável `url` conforme necessário e, em seguida, execute o seguinte comando em um prompt de comando **terminal. app** :

```bash
csharp -e 'var url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"; string.Concat((System.Security.Cryptography.MD5.Create().ComputeHash(System.Text.Encoding.UTF8.GetBytes(url))).Select(b => b.ToString("X02")))'
```

Saída de exemplo:

```bash
F16A3455987DBAE5783F058F19F7FCDF
```

Copie o **android\_m2repository. zip** para a pasta **$Home/.local/share/xamarin/zips/** . Renomeie o arquivo para usar o hash MD5 da etapa de cálculo de hash MD5 anterior. Por exemplo:

**$HOME/.local/share/Xamarin/zips/F16A3455987DBAE5783F058F19F7FCDF.zip**

Adicional Descompacte o arquivo em: 

**$HOME/.local/share/Xamarin/Xamarin.Android.Support.v4/23.4.0.0/content/**

(criando um subdiretório **Content/m2repository** ). Se você ignorar essa etapa, a primeira compilação que usa a biblioteca levará um pouco mais de tempo porque será necessário concluir esta etapa.

O número de versão do subdiretório (**23.4.0.0** neste exemplo) não é exatamente o mesmo que a versão do pacote NuGet. Como na etapa de **ILDASM** anterior, você pode usar o navegador de Assembly em Visual Studio para Mac para localizar o número de versão correto. Procure a propriedade `Version` em um dos atributos `IncludeAndroidResourcesFrom` ou `JavaLibraryReference`:

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

-----

## <a name="additional-references"></a>Referências adicionais

- [Bug 43245](https://bugzilla.xamarin.com/show_bug.cgi?id=43245) – inexato "falha no download. Baixe {0} e coloque-o no diretório {1} ". e "Instale o pacote: '{0}' disponível no instalador do SDK" mensagens de erro relacionadas aos pacotes Xamarin. Android. support

### <a name="next-steps"></a>Próximas etapas

Este documento discute o comportamento atual a partir de agosto de 2016. A técnica descrita neste documento não faz parte do pacote de testes estável para o Xamarin, portanto, ele pode ser interrompido no futuro.

Para obter mais assistência, entrar em contato conosco ou, se esse problema permanecer mesmo depois de utilizar as informações acima, consulte [quais opções de suporte estão disponíveis para o Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obter informações sobre opções de contato, sugestões, e como arquivar um novo bug, se necessário.
