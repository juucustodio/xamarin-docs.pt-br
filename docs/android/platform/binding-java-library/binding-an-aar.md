---
title: Associação de um .AAR
description: Este passo a passo fornece instruções passo a passo para criar uma biblioteca de associações Java do Xamarin. Android a partir de um Android. Arquivo AAR.
ms.prod: xamarin
ms.assetid: 380413B8-6A99-4BB8-B64C-3EAF9F359C22
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/11/2018
ms.openlocfilehash: 6d80af60ae94c638c7d90229852f304a40ce0b0c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643814"
---
# <a name="binding-an-aar"></a>Associação de um .AAR

_Este passo a passo fornece instruções passo a passo para criar uma biblioteca de associações Java do Xamarin. Android a partir de um Android. Arquivo AAR._


## <a name="overview"></a>Visão geral

O *arquivo morto do Android (. AAR)* é o formato de arquivo para bibliotecas Android.
Um. O arquivo AAR é um. Arquivo ZIP que contém o seguinte:

-   Código Java compilado
-   IDs de recurso
-   Recursos
-   Metadados (por exemplo, declarações de atividade, permissões)

Neste guia, vamos percorrer as noções básicas de criação de uma biblioteca de associações para um único. Arquivo AAR. Para obter uma visão geral da Associação de biblioteca Java em geral (com um exemplo de código básico), consulte [associando uma biblioteca Java](~/android/platform/binding-java-library/index.md).


