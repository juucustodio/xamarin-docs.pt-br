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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73026936"
---
# <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>Como posso instalar manualmente as bibliotecas de suporte do Android exigidas pelos pacotes Xamarin.Android.Support?

## <a name="example-steps-for-xamarinandroidsupportv4"></a>Exemplos de passos para Xamarin.Android.Support.v4 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Baixe o pacote Xamarin.Android.Support NuGet desejado (por exemplo, instalando-o com o gerenciador de pacotes NuGet).

Use `ildasm` para verificar qual versão do **android_m2repository.zip** que o pacote NuGet precisa:

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr SourceUrl
```

Saída de exemplo:

```cmd
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
```

Baixe **\_o android m2repository.zip** do Google usando a URL retornada do **ildasm**. Alternativamente, você pode verificar qual versão do _Repositório_ de Suporte ao Android você atualmente instalou no Android SDK Manager:

!["Android SDK Manager mostrando o Repositório de Suporte ao Android versão 32 instalado"](install-android-support-library-images/sdk-extras.png)

Se a versão corresponde à que você precisa para o pacote NuGet, então você não precisa baixar nada de novo. Em vez disso, você pode re-zip o diretório **m2repository** existente que está localizado sob **\\extras android** no _SDK Path_ (como mostrado o topo da janela Android SDK Manager).

Calcular o hash MD5 da URL retornada do **ildasm**. Formatar a seqüência resultante para usar todas as letras maiúsculas e sem espaços. Por exemplo, `$url` ajuste a variável conforme necessário e execute as seguintes 2 linhas (com base [no código C# original do Xamarin.Android)](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)no PowerShell:

```powershell
$url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"
(([System.Security.Cryptography.MD5]::Create()).ComputeHash([System.Text.Encoding]::UTF8.GetBytes($url)) | %{ $_.ToString("X02") }) -join ""
```

Saída de exemplo:

```powershell
F16A3455987DBAE5783F058F19F7FCDF
```

Copie **\_o android m2repository.zip** na pasta **de zips\\\\\\ %LOCALAPPDATA% Xamarin.** Renomeie o arquivo para usar o hash MD5 da etapa de cálculo de hash md5 anterior. Por exemplo:

**%LOCALAPPDATA%\\Xamarin\\\\zips F16A3455987DBAE5783F058F19F7FCDF.zip**

(Opcional) Descompacte o arquivo em **%LOCALAPPDATA%\\Xamarin\\Xamarin.Android.Support.v4\\\\ \\23.4.0.0 conteúdo** (criando um subdiretório **m2repository de conteúdo).\\** Se você pular esse passo, então a primeira compilação que usa a biblioteca levará um pouco mais de tempo porque ela precisará completar essa etapa.
O número da versão para o subdiretório **(23.4.0.0** neste exemplo) não é exatamente o mesmo que a versão do pacote NuGet. Você pode `ildasm` usar para encontrar o número correto da versão:

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

Baixe o pacote Xamarin.Android.Support NuGet desejado (por exemplo, instalando-o com o gerenciador de pacotes NuGet).

Clique duas vezes no _conjunto Xamarin.Android.Support.v4_ na seção _Referências_ do projeto Android no Visual Studio para Mac para abrir o conjunto no Navegador de Montagem. Certifique-se de que a lista de paradas do _Idioma_ está definida como _C#_ e selecione o conjunto _Xamarin.Android.Support.v4_ de nível superior na árvore de navegação Assembly Browser. Localize `SourceUrl` a propriedade `IncludeAndroidResourcesFrom` sob `JavaLibraryReference` um dos atributos:

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

Baixe **\_o android m2repository.zip** do Google usando o `SourceUrl` retorno de **ildasm**. Alternativamente, você pode verificar qual versão do _Repositório_ de Suporte ao Android você atualmente instalou no Android SDK Manager:

!["Android SDK Manager mostrando o Repositório de Suporte ao Android versão 32 instalado"](install-android-support-library-images/sdk-extras.png)

Se a versão corresponde à que você precisa para o pacote NuGet, então você não precisa baixar nada de novo. Em vez disso, você pode re-zip o diretório **m2repository** existente que está localizado sob **extras /android** no _SDK Path_ (como mostrado o topo da janela Android SDK Manager).

Calcular o hash MD5 da URL retornada do **ildasm**. Formatar a seqüência resultante para usar todas as letras maiúsculas e sem espaços. Por exemplo, ajuste a seqüência de URL conforme necessário e execute o seguinte comando em uma solicitação de comando **Terminal.app:**

```bash
echo -n "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip" | md5 | tr '[:lower:]' '[:upper:]'
```

Outra opção é `csharp` usar o intérprete para executar [o mesmo código C# que o próprio Xamarin.Android usa](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208).
Para isso, ajuste `url` a variável conforme necessário e execute o seguinte comando em um prompt de comando **Terminal.app:**

```bash
csharp -e 'var url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"; string.Concat((System.Security.Cryptography.MD5.Create().ComputeHash(System.Text.Encoding.UTF8.GetBytes(url))).Select(b => b.ToString("X02")))'
```

Saída de exemplo:

```bash
F16A3455987DBAE5783F058F19F7FCDF
```

Copie **\_o android m2repository.zip** para a **pasta $HOME/.local/share/Xamarin/zips/.** Renomeie o arquivo para usar o hash MD5 da etapa de cálculo de hash md5 anterior. Por exemplo:

**$HOME/.local/share/Xamarin/zips/F16A3455987DBAE5783F058F19F7FCDF.zip**

(Opcional) Descompacte o arquivo em: 

**$HOME/.local/share/Xamarin/Xamarin.Android.Support.v4/23.4.0/content/**

(criando um subdiretório **de conteúdo/m2repository).** Se você pular esse passo, então a primeira compilação que usa a biblioteca levará um pouco mais de tempo porque ela precisará completar essa etapa.

O número da versão para o subdiretório **(23.4.0.0** neste exemplo) não é exatamente o mesmo que a versão do pacote NuGet. Como no passo **ildasm** anteriormente, você pode usar o Navegador de Montagem no Visual Studio para Mac para encontrar o número da versão correta. Procure a `Version` propriedade sob `IncludeAndroidResourcesFrom` um `JavaLibraryReference` dos atributos:

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

-----

## <a name="additional-references"></a>Referências adicionais

- [Bug 43245](https://bugzilla.xamarin.com/show_bug.cgi?id=43245) – Impreciso "Falha no download. Por {0} favor, baixe {1} e coloque no diretório." e mensagens de{0}erro "Por favor, instale o pacote: ' disponível no instalador do SDK" relacionadas aos pacotes de erro Xamarin.Android.Support

### <a name="next-steps"></a>Próximas etapas

Este documento discute o comportamento atual a partir de agosto de 2016. A técnica descrita neste documento não faz parte da suíte de testes estáveis para Xamarin, por isso pode quebrar no futuro.

Para obter mais assistência, entre em contato conosco, ou se esse problema permanecer mesmo após utilizar as informações acima, consulte [Quais opções de suporte estão disponíveis para Xamarin?](~/cross-platform/troubleshooting/support-options.md)
