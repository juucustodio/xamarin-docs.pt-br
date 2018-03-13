---
title: "Associação de um. AAR"
description: "Este passo a passo fornece instruções passo a passo para criar uma biblioteca de associações de Java do xamarin de um Android. Arquivo AAR."
ms.topic: article
ms.prod: xamarin
ms.assetid: 380413B8-6A99-4BB8-B64C-3EAF9F359C22
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: ae209f8099925cc160e16cb5365625e48e6c384d
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="binding-an-aar"></a>Associação de um. AAR

_Este passo a passo fornece instruções passo a passo para criar uma biblioteca de associações de Java do xamarin de um Android. Arquivo AAR._


## <a name="overview"></a>Visão geral

O *arquivamento Android (. AAR)* arquivo é o formato de arquivo para bibliotecas do Android.
Um arquivo. Arquivo AAR é um. Arquivo ZIP que contém o seguinte:

-   Código de Java compilado
-   IDs de recurso
-   Recursos
-   Metadados (por exemplo, declarações de atividade, as permissões)

Neste guia, forneceremos Noções básicas de criação de uma biblioteca de ligações para um único. Arquivo AAR. Para obter uma visão geral de Java biblioteca associação em geral (com um exemplo de código básico), consulte [associação a uma biblioteca Java](~/android/platform/binding-java-library/index.md).


