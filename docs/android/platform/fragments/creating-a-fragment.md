---
title: Criando um fragmento
ms.prod: xamarin
ms.assetid: F2997242-BC29-1440-7F1A-CFC447CD73FA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 0e8d3748c7ddd337cf2f27f5b272b208e79d503a
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027512"
---
# <a name="creating-a-fragment"></a>Criando um fragmento

Para criar um fragmento, uma classe deve herdar de `Android.App.Fragment` e, em seguida, substituir o método `OnCreateView`. `OnCreateView` será chamado pela atividade de hospedagem quando for o momento de colocar o fragmento na tela e retornará um `View`. Um `OnCreateView` típico criará esse `View` ao replanar um arquivo de layout e, em seguida, anexá-lo a um contêiner pai. As características do contêiner são importantes, pois o Android aplicará os parâmetros de layout do pai à interface do usuário do fragmento. O exemplo a seguir ilustra isso:

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    return inflater.Inflate(Resource.Layout.Example_Fragment, container, false);
}
```

O código acima irá inflar a exibição `Resource.Layout.Example_Fragment`e adicioná-la como uma exibição filho ao contêiner `ViewGroup`.

> [!NOTE]
> As subclasses de fragmento devem ter um construtor público padrão sem argumento.

## <a name="adding-a-fragment-to-an-activity"></a>Adicionando um fragmento a uma atividade

Há duas maneiras pelas quais um fragmento pode ser hospedado dentro de uma atividade:

- Os fragmentos de &ndash; **declarativamente** podem ser usados de forma declarativa em arquivos de layout de `.axml` usando a marca `<Fragment>`.

- Os fragmentos de &ndash; **programaticamente** também podem ser instanciados dinamicamente usando a API da classe `FragmentManager`.

O uso programático por meio da classe `FragmentManager` será discutido posteriormente neste guia.

### <a name="using-a-fragment-declaratively"></a>Usando um fragmento declarativamente

A adição de um fragmento dentro do layout requer o uso da marca `<fragment>` e, em seguida, a identificação do fragmento fornecendo o atributo `class` ou o atributo `android:name`. O trecho a seguir mostra como usar o atributo `class` para declarar uma `fragment`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment class="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Este próximo trecho mostra como declarar um `fragment` usando o atributo `android:name` para identificar a classe de fragmento:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment android:name="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Quando a atividade estiver sendo criada, o Android criará uma instância de cada fragmento especificado no arquivo de layout e inserirá a exibição criada a partir do `OnCreateView` no lugar do elemento `Fragment`.
Os fragmentos que são adicionados declarativamente a uma atividade são estáticos e permanecerão na atividade até que sejam destruídos; Não é possível substituir ou remover esse fragmento dinamicamente durante o tempo de vida da atividade à qual ele está anexado.

Cada fragmento deve ser atribuído a um identificador exclusivo:

- **Android: id** &ndash; como com outros elementos da interface do usuário em um arquivo de layout, essa é uma ID exclusiva.

- **Android: marca** &ndash; esse atributo é uma cadeia de caracteres exclusiva.

Se nenhum dos dois métodos anteriores for usado, o fragmento assumirá a ID da exibição do contêiner. No exemplo a seguir, em que nem `android:id` nem `android:tag` é fornecido, o Android atribuirá a ID `fragment_container` ao fragmento:

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
                android:id="+@id/fragment_container"
                android:orientation="horizontal"
                android:layout_width="match_parent"
                android:layout_height="match_parent">

        <fragment class="com.example.android.apis.app.TitlesFragment"
                android:layout_width="match_parent"
                android:layout_height="match_parent" />
</LinearLayout>
```

### <a name="package-name-case"></a>Caso de nome de pacote

O Android não permite caracteres maiúsculos em nomes de pacote; ele gerará uma exceção ao tentar aumentar a exibição se um nome de pacote contiver um caractere maiúsculo. No entanto, o Xamarin. Android é mais tolerante e tolerará caracteres em maiúsculas no namespace.

Por exemplo, ambos os trechos de código a seguir funcionarão com o Xamarin. Android. No entanto, o segundo trecho fará com que um `android.view.InflateException` seja lançado por um aplicativo Android baseado em Java puro.

```xml
<fragment class="com.example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

OU

```xml
<fragment class="Com.Example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

## <a name="fragment-lifecycle"></a>Ciclo de vida do fragmento

Os fragmentos têm seu próprio ciclo de vida que é, de certa forma, independente do, mas ainda são afetados pelo [ciclo de vida da atividade de hospedagem](~/android/app-fundamentals/activity-lifecycle/index.md).
Por exemplo, quando uma atividade é pausada, todos os fragmentos associados são pausados. O diagrama a seguir descreve o ciclo de vida do fragmento.

