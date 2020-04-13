---
title: 'Passo a passo: Ligue uma biblioteca Android Kotlin'
description: Este artigo fornece um passo a passo prático da criação de um vinculação Xamarin.Android para uma biblioteca Kotlin existente, BubblePicker. Ele abrange tópicos como compilar uma biblioteca Kotlin, vinculá-la e usar a vinculação em um aplicativo Xamarin.Android.
ms.prod: xamarin
ms.assetid: 5E45451F-514F-4F2B-A6E8-599ED2EFDA2C
ms.technology: xamarin-android
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: cbd7c796cd13aa45dc107bddf06ca44d6adbdf9d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291740"
---
# <a name="walkthrough-bind-an-android-kotlin-library"></a>Passo a passo: Ligue uma biblioteca Android Kotlin

A Xamarin permite que desenvolvedores móveis criem aplicativos móveis nativos multiplataforma usando o Visual Studio e o C#. Você pode usar os componentes SDK da plataforma Android fora da caixa, mas em muitos casos você também quer usar SDKs de terceiros escritos para essa plataforma e Xamarin permite que você faça isso através de vinculações. Para incorporar uma estrutura Android de terceiros ao seu aplicativo Xamarin.Android, você precisa criar uma vinculação Xamarin.Android para ele antes de poder usá-lo em seus aplicativos.

A plataforma Android, juntamente com suas línguas nativas e ferramentas, estão em constante evolução, incluindo a recente introdução da língua Kotlin, que é definida eventualmente para substituir Java. Há uma série de SDKs de partido 3d, que já foram migrados de Java para Kotlin e nos apresenta novos desafios. Embora o processo de vinculação kotlin seja semelhante ao Java, ele requer etapas adicionais e configurações para construir e executar com sucesso como parte de um aplicativo Xamarin.Android.  

O objetivo deste documento é traçar uma abordagem de alto nível para abordar esse cenário e fornecer um guia passo-a-passo detalhado com um exemplo simples.

## <a name="background"></a>Segundo plano

Kotlin foi lançado em fevereiro de 2016 e foi posicionado como uma alternativa ao compilador Java padrão no Android Studio em 2017. Mais tarde, em 2019, o Google anunciou que a linguagem de programação Kotlin se tornaria sua linguagem preferida para desenvolvedores de aplicativos android. A abordagem de ligação de alto nível é semelhante [ao processo de vinculação de bibliotecas Java regulares](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/) com algumas etapas específicas do Kotlin importantes.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este passo a passo, você precisará de:

- [Estúdio Android](https://developer.android.com/studio)
- [Visual Studio para Mac](https://visualstudio.microsoft.com/downloads)
- [Descompilador Java](http://java-decompiler.github.io/)

## <a name="build-a-native-library"></a>Construa uma biblioteca nativa

O primeiro passo é construir uma biblioteca Kotlin nativa usando o Android Studio. A biblioteca geralmente é fornecida por um desenvolvedor de terceiros ou disponível [no repositório Maven do Google](https://maven.google.com/web/index.html) e outros repositórios remotos. Como exemplo, neste tutorial é criada uma vinculação para a Biblioteca Kotlin do Bubble Picker:

![Demonstração do GitHub BubblePicker](walkthrough-images/github-bubblepicker-demo.png)

1. Baixe [o código-fonte](https://github.com/igalata/Bubble-Picker/archive/develop.zip) do GitHub para a biblioteca e desempacote-o para uma pasta local **Bubble-Picker**.

1. Inicie o Android Studio e selecione Abra uma opção **de menu de projeto Android Studio existente** escolhendo a pasta local Bubble-Picker:

    ![Projeto Aberto do Estúdio Android](walkthrough-images/android-studio-open-project.png)

1. Verifique se o Android Studio está atualizado, incluindo Gradle. O código-fonte pode ser construído com sucesso no Android Studio v3.5.3, Gradle v5.4.1. Instruções sobre como atualizar Gradle para a versão gradle mais recente [podem ser encontradas aqui](https://gradle.org/install/).

1. Verifique se o SDK do Android necessário está instalado. O código-fonte requer Android SDK v25. Abrir ferramentas > opção de menu **sdk manager** para instalar componentes SDK.

1. Atualize e sincronize o arquivo principal de configuração **build.gradle** localizado na raiz da pasta do projeto:

    - Defina a versão Kotlin para 1.3.10

        ```gradle
        buildscript {
            ext.kotlin_version = '1.3.10'
        }
        ```

    - Registre o repositório Maven padrão do Google para que a dependência da biblioteca de suporte possa ser resolvida:

        ```gradle
        allprojects {
            repositories {
                jcenter()
                maven {
                    url "https://maven.google.com"
                }
            }
        }
        ```

    - Uma vez que o arquivo de configuração é atualizado, ele está fora de sincronia e Gradle mostra o botão **Sync Now,** pressione-o e aguarde que o processo de sincronização seja concluído:

        ![Android Studio Gradle Sync Now](walkthrough-images/android-studio-gradle-syncnow.png)

        > [!TIP]
        > O cache de dependência do Gradle pode estar corrompido, isso às vezes ocorre após um tempo de conexão de rede. Rebaixe dependências e sincronia do projeto (requer rede).

        > [!TIP]
        > O estado de um processo de construção gradle (daemon) pode ser corrupto. Parar todos os daemons gradle pode resolver este problema. Parar os processos de compilação do Gradle (requer reinicialização). No caso de processos Gradle corrompidos, você também pode tentar fechar o IDE e, em seguida, matar todos os processos Java.

        > [!TIP]
        > Seu projeto pode estar usando um plugin de terceiros, que não é compatível com os outros plugins do projeto ou com a versão do Gradle solicitada pelo projeto.

1. Abra o menu Gradle à direita, navegue até o menu **bubblepicker > Tasks,** execute a tarefa **de compilação** tocando duas vezes nele e aguarde que o processo de compilação seja concluído:

    ![Tarefa de execução do Android Studio Gradle](walkthrough-images/android-studio-gradle-execute-task.png)

1. Abra o navegador de arquivos de pasta raiz e navegue até a pasta de compilação: **Bubble-Picker-> bubblepicker -> build -> outputs -> aar**, salve o arquivo **bubblepicker-release.aar** como **bubblepicker-v1.0.aar**, este arquivo será usado mais tarde no processo de vinculação:

    ![Saída Android Studio AAR](walkthrough-images/android-studio-aar-output.png)

O arquivo AAR é um arquivo Android, que contém o código fonte e os ativos do Kotlin compilados, exigidos pelo Android para executar um aplicativo usando este SDK.  

## <a name="prepare-metadata"></a>Preparar metadados

O segundo passo é preparar o arquivo de transformação de metadados, que é usado pelo Xamarin.Android para gerar suas respectivas classes C#. Um Projeto de Vinculação Xamarin.Android descobrirá todas as classes e membros nativos de um determinado arquivo Android, gerando posteriormente um arquivo XML com os metadados apropriados. O arquivo de transformação de metadados criado manualmente é então aplicado à linha de base gerada anteriormente para criar o arquivo de definição XML final usado para gerar o código C#.

Os metadados usam a sintaxe [XPath](https://www.w3.org/TR/xpath/) e são usados pelo Gerador de Vinculações para influenciar a criação do conjunto de vinculações. O artigo [Java Binding Metadata](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata) fornece mais informações sobre transformações, que podem ser aplicadas:

1. Criar um arquivo **Metadata.xml** vazio:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <metadata>
    </metadata>
    ```

1. Definir transformações xml:

- A biblioteca nativa kotlin tem duas dependências, que você não quer expor ao mundo C#, definir duas transformações para ignorá-las completamente. Importante dizer que os membros nativos não serão retirados do binário resultante, apenas as classes C# não serão geradas. [Java Decompiler](http://java-decompiler.github.io/) pode ser usado para identificar as dependências. Execute a ferramenta e abra o arquivo AAR criado anteriormente, como resultado, a estrutura do arquivo Android será mostrada, refletindo todas as dependências, valores, recursos, manifesto e classes:  

    ![Dependências do Decompiler Java](walkthrough-images/java-decompiler-dependencies.png)

    As transformações para pular o processamento desses pacotes são definidas usando instruções do XPath:

    ```xml
    <remove-node path="/api/package[starts-with(@name,'org.jbox2d')]" />
    <remove-node path="/api/package[starts-with(@name,'org.slf4j')]" />
    ```

- A `BubblePicker` classe nativa tem `getBackgroundColor` `setBackgroundColor` dois métodos e a seguinte `BackgroundColor` transformação irá transformá-la em uma propriedade C#:

    ```xml
    <attr path="/api/package[@name='com.igalata.bubblepicker.rendering']/class[@name='BubblePicker']/method[@name='getBackground' and count(parameter)=0]" name="propertyName">BackgroundColor</attr>
    <attr path="/api/package[@name='com.igalata.bubblepicker.rendering']/class[@name='BubblePicker']/method[@name='setBackground' and count(parameter)=1 and parameter[1][@type='int']]" name="propertyName">BackgroundColor</attr>
    ```

- Tipos não `UInt, UShort, ULong, UByte` assinados requerem tratamento especial. Para esses tipos, o Kotlin altera automaticamente os nomes e os tipos de parâmetros do método, o que se reflete no código gerado:

    ```Kotlin
    public open fun fooUIntMethod(value: UInt) : String {
        return "fooUIntMethod${value}"
    }
    ```

    Este código é compilado no seguinte código de byte java:

    ```Java byte code
    @NotNull
    public String fooUIntMethod-WZ4Q5Ns(int value) {
    return "fooUIntMethod" + UInt.toString-impl(value);
    }
    ```

    Além disso, tipos `UIntArray, UShortArray, ULongArray, UByteArray` relacionados, como também são afetados pelo Kotlin. O nome do método é alterado para incluir um sufixo adicional e os parâmetros são alterados para uma matriz de elementos de versões assinadas dos mesmos tipos. No exemplo abaixo, um `UIntArray` parâmetro de tipo `int[]` é convertido automaticamente em `fooUIntArrayMethod` `fooUIntArrayMethod--ajY-9A`e o nome do método é alterado de para . Este último é descoberto pelas ferramentas Xamarin.Android e gerado como um nome de método válido:

    ```Kotlin
    public open fun fooUIntArrayMethod(value: UIntArray) : String {
        return "fooUIntArrayMethod${value.size}"
    }
    ```

    Este código é compilado no seguinte código de byte java:

    ```Java byte code
    @NotNull
    public String fooUIntArrayMethod--ajY-9A(@NotNull int[] value) {
        Intrinsics.checkParameterIsNotNull(value, "value");
        return "fooUIntArrayMethod" + UIntArray.getSize-impl(value);
    }
    ```

    Para dar-lhe um nome significativo, os seguintes metadados podem ser adicionados ao **Metadata.xml**, que atualizará o nome de volta ao originalmente definido no código Kotlin:

    ```xml
    <attr path="/api/package[@name='com.microsoft.simplekotlinlib']/class[@name='FooClass']/method[@name='fooUIntArrayMethod--ajY-9A']" name="managedName">fooUIntArrayMethod</attr>
    ```

    Na amostra BubblePicker, não há membros usando tipos não assinados, portanto, não são necessárias alterações adicionais.

- Membros kotlin com parâmetros genéricos por padrão transformados em parâmetros de Java.`Lang.Object` Tipo. Por exemplo, um método Kotlin \<tem um parâmetro genérico T>:

    ```Kotlin
    public open fun <T>fooGenericMethod(value: T) : String {
    return "fooGenericMethod${value}"
    }
    ```

    Uma vez que uma ligação Xamarin.Android é gerada, o método é exposto a C# como abaixo:

    ```csharp
    [Register ("fooGenericMethod", "(Ljava/lang/Object;)Ljava/lang/String;", "GetFooGenericMethod_Ljava_lang_Object_Handler")]
    [JavaTypeParameters (new string[] {
        "T"
    })]

    public virtual string FooGenericMethod (Java.Lang.Object value);
    ```

    Os genéricos Java e Kotlin não são suportados por ligações Xamarin.Android, portanto, um método C# generalizado para acessar a API genérica é criado. Como um trabalho em torno de você pode criar uma biblioteca Kotlin invólucro e expor APIs necessárias de uma maneira forte, sem genéricos. Alternativamente, você pode criar ajudantes no lado C# para resolver o problema da mesma forma através de APIs de digitamento forte.

    > [!TIP]
    > Ao transformar os metadados, quaisquer alterações poderiam ser aplicadas à vinculação gerada. O artigo [da Biblioteca Java vinculante](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/) explica em detalhes como os metadados são gerados e processados.

## <a name="build-a-binding-library"></a>Construa uma biblioteca de vinculação

O próximo passo é criar um projeto de vinculação Xamarin.Android usando o modelo de vinculação do Visual Studio, adicionar metadados necessários, referências nativas e, em seguida, construir o projeto para produzir uma biblioteca consumível:

1. Abra o Visual Studio para Mac e crie um novo projeto da Biblioteca vinculante Xamarin.Android, dê-lhe um nome, neste caso **testbubbleBubblePicker.Binding** e complete o assistente. O modelo de vinculação Xamarin.Android está localizado pelo seguinte caminho: **Biblioteca > Android > Biblioteca de Vinculação**:

    ![Estúdio visual criar vinculação](walkthrough-images/visual-studio-create-binding.png)

    Na pasta Transformações há três arquivos principais de transformação:

    - **Metadata.xml** – Permite que alterações sejam feitas na API final, como alterar o namespace da vinculação gerada.
    - **EnumFields.xml** – Contém o mapeamento entre as constantes java int e c# enums.
    - **EnumMethods.xml** – Permite alterar parâmetros do método e tipos de retorno de constantes java int para enums C#.

    Mantenha vazio os arquivos **EnumFields.xml** e **EnumMethods.xml** e atualize o **Metadata.xml** para definir suas transformações.

1. Substitua o arquivo **Transformações/Metadados.xml** existentes pelo arquivo **Metadata.xml** criado na etapa anterior. Na janela propriedades, verifique se o arquivo **Build Action** está definido como **TransformationFile**:

    ![Metadados do Estúdio Visual](walkthrough-images/visual-studio-metadata.png)

1. Adicione o arquivo **bubblepicker-v1.0.aar** que você construiu no Passo 1 ao projeto de vinculação como referência nativa. Para adicionar referências de biblioteca nativa, abra o localizador e navegue até a pasta com o arquivo Android. Arraste e solte o arquivo na pasta Jars no Solution Explorer. Alternativamente, você pode usar a opção **Adicionar** menu de contexto na pasta Jars e escolher **Arquivos Existentes...**. Escolha copiar o arquivo para o diretório para os fins deste passo a passo. Certifique-se de verificar se a **Ação de compilação** está definida como **LibraryProjectZip**:

    ![Referência nativa do estúdio visual](walkthrough-images/visual-studio-native-reference.png)

1. Adicione uma referência ao pacote [Xamarin.Kotlin.StdLib NuGet.](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/) Este pacote é um vinculo para a Biblioteca Padrão Kotlin. Sem este pacote, a vinculação só funcionará se a biblioteca Kotlin não usar nenhum tipo específico kotlin, caso contrário, todos esses membros não serão expostos a C# e qualquer aplicativo que tentar consumir a vinculação falhará em tempo de execução.

    > [!TIP]
    > Devido a uma limitação do Xamarin.Android, ferramentas de vinculação apenas um único arquivo Android (AAR) pode ser adicionado por projeto de vinculação. Se vários arquivos AAR precisarem ser incluídos, então vários projetos Xamarin.Android são necessários, um por cada AAR. Se esse fosse o caso deste passo a passo, então as quatro ações anteriores desta etapa teriam que ser repetidas para cada arquivo. Como uma opção alternativa, é possível mesclar manualmente vários arquivos Android como um único arquivo e, como resultado, você pode usar um único projeto de vinculação Xamarin.Android.

1. A ação final é construir a biblioteca e fazer não ter nenhum erro de compilação. Em caso de erros de compilação, eles podem ser endereçados e manipulados usando o arquivo Metadata.xml, que você criou anteriormente adicionando metadados de transformação xml, que adicionará, removerá ou renomeará membros da biblioteca.

## <a name="consume-the-binding-library"></a>Consumir a biblioteca de vinculação

O passo final é consumir a biblioteca de vinculação Xamarin.Android em um aplicativo Xamarin.Android. Crie um novo projeto Xamarin.Android, adicione referência à biblioteca de vinculação e torne a II do Bubble Picker:

1. Crie o projeto Xamarin.Android. Use o **Aplicativo para > do Android > aplicativo Android** como ponto de partida e selecione A opção Mais Recente e **Maior** como você visa plataformas para evitar problemas de compatibilidade. Todas as seguintes etapas visam este projeto:

    ![Visual Studio Create App](walkthrough-images/visual-studio-create-app.png)

1. Adicione uma referência de projeto ao projeto de vinculação ou adicione uma referência que a DLL criou anteriormente:

    ![Visual Studio Adicionar referência de vinculação.png](walkthrough-images/visual-studio-add-binding-reference.png)

1. Adicione uma referência ao pacote [Xamarin.Kotlin.StdLib NuGet,](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/) que você adicionou ao projeto de vinculação Xamarin.Android anteriormente. Ele adiciona suporte a quaisquer tipos específicos kotlin que precisam ser entregues em tempo de execução.  Sem este pacote, o aplicativo pode ser compilado, mas travará em tempo de execução:

    ![Visual Studio Adicionar StdLib NuGet](walkthrough-images/visual-studio-add-stdlib-nuget.png)

1. Adicione `BubblePicker` o controle ao `MainActivity`layout do Android para . Abra **o testeBubblePicker/Resources/layout/content_main.xml** e apêndice o nó de controle BubblePicker como o último elemento do controle de layout relativo raiz:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <RelativeLayout …>
        …
        <com.igalata.bubblepicker.rendering.BubblePicker
            android:id="@+id/picker"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:backgroundColor="@android:color/white" />
    </RelativeLayout>
    ```

1. Atualize o código-fonte do aplicativo e `MainActivity`adicione a lógica de inicialização ao , que ativa o Bubble Picker SDK:

    ```csharp
    protected override void OnCreate(Bundle savedInstanceState)
    {
        ...
        var picker = FindViewById<BubblePicker>(Resource.Id.picker);
        picker.BubbleSize = 20;
        picker.Adapter = new BubblePickerAdapter();
        picker.Listener = new BubblePickerListener(picker);
        ...
    }
    ```

    `BubblePickerAdapter`e `BubblePickerListener` são duas classes a serem criadas a partir do zero, que lidam com os dados das bolhas e a interação de controle:

    ```csharp
    public class BubblePickerAdapter : Java.Lang.Object, IBubblePickerAdapter
    {
        private List<string> _bubbles = new List<string>();
        public int TotalCount => _bubbles.Count;
        public BubblePickerAdapter()
        {
            for (int i = 0; i < 10; i++)
            {
                _bubbles.Add($"Item {i}");
            }
        }

        public PickerItem GetItem(int itemIndex)
        {
            if (itemIndex < 0 || itemIndex >= _bubbles.Count)
                return null;

            var result = _bubbles[itemIndex];
            var item = new PickerItem(result);
            return item;
        }
    }

    public class BubblePickerListener : Java.Lang.Object, IBubblePickerListener
    {
        public View Picker { get; }
        public BubblePickerListener(View picker)
        {
            Picker = picker;
        }

        public void OnBubbleDeselected(PickerItem item)
        {
            Snackbar.Make(Picker, $"Deselected: {item.Title}", Snackbar.LengthLong)
                .SetAction("Action", (Android.Views.View.IOnClickListener)null)
                .Show();
        }

        public void OnBubbleSelected(PickerItem item)
        {
            Snackbar.Make(Picker, $"Selected: {item.Title}", Snackbar.LengthLong)
            .SetAction("Action", (Android.Views.View.IOnClickListener)null)
            .Show();
        }
    }
    ```

1. Execute o aplicativo, que deve renderizar a ui bubble picker:

    ![Demonstração bubblepicker](walkthrough-images/bubble-picker-demo.png)

    A amostra requer código adicional para renderizar `BubblePicker` o estilo dos elementos e lidar com interações, mas o controle foi criado e ativado com sucesso.

Parabéns! Você criou com sucesso um aplicativo Xamarin.Android e uma biblioteca de vinculação, que consome uma biblioteca Kotlin.  

Agora você deve ter um aplicativo básico xamarin.Android que usa uma biblioteca Kotlin nativa através de uma biblioteca de vinculação Xamarin.Android. Este passo a passo intencionalmente usa um exemplo básico para enfatizar melhor os conceitos-chave que estão sendo introduzidos. Em cenários do mundo real, você provavelmente será obrigado a expor um maior número de APIs e aplicar transformações de metadados a elas.

## <a name="related-links"></a>Links relacionados

- [Estúdio Android](https://developer.android.com/studio)
- [Instalação de Gradle](https://gradle.org/install/)
- [Visual Studio para Mac](https://visualstudio.microsoft.com/downloads)
- [Descompilador Java](http://java-decompiler.github.io/)
- [Biblioteca BubblePicker Kotlin](https://github.com/igalata/Bubble-Picker)
- [Biblioteca Java vinculante](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)
- [Xpath](https://www.w3.org/TR/xpath/)
- [Metadados de vinculação java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata)
- [Xamarin.Kotlin.StdLib NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)
- [Repositório de projetos de amostra](https://github.com/alexeystrakh/xamarin-binding-kotlin-framework)
