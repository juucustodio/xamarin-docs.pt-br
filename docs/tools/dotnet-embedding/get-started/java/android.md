---
title: Introdução ao Android
description: Este documento descreve como começar a usar a inserção do .NET com o Android. Ele aborda a instalação da inserção do .NET, a criação de um projeto de biblioteca do Android, o uso de uma saída gerada em um projeto Android Studio e outras considerações.
ms.prod: xamarin
ms.assetid: 870F0C18-A794-4C5D-881B-64CC78759E30
author: conceptdev
ms.author: crdun
ms.date: 03/28/2018
ms.openlocfilehash: 9b0da6f5b195ecef5fd4e5e2b4585b660573a5be
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278570"
---
# <a name="getting-started-with-android"></a>Introdução ao Android

Além dos requisitos do guia de [introdução ao Java](~/tools/dotnet-embedding/get-started/java/index.md) , você também precisará de:

- [Xamarin. Android 7,5](https://visualstudio.microsoft.com/xamarin/) ou posterior
- [Android Studio 3. x](https://developer.android.com/studio/index.html) com Java 1,8

Como uma visão geral, iremos:

- Criar um C# projeto de biblioteca do Android
- Instalar a inserção do .NET via NuGet
- Executar a inserção do .NET no assembly da biblioteca do Android
- Usar o arquivo AAR gerado em um projeto Java no Android Studio

## <a name="create-an-android-library-project"></a>Criar um projeto de biblioteca do Android

Abra o Visual Studio para Windows ou Mac, crie um novo projeto de biblioteca de classes do Android, nomeie-o **Hello-from-Csharp**e salve-o em **~/Projects/Hello-from-Csharp** ou **%USERPROFILE%\Projects\hello-from-Csharp**.

Adicione uma nova atividade do Android chamada **HelloActivity.cs**, seguida por um layout do Android em **Resource/layout/Hello. axml**.

Adicione um novo `TextView` ao seu layout e altere o texto para algo agradável.

A origem do layout deve ser semelhante a esta:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <TextView
        android:text="Hello from C#!"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center" />
</LinearLayout>
```

Em sua atividade, verifique se você está chamando `SetContentView` com o novo layout:

```csharp
[Activity(Label = "HelloActivity"),
    Register("hello_from_csharp.HelloActivity")]
public class HelloActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        SetContentView(Resource.Layout.hello);
    }
}
```

> [!NOTE]
> Não se esqueça `[Register]` do atributo. Para obter detalhes, consulte [limitações](#current-limitations-on-android).

Compile o projeto. O assembly resultante será salvo no `bin/Debug/hello-from-csharp.dll`.

## <a name="installing-net-embedding-from-nuget"></a>Instalando a incorporação .NET do NuGet

Siga estas [instruções](~/tools/dotnet-embedding/get-started/install/install.md) para instalar e configurar a inserção do .net para seu projeto.

A invocação de comando que você deve configurar terá a seguinte aparência:

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

```shell
mono '${SolutionDir}/packages/Embeddinator-4000.0.4.0.0/tools/Embeddinator-4000.exe' '${TargetPath}' --gen=Java --platform=Android --outdir='${SolutionDir}/output' -c
```

#### <a name="visual-studio-2017"></a>Visual Studio 2017

```shell
set E4K_OUTPUT="$(SolutionDir)output"
if exist %E4K_OUTPUT% rmdir /S /Q %E4K_OUTPUT%
"$(SolutionDir)packages\Embeddinator-4000.0.2.0.80\tools\Embeddinator-4000.exe" "$(TargetPath)" --gen=Java --platform=Android --outdir=%E4K_OUTPUT% -c
```

## <a name="use-the-generated-output-in-an-android-studio-project"></a>Usar a saída gerada em um projeto Android Studio

1. Abra Android Studio e crie um novo projeto com uma **atividade vazia**.
2. Clique com o botão direito do mouse no módulo do seu **aplicativo** e escolha **novo módulo >** .
3. Selecione **importar. JAR/. Pacote AAR**.
4. Use o navegador de diretório para localizar **~/Projects/Hello-from-Csharp/output/hello_from_csharp.aar** e clique em **concluir**.

![Importar AAR para Android Studio](android-images/androidstudioimport.png)

Isso copiará o arquivo AAR em um novo módulo chamado **hello_from_csharp**.

![Android Studio projeto](android-images/androidstudioproject.png)

Para usar o novo módulo do seu **aplicativo**, clique com o botão direito do mouse e escolha **abrir configurações do módulo**. Na guia **dependências** , adicione uma nova **dependência de módulo** e escolha **: hello_from_csharp**.

![Dependências de Android Studio](android-images/androidstudiodependencies.png)

Em sua atividade, adicione um novo `onResume` método e use o código a seguir para iniciar a C# atividade:

```java
import hello_from_csharp.*;