[diagrama de fluxo de ![ilustrando o ciclo de vida do fragmento](creating-a-fragment-images/fragment-lifecycle.png)](creating-a-fragment-images/fragment-lifecycle.png#lightbox)

### <a name="fragment-creation-lifecycle-methods"></a>Métodos de ciclo de vida de criação de fragmento

A lista abaixo mostra o fluxo dos vários retornos de chamada no ciclo de vida de um fragmento à medida que ele está sendo criado:

- **`OnInflate()`** &ndash; chamado quando o fragmento está sendo criado como parte de um layout de exibição. Isso pode ser chamado imediatamente depois que o fragmento é criado declarativamente a partir de um arquivo de layout XML. O fragmento ainda não está associado à sua atividade, mas a **atividade**, o pacote e o **conjunto**de **atributos** da hierarquia de exibição são passados como parâmetros. Esse método é melhor usado para analisar o **attributeset** e salvar os atributos que podem ser usados posteriormente pelo fragmento.

- **`OnAttach()`** &ndash; chamado depois que o fragmento é associado à atividade. Este é o primeiro método a ser executado quando o fragmento estiver pronto para ser usado. Em geral, os fragmentos não devem implementar um construtor ou substituir o construtor padrão. Todos os componentes necessários para o fragmento devem ser inicializados nesse método.

- **`OnCreate()`** &ndash; chamado pela atividade para criar o fragmento. Quando esse método é chamado, a hierarquia de exibição da atividade de hospedagem pode não ser completamente instanciada, portanto, o fragmento não deve depender de nenhuma parte da hierarquia de exibição da atividade até mais tarde no ciclo de vida do fragmento. Por exemplo, não use esse método para executar alterações ou ajustes na interface do usuário do aplicativo. Essa é a primeira hora em que o fragmento pode começar a coletar os dados necessários. O fragmento está sendo executado no thread da interface do usuário neste ponto, portanto evite qualquer processamento longo ou execute esse processamento em um thread em segundo plano. Esse método poderá ser ignorado se **SetRetainInstance (true)** for chamado.
    Essa alternativa será descrita em mais detalhes abaixo.

- **`OnCreateView()`** &ndash; cria a exibição para o fragmento.
    Esse método é chamado assim que o método **OnCreate ()** da atividade é concluído. Neste ponto, é seguro interagir com a hierarquia de exibição da atividade. Esse método deve retornar a exibição que será usada pelo fragmento.

- **`OnActivityCreated()`** &ndash; chamado após **Activity. OnCreate** foi concluído pela atividade de hospedagem.
    Os ajustes finais para a interface do usuário devem ser executados no momento.

- **`OnStart()`** &ndash; chamado depois que a atividade que a contém foi retomada. Isso torna o fragmento visível para o usuário. Em muitos casos, o fragmento conterá o código que, de outra forma, estaria no método **OnStart ()** de uma atividade.

- **`OnResume()`** &ndash; este é o último método chamado antes que o usuário possa interagir com o fragmento. Um exemplo do tipo de código que deve ser executado nesse método seria habilitar recursos de um dispositivo com o qual o usuário pode interagir, como a câmera que os serviços de localização. Os serviços como esses podem causar o esgotamento excessivo da bateria, mas um aplicativo deve minimizar seu uso para preservar a vida útil da bateria.

### <a name="fragment-destruction-lifecycle-methods"></a>Métodos de ciclo de vida de destruição de fragmento

A próxima lista explica os métodos de ciclo de vida que são chamados como um fragmento está sendo destruído:

- **`OnPause()`** &ndash; o usuário não pode mais interagir com o fragmento. Essa situação existe porque alguma outra operação de fragmento está modificando esse fragmento ou a atividade de hospedagem está em pausa. É possível que a atividade que hospeda esse fragmento ainda esteja visível, ou seja, a atividade em foco seja parcialmente transparente ou não ocupe a tela inteira. Quando esse método se torna ativo, é a primeira indicação de que o usuário está saindo do fragmento. O fragmento deve salvar as alterações.

- **`OnStop()`** &ndash; o fragmento não está mais visível. A atividade do host pode ser interrompida ou uma operação de fragmento a está modificando na atividade. Esse retorno de chamada tem a mesma finalidade que **Activity. OnStop**.

- **`OnDestroyView()`** &ndash; esse método é chamado para limpar os recursos associados à exibição. Isso é chamado quando a exibição associada ao fragmento foi destruída.

- **`OnDestroy()`** &ndash; esse método é chamado quando o fragmento não está mais em uso. Ele ainda está associado à atividade, mas o fragmento não está mais funcional. Esse método deve liberar todos os recursos que estão em uso pelo fragmento, como um [**SurfaceView**](xref:Android.Views.SurfaceView) que pode ser usado para uma câmera. Esse método poderá ser ignorado se **SetRetainInstance (true)** for chamado. Essa alternativa será descrita em mais detalhes abaixo.

- **`OnDetach()`** &ndash; esse método é chamado logo antes que o fragmento não esteja mais associado à atividade. A hierarquia de exibição do fragmento não existe mais e todos os recursos que são usados pelo fragmento devem ser liberados neste ponto.

### <a name="using-setretaininstance"></a>Usando SetRetainInstance

É possível que um fragmento especifique que ele não deve ser completamente destruído se a atividade estiver sendo recriada. A classe `Fragment` fornece o método `SetRetainInstance` para essa finalidade. Se `true` for passado para esse método, quando a atividade for reiniciada, a mesma instância do fragmento será usada. Se isso acontecer, todos os métodos de retorno de chamada serão invocados, exceto os retornos de chamada `OnCreate` e `OnDestroy` ciclo de vida. Esse processo é ilustrado no diagrama do ciclo de vida mostrado acima (pelas linhas pontilhadas verdes).

## <a name="fragment-state-management"></a>Gerenciamento de estado do fragmento

Os fragmentos podem salvar e restaurar seu estado durante o ciclo de vida do fragmento usando uma instância de um `Bundle`. O pacote permite que um fragmento salve dados como pares de chave/valor e é útil para dados simples que não exigem muita memória. Um fragmento pode salvar seu estado com uma chamada para `OnSaveInstanceState`:

```csharp
public override void OnSaveInstanceState(Bundle outState)
{
    base.OnSaveInstanceState(outState);
    outState.PutInt("current_choice", _currentCheckPosition);
}
```

Quando uma nova instância de um fragmento é criada, o estado salvo no `Bundle` ficará disponível para a nova instância por meio dos métodos `OnCreate`, `OnCreateView`e `OnActivityCreated` da nova instância.
O exemplo a seguir demonstra como recuperar o valor `current_choice` do `Bundle`:

```csharp
public override void OnActivityCreated(Bundle savedInstanceState)
{
    base.OnActivityCreated(savedInstanceState);
    if (savedInstanceState != null)
    {
        _currentCheckPosition = savedInstanceState.GetInt("current_choice", 0);
    }
}
```

A substituição de `OnSaveInstanceState` é um mecanismo apropriado para salvar dados transitórios em um fragmento entre alterações de orientação, como o valor de `current_choice` no exemplo acima. No entanto, a implementação padrão de `OnSaveInstanceState` cuida de salvar dados transitórios na interface do usuário para cada exibição que tenha uma ID atribuída. Por exemplo, examine um aplicativo que tem um elemento `EditText` definido em XML da seguinte maneira:

```xml
<EditText android:id="@+id/myText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"/>
```

Como o controle de `EditText` tem um `id` atribuído, o fragmento salva automaticamente os dados no widget quando `OnSaveInstanceState` é chamado.

### <a name="bundle-limitations"></a>Limitações de pacote

Embora o uso de `OnSaveInstanceState` facilita a economia de dados transitórios, o uso desse método tem algumas limitações:

- Se o fragmento não for adicionado à pilha voltar, seu estado não será restaurado quando o usuário pressionar o botão **voltar** .

- Quando o pacote é usado para salvar dados, esses dados são serializados. Isso pode levar a atrasos de processamento.

## <a name="contributing-to-the-menu"></a>Contribuindo para o menu

Os fragmentos podem contribuir com itens para o menu de sua atividade de hospedagem.
Uma atividade manipula itens de menu primeiro. Se a atividade não tiver um manipulador, o evento será passado para o fragmento, que irá tratá-lo.

Para adicionar itens ao menu da atividade, um fragmento deve fazer duas coisas.
Primeiro, o fragmento deve implementar o método `OnCreateOptionsMenu` e posicionar seus itens no menu, conforme mostrado no código a seguir:

```csharp
public override void OnCreateOptionsMenu(IMenu menu, MenuInflater menuInflater)
{
    menuInflater.Inflate(Resource.Menu.menu_fragment_vehicle_list, menu);
    base.OnCreateOptionsMenu(menu, menuInflater);
}
```

O menu no trecho de código anterior é replanado do seguinte XML, localizado no arquivo `menu_fragment_vehicle_list.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/add_vehicle"
        android:icon="@drawable/ic_menu_add_data"
        android:title="@string/add_vehicle" />
</menu>
```

Em seguida, o fragmento deve chamar `SetHasOptionsMenu(true)`. A chamada para esse método anuncia ao Android que o fragmento tem itens de menu para contribuir para o menu de opções. A menos que a chamada para esse método seja feita, os itens de menu do fragmento não serão adicionados ao menu de opção da atividade. Isso normalmente é feito no método de ciclo de vida `OnCreate()`, conforme mostrado no próximo trecho de código:

```csharp
public override void OnCreate(Bundle savedState)
{
    base.OnCreate(savedState);
    SetHasOptionsMenu(true);
}
```

A tela a seguir mostra como esse menu ficaria:

[![captura de tela de exemplo do meu aplicativo de viagens exibindo itens de menu](creating-a-fragment-images/fragment-menu-example.png)](creating-a-fragment-images/fragment-menu-example.png#lightbox)