> [!IMPORTANT]
> Um projeto de associação só pode incluir um. Arquivo AAR. Se o. AAR dependências em outros. AAR, essas dependências devem estar contidas em seu próprio projeto de associação e, em seguida, referenciadas. Consulte o [Bug 44573](https://bugzilla.xamarin.com/show_bug.cgi?id=44573).


## <a name="walkthrough"></a>Passo a passo

Criaremos uma biblioteca de associações para um arquivo morto do Android de exemplo que foi criado em Android Studio, textanalyze [. aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true). Esta. AAR contém uma `TextCounter` classe com métodos estáticos que contam o número de vogais e consoantes em uma cadeia de caracteres. Além disso, **textanalyzer. aar** contém um recurso de imagem para ajudar a exibir os resultados da contagem.

Usaremos as etapas a seguir para criar uma biblioteca de associações do. Arquivo AAR:

1. Crie um novo projeto de biblioteca de associações Java.

2. Adicione um único. Arquivo AAR para o projeto. Um projeto de associação só pode conter um único. AAR.

3. Defina a ação de compilação apropriada para o. Arquivo AAR.

4. Escolha uma estrutura de destino que o. O AAR dá suporte a.

5. Crie a biblioteca de associações.

Depois de criarmos a biblioteca de associações, desenvolveremos um pequeno aplicativo Android que solicita ao usuário uma cadeia de caracteres de texto, chamadas. Os métodos AAR para analisar o texto recuperam a imagem do. AAR e exibe os resultados junto com a imagem.

O aplicativo de exemplo acessará a `TextCounter` classe de textanalyze **. aar**:

```java
package com.xamarin.textcounter;

public class TextCounter
{
    ...
    public static int numVowels (String text) { ... };
    ...
    public static int numConsonants (String text) { ... };
    ...
}
```

Além disso, este aplicativo de exemplo recuperará e exibirá um recurso de imagem que é empacotado em textanalyze **. aar**:

[![Imagem de macaco do Xamarin](binding-an-aar-images/00-monkey-sml.png)](binding-an-aar-images/00-monkey.png#lightbox)

Esse recurso de imagem reside em **res/empate/macaco. png** em textanalyze **. aar**.



### <a name="creating-the-bindings-library"></a>Criando a biblioteca de associações

Antes de começar com as etapas abaixo, baixe o arquivo de exemplo textanalyze [. aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true) Android:

1.  Crie um novo projeto de biblioteca de associações começando com o modelo de biblioteca de associações do Android. Você pode usar o Visual Studio para Mac ou o Visual Studio (as capturas de tela abaixo mostram o Visual Studio, mas Visual Studio para Mac é muito semelhante). Nomeie a solução **AarBinding**:

    [![Criar projeto AarBindings](binding-an-aar-images/01-new-bindings-library-vs-sml.w157.png)](binding-an-aar-images/01-new-bindings-library-vs.w157.png#lightbox)

2.  O modelo inclui uma pasta **jars** na qual você adiciona o. AAR (s) para o projeto de biblioteca de associações. Clique com o botão direito do mouse na pasta **jars** e selecione **Adicionar > item existente**:

    [![Adicionar item existente](binding-an-aar-images/02-add-existing-item-vs-sml.png)](binding-an-aar-images/02-add-existing-item-vs.png#lightbox)


3.  Navegue até o arquivo textanalyze **. aar** baixado anteriormente, selecione-o e clique em **Adicionar**:

    [![Adicionar textanalayzer. aar](binding-an-aar-images/03-select-aar-file-vs-sml.png)](binding-an-aar-images/03-select-aar-file-vs.png#lightbox)


4.  Verifique se o arquivo textanalyze **. aar** foi adicionado com êxito ao projeto:

    [![O arquivo textanalyzer. aar foi adicionado](binding-an-aar-images/04-aar-added-vs-sml.png)](binding-an-aar-images/04-aar-added-vs.png#lightbox)

5.  Defina a ação de compilação para textanalyze **. aar** como `LibraryProjectZip`. Em Visual Studio para Mac, clique com o botão direito do mouse em textanalyze **. aar** para definir a ação de compilação. No Visual Studio, a ação de compilação pode ser definida no painel **Propriedades** ):

    [![Definindo a ação de compilação textanalyze. aar como LibraryProjectZip](binding-an-aar-images/05-embedded-aar-vs-sml.png)](binding-an-aar-images/05-embedded-aar-vs.png#lightbox)

6.  Abra as propriedades do projeto para configurar a *estrutura de destino*. Se o. AAR usa quaisquer APIs do Android, define a estrutura de destino para o nível de API que o. AAR espera. (Para obter mais informações sobre a configuração da estrutura de destino e os níveis de API do Android em geral, consulte [noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).)

    Defina o nível da API de destino para sua biblioteca de associações. Neste exemplo, estamos livres para usar o nível mais recente da API da plataforma (nível 23 da API) porque nosso textanalyzer não tem uma dependência em APIs do Android:

    [![Definindo o nível de destino como API 23](binding-an-aar-images/06-set-target-framework-vs-sml.png)](binding-an-aar-images/06-set-target-framework-vs.png#lightbox)

7.  Crie a biblioteca de associações. O projeto de biblioteca de associações deve compilar com êxito e produzir uma saída. DLL no seguinte local: **AarBinding/bin/Debug/AarBinding.dll**



### <a name="using-the-bindings-library"></a>Usando a biblioteca de associações

Para consumir isso. DLL em seu aplicativo Xamarin. Android, você deve primeiro adicionar uma referência à biblioteca de associações. Use as seguintes etapas:

1.  Estamos criando esse aplicativo na mesma solução que a biblioteca de associações para simplificar este passo a passos. (O aplicativo que consome a biblioteca de associações também pode residir em uma solução diferente.) Criar um novo aplicativo Xamarin. Android: clique com o botão direito do mouse na solução e selecione **Adicionar novo projeto**. Nomeie o novo projeto **BindingTest**:

    [![Criar novo projeto BindingTest](binding-an-aar-images/07-add-new-project-vs-sml.w157.png)](binding-an-aar-images/07-add-new-project-vs.w157.png#lightbox)

2.  Clique com o botão direito do mouse no nó **referências** do projeto **BindingTest** e selecione **Adicionar referência...** :

    [![Clique em Adicionar referência](binding-an-aar-images/08-add-reference-vs-sml.png)](binding-an-aar-images/08-add-reference-vs.png#lightbox)

3.  Selecione o projeto **AarBinding** criado anteriormente e clique em **OK**:

    [![Verificar o projeto de associação AAR](binding-an-aar-images/09-choose-aar-binding-vs-sml.png)](binding-an-aar-images/09-choose-aar-binding-vs.png#lightbox)

4.  Abra o nó **referências** do projeto **BindingTest** para verificar se a referência **AarBinding** está presente:

    [![AarBinding está listado em referências](binding-an-aar-images/10-references-shows-aarbinding-vs-sml.png)](binding-an-aar-images/10-references-shows-aarbinding-vs.png#lightbox)


Se desejar exibir o conteúdo do projeto de biblioteca de associação, você pode clicar duas vezes na referência para abri-lo no Pesquisador de **objetos**. Você pode ver o conteúdo mapeado do `Com.Xamarin.Textcounter` namespace (mapeado do pacote Java `com.xamarin.textanalyzezr` ) e pode `TextCounter` exibir os membros da classe:

[![Exibindo o pesquisador de objetos](binding-an-aar-images/11-object-browser-vs-sml.png)](binding-an-aar-images/11-object-browser-vs.png#lightbox)

A captura de tela acima realça `TextAnalyzer` os dois métodos que o aplicativo de exemplo `NumConsonants` chamará: (que encapsula o `numConsonants` método Java subjacente) `NumVowels` e (que encapsula o método Java `numVowels` subjacente).



### <a name="accessing-aar-types"></a>Acess. Tipos de AAR

Depois de adicionar uma referência ao seu aplicativo que aponta para a biblioteca de associação, você pode acessar os tipos de Java no. AAR como você acessaria C# tipos (graças aos C# wrappers). C#o código do aplicativo `TextAnalyzer` pode chamar métodos conforme ilustrado neste exemplo:

```csharp
using Com.Xamarin.Textcounter;
...
int numVowels = TextCounter.NumVowels (myText);
int numConsonants = TextCounter.NumConsonants (myText);
```

No exemplo acima, estamos chamando métodos estáticos na `TextCounter` classe. No entanto, você também pode instanciar classes e chamar métodos de instância. Por exemplo, se seu. AAR encapsula uma classe chamada `Employee` que tem o método `buildFullName`de instância, você pode criar `MyClass` uma instância e usá-la como visto aqui:

```csharp
var employee = new Com.MyCompany.MyProject.Employee();
var name = employee.BuildFullName ();
```

As etapas a seguir adicionam o código ao aplicativo para que ele solicite ao usuário o texto `TextCounter` , use para analisar o texto e, em seguida, exibe os resultados.

Substitua o layout **BindingTest** (**Main. axml**) pelo seguinte XML. Esse layout tem uma `EditText` entrada de texto e dois botões para iniciar contagens de vogal e consoante:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation             ="vertical"
    android:layout_width            ="fill_parent"
    android:layout_height           ="fill_parent" >
    <TextView
        android:text                ="Text to analyze:"
        android:textSize            ="24dp"
        android:layout_marginTop    ="30dp"
        android:layout_gravity      ="center"
        android:layout_width        ="wrap_content"
        android:layout_height       ="wrap_content" />
    <EditText
        android:id                  ="@+id/input"
        android:text                ="I can use my .AAR file from C#!"
        android:layout_marginTop    ="10dp"
        android:layout_gravity      ="center"
        android:layout_width        ="300dp"
        android:layout_height       ="wrap_content"/>
    <Button
        android:id                  ="@+id/vowels"
        android:layout_marginTop    ="30dp"
        android:layout_width        ="240dp"
        android:layout_height       ="wrap_content"
        android:layout_gravity      ="center"
        android:text                ="Count Vowels" />
    <Button
        android:id                  ="@+id/consonants"
        android:layout_width        ="240dp"
        android:layout_height       ="wrap_content"
        android:layout_gravity      ="center"
        android:text                ="Count Consonants" />
</LinearLayout>
```

Substitua o conteúdo de **MainActivity.cs** pelo código a seguir. Como visto neste exemplo, os manipuladores de eventos de botão chamam `TextCounter` os métodos encapsulados que residem no. AAR e use os torradeiras para exibir os resultados. Observe a `using` instrução para o namespace da biblioteca associada (nesse caso, `Com.Xamarin.Textcounter`):

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;
using Android.Views.InputMethods;
using Com.Xamarin.Textcounter;

namespace BindingTest
{
    [Activity(Label = "BindingTest", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        InputMethodManager imm;

        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            SetContentView(Resource.Layout.Main);

            imm = (InputMethodManager)GetSystemService(Context.InputMethodService);

            var vowelsBtn = FindViewById<Button>(Resource.Id.vowels);
            var consonBtn = FindViewById<Button>(Resource.Id.consonants);
            var edittext = FindViewById<EditText>(Resource.Id.input);
            edittext.InputType = Android.Text.InputTypes.TextVariationPassword;

            edittext.KeyPress += (sender, e) =>
            {
                imm.HideSoftInputFromWindow(edittext.WindowToken, HideSoftInputFlags.NotAlways);
                e.Handled = true;
            };

            vowelsBtn.Click += (sender, e) =>
            {
                int count = TextCounter.NumVowels(edittext.Text);
                string msg = count + " vowels found.";
                Toast.MakeText (this, msg, ToastLength.Short).Show ();
            };

            consonBtn.Click += (sender, e) =>
            {
                int count = TextCounter.NumConsonants(edittext.Text);
                string msg = count + " consonants found.";
                Toast.MakeText (this, msg, ToastLength.Short).Show ();
            };

        }
    }
}
```

Compile e execute o projeto **BindingTest** . O aplicativo será iniciado e apresentará a captura de tela à esquerda `EditText` (o é inicializado com algum texto, mas você pode tocar nele para alterá-lo). Quando você toca em VOGAl de **contagem**, um sistema exibe o número de vogais, conforme mostrado à direita:

[![Capturas de tela da execução de BindingTest](binding-an-aar-images/12-count-vowels.png)](binding-an-aar-images/12-count-vowels.png#lightbox)

Tente tocar no botão **contagem** de consoantes. Além disso, você pode modificar a linha de texto e tocar esses botões novamente para testar contagens de vogal e consoante diferentes.



### <a name="accessing-aar-resources"></a>Acess. Recursos do AAR

As ferramentas do Xamarin mesclam os dados do **R** do. AAR na classe de **recurso** do aplicativo. Como resultado, você pode acessar o. AAR recursos do seu layout (e do code-behind) da mesma maneira que você acessaria os recursos que estão no caminho de **recursos** do seu projeto.

Para acessar um recurso de imagem, use o nome de **recurso. desenhável** para a imagem armazenada dentro do. AAR. Por exemplo, você pode fazer referência a **Image. png** no. Arquivo AAR usando `@drawable/image`:

```xml
<ImageView android:src="@drawable/image" ... />
```

Você também pode acessar layouts de recursos que residem no. AAR. Para fazer isso, use o nome do **recurso. layout** para o layout empacotado dentro do. AAR. Por exemplo:

```csharp
var a = new ArrayAdapter<string>(this, Resource.Layout.row_layout, ...);
```

O exemplo textanalyze **. aar** contém um arquivo de imagem que reside em **res/Drawable/macaco. png**. Vamos acessar esse recurso de imagem e usá-lo em nosso aplicativo de exemplo:

Edite o layout **BindingTest** (**Main. axml**) e adicione `ImageView` um ao final do `LinearLayout` contêiner. Isso `ImageView` exibe a imagem encontrada em **@drawable/monkey** ; essa imagem será carregada a partir da seção de recursos de textanalyze **. aar**:

```xml
    ...
    <ImageView
        android:src                 ="@drawable/monkey"
        android:layout_marginTop    ="40dp"
        android:layout_width        ="200dp"
        android:layout_height       ="200dp"
        android:layout_gravity      ="center" />

</LinearLayout>
```

Compile e execute o projeto **BindingTest** . O aplicativo será iniciado e apresentará a captura de tela &ndash; à esquerda quando você tocar em **contar consoantes**, os resultados serão exibidos conforme mostrado à direita:

[![BindingTest exibindo contagem de consoante](binding-an-aar-images/13-count-consonants.png)](binding-an-aar-images/13-count-consonants.png#lightbox)


Parabéns! Você uma biblioteca Java com êxito. AAR!



## <a name="summary"></a>Resumo

Neste tutorial, criamos uma biblioteca de associações para um. AAR arquivo, adicionou a biblioteca de associações a um aplicativo de teste mínimo e executou o aplicativo para verificar se C# nosso código pode chamar o código Java que reside no. Arquivo AAR.
Além disso, estendemos o aplicativo para acessar e exibir um recurso de imagem que reside no. Arquivo AAR.


## <a name="related-links"></a>Links relacionados

- [Criando uma biblioteca de associações Java (vídeo)](https://university.xamarin.com/classes#10090)
- [Associação de um .JAR](~/android/platform/binding-java-library/binding-a-jar.md)
- [Associação de uma biblioteca Java](~/android/platform/binding-java-library/index.md)
- [AarBinding (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/javaintegration-aarbinding)
- [Bug 44573-um projeto não pode associar vários arquivos. aar](https://bugzilla.xamarin.com/show_bug.cgi?id=44573)
