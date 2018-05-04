---
title: Guia de Introdução do Android
ms.prod: xamarin
ms.assetid: 870F0C18-A794-4C5D-881B-64CC78759E30
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/28/2018
ms.openlocfilehash: 1d50d7dbc53271a15e06a7a18d2fa95f91b76ea4
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2018
---
# <a name="getting-started-with-android"></a>Guia de Introdução do Android

Além dos requisitos do [guia de Introdução com Java](~/tools/dotnet-embedding/get-started/java/index.md) guia, você também precisará:

* [Xamarin 7.5](https://www.visualstudio.com/xamarin/) ou posterior
* [O Android Studio 3. x](https://developer.android.com/studio/index.html) com Java 1.8

Como uma visão geral, você irá:

* Criar um projeto de biblioteca Android c#
* Instalar o .NET inserindo por meio do NuGet
* Executar a inserção do .NET no assembly de biblioteca Android
* Use o arquivo AAR gerado em um projeto Java no Android Studio

## <a name="create-an-android-library-project"></a>Criar um projeto de biblioteca Android

Abra o Visual Studio para Windows ou Mac, crie um novo projeto de biblioteca de classe Android, nomeie-o **saudação do csharp**e salvá-lo para **~/Projects/hello-from-csharp** ou **%USERPROFILE%\ Projects\hello do csharp**.

Adicionar uma nova atividade Android denominado **HelloActivity.cs**, seguido de um Layout Android em **Resource/layout/hello.axml**.

Adicionar um novo `TextView` seu layout, e alterar o texto para algo interessante.

A fonte de layout deve ter esta aparência:

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

Em sua atividade, certifique-se de que está chamando `SetContentView` com seu novo layout:

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
> Não se esqueça de `[Register]` atributo. Para obter detalhes, consulte [limitações](#current-limitations-on-android).

Compile o projeto. O assembly resultante será salvo em `bin/Debug/hello-from-csharp.dll`.

## <a name="installing-net-embedding-from-nuget"></a>Instalando o .NET incorporação do NuGet

Siga estas [instruções](~/tools/dotnet-embedding/get-started/install/install.md) para instalar e configurar a incorporação do .NET para o seu projeto.

Você deve configurar a chamada do comando será semelhante a isso:

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

## <a name="use-the-generated-output-in-an-android-studio-project"></a>Use a saída gerada em um projeto Android Studio

1. Abra o Android Studio e crie um novo projeto com uma **atividade vazio**.
2. Clique com botão direito no seu **aplicativo** módulo e escolha **Novo > módulo**.
3. Selecione **importação. JAR /. Pacote de AAR**.
4. Use o navegador do diretório para localizar **~/Projects/hello-from-csharp/output/hello_from_csharp.aar** e clique em **concluir**.

![Importar AAR no Android Studio](android-images/androidstudioimport.png)

Isso copiará o arquivo AAR para um novo módulo chamado **hello_from_csharp**.

![Projeto Android Studio](android-images/androidstudioproject.png)

Para usar o novo módulo de sua **aplicativo**, com o botão direito e escolha **abrir configurações de módulo**. Sobre o **dependências** guia, adicione uma nova **dependência de módulo** e escolha **: hello_from_csharp**.

![Dependências do Android Studio](android-images/androidstudiodependencies.png)

Em sua atividade, adicione um novo `onResume` método e use o código a seguir para iniciar a atividade do c#:

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

Uma alteração adicional é necessária para a incorporação de .NET em seu projeto Android Studio.

Abra o aplicativo **gradle** de arquivo e adicione a seguinte alteração:

```groovy
android {
    // ...
    aaptOptions {
        noCompress 'dll'
    }
}
```

Xamarin atualmente carrega assemblies .NET diretamente a partir de APK, mas exige os assemblies a não ser compactados.

Se você não tiver essa configuração, o aplicativo será falhas na inicialização e imprimir algo parecido ao console:

```shell
com.xamarin.hellocsharp A/monodroid: No assemblies found in '(null)' or '<unavailable>'. Assuming this is part of Fast Deployment. Exiting...
```

## <a name="run-the-app"></a>Executar o aplicativo

Após iniciar o aplicativo:

![Saudação de exemplo em c# em execução no emulador](android-images/hello-from-csharp-android.png)

Observe o que aconteceu aqui:

* Temos uma classe c#, `HelloActivity`, que as subclasses Java
* Temos os arquivos de recurso do Android
* Usamos essas do Java no Android Studio

Para esse exemplo funcione, todos os itens a seguir é definidos no APK final:

* Xamarin está configurado na inicialização do aplicativo
* Assemblies .NET incluídos no **ativos/assemblies**
* **AndroidManifest.xml** modificações para o c# atividades, etc.
* Recursos do Android e ativos de bibliotecas do .NET
* [Android Callable Wrappers](~/android/platform/java-integration/android-callable-wrappers.md) para qualquer `Java.Lang.Object` subclasse

Se você estiver procurando uma passo a passo adicional, confira o vídeo a seguir, que demonstra a incorporação de Charles Petzold [demonstração de pintura a dedo](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint/) em um projeto Android Studio:

[![Embeddinator-4000 para Android](https://img.youtube.com/vi/ZVcrXUpCNpI/0.jpg)](https://www.youtube.com/watch?v=ZVcrXUpCNpI)

## <a name="using-java-18"></a>Usando o Java 1.8

A partir de escrever isso, a melhor opção é usar o Android Studio 3.0 ([baixar aqui](https://developer.android.com/studio/index.html)).

Para habilitar o Java 1.8 em seu módulo de aplicativo **gradle** arquivo:

```groovy
android {
    // ...
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
```

Você também pode dar uma olhada em um [projeto de teste do Android Studio](https://github.com/mono/Embeddinator-4000/blob/master/tests/android/app/build.gradle) para obter mais detalhes. 

Se você é desejam usar estável do Android Studio 2.3, você terá que habilitar a cadeia de ferramentas tomada preterida:

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

Agora, se você subclasse `Java.Lang.Object`, xamarin irá gerar o stub de Java (Android Callable Wrapper) em vez de inserir .NET. Por isso, você deve seguir as mesmas regras para exportar c# para Java como xamarin. Por exemplo:

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

* `[Register]` é necessário mapear para um nome de pacote Java desejado
* `[Export]` é necessário para tornar um método visível para Java

Podemos usar `ViewSubclass` em Java da seguinte forma:

```java
import mono.embeddinator.android.ViewSubclass;
//...
ViewSubclass v = new ViewSubclass(this);
v.apply("Hello");
```

Leia mais sobre [integração de Java com o xamarin](~/android/platform/java-integration/index.md).

## <a name="multiple-assemblies"></a>Vários assemblies

A incorporação de um único assembly é simples; No entanto, é muito mais provável que você terá mais de um c# assembly. Muitas vezes você terá as dependências em pacotes do NuGet como as bibliotecas de suporte do Android ou Google executar serviços mais complicar as coisas.

Isso faz com que um dilema, desde que o .NET inserindo precisa incluir vários tipos de arquivos no final AAR, como:

* Android ativos
* Recursos do Android
* Android bibliotecas nativas
* Código-fonte java Android

Provavelmente, você não deseja incluir esses arquivos da biblioteca de suporte do Android ou Google executar serviços em sua AAR, mas preferir usar a versão oficial do Google no Android Studio.

Aqui está a abordagem recomendada:

* Passar .NET incorporar qualquer assembly que você possui (ter origem para) e deseja chamar do Java
* Passar .NET incorporar qualquer assembly que você precisa ativos Android, bibliotecas nativas ou recursos de
* Adicionar dependências de Java como o Android oferece suporte a biblioteca ou o Google executar serviços no Android Studio

Portanto, o comando pode ser:

```shell
mono Embeddinator-4000.exe --gen=Java --platform=Android -c -o output YourMainAssembly.dll YourDependencyA.dll YourDependencyB.dll
```

Nada devem ser excluídos do NuGet, a menos que você descobrir que ele contenha ativos Android, recursos, etc. que será necessário em seu projeto Android Studio. Você também pode omitir dependências que você não precisa chamar a partir de Java e o vinculador _devem_ incluem as partes da biblioteca que são necessários.

Para adicionar todas as dependências de Java necessárias no Android Studio, o **gradle** arquivo pode parecer com:

```groovy
dependencies {
    // ...
    compile 'com.android.support:appcompat-v7:25.3.1'
    compile 'com.google.android.gms:play-services-games:11.0.4'
    // ...
}
```

## <a name="further-reading"></a>Leitura adicional

* [Retornos de chamada no Android](~/tools/dotnet-embedding/android/callbacks.md)
* [Pesquisa Android preliminar](~/tools/dotnet-embedding/android/index.md)
* [Limitações de incorporação de .NET](~/tools/dotnet-embedding/limitations.md)
* [Contribuindo para o projeto de código-fonte aberto](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Descrições e códigos de erro](~/tools/dotnet-embedding/errors.md)

## <a name="related-links"></a>Links relacionados

- [Exemplo de clima (Android)](https://github.com/jamesmontemagno/embeddinator-weather)