public class MainActivity extends AppCompatActivity {
    //... Other stuff here ...
    @Override
    protected void onResume() {
        super.onResume();

        Intent intent = new Intent(this, HelloActivity.class);
        startActivity(intent);
    }
}
```

### <a name="assembly-compression-important"></a>Compactação de assembly (*importante*)

Uma alteração adicional é necessária para a inserção do .NET em seu projeto de Android Studio.

Abra o arquivo **Build. gradle** do seu aplicativo e adicione a seguinte alteração:

```groovy
android {
    // ...
    aaptOptions {
        noCompress 'dll'
    }
}
```

O Xamarin. Android atualmente carrega assemblies .NET diretamente do APK, mas requer que os assemblies não sejam compactados.

Se você não tiver essa configuração, o aplicativo falhará na inicialização e imprimirá algo como este no console:

```shell
com.xamarin.hellocsharp A/monodroid: No assemblies found in '(null)' or '<unavailable>'. Assuming this is part of Fast Deployment. Exiting...
```

## <a name="run-the-app"></a>Executar o aplicativo

Após iniciar seu aplicativo:

![Olá do C# exemplo em execução no emulador](android-images/hello-from-csharp-android.png)

Observe o que aconteceu aqui:

- Temos uma C# classe, `HelloActivity`, que subclasses Java
- Temos arquivos de recurso do Android
- Nós os usamos do Java em Android Studio

Para que este exemplo funcione, todos os itens a seguir são configurados no APK final:

- O Xamarin. Android está configurado no início do aplicativo
- Assemblies .NET incluídos em **ativos/assemblies**
- Modificações de **AndroidManifest. xml** para C# suas atividades, etc.
- Recursos e ativos do Android de bibliotecas do .NET
- [Wrappers](~/android/platform/java-integration/android-callable-wrappers.md) que podem ser chamados `Java.Lang.Object` pelo Android para qualquer subclasse

Se você estiver procurando por um passo a passos adicional, confira o vídeo a seguir, que demonstra a inserção de [demonstração de FingerPaint](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) de Charles Petzold em um projeto Android Studio:

[![Embeddinator-4000 para Android](https://img.youtube.com/vi/ZVcrXUpCNpI/0.jpg)](https://www.youtube.com/watch?v=ZVcrXUpCNpI)

## <a name="using-java-18"></a>Usando o Java 1,8

Ao escrever isso, a melhor opção é usar o Android Studio 3,0 ([download aqui](https://developer.android.com/studio/index.html)).

Para habilitar o Java 1,8 no arquivo **Build. gradle** do seu módulo de aplicativo:

```groovy
android {
    // ...
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
```

Você também pode dar uma olhada em um [projeto de teste Android Studio](https://github.com/mono/Embeddinator-4000/blob/master/tests/android/app/build.gradle) para obter mais detalhes. 

Se você quiser usar o Android Studio 2.3. x estável, terá que habilitar o ferramentas de tomada preterido:

```groovy
android {
    // ..
    defaultConfig {
        // ...
        jackOptions.enabled true
    }
}
```

## <a name="current-limitations-on-android"></a>Limitações atuais no Android

No momento, se você criar uma `Java.Lang.Object`subclasse, o Xamarin. Android gerará o stub do Java (Android Callable Wrapper) em vez da incorporação do .net. Por isso, você deve seguir as mesmas regras para exportar C# para Java como Xamarin. Android. Por exemplo:

```csharp
[Register("mono.embeddinator.android.ViewSubclass")]
public class ViewSubclass : TextView
{
    public ViewSubclass(Context context) : base(context) { }

    [Export("apply")]
    public void Apply(string text)
    {
        Text = text;
    }
}
```

- `[Register]`é necessário para mapear para um nome de pacote Java desejado
- `[Export]`é necessário para tornar um método visível para Java

Podemos usar `ViewSubclass` em Java da seguinte forma:

```java
import mono.embeddinator.android.ViewSubclass;
//...
ViewSubclass v = new ViewSubclass(this);
v.apply("Hello");
```

Leia mais sobre [a integração do Java com o Xamarin. Android](~/android/platform/java-integration/index.md).

## <a name="multiple-assemblies"></a>Vários assemblies

A inserção de um único assembly é simples; no entanto, é muito mais provável que você tenha mais de C# um assembly. Muitas vezes, você terá dependências em pacotes NuGet, como as bibliotecas de suporte do Android ou Google Play Services que complicam ainda mais as coisas.

Isso causa um dilema, já que a incorporação do .NET precisa incluir muitos tipos de arquivos no AAR final, como:

- Ativos do Android
- Recursos do Android
- Bibliotecas nativas do Android
- Origem do Android Java

É mais provável que você não queira incluir esses arquivos da biblioteca de suporte do Android ou Google Play Services em seu AAR, mas usará a versão oficial do Google no Android Studio.

Esta é a abordagem recomendada:

- Passe o .NET inserindo qualquer assembly que você possui (tem fonte para) e deseja chamar de Java
- Passe o .NET incorporando qualquer assembly que você precise de ativos do Android, bibliotecas nativas ou recursos de
- Adicionar dependências Java como a biblioteca de suporte do Android ou Google Play Services no Android Studio

Portanto, o comando pode ser:

```shell
mono Embeddinator-4000.exe --gen=Java --platform=Android -c -o output YourMainAssembly.dll YourDependencyA.dll YourDependencyB.dll
```

Você deve excluir qualquer coisa do NuGet, a menos que descubra que ele contém ativos, recursos etc. do Android que serão necessários no seu Android Studio projeto. Você também pode omitir as dependências que não precisa chamar do Java e o vinculador _deve_ incluir as partes da biblioteca que são necessárias.

Para adicionar qualquer dependência de Java necessária no Android Studio, o arquivo **Build. gradle** pode ser semelhante a:

```groovy
dependencies {
    // ...
    compile 'com.android.support:appcompat-v7:25.3.1'
    compile 'com.google.android.gms:play-services-games:11.0.4'
    // ...
}
```

## <a name="further-reading"></a>Leitura adicional

- [Retornos de chamada no Android](~/tools/dotnet-embedding/android/callbacks.md)
- [Pesquisa do Android preliminar](~/tools/dotnet-embedding/android/index.md)
- [Limitações de incorporação do .NET](~/tools/dotnet-embedding/limitations.md)
- [Contribuindo para o projeto de código-fonte aberto](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
- [Códigos de erro e descrições](~/tools/dotnet-embedding/errors.md)

## <a name="related-links"></a>Links relacionados

- [Exemplo de clima (Android)](https://github.com/jamesmontemagno/embeddinator-weather)
