---
title: 'Walkthrough: associar uma biblioteca Kotlin do Android'
description: Este artigo fornece uma explicação prática de como criar uma associação do Xamarin. Android para uma biblioteca Kotlin existente, BubblePicker. Ele aborda tópicos como compilar uma biblioteca Kotlin, associá-la e usar a associação em um aplicativo Xamarin. Android.
ms.prod: xamarin
ms.assetid: 5E45451F-514F-4F2B-A6E8-599ED2EFDA2C
ms.technology: xamarin-android
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: af926b518c55bd0d6c73180e512dd669e93778f7
ms.sourcegitcommit: a3f13a216fab4fc20a9adf343895b9d6a54634a5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85853065"
---
# <a name="walkthrough-bind-an-android-kotlin-library"></a>Walkthrough: associar uma biblioteca Kotlin do Android

> [!IMPORTANT]
> No momento, estamos investigando o uso de associação personalizada na plataforma Xamarin. Faça [**esta pesquisa**](https://www.surveymonkey.com/r/KKBHNLT) para informar os futuros esforços de desenvolvimento.

O Xamarin permite que os desenvolvedores móveis criem aplicativos móveis nativos entre plataformas usando o Visual Studio e o C#. Você pode usar os componentes do SDK da plataforma Android prontos para uso, mas, em muitos casos, você também deseja usar SDKs de terceiros escritos para essa plataforma e o Xamarin permite que você faça isso por meio de associações. Para incorporar uma estrutura Android de terceiros em seu aplicativo Xamarin. Android, você precisa criar uma associação Xamarin. Android para ela antes de poder usá-la em seus aplicativos.

A plataforma Android, juntamente com suas ferramentas e linguagens nativas, estão constantemente evoluindo, incluindo a recente introdução da linguagem Kotlin, que é definida eventualmente para substituir o Java. Há vários SDKs de terceiros, que já foram migrados do Java para o Kotlin e nos apresentam novos desafios. Embora o processo de associação de Kotlin seja semelhante ao Java, ele requer etapas adicionais e definições de configuração para compilar e executar com êxito como parte de um aplicativo Xamarin. Android.  

O objetivo deste documento é descrever uma abordagem de alto nível para lidar com esse cenário e fornecer um guia passo a passo detalhado com um exemplo simples.

## <a name="background"></a>Segundo plano

O Kotlin foi lançado em fevereiro de 2016 e foi posicionado como uma alternativa ao compilador Java padrão em Android Studio por 2017. Mais tarde, no 2019, o Google anunciou que a linguagem de programação Kotlin se tornou sua linguagem preferida para desenvolvedores de aplicativos Android. A abordagem de associação de alto nível é semelhante ao [processo de vinculação de bibliotecas Java regulares](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/) com algumas etapas específicas de Kotlin importantes.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este passo a passo, você precisará de:

- [Android Studio](https://developer.android.com/studio)
- [Visual Studio para Mac](https://visualstudio.microsoft.com/downloads)
- [Descompilador de Java](http://java-decompiler.github.io/)

## <a name="build-a-native-library"></a>Criar uma biblioteca nativa

A primeira etapa é criar uma biblioteca Kotlin nativa usando Android Studio. A biblioteca é geralmente fornecida por um desenvolvedor de terceiros ou disponível no [repositório Maven do Google](https://maven.google.com/web/index.html) e em outros repositórios remotos. Por exemplo, neste tutorial, uma associação para a biblioteca Kotlin do seletor de bolha é criada:

![Demonstração do GitHub BubblePicker](walkthrough-images/github-bubblepicker-demo.png)

1. Baixe [o código-fonte](https://github.com/igalata/Bubble-Picker/archive/develop.zip) do GitHub para a biblioteca e descompacte-o em uma pasta local **bolha-Picker**.

1. Inicie o Android Studio e selecione **abrir uma opção de menu Android Studio projeto existente** escolhendo a pasta local do seletor de bolhas:

    ![Android Studio Abrir projeto](walkthrough-images/android-studio-open-project.png)

1. Verifique se o Android Studio está atualizado, incluindo gradle. O código-fonte pode ser criado com êxito em Android Studio v 3.5.3, gradle v 5.4.1. Instruções sobre como atualizar o gradle para a versão mais recente do gradle [podem ser encontradas aqui](https://gradle.org/install/).

1. Verifique se o SDK do Android necessário está instalado. O código-fonte requer SDK do Android v25. Abra a opção de menu **ferramentas > SDK Manager** para instalar os componentes do SDK.

1. Atualize e sincronize o arquivo de configuração **Build. gradle** principal localizado na raiz da pasta do projeto:

    - Definir a versão do Kotlin como 1.3.10

        ```gradle
        buildscript {
            ext.kotlin_version = '1.3.10'
        }
        ```

    - Registrar o repositório Maven padrão do Google para que a dependência da biblioteca de suporte possa ser resolvida:

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

    - Depois que o arquivo de configuração for atualizado, ele estará fora de sincronia e o gradle mostrará o botão **sincronizar agora** , pressione-o e aguarde a conclusão do processo de sincronização:

        ![Android Studio gradle sincronizar agora](walkthrough-images/android-studio-gradle-syncnow.png)

        > [!TIP]
        > O cache de dependência do gradle pode estar corrompido, isso às vezes ocorre após um tempo limite de conexão de rede. Rebaixar dependências e sincronizar o projeto (requer rede).

        > [!TIP]
        > O estado de um daemon (processo de compilação) do gradle pode estar corrompido. Parar todos os daemons do gradle pode resolver esse problema. Pare os processos de compilação do gradle (requer reinicialização). No caso de processos gradle corrompidos, você também pode tentar fechar o IDE e, em seguida, finalizar todos os processos Java.

        > [!TIP]
        > Seu projeto pode estar usando um plug-in de terceiros, que não é compatível com os outros plug-ins no projeto ou com a versão do gradle solicitada pelo projeto.

1. Abra o menu gradle à direita, navegue até o menu **bubblepicker > tarefas** , execute a tarefa de **compilação** tocando duas vezes nela e aguarde a conclusão do processo de compilação:

    ![Android Studio executar tarefa gradle](walkthrough-images/android-studio-gradle-execute-task.png)

1. Abra o navegador arquivos de pasta raiz e navegue até a pasta build: **bolha-seletor-> bubblepicker-> Build-> Outputs-> AAR**, salve o arquivo **bubblepicker-Release. aar** como **bubblepicker-v 1.0. aar**, esse arquivo será usado posteriormente no processo de associação:

    ![Saída de Android Studio AAR](walkthrough-images/android-studio-aar-output.png)

O arquivo AAR é um arquivo Android, que contém o código-fonte Kotlin compilado e os ativos, exigidos pelo Android para executar um aplicativo usando esse SDK.  

## <a name="prepare-metadata"></a>Preparar metadados

A segunda etapa é preparar o arquivo de transformação de metadados, que é usado pelo Xamarin. Android para gerar as respectivas classes C#. Um projeto de associação do Xamarin. Android descobrirá todas as classes e membros nativos de um determinado arquivo do Android, gerando subsequentemente um arquivo XML com os metadados apropriados. O arquivo de transformação de metadados criado manualmente é aplicado à linha de base gerada anteriormente para criar o arquivo de definição XML final usado para gerar o código C#.

Os metadados usam a sintaxe [XPath](https://www.w3.org/TR/xpath/)   e são usados pelo gerador de associações para influenciar a criação do assembly de associação. O artigo de [metadados de associação Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata) fornece mais informações sobre transformações, que podem ser aplicadas:

1. Criar um arquivo de **Metadata.xml** vazio:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <metadata>
    </metadata>
    ```

1. Definir transformações XML:

- A biblioteca Kotlin nativa tem duas dependências, que você não deseja expor ao C# World, definir duas transformações para ignorá-las completamente. Importante para dizer que os membros nativos não serão removidos do binário resultante, somente as classes C# não serão geradas. O [descompilador Java](http://java-decompiler.github.io/) pode ser usado para identificar as dependências. Execute a ferramenta e abra o arquivo AAR criado anteriormente, como resultado, a estrutura do arquivo morto do Android será mostrada, refletindo todas as dependências, valores, recursos, manifesto e classes:  

    ![Dependências de descompilador Java](walkthrough-images/java-decompiler-dependencies.png)

    As transformações para ignorar o processamento desses pacotes são definidas usando instruções XPath:

    ```xml
    <remove-node path="/api/package[starts-with(@name,'org.jbox2d')]" />
    <remove-node path="/api/package[starts-with(@name,'org.slf4j')]" />
    ```

- A `BubblePicker` classe nativa tem dois métodos `getBackgroundColor` e `setBackgroundColor` a transformação a seguir irá alterá-lo em uma `BackgroundColor` Propriedade C#:

    ```xml
    <attr path="/api/package[@name='com.igalata.bubblepicker.rendering']/class[@name='BubblePicker']/method[@name='getBackground' and count(parameter)=0]" name="propertyName">BackgroundColor</attr>
    <attr path="/api/package[@name='com.igalata.bubblepicker.rendering']/class[@name='BubblePicker']/method[@name='setBackground' and count(parameter)=1 and parameter[1][@type='int']]" name="propertyName">BackgroundColor</attr>
    ```

- Tipos não assinados `UInt, UShort, ULong, UByte` exigem tratamento especial. Para esses tipos, Kotlin altera os nomes de método e os tipos de parâmetros automaticamente, que é refletido no código gerado:

    ```Kotlin
    public open fun fooUIntMethod(value: UInt) : String {
        return "fooUIntMethod${value}"
    }
    ```

    Esse código é compilado no seguinte código de byte Java:

    ```Java byte code
    @NotNull
    public String fooUIntMethod-WZ4Q5Ns(int value) {
    return "fooUIntMethod" + UInt.toString-impl(value);
    }
    ```

    Além disso, tipos relacionados como `UIntArray, UShortArray, ULongArray, UByteArray` também são afetados pelo Kotlin. O nome do método é alterado para incluir um sufixo adicional e os parâmetros são alterados para uma matriz de elementos de versões assinadas dos mesmos tipos. No exemplo abaixo, um parâmetro do tipo `UIntArray` é convertido automaticamente em `int[]` e o nome do método é alterado de `fooUIntArrayMethod` para `fooUIntArrayMethod--ajY-9A` . O último é descoberto pelas ferramentas do Xamarin. Android e geradas como um nome de método válido:

    ```Kotlin
    public open fun fooUIntArrayMethod(value: UIntArray) : String {
        return "fooUIntArrayMethod${value.size}"
    }
    ```

    Esse código é compilado no seguinte código de byte Java:

    ```Java byte code
    @NotNull
    public String fooUIntArrayMethod--ajY-9A(@NotNull int[] value) {
        Intrinsics.checkParameterIsNotNull(value, "value");
        return "fooUIntArrayMethod" + UIntArray.getSize-impl(value);
    }
    ```

    Para dar a ele um nome significativo, os metadados a seguir podem ser adicionados ao **Metadata.xml**, que atualizará o nome de volta para originalmente definido no código Kotlin:

    ```xml
    <attr path="/api/package[@name='com.microsoft.simplekotlinlib']/class[@name='FooClass']/method[@name='fooUIntArrayMethod--ajY-9A']" name="managedName">fooUIntArrayMethod</attr>
    ```

    No exemplo de BubblePicker, não há nenhum membro usando tipos não assinados, portanto, nenhuma alteração adicional é necessária.

- Membros de Kotlin com parâmetros genéricos são transformados por padrão em parâmetros de Java.`Lang.Object` Escreva. Por exemplo, um método Kotlin tem um parâmetro genérico \<T> :

    ```Kotlin
    public open fun <T>fooGenericMethod(value: T) : String {
    return "fooGenericMethod${value}"
    }
    ```

    Depois que uma associação do Xamarin. Android é gerada, o método é exposto ao C#, como a seguir:

    ```csharp
    [Register ("fooGenericMethod", "(Ljava/lang/Object;)Ljava/lang/String;", "GetFooGenericMethod_Ljava_lang_Object_Handler")]
    [JavaTypeParameters (new string[] {
        "T"
    })]

    public virtual string FooGenericMethod (Java.Lang.Object value);
    ```

    Os genéricos Java e Kotlin não têm suporte de associações do Xamarin. Android, portanto, um método C# generalizado para acessar a API genérica é criado. Como solução alternativa, você pode criar uma biblioteca Kotlin do wrapper e expor as APIs necessárias de forma forte, sem genéricos. Como alternativa, você pode criar auxiliares no lado do C# para resolver o problema da mesma maneira por meio de APIs de tipo forte.

    > [!TIP]
    > Ao transformar os metadados, todas as alterações podem ser aplicadas à associação gerada. O artigo da [biblioteca Java de associação](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/) explica em detalhes como os metadados são gerados e processados.

## <a name="build-a-binding-library"></a>Criar uma biblioteca de associação

A próxima etapa é criar um projeto de associação do Xamarin. Android usando o modelo de associação do Visual Studio, adicionar metadados necessários, referências nativas e, em seguida, compilar o projeto para produzir uma biblioteca de consumo:

1. Abra Visual Studio para Mac e crie um novo projeto de biblioteca de associação do Xamarin. Android, dê a ele um nome, neste caso, **testBubblePicker. Binding** e conclua o assistente. O modelo de associação do Xamarin. Android está localizado pelo seguinte caminho: biblioteca de **> do Android > biblioteca de associação**:

    ![Criar associação do Visual Studio](walkthrough-images/visual-studio-create-binding.png)

    Na pasta transformações, há três arquivos de transformação principais:

    - **Metadata.xml** – permite que as alterações sejam feitas na API final, como alterar o namespace da Associação gerada.
    - **EnumFields.xml** – contém o mapeamento entre constantes Java int e enumerações C#.
    - **EnumMethods.xml** – permite alterar os parâmetros do método e retornar tipos de constantes Java int para enums C#.

    Mantenha os arquivos de **EnumFields.xml** e **EnumMethods.xml** vazios e atualize o **Metadata.xml** para definir suas transformações.

1. Substitua o arquivo de **transformações/Metadata.xml** existente pelo arquivo de **Metadata.xml** criado na etapa anterior. Na janela Propriedades, verifique se a ação de **compilação** do arquivo está definida **como**Transformation:

    ![Metadados do Visual Studio](walkthrough-images/visual-studio-metadata.png)

1. Adicione o arquivo **bubblepicker-v 1.0. aar** criado na etapa 1 ao projeto de associação como uma referência nativa. Para adicionar referências de biblioteca nativa, abra o localizador e navegue até a pasta com o arquivo morto do Android. Arraste e solte o arquivo morto na pasta jars em Gerenciador de Soluções. Como alternativa, você pode usar a opção de menu **Adicionar** contexto na pasta jars e escolher **arquivos existentes...**. Escolha copiar o arquivo para o diretório para os fins deste guia. Certifique-se de verificar se a **ação de compilação** está definida como **LibraryProjectZip**:

    ![Referência nativa do Visual Studio](walkthrough-images/visual-studio-native-reference.png)

1. Adicione uma referência ao pacote [NuGet Xamarin. Kotlin. StdLib](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/) . Este pacote é uma associação para a biblioteca padrão do Kotlin. Sem esse pacote, a associação só funcionará se a biblioteca Kotlin não usar tipos específicos de Kotlin; caso contrário, todos esses membros não serão expostos ao C# e qualquer aplicativo que tentar consumir a associação falhará em tempo de execução.

    > [!TIP]
    > Devido a uma limitação do Xamarin. Android, as ferramentas de associação apenas de um único arquivo Android (AAR) podem ser adicionadas por projeto de associação. Se vários arquivos AAR precisarem ser incluídos, vários projetos Xamarin. Android serão necessários, um por cada AAR. Se esse for o caso deste passo a passos, as quatro ações anteriores desta etapa teriam que ser repetidas para cada arquivo morto. Como opção alternativa, é possível mesclar manualmente vários arquivos mortos do Android como um único arquivo e, como resultado, você pode usar um único projeto de associação Xamarin. Android.

1. A ação final é criar a biblioteca e não ter nenhum erro de compilação. No caso de erros de compilação, eles podem ser endereçados e tratados usando o arquivo Metadata.xml, que você criou anteriormente adicionando metadados de transformação XML, que adicionarão, removerão ou renomearão os membros da biblioteca.

## <a name="consume-the-binding-library"></a>Consumir a biblioteca de associação

A etapa final é consumir a biblioteca de associação do Xamarin. Android em um aplicativo Xamarin. Android. Crie um novo projeto Xamarin. Android, adicione referência à biblioteca de associação e a interface do usuário do seletor de bolha:

1. Crie um projeto Xamarin. Android. Use o aplicativo **android > > aplicativo Android** como um ponto de partida e selecione a opção **mais recente e melhor** à medida que você visa as plataformas para evitar problemas de compatibilidade. Todas as etapas a seguir destinam-se a este projeto:

    ![Criar aplicativo do Visual Studio](walkthrough-images/visual-studio-create-app.png)

1. Adicione uma referência de projeto ao projeto de associação ou adicione uma referência à DLL criada anteriormente:

    ![Adicionar Reference.png de associação do Visual Studio](walkthrough-images/visual-studio-add-binding-reference.png)

1. Adicione uma referência ao pacote [NuGet xamarin. Kotlin. StdLib](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/) , que você adicionou ao projeto de associação Xamarin. Android anteriormente. Ele adiciona suporte a qualquer tipo específico de Kotlin que precise de uma mão no tempo de execução.  Sem esse pacote, o aplicativo pode ser compilado, mas falhará em tempo de execução:

    ![Adicionar NuGet do StdLib do Visual Studio](walkthrough-images/visual-studio-add-stdlib-nuget.png)

1. Adicione o `BubblePicker` controle ao layout do Android para `MainActivity` . Abra o arquivo **testBubblePicker/Resources/layout/content_main.xml** e acrescente o nó de controle BubblePicker como o último elemento do controle RelativeLayout raiz:

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

1. Atualize o código-fonte do aplicativo e adicione a lógica de inicialização ao `MainActivity` , que ativa o SDK do seletor de bolhas:

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

    `BubblePickerAdapter`e `BubblePickerListener` são duas classes a serem criadas a partir do zero, que lidam com a interação de controle e dados de bolhas:

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

1. Execute o aplicativo, que deve renderizar a interface do usuário do seletor de bolhas:

    ![Demonstração do BubblePicker](walkthrough-images/bubble-picker-demo.png)

    O exemplo requer código adicional para renderizar as interações em estilo e manipular elementos, mas o `BubblePicker` controle foi criado e ativado com êxito.

Parabéns! Você criou com êxito um aplicativo Xamarin. Android e uma biblioteca de associação, que consome uma biblioteca Kotlin.  

Agora você deve ter um aplicativo Xamarin. Android básico que usa uma biblioteca Kotlin nativa por meio de uma biblioteca de associação do Xamarin. Android. Este passo a passo usa intencionalmente um exemplo básico para enfatizar melhor os principais conceitos que estão sendo introduzidos. Em cenários reais, provavelmente será necessário expor um número maior de APIs e aplicar transformações de metadados a elas.

## <a name="related-links"></a>Links relacionados

- [Android Studio](https://developer.android.com/studio)
- [Instalação do gradle](https://gradle.org/install/)
- [Visual Studio para Mac](https://visualstudio.microsoft.com/downloads)
- [Descompilador de Java](http://java-decompiler.github.io/)
- [Biblioteca BubblePicker Kotlin](https://github.com/igalata/Bubble-Picker)
- [Biblioteca Java de associação](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)
- [XPath](https://www.w3.org/TR/xpath/)
- [Metadados de associação Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata)
- [Xamarin. Kotlin. StdLib NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)
- [Repositório de projetos de exemplo](https://github.com/alexeystrakh/xamarin-binding-kotlin-framework)