> [!IMPORTANT]
> Um projeto de associação pode incluir apenas um. Arquivo AAR. Se o. Dependências de AAR em si. AAR e, em seguida, essas dependências devem ser contidos em seu próprio projeto de associação e depois referenciado. Consulte [Bug 44573](https://bugzilla.xamarin.com/show_bug.cgi?id=44573).


## <a name="walkthrough"></a>Passo a passo

Vamos criar uma biblioteca de associações de um exemplo de arquivo compactado Android foi criado no Android Studio, [textanalyzer.aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true). Isso. AAR contém um `TextCounter` classe com métodos estáticos que contar o número das vogais e consoantes em uma cadeia de caracteres. Além disso, **textanalyzer.aar** contém um recurso de imagem para ajudar a exibir os resultados de contagem.

Vamos usar as etapas a seguir para criar uma biblioteca de associações da. Arquivo AAR:

1. Crie um novo projeto de biblioteca de associações de Java.

2. Adicione um único. Arquivo AAR ao projeto. Um projeto de associação pode conter apenas um único. AAR.

3. Definir a ação de compilação apropriado para o. Arquivo AAR.

4. Escolha uma estrutura de destino que o. Dá suporte a AAR.

5. Crie a biblioteca de associações.

Depois de criar a biblioteca de associações, podemos desenvolverá um pequeno aplicativo do Android que solicita ao usuário uma cadeia de caracteres de texto, chamadas. Métodos de AAR para analisar o texto, recupera a imagem da. AAR e exibe os resultados junto com a imagem.

O aplicativo de exemplo irá acessar a `TextCounter` classe de **textanalyzer.aar**:

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

Além disso, este aplicativo de exemplo irá recuperar e exibir um recurso de imagem é empacotado em **textanalyzer.aar**:

[![Imagem de monkey Xamarin](binding-an-aar-images/00-monkey-sml.png)](binding-an-aar-images/00-monkey.png#lightbox)

Esse recurso de imagem reside no **res/drawable/monkey.png** na **textanalyzer.aar**.



### <a name="creating-the-bindings-library"></a>Criando a biblioteca de associações

Antes de começar as etapas abaixo, baixe o exemplo [textanalyzer.aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true) arquivo morto Android:

1.  Crie um novo projeto de biblioteca de associações, começando com o modelo de biblioteca de associações Android. Você pode usar o Visual Studio para Mac ou o Visual Studio (capturas de tela abaixo mostram o Visual Studio, mas o Visual Studio para Mac é muito semelhante). Nome da solução **AarBinding**:

    [![Criar projeto AarBindings](binding-an-aar-images/01-new-bindings-library-vs-sml.png)](binding-an-aar-images/01-new-bindings-library-vs.png#lightbox)

2.  O modelo inclui uma **Jars** pasta em que você adicionar seu. AAR(s) para o projeto de biblioteca de associações. Clique com botão direito do **Jars** pasta e selecione **Adicionar > Existing Item**:

    [![Adicionar item existente](binding-an-aar-images/02-add-existing-item-vs-sml.png)](binding-an-aar-images/02-add-existing-item-vs.png#lightbox)


3.  Navegue até o **textanalyzer.aar** arquivos baixados anteriormente, selecione-o e clique em **adicionar**:

    [![Adicionar textanalayzer.aar](binding-an-aar-images/03-select-aar-file-vs-sml.png)](binding-an-aar-images/03-select-aar-file-vs.png#lightbox)


4.  Verifique o **textanalyzer.aar** arquivo foi adicionado com êxito ao projeto:

    [![O arquivo textanalyzer.aar foi adicionado](binding-an-aar-images/04-aar-added-vs-sml.png)](binding-an-aar-images/04-aar-added-vs.png#lightbox)

5.  Definir a ação de compilação **textanalyzer.aar** para `LibraryProjectZip`. No Visual Studio para Mac, clique com botão direito **textanalyzer.aar** para definir a ação de compilação. No Visual Studio, em que a ação de compilação pode definir o **propriedades** painel):

    [![Definir a ação de compilação textanalyzer.aar como LibraryProjectZip](binding-an-aar-images/05-embedded-aar-vs-sml.png)](binding-an-aar-images/05-embedded-aar-vs.png#lightbox)

6.  Abra o projeto de propriedades para configurar o *Framework de destino*. Se o. AAR usa APIs Android, defina a estrutura de destino para o nível de API que o. Espera AAR. (Para obter mais informações sobre a configuração de estrutura de destino e os níveis de API do Android em geral, consulte [Noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).)

    Defina o destino de nível de API para a biblioteca de associações. Neste exemplo, estamos livres para usar a plataforma mais recente de nível de API (API nível 23) porque nossa **textanalyzer** não tem uma dependência no Android APIs:

    [![Definir o nível de destino como 23 de API](binding-an-aar-images/06-set-target-framework-vs-sml.png)](binding-an-aar-images/06-set-target-framework-vs.png#lightbox)

7.  Crie a biblioteca de associações. O projeto de biblioteca de associações deve compilar com êxito e produzir uma saída. DLL no seguinte local: **AarBinding/bin/Debug/AarBinding.dll**



### <a name="using-the-bindings-library"></a>Usando a biblioteca de associações

Para consumir isso. DLL em seu aplicativo xamarin, primeiro você deve adicionar uma referência à biblioteca de associações. Use as seguintes etapas:

1.  Estamos criando este aplicativo na mesma solução que a biblioteca de associações para simplificar este passo a passo. (O aplicativo que consome a biblioteca de associações também pode residir em uma solução diferente.) Criar um novo aplicativo xamarin: a solução e selecione **adicionar novo projeto**. Nomeie o novo projeto **BindingTest**:

    [![Criar novo projeto de BindingTest](binding-an-aar-images/07-add-new-project-vs-sml.png)](binding-an-aar-images/07-add-new-project-vs.png#lightbox)

2.  Com o botão direito do **referências** nó do **BindingTest** do projeto e selecione **adicionar referência...** :

    [![Clique em Adicionar referência](binding-an-aar-images/08-add-reference-vs-sml.png)](binding-an-aar-images/08-add-reference-vs.png#lightbox)

3.  Selecione o **AarBinding** projeto criado anteriormente e clique em **Okey**:

    [![Verifique o projeto de vinculação AAR](binding-an-aar-images/09-choose-aar-binding-vs-sml.png)](binding-an-aar-images/09-choose-aar-binding-vs.png#lightbox)

4.  Abra o **referências** nó do **BindingTest** projeto para verificar se o **AarBinding** referência está presente:

    [![AarBinding é listado em referências](binding-an-aar-images/10-references-shows-aarbinding-vs-sml.png)](binding-an-aar-images/10-references-shows-aarbinding-vs.png#lightbox)


Se você deseja exibir o conteúdo do projeto de biblioteca de associação, você pode clicar duas vezes a referência para abri-lo no **Pesquisador de objetos**. Você pode ver o conteúdo mapeado do `Com.Xamarin.Textcounter` namespace (mapeada a partir de Java `com.xamarin.textanalyzezr` pacote) e você pode exibir os membros a `TextCounter` classe:

[![Exibindo o Pesquisador de objetos](binding-an-aar-images/11-object-browser-vs-sml.png)](binding-an-aar-images/11-object-browser-vs.png#lightbox)

Captura de tela acima realça os dois `TextAnalyzer` métodos que o aplicativo de exemplo chamará: `NumConsonants` (que encapsula o Java subjacente `numConsonants` método), e `NumVowels` (que encapsula o Java subjacente `numVowels` método).



### <a name="accessing-aar-types"></a>Acessando. Tipos de AAR

Depois de adicionar uma referência ao seu aplicativo que aponta para a biblioteca de vinculação, você pode acessar os tipos de Java na. AAR como você acessaria tipos c# (graças ao c# wrappers). O código de aplicativo c# pode chamar `TextAnalyzer` métodos conforme ilustrado neste exemplo:

```csharp
using Com.Xamarin.Textcounter;
...
int numVowels = TextCounter.NumVowels (myText);
int numConsonants = TextCounter.NumConsonants (myText);
```

No exemplo acima, estamos ligando para métodos estáticos `TextCounter` classe. No entanto, também pode criar uma instância de classes e chamar os métodos de instância. Por exemplo, se seu. AAR encapsula uma classe chamada `Employee` que tem o método de instância `buildFullName`, você pode instanciar `MyClass` e usá-lo como visto aqui:

```csharp
var employee = new Com.MyCompany.MyProject.Employee();
var name = employee.BuildFullName ();
```

As etapas a seguir adicionam código ao aplicativo para que ele solicita ao usuário para texto, usa `TextCounter` para analisar o texto e, em seguida, exibe os resultados.

Substitua o **BindingTest** layout (**Main.axml**) com o XML a seguir. Esse layout tem um `EditText` para entrada de texto e dois botões para iniciar as contagens de vogal e consoante:

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

Substitua o conteúdo do **MainActivity.cs** com o código a seguir. Como mostrado neste exemplo, a chamada de manipuladores de eventos do botão encapsulado `TextCounter` métodos que residem na. Notificações do sistema AAR e use a exibição dos resultados. Observe o `using` declaração para o namespace da biblioteca associada (nesse caso, `Com.Xamarin.Textcounter`):

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

Compilar e executar o **BindingTest** projeto. O aplicativo irá iniciar e apresentar a captura de tela da esquerda (o `EditText` é inicializada com algum texto, mas você pode tocar para alterá-lo). Quando você tocar em **contagem VOGAIS**, uma notificação do sistema exibe o número das vogais, conforme mostrado no lado direito:

[![Capturas de tela da execução de BindingTest](binding-an-aar-images/12-count-vowels.png)](binding-an-aar-images/12-count-vowels.png#lightbox)

Tente tocar o **contagem CONSOANTES** botão. Além disso, você pode modificar a linha de texto e toque nesses botões novamente para testar diferente vogal e consoante contagens.



### <a name="accessing-aar-resources"></a>Acessando. Recursos de AAR

O Xamarin ferramentas mesclagens o **R** dados das. AAR em seu aplicativo **recurso** classe. Como resultado, você pode acessar. Os recursos de AAR do seu layout (e partir code-behind) da mesma maneira que você acessa recursos que estão no **recursos** caminho do projeto.

Para acessar um recurso de imagem, você deve usar o **Resource.Drawable** nome para a imagem compactada dentro do. AAR. Por exemplo, você pode referenciar **image.png** em de. Arquivo AAR usando `@drawable/image`:

```xml
<ImageView android:src="@drawable/image" ... />
```

Você também pode acessar os layouts de recursos que residem na. AAR. Para fazer isso, use o **Resource.Layout** nome para o layout empacotado dentro do. AAR. Por exemplo:

```csharp
var a = new ArrayAdapter<string>(this, Resource.Layout.row_layout, ...);
```

O **textanalyzer.aar** exemplo contém um arquivo de imagem que reside na **res/drawable/monkey.png**. Permite acesso a esse recurso de imagem e usá-lo em nosso aplicativo de exemplo:

Editar o **BindingTest** layout (**Main.axml**) e adicione um `ImageView` ao final do `LinearLayout` contêiner. Isso `ImageView` exibe a imagem encontrada em  **@drawable/monkey** ; esta imagem será carregada de seção de recursos **textanalyzer.aar**:

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

Compilar e executar o **BindingTest** projeto. O aplicativo irá iniciar e apresentar a captura de tela da esquerda &ndash; ao tocar em **contagem CONSOANTES**, os resultados são exibidos, conforme mostrado no lado direito:

[![BindingTest exibindo consoante contagem](binding-an-aar-images/13-count-consonants.png)](binding-an-aar-images/13-count-consonants.png#lightbox)


Parabéns! Você já associada com êxito uma biblioteca de Java. AAR!



## <a name="summary"></a>Resumo

Neste passo a passo, criamos uma biblioteca de ligações para um. Arquivo AAR, adicionada a biblioteca de ligações para um aplicativo de teste mínimo e executado o aplicativo para verificar que nosso código c# pode chamar código Java que residem em de. Arquivo AAR.
Além disso, podemos estendida que o aplicativo para acessar e exibir um recurso de imagem que reside na. Arquivo AAR.


## <a name="related-links"></a>Links relacionados

- [Criar uma biblioteca de associações de Java (vídeo)](https://university.xamarin.com/classes#10090)
- [Associação de um .JAR](~/android/platform/binding-java-library/binding-a-jar.md)
- [Associação de uma biblioteca Java](~/android/platform/binding-java-library/index.md)
- [AarBinding (exemplo)](https://developer.xamarin.com/samples/monodroid/JavaIntegration/AarBinding)
- [Projeto de 44573 um bug não é possível associar vários arquivos. aar](https://bugzilla.xamarin.com/show_bug.cgi?id=44573)
